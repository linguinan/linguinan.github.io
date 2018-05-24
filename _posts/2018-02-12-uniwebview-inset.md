---
layout:     post
title:      "UniWebView自定义区域的坑"
subtitle:   "充分的测试很重要"
date:       2018-02-12
author:     "Lgn"
tags:
    - Unity
    - iOS
    - Plugin
---

>注意平台细节差异


在Unity中嵌入web页面，通常会用到插件：**UniWebView**

其默认打开网页是全屏方式

而如果要控制网页显示的区域和位置，就要设置：
````c#
new UniWebViewEdgeInsets(top, left, bottom, right);
````
一般来说，通过计算像素偏移值就好了，但在**Android**上显示正常，到**iOS**就有bug了

如果修改的是top值，则顶部会空余出一段空白，这可不是html页面的适配问题

原因：
在安卓平台插入的值是以**像素**为单位，
而在iOS平台，因为不同机型屏幕scale值不同，同一个值插入效果不一样
[参考链接](http://blog.csdn.net/u012662020/article/details/48264171)

[iOS获取屏幕尺寸和分辨率](https://www.jianshu.com/p/1cba3a285811)

````objc
float _Scale()
{
    CGRect screenRect = [[UIScreen mainScreen] bounds];
    CGSize screenSize = screenRect.size;
    NSLog(@"screen size %f - %f", screenSize.width, screenSize.height);

    CGFloat scale = [UIScreen mainScreen].scale;
    NSLog(@"screen scale %f", scale);
    return scale;
}
````

如上，把像素单位计算的值，再除以scale，才是UniWebViewEdgeInsets上该填的值
````c#
#if !UNITY_EDITOR && UNITY_IPHONE
        float scale = _Scale();
        top = (int)(top / scale);
#endif
````

>注：最近在新项目中实现时发现新问题

如果单纯取UI组件的像素值来计算的话，除了在设计分辨率下正常外，其他分辨率下都有或多或少的偏移。
经过实验，发现是因为在UI组件做了机型适配，在高度小于设计高度时，UI的实际高度被缩放了，处理如下：
````c#
int designHeight = 1080;
if(Screen.height < designHeight)
{//bar的高度不变，如果屏高小则需缩小后计算
    top = (int)(top * (Screen.height * 1.0f / designHeight));
}
````