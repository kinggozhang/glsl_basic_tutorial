# GLSL basic tutorial
GLSL is magic programming language, many fantastic,brilliant art works can be created by creative users. but its learning curve is not so smooth. that is the reason I write the basic tutorial, I hope it can help you to enter the greate world of GLSL :)
before we start, let me list what will be covered in the tutorial
## GLSL basic introduction
### GLSL globle variables
gl_FragCoord：这是一个全局变量，类型是vec4, 代表计算分量的坐标xyzw。因为GPU运算都是并行的，可以想象屏幕被分成很多小块，这个变量就用来代表每个块的坐标。
gl_FragColor: 这个全局变量也是vec4,代表分块的颜色信息rgba.最后屏幕什么就看你如何控制这个变量了。
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
demo http://www.sumoon.com/glsl/sandbox_basic.html?id=5e1981de43c257006fe4abf0
### draw a dot
we start our tutorial with very simple one: draw a white dot on specified position.
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
因为GPU都是浮点运算，浮点的精度支持三种:lowp, mediump, highp .精度越高当然需要更多的运算力。
首先定义一个子函数drawdot
http://www.sumoon.com/glsl/sandbox_basic.html?id=5e1976ad5620710077b857dd

### draw a line
### draw a letter
### draw hello world

### draw circle
### draw olymic logo
### draw ellipse
### draw triangle
### draw square
### draw hexagon
### draw polygon

### draw curve


