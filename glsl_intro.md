### 基本类型:

|类型|说明|
|---|---|
|__void__|空类型,即不返回任何值|
|__bool__|布尔类型 true,false|
|__int__|带符号的整数 signed integer|
|__float__|带符号的浮点数 floating scalar|
|__vec2, vec3, vec4__|n维浮点数向量 n-component floating point vector|
|__bvec2, bvec3, bvec4__|n维布尔向量 Boolean vector|
|__ivec2, ivec3, ivec4__|n维整数向量 signed integer vector|
|__mat2, mat3, mat4__|2x2, 3x3, 4x4 浮点数矩阵 float matrix|
|__sampler2D__|2D纹理 a 2D texture|
|__samplerCube__|盒纹理 cube mapped texture|


### 基本结构和数组:

|类型|说明|
|---|---|
|结构|struct type-name{} 类似c语言中的 结构体|
|数组| float foo[3] glsl只支持1维数组,数组可以是结构体的成员|


### 向量的分量访问:

glsl中的向量(vec2,vec3,vec4)往往有特殊的含义,比如可能代表了一个空间坐标(x,y,z,w),或者代表了一个颜色(r,g,b,a),再或者代表一个纹理坐标(s,t,p,q) 
所以glsl提供了一些更人性化的分量访问方式.

`vector.xyzw`  其中xyzw 可以任意组合

`vector.rgba`  其中rgba 可以任意组合

`vector.stpq`  其中rgba 可以任意组合

```cpp
vec4 v=vec4(1.0,2.0,3.0,1.0);
float x = v.x; //1.0
float x1 = v.r; //1.0
float x2 = v[0]; //1.0

vec3 xyz = v.xyz; //vec3(1.0,2.0,3.0)
vec3 xyz1 = vec(v[0],v[1],v[2]); //vec3(1.0,2.0,3.0)
vec3 rgb = v.rgb; //vec3(1.0,2.0,3.0)

vec2 xyzw = v.xyzw; //vec4(1.0,2.0,3.0,1.0);
vec2 rgba = v.rgba; //vec4(1.0,2.0,3.0,1.0);

```

### 运算符:

|优先级(越小越高)|运算符|说明|结合性|
|---|---|---|---|
|1|__()__|聚组:a*(b+c)|N/A|
|2|__[] () . ++ --__ |数组下标__[]__,方法参数__fun(arg1,arg2,arg3)__,属性访问__a.b__,自增/减后缀__a++  a--__|L - R|
|3|__++ -- + - !__|自增/减前缀__++a  --a__,正负号(一般正号不写)__a ,-a__,取反__!false__|R - L|
|4|__* /__|乘除数学运算|L - R|
|5|__+ -__|加减数学运算|L - R|
|7|__< > <= >=__|关系运算符|L - R|
|8|__== !=__|相等性运算符|L - R|
|12|__&&__|逻辑与|L - R|
|13|__^^__|逻辑排他或(用处基本等于!=)|L - R|
|14|__\|\|__|逻辑或|L - R|
|15|__? :__|三目运算符|L - R|
|16|__= += -= *= /=__|赋值与复合赋值|L - R|
|17|__,__|顺序分配运算|L - R|

*ps 左值与右值:*

    左值:表示一个储存位置,可以是变量,也可以是表达式,但表达式最后的结果必须是一个储存位置.

    右值:表示一个值, 可以是一个变量或者表达式再或者纯粹的值.

    操作符的优先级：决定含有多个操作符的表达式的求值顺序，每个操作的优先级不同.

    操作符的结合性：决定相同优先级的操作符是从左到右计算，还是从右到左计算。

### 基础类型间的运算:

glsl中,没有隐式类型转换,原则上glsl要求任何表达式左右两侧(l-value),(r-value)的类型必须一致 也就是说以下表达式都是错误的:

```cpp
int a =2.0; //错误,r-value为float 而 lvalue 为int.
int a =1.0+2;
float a =2;
float a =2.0+1;
bool a = 0; 
vec3 a = vec3(1.0, 2.0, 3.0) * 2;

```
