---
layout:     post
title:      "关于FB接入踩的坑"
subtitle:   "帐号无法切换，分享权限审核"
date:       2018-02-03
author:     "Lgn"
tags:
    - Unity
    - iOS
    - Facebook
---

> 总结插件开发方式，提高底层原生调用的能力

# 关于登录

iOS接入fb后，在跳出的页面登录时，
如果选择了在页面里进行帐号登录，会被fb记录在浏览器中，后续都会通过这个帐号登录，
即使在app中调用了logout或者fb中退出了帐号也没用。

后来在某个论坛中才看到有人说，要在浏览器中打开fb，然后在个人设置中点击退出，才会真的退出了。
这时再回到我们自己的app中点击fb登录才会重新出现选择帐号的界面。。。

# 关于分享权限

之前接fb分享时，听说要申请权限才行。
拖了好久后，终于打了模拟器包提交fb审核。
隔天收到回复-》不用申请“publish_actions”权限，因为是[分享对话框](https://developers.facebook.com/docs/sharing/reference/share-dialog) 
--》我狂汗。。。

打包流程还是在这里简单记录下吧：
* 创建模拟器版本，直接看[官方介绍](https://developers.facebook.com/docs/ios/getting-started/advanced?locale=zh_CN)

* Unity打包模拟器，需要在发布前修改如下设置，以期导出模拟器版本文件
![img](/img/in-post/simulator_setting2.png)

* 静态库的坑

如果项目中有自己写的静态库
在打包模拟器版本时可能会崩溃，因为静态库中可能没有包含模拟器所用的框架
````
模拟器：
4s~5 : i386
5s~6plus : x86_64
真机：
3gs~4s : armv7
5~5c : armv7s （静态库只要支持了armv7，就可以跑在armv7s的架构上）
5s~6plus : arm64
````

解决此情况，需重新打包静态库
进入Build Settings设置，在目标中选择模拟器后再运行打包，再次将导出的静态库替换到项目中即可

*xcode9进入DerivedData的[方式](https://stackoverflow.com/questions/46468220/how-to-delete-derived-data-in-xcode-9)
````
Shift+command+G
英文状态输入：~/Library/Application Support/
回车
````