---
layout:     post
title:      "记Unity项目接入应用宝的YSDK遇到的坑"
subtitle:   ""
date:       2020-01-20
author:     "Lgn"
tags:
    - Unity
    - Android 
    - YSDK
---

>年末了，总算是有点闲工夫来记录点开发日常了

>记录YSDK接入过程中遇到的两个无解答无资料可查的bug及其解决过程

题外话：本来公司发行方是接的聚合渠道平台的，按理所有渠道都通过平台输出对应渠道包就好了， 
奈何聚合平台那边在应用宝的单机游戏这块，没接入支付系统，没支付，支付！！！ 
好吧，赶急着在最后上线前几天要求开发商自己来接了...

# 一、按官方wiki接入后

初始化正常，点击登录，能拉取对应平台的授权，授权后回调游戏，提示：
“微信登录失败，请重试”、“QQ登录失败，请重试”。

经过排查，点击登录时有以下日志输出与Demo中的不同：
````java
D/YSDK_DOCTOR: YSDKSo module Version :10
W/System.err:     at com.tencent.ysdk.libware.device.c.d(Unknown Source:13)
W/System.err:     at com.tencent.ysdk.framework.request.e.a(Unknown Source:314)
W/System.err:     at com.tencent.ysdk.module.user.impl.wx.request.b.a(Unknown Source:210)
W/System.err:     at com.tencent.ysdk.framework.request.j.c(Unknown Source:9)
W/System.err:     at com.tencent.ysdk.framework.request.j.b(Unknown Source:0)
W/System.err:     at com.tencent.ysdk.framework.request.l.run(Unknown Source:4)
E/YSDK_DOCTOR: 获取URL通用参数异常
W/System.err:     at com.tencent.ysdk.framework.request.b.<init>(Unknown Source:8)
W/System.err:     at com.tencent.ysdk.framework.request.j.c(Unknown Source:119)
W/System.err:     at com.tencent.ysdk.framework.request.j.b(Unknown Source:0)
W/System.err:     at com.tencent.ysdk.framework.request.l.run(Unknown Source:4)
E/YSDK_REQUEST: URLConnection is null
E/YSDK_SERVER: responseBody is null
````
从日志中完全摸不着问题所在，咨询平台方，答曰：**“检查下资源文件，还有生命周期”**  
&&好吧&&听话，经多次检查/打包/安装测试，问题依旧。  
多次咨询无回，至此我都快产生自我怀疑了，一个小小的sdk都能接到这么扎心。

最后，在测试中偶然想到去看看应用的权限设置。然后把所有权限都开了，居然发现登录成功了！！！  
经过排查，是以下权限没开启导致的：
````xml
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
````
安卓官方说明：从Android 6.0开始，每款 Android 应用都需要在运行时请求用户批准每项权限  
而为啥Demo没见到有这个呢，原来demo是按老版本编译的**compileSdkVersion = 22**  
这里不是吐槽啥，只是想指出一点，sdk接入中要求版本不低于26，为什么官方demo用的是**22**

## 解决方法(在登录时或启动时调用)：
````java
private static int requestCode = 123;

private boolean check() {
    boolean result = false;
    //判断当前系统的版本
    if(Build.VERSION.SDK_INT >= 23){
        int checkPermission = ContextCompat.checkSelfPermission(this,
                Manifest.permission.READ_PHONE_STATE);
        //如果没有被授予
        if(checkPermission != PackageManager.PERMISSION_GRANTED){
            //请求权限,此处可以同时申请多个权限
            ActivityCompat.requestPermissions(this, new String[]{Manifest.permission.READ_PHONE_STATE},  requestCode);
        }else{
            result = true;
        }
    }else {
        result = true;
    }
    return result;
}

public void loginQQ() {
    if (check()) {
        YSDKApi.login(ePlatform.QQ);
    }
}
````
参考自：  
[Android6.0请求权限方式](https://www.cnblogs.com/neo-java/p/10184958.html)  
[请求应用权限](https://developer.android.com/training/permissions/requesting?hl=zh-cn#java)


# 二、提审时才反馈的问题

接入时就看到的如下报错
````java
E/YSDK_TIPS: ******************* 游戏接入过程中请重点关注  *********************
E/YSDK_TIPS: * YSDK版本更新 - 1.3.1* 	
E/YSDK_TIPS: ***************************************************************
D/YSDK_CHANNEL: ApkChannelTool is V2Apk
D/YSDK_CHANNEL: com.tencent.ysdk.framework.channel.v2.a$a: No Channel block in APK Signing Block
W/System.err: com.tencent.ysdk.framework.channel.v2.a$a: No Channel block in APK Signing Block
W/System.err:     at com.tencent.ysdk.framework.channel.v2.a.b(Unknown Source:190)
W/System.err:     at com.tencent.ysdk.framework.channel.v2.b.a(Unknown Source:62)
W/System.err:     at com.tencent.ysdk.framework.channel.v2.b.b(Unknown Source:3)
W/System.err:     at com.tencent.ysdk.framework.channel.a.b(Unknown Source:19)
W/System.err:     at com.tencent.ysdk.framework.channel.a.a(Unknown Source:0)
W/System.err:     at com.tencent.ysdk.framework.a.b(Unknown Source:6)
W/System.err:     at com.tencent.ysdk.framework.a.a(Unknown Source:0)
W/System.err:     at com.tencent.ysdk.framework.f.a(Unknown Source:252)
W/System.err:     at com.tencent.ysdk.api.YSDKInnerApi.onCreate(Unknown Source:11)
W/System.err:     at com.tencent.ysdk.api.YSDKApi.onCreate(Unknown Source:34)
D/YSDK_CHANNEL: comment is null
D/YSDK_CHANNEL: ZIPComment [p={}, otherData=null]
D/YSDK_CHANNEL: ZIPComment [p={}, otherData=null]
D/YSDK a.b:  Comment: null
E/YSDK_TIPS: ******************* 游戏接入过程中请重点关注  *********************
````
此处不吐槽了，经过咨询和查询wiki也没结果，游戏各个接口调用也正常，鉴于这类日志很多，也就没深入了。

后来，在提审时才提示有问题：
![img](/img/in-post/ysdk_sign_error.jpg)

经过查阅得知，安卓中的签名共有两种，V1和V2。V2是从Android 7.0新增的签名。  
从Unity2017开始，默认使用V1和V2两种签名，项目用的是Unity2018，直接降低版本是不现实的，可能导致更多问题。

那只能在打包时去掉v2签名，仅用v1签名了，而Unity本身没有这种选项设置。

## 解决方法（多种尝试后得出）：

* Build Settings 中修改 Build System 为 Gradle，勾选 Export Project

* 打包输出AS项目后，在gradle中追加设置如下：
````java
signingConfigs {
    release {
        ... ...
        v2SigningEnabled false
    }
}
````

* 用assemble打包输出apk  
参考：[android多渠道打包方案总结及APK signature scheme v2兼容](https://blog.csdn.net/cdecde111/article/details/55506311)  

* 其他遇到的问题：  
[Unity2018 Gradle打包安卓包报错 Program type already present:com.xx.xx.BuildConfig](https://blog.csdn.net/cl6348/article/details/89187939)  

mac下删除jar包里面文件的命令
````sh
zip -d classes.jar com/tencent/tmgp/***/BuildConfig.class
````
参考：[How to delete a file from a Java Jar file (use zip)](https://alvinalexander.com/source-code/java/how-delete-file-java-jar-file-zip-d)