---
layout:     post
title:      "Unity调用Android底层方法异常"
subtitle:   "AndroidJavaException: java.lang.NoSuchMethodError: no non-static method"
date:       2018-03-25
author:     "Lgn"
tags:
    - Unity
    - Android 
    - 笔记
---

>踩坑笔记
>
>不懂的知识点要认真对待，否则坑到的是自己

最近换了mac机，迫不及待的配置好了各种开发环境，把原先未开发完的项目导入Unity中发布apk后，调用自己用AS写的接口时，报了如下错误：
![img](/img/in-post/NoSuchMethodError.png)

因为是第一次中mac机上用Android Studio导出jar包，所以下意识的以为肯定是哪里配置错了，反复的检查对比，发布调试，错误依旧！

**参考官方手册**：  
[Extending the UnityPlayerActivity Java Code](https://docs.unity3d.com/Manual/AndroidUnityPlayerActivity.html)

**调用方法**：
````c#
using (AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
{
    using (AndroidJavaObject jo = jc.GetStatic<AndroidJavaObject>("currentActivity"))
    {
        int result = jo.Call<int>("add", 3, 6);
        Debug.Log("add result is ", result);
    }
}
````

如上设置和调用都没看出问题，Google到的回答无外乎配置或方法调用，都没对上

最好还是笃定自己代码没错，那就重新建个项目，写个简单接口，什么库都不用。
发布-》成功调用！！！

那排除配置和第三方库后，唯一可能就是Unity项目中都设置问题了。

经过检查，唯一差别就是发布设置了：  
在报错的项目中，Build System选都是**Gradle (New)**，而新建的项目用的是默认值！
修改-发布-》成功调用...

汗！赶紧查查这几个配置有啥不同！  
[Build Settings](https://docs.unity3d.com/Manual/BuildSettings.html)

| Build System |	说明（Google翻译） |
| - | - |
| Internal (Default)	| 使用基于Android SDK实用程序的内部Unity构建过程生成输出包（APK）。 |
| Gradle (New)	| 使用Gradle构建系统生成输出包（APK）。 支持直接编译和运行并将项目导出到目录。 这是导出项目的首选选项，因为Gradle是Android Studio的本机格式。 |
| ADT (Legacy)	| 以ADT（eclipse）格式导出项目。 输出包（APK）可以在eclipse或Android Studio中构建。 该项目类型不再受Google支持，并被视为已过时。 |

>如果不是自己自定义了Gradle打包，最好用Internal打包

>猜想是用Gradle打包时，默认的Gradle方法中没有把自定义的方法一起打包进去——》待验证