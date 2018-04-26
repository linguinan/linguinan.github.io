---
layout:     post
title:      "iOS项目app名字本地化"
subtitle:   "Unity导出Xcode图文详解设置流程"
date:       2018-04-26
author:     "Lgn"
tags:
    - Unity
    - iOS
---

>最近用Unity发布了个Xcode项目，要全地区发布，所以app的名字需要做多语言本地化处理
>看了些文章介绍，讲的有点笼统，遂自己摸索后记录了个图文过程如下：

在这里切换到Project
![img](/img/in-post/localizing-1.png)

添加需要配置的语言，点击Finish
![img](/img/in-post/localizing-2.png)

新建多语言文本文件
![img](/img/in-post/localizing-3.png)

选择文件格式
![img](/img/in-post/localizing-4.png)

命名并保存到Classes目录
![img](/img/in-post/localizing-5.png)

此时选中新建的文件，添加如下内容
![img](/img/in-post/localizing-6.png)

在右侧属性栏，点击Localize，弹窗按默认确定即可
![img](/img/in-post/localizing-7.png)

勾选新加的语言
![img](/img/in-post/localizing-8.png)

勾选后，这里会出现不同语言的子项，选中修改
![img](/img/in-post/localizing-9.png)

最后切回到Targets中的Info，新增一条记录， 并将值设为**YES**
![img](/img/in-post/localizing-10.png)

大功告成！

>另外看到有介绍直接在Unity导出Xcode时脚本设置本地化，将在下篇中总结过程