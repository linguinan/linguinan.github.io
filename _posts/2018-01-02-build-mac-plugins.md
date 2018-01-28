---
layout:     keynote
title:      "打包mac和ios用的Unity的plugins"
subtitle:   "原生插件打包"
date:       2018-01-01
author:     "Lgn"
tags:
    - Unity
    - iOS
    - Plugin
---


> 总结插件开发方式，提高底层原生调用的能力


#[Building a Plugin for Mac OS X](https://docs.unity3d.com/Manual/PluginsForDesktop.html)
````c#
[DllImport ("PluginName")]
private static extern float FooPluginFunction ();
````

实验过了，可用例子里的cpp
也可以跟ios的一样，用一样的objc代码，只是上面的名字一定要用导出的**bundle文件名**

##新建bundle项目
``` 
[XCode] --> [File] --> [NewProject
[macOS] --> [Framework & Library] --> [Bundle]
```


#[Building Plugins for iOS](https://docs.unity3d.com/Manual/PluginsForIOS.html)
````c#
[DllImport ("__Internal")]
private static extern float FooPluginFunction();
````

要实现重写Unity的启动程序，需在末尾加上：
````c#
//自动修改启动程序
IMPL_APP_CONTROLLER_SUBCLASS(MainDelegate)
````
原理在底部[说明](#mark)

##两种原生实现方式：
一、直接在Plugin/iOS目录下编写.m/.h文件，暴露接口直接调用

二、把独立实现的c/cpp/objc单独打包成静态库.a文件

打包流程：
[iOS开发手把手教你如何打包静态库.a文件](https://www.jianshu.com/p/e25e4b391a68)
* 1.建库
![img](/img/in-post/plugin-1.png)

* 2.设置release
![img](/img/in-post/plugin-2.png)

* 3.选择真机进行运行，在运行后我们找到生成的.a文件右键选择show in finder就可以了
![img](/img/in-post/plugin-3.png)

- [ ]mac sample
- [ ]ios sample

<p id = "mark"></p>
---

UnityAppController.h 里面有这样一个宏：
````objective-c
#define IMPL_APP_CONTROLLER_SUBCLASS(ClassName) 
@interface ClassName(OverrideAppDelegate)       
{                                               
}                                               
+(void)load;                                    
@end  

@implementation ClassName(OverrideAppDelegate)  
+(void)load                                     
{                                               
    extern const char* AppControllerClassName;  
    AppControllerClassName = #ClassName;        
}                                               
@end
````

将这个宏加到 CustomAppController.mm 中，即可实现自动设置 AppControllerClassName ：
IMPL_APP_CONTROLLER_SUBCLASS (CustomAppController)

是不是很神奇呢！
IMPL_APP_CONTROLLER_SUBCLASS 使用了两个 Objective-C 的特性，
* 一是 category ，用来给已有的类扩展新的方法；
* 二是 +(void)load 静态方法，它会在运行时 CustomAppController 类被加载到内存中时触发，
这个时间点比 int main() 函数还要早，所以能够提前“篡改” AppControllerClassName，达到我们的目的。
