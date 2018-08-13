---
layout:     post
title:      "Cornerstone简要使用"
subtitle:   "记录常用设置"
date:       2018-08-13
author:     "Lgn"
tags:
    - mac
    - svn
---

今天想把项目提交到svn，好久没用Cornerstone，一上手有点蒙

* 1.创建仓库
在Cornerstone上没找到直接创建的方式，遂直接在后台建好了项目目录
也可在win上操作先行建好目录

* 2.导入仓库
在Repositories面板点击“+”，弹出设置：
![img](/img/in-post/svn_setting.png)

* 3.检出项目
对着导出的仓库项目右键，选择“Check Out Working Copy...”
在Working Copies面板中选中检出的项目

* 4.设置忽略文件
参考如下：
[Cornerstone设置项目提交到SVN上的忽略文件](https://www.jianshu.com/p/d662ca680c3d)
ios项目忽略文件设置
```swift
.DS_Store,build/,DerivedData/,*.pbxuser,!default.pbxuser,*.mode1v3,!default.mode1v3,*.mode2v3,!default.mode2v3,*.perspectivev3,!default.perspectivev3,xcuserdata/,*.moved-aside,*.xccheckout,*.xcscmblueprint,*.hmap,*.ipa,*.dSYM.zip,*.dSYM,timeline.xctimeline,playground.xcworkspace,.build/,Pods/
```

* 5.提交
后面都是常规的提交、更新等操作了