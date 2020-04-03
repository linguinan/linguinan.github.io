---
layout:     post
title:      "整理Android中的BuildConfig类"
subtitle:   ""
date:       2020-04-03
author:     "Lgn"
tags:
    - Android 
    - BuildConfig
---

>了解来龙去脉

参考文章如下：  
[Android中BuildConfig类的那些事<一>](https://www.jianshu.com/p/33b5f40cd326)  
[Android中BuildConfig类的那些事<二>](https://www.jianshu.com/p/3474ce4609a8)  
[Exclude BuildConfig.class from Android library jar in Gradle](https://stackoverflow.com/questions/24931126/exclude-buildconfig-class-from-android-library-jar-in-gradle)  

# 处理
* mac下删除jar包里面文件的命令
````sh
zip -d classes.jar com/*/*/BuildConfig.class
````

* 如实在不需要，可直接在gradle中排除
````java
apply plugin: 'com.android.library'

afterEvaluate {
  generateReleaseBuildConfig.enabled = false
}
````
如上设置经实验可行，如发现该类还存在jar包中，可尝试先"clean"再"assembleRelease"

# 由来
此类为AS中的Build系统生成  
是由 AndroidManifest.xml 文件中的 $\color{red}{manifest}$ 标签中的 $\color{yellow}{package}$ 属性指定的包路径
````java
package com.lgn.demo;

public final class BuildConfig {
    //这个常量是标识当前是否为`debug`环境，由`buildType`中的`debuggable`来确定的，这是修改此类值的一个方式
    public static final boolean DEBUG = Boolean.parseBoolean("true");
    //application id
    public static final String APPLICATION_ID = "com.jay.demo";
    //当前编译方式
    public static final String BUILD_TYPE = "debug";
    //编译渠道，如果没有渠道，则为空
    public static final String FLAVOR = "";
    //版本号
    public static final int VERSION_CODE = 1;
    //版本名，所以获取当前应用的版本名可以直接 BuildConfig.VERSION_NAME
    public static final String VERSION_NAME = "1.0";
    
    public BuildConfig() {
    }
}
````

给FLAVOR字段赋值
````json
android {
    ......
    productFlavors{
        应用宝{

        }
    }
    ......
}
````

添加自定义字段
````json
android {
    ......
    buildType {
        debug {
            buildConfigField "String","BASE_URL","\"http://www.test.com/\""
            buildConfigField "int","DATE","20160701"
        }
    }
}
````
｜String type ｜ 要创建的字段类型，如上面的String与int｜  
｜String name ｜ 要创建的字段名，如上面的BASE_URL与DATE｜  
｜String value ｜ 创建此字段的值，如上面的\"http://www.test.com/\"与20160701｜  
