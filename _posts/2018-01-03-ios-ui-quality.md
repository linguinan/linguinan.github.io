---
layout:     post
title:      "iOS中UI图片显示模糊"
subtitle:   ""
date:       2018-01-03
author:     "Lgn"
tags:
    - Unity
    - iOS
---


> 出现问题要冷静分析


最近发布ios测试，发现UI显示的质量比Android还低，网上查到的都说改图片格式之类的，皆无用

后来细想比Android机显示的品质还低，出去图片的压缩格式设置外，那肯定是两者的一些配置上的差异问题：
* 1.检查图片的压缩格式，发现没设置压缩
* 2.检查Edit——Project Settings——Quality

发现ios和Android设置不一，ios上设置的是Fastest，在改为Simple后，再发布，图片清晰了，汗...

设置如下：
![img](/img/in-post/quality-settings.png)