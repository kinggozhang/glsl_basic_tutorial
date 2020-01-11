# GLSL basic tutorial
GLSL is magic programming language, many fantastic,brilliant art works can be created by creative users. but its learning curve is not so smooth. that is the reason I write the basic tutorial, I hope it can help you to enter the greate world of GLSL :)
before we start, let me list what will be covered in the tutorial
## GLSL basic introduction
### 入口函数
GLSL的入口函数是void main, 跟C程序类似的。每个GLSL程序必须实现入口函数，否则编译错误。入口函数不需要返回值，也不需要参数。
```
void main(void)
{

}
```
### GPU分量
我们知道GPU都是并行计算，所以在进行浮点运算时比CPU要快很多。因为图像是经由GPU渲染后显示到屏幕上的，屏幕在物理上都是方形的像素阵列。GPU会把屏幕分成很多小块，然后并行渲染。在渲染之前，就会调用GLSL程序，进行最后的着色过程，此时我们可以思考下，为了GLSL能够更好地着色，至少需要哪些信息？

### GLSL globle variables
-gl_FragCoord：这个全局变量类型是vec4, 代表计算分量的坐标xyzw。因为GPU运算都是并行的，可以想象屏幕被分成很多小块，这个变量就用来代表每个块的坐标。
-gl_FragColor: 这个全局变量也是vec4,代表分块的颜色信息rgba.最后屏幕什么就看你如何控制这个变量了。
### draw white backgroud
we start our tutorial with very simple one:draw white backgroud. you have known gl_FragColor can control color of your device. generally, RGB value ranges from 0 to 255. but in GLSL, RGB value ranges from 0.0 to 1.0 . actually, many of other values have same range. to draw white backgroud, simply, we set every fract white color,that is:RGB(1.0,1.0,1.0).
```
#ifdef GL_ES
precision mediump float;
#endif

void main(void) 
{
    gl_FragColor = vec4(vec3(1.), 1.);
}
```
precision mediump float;这行代码用来设置浮点精度。
因为GPU都是浮点运算，浮点的精度支持三种:lowp, mediump, highp .精度越高当然需要更多的运算力。
demo http://www.sumoon.com/glsl/sandbox_basic.html?id=5e1981de43c257006fe4abf0
### draw a dot
如何画一个白点呢？  通常情况，点应该是圆形的，此时为了方便起见，我们画一个方形的点。那么，最小的点就应该是屏幕上的一个像素。通过分量的坐标，只要其在指定范围就返回RGB(1.0,1.0,1.0);
```
#ifdef GL_ES
precision mediump float;
#endif

vec3 drawdot(float x, float y)
{
    float r=0.0;
    if(gl_FragCoord.x <= x && gl_FragCoord.x >= (x-2.) && gl_FragCoord.y <= y && gl_FragCoord.y >= (y-2.) )
        r=1.;    
    return vec3(r);
}
void main(void) 
{
    vec3 col = drawdot(350., 150.);
    gl_FragColor = vec4(col, 1.);
}
```

首先定义一个子函数drawdot，这个函数接收两个参数x,y 分别表示分量的坐标。返回的是rgb颜色。
http://www.sumoon.com/glsl/sandbox_basic.html?id=5e1976ad5620710077b857dd

### draw a line
学会了上面的画点，画一条线就没那么难了，毕竟多个点可以组成线。 类似下面这样。
```
for(int i=0;i<10;i++)
   drawdot(x+i, y);
```
这种方法可行，但是有一定的局限性。我们稍微扩展一下上面画点的思维，将点的长度，或者宽度延展一下，不就成了线吗？
```
#ifdef GL_ES
precision mediump float;
#endif

vec3 drawline(float x, float y, float xl, float yl)
{
	
    xl = max(1., xl);
    yl = max(1., yl);
    float r=0.0;
    if(gl_FragCoord.x <= (x+xl) && gl_FragCoord.x >= x && gl_FragCoord.y <= y && gl_FragCoord.y >= (y-yl) )
        r=1.;    
    return vec3(r);
}
void main(void) 
{
    vec3 col = drawline(150.,150.,100., 0.);
    gl_FragColor = vec4(col, 1.);
}
```
x,y代表线的起点，xl代表水平方向的长度，yl代表垂直方向的长度。 这样的参数设计可能并不理想，但是我们只是为了演示，问题不大。
演示http://www.sumoon.com/glsl/sandbox_basic.html?id=5e182dd27d5774006ab8c627  
这个函数可以画水平和垂直的线，但是斜线他就没办法了。那么如何画一条斜线呢？首先，需要知道起点和终点， 然后判断任一点是否在起点跟终点的连线上。
这时，我们需要一点三角函数的知识，判断两点的斜率是否相同即可。
```
vec3 drawline(vec2 start, vec2 end)
{   
   float len = distance(start, end);
   float r=0.0;
   float yl = end.y-start.y;
   float sinv = yl/len;
   //check frag coord, it should be in the range of start,end;
   if(gl_FragCoord.x >= min(start.x,end.x) && gl_FragCoord.x <= max(start.x, end.x) 
      && gl_FragCoord.y >= min(start.y, end.y) && gl_FragCoord.y <= max(start.y, end.y))
   {
	   float slen = distance(start, gl_FragCoord.xy);
	   float ssinv = (gl_FragCoord.y - start.y)/slen;
	   if(abs(sinv-ssinv) <0.01)
	   {
		   r = 1.0;
	   }
   }
   return vec3(r);
}

```
你会可能会发现，斜线画的并不完美，主要是因为坐标精度问题。这一点后续一并解决。
### draw letters(HELLO WORLD)
来到这里，我们既可以画水平、垂直线段，也可以画斜线，我们就可以画字母了。 
H可以用三条线段解决,O可以用方形表达。比较麻烦的是W，需要用斜线来画。
```
#ifdef GL_ES
precision mediump float;
#endif

vec3 drawline(float x, float y, float xl, float yl)
{
	
    xl = max(1., xl);
    yl = max(1., yl);
    float r=0.0;
    if(gl_FragCoord.x <= (x+xl) && gl_FragCoord.x >= x && gl_FragCoord.y <= y && gl_FragCoord.y >= (y-yl) )
        r=1.;    
    return vec3(r);
}
vec3 drawline(vec2 start, vec2 end)
{   
   float len = distance(start, end);
   float r=0.0;
   float yl = end.y-start.y;
   float sinv = yl/len;
   //check frag coord, it should be in the range of start,end;
   if(gl_FragCoord.x >= min(start.x,end.x) && gl_FragCoord.x <= max(start.x, end.x) 
      && gl_FragCoord.y >= min(start.y, end.y) && gl_FragCoord.y <= max(start.y, end.y))
   {
	   float slen = distance(start, gl_FragCoord.xy);
	   float ssinv = (gl_FragCoord.y - start.y)/slen;
	   if(abs(sinv-ssinv) <0.01)
	   {
		   r = 1.0;
	   }
   }
   return vec3(r);
}
vec3 drawW(float x, float y)
{
	vec3 col = drawline(vec2(x,y+20.5), vec2(x+10., y));
	col += drawline(vec2(x+10.,y), vec2(x+15., y+10.5));
	col += drawline(vec2(x+15.,y+10.5), vec2(x+20., y));
	col += drawline(vec2(x+20.,y), vec2(x+30., y+20.5));
	return col;
}
vec3 drawH(float x, float y)
{   
    vec3 col = drawline(x,y+10.,10., 0.);
    col += drawline(x,y+20.,0.,20.);
    col += drawline(x+10.,y+20.,0.,20.);
    return col;
}
vec3 drawR(float x, float y)
{   
    vec3 col = drawline(x,y+10.,10., 0.);
    col += drawline(x,y+20.,0.,20.);
    col += drawline(x+10.,y+20.,0.,10.);	
    col += drawline(x,y+20.,10.,0.);
    col += drawline(vec2(x+5.5,y+10.5), vec2(x+10.5, y));	
    return col;
}
vec3 drawE(float x, float y)
{   
    vec3 col = drawline(x,y+10.,10., 0.);
    col += drawline(x,y+20.,0.,20.);
    col += drawline(x,y+20.,10.,0.);
    col += drawline(x,y,10.,0.);
	
    return col;
}
vec3 drawL(float x, float y)
{   
    vec3 col = drawline(x,y,10., 0.);
    col += drawline(x,y+20.,0.,20.);
    
    return col;
}
vec3 drawO(float x, float y)
{   
    vec3 col = drawline(x,y,10., 0.);
    col += drawline(x,y+20.,0.,20.);
    col += drawline(x+10.,y+20.,0.,20.);
    col += drawline(x,y+20.,10., 0.);	
    return col;
}
vec3 drawD(float x, float y)
{   
    vec3 col = drawline(x,y,5., 0.);
    col += drawline(x,y+20.,0.,20.);
    col += drawline(x+10.,y+15.,0.,10.);
    col += drawline(x,y+20.,5., 0.);	
    col += drawline(vec2(x+5.,y), vec2(x+10., y+5.));
    col += drawline(vec2(x+5.,y+20.), vec2(x+10., y+15.));
	return col;
}
void main(void) 
{
    vec3  col = drawH(50., 50.);
    col += drawE(65.,50.);
    col += drawL(80.,50.);
    col += drawL(95.,50.);
    col += drawO(110.,50.);
	
	
    col += drawW(130.,50.);	
col += drawO(165.,50.);
col += drawR(180.,50.);	
col += drawL(195.,50.);
col += drawD(210.,50.);
    gl_FragColor = vec4(col, 1.);
}
```
http://www.sumoon.com/glsl/sandbox_basic.html?id=5e18425c21460d006a7cc52e 你可以看到，直线部分比较清晰，但是斜线不够完美，留待后续解决。至少我们已经用GLSL实现了 HELLO WORLD. 这可能是我写过的最复杂的HELLO WORLD :(
### 坐标映射
前面的例子我们可以看到，gl_FragCoord的坐标值跟屏幕分辨率相关。以我机器为例，1920*1080分辨率下，gl_FragCoord.x最大值为620左右。因为该值决定了GPU分量的个数，我想应该与显卡性能有关。未经证实。 如果我们的程序都是基于gl_FragCoord，  有时候在不同分辨率下，现实效果就不一样了。因此我们需要一个相对坐标。就是将gl_FragCoord映射到0~1之间的浮点数值。这样跟gl_FragColor就更搭配，渐渐你会发现这样做的好处。先介绍一个常量：uniform vec2 resolution;这就是你的GL上下文的分辨率。

```
uniform vec2 resolution;
void main() {
    vec2 st = gl_FragCoord.xy/resolution;
    vec3 col;        	
    if(st.x> .5)
       col = vec3(1.,0.,0.);
    if(gl_FragCoord.x > 600.)
           col += vec3(0.,1.,0.); 
      
    gl_FragColor = vec4(col,1.);
}
```
 你会发现，无论分辨率如何变。 st.x都是在0到1之间变化。而gl_FragCoord.x却会随着分辨率的变化而变化。 自己去试试看吧。http://www.sumoon.com/glsl/sandbox_basic.html?id=5e19d8ac21b47e007027854a

### draw circle
### draw olymic logo
### draw ellipse
### draw triangle
### draw square
### draw hexagon
### draw polygon

### draw curve


