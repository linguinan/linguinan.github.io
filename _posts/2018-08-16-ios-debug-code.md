---
layout:     post
title:      "Xcode项目中设置DEBUG标签"
subtitle:   "调试利器，减少频繁切换模式"
date:       2018-08-16
author:     "Lgn"
tags:
    - xcode
    - debug
---

最近项目提交外网测试了，一些在本地用的调试UI和接口等，在外网就要禁用
而内网开发测试时确有需要用到，遂在考虑是否在Xcode项目中也用个标签来区分

找到这个参考：
[Xcode / iOS: How to determine whether code is running in DEBUG / RELEASE build?](https://stackoverflow.com/questions/9063100/xcode-ios-how-to-determine-whether-code-is-running-in-debug-release-build)

* 1.项目设置（一般默认就设置好的了）
![img](/img/in-post/ios_debug1.png)
![img](/img/in-post/ios_debug2.png)

* 2.代码中使用
```swift
#if DEBUG
    print("*********** is debug mode ***********")
    //code
#else
    print("*********** is release mode ***********")
#endif
```
