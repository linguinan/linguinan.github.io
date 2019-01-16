---
layout:     post
title:      "iOS多语言的工具化处理"
subtitle:   ""
date:       2019-01-16
author:     "Lgn"
tags:
    - ios
---

>项目到后期基本完成时，iOS中的多语言的处理的麻烦性就来了，有如下几点及其解决办法

## 问题一：  
在xcode中通过Localize生成了多语言后，在storyboard中有新增或改变内容时，没有同步到生成的strings文件中  
解决：（用BartyCrouch命令追加新的内容到strings文件中）
```shell
#安装
$ brew install bartycrouch
#使用
$ bartycrouch interfaces -p "/absolute/path/to/project"
```

## 问题二：  
每个storyboard都会生成strings，需配置的语言太多时，一个个配就很麻烦了  
解决：（通过借鉴一个[开源项目](https://github.com/linguinan/Localizable.strings2Excel)，自己实现了转换脚本）  

我的使用是这样的：  
* 在iOS项目下新建个Localizable文件夹，用来存放导出的xls文件
* 根据说明，安装两个组件pyexcelerator和xld
* strings转xls
```shell
python StringsAll2Xls.py -f /absolute/path/to/project/project -t /absolute/path/to/project/Localizable
```
* xls文件翻译完后，替换回原先的strings文件中的文字，不覆盖注释
```shell
python XlsMatch2Strings.py -f /absolute/path/to/project/Localizable -t /absolute/path/to/project/project
```
* 每个项目各写个脚本一键执行就好了

## 问题三：  
这个跟多语言无关了，就是本想在Localizable目录下添加两个shell脚本时遇到的问题：  
在shell中使用cd命令无效，解决是用". xxx.sh"来执行

后来考虑每个项目的特殊性，还是不做这种跳转了  
直接在python脚本下，根据各个项目分别配置脚本即可