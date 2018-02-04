---
layout:     post
title:      "Shader中条件语句的优化使用方式"
subtitle:   "0乘与任何数都为0"
date:       2018-02-04
author:     "Lgn"
tags:
    - Unity
    - Shader
    - GPU
---

> 同样的结果，换种方式实现可能更有效

原参考帖子
[if statements in fragment](https://forum.unity.com/threads/if-statements-in-fragment.179707/)

shader中一般不推荐使用if语句，因为使用if会使得GPU计算变慢，且在某些机型上还有兼容的问题。

在该篇帖子中，有个回复解释如下：

>when one part of the threads in a warp needs to run the first branch, 
>and another part needs to run the second branch, both branches will be calculated for both parts. 
>As a result, the shader takes as much time as both of the branches combined.

大体意思是，即使用了条件判断，但实际上CPU在每个分支的处理中，都会把其他分支内容都执行一遍，这将导致计算消耗上的几何倍数的增加。


# 常规条件语句IF的写法

```` c#
if(i.screenPos.x < _Clip.x){
    texcol.a = 0;
};
if(i.screenPos.x > (_Clip.z)){
    texcol.a = 0;
};
if(i.screenPos.y < (_Clip.y)){
    texcol.a = 0;
};
if(i.screenPos.y > (_Clip.w)){
    texcol.a = 0;
};
````

优化的关键是一个**HLSL内置函数**
```` c#
step(x, y)             //返回（y >= x）? 1 : 0。
````

如上可替换为：
```` c#
texcol.a *= (step(_Clip.x, i.screenPos.x) * step(i.screenPos.x, _Clip.z)) * (step(_Clip.y, i.screenPos.y) * step(i.screenPos.y, _Clip.w));
````

---

实际开发中的使用：
```` c#
float4 restult = float4(0.0, 0.0, 0.0, 0.0);

float sign1 = step(row, 255);   //条件：row小于255，则只为1
float4 restult1 = sign1 * func1(_MainTex, row, col);

//(1.0 - sign1)排除了第一种情况，进入第二个条件
float sign2 = step(row, 383);
float4 restult2 = (1.0 - sign1) * sign2 * func2(_MainTex, row, col);    

restult = restult1 + restult2;
````