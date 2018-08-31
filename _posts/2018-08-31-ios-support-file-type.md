---
layout:     post
title:      "为App添加特定文件支持类型"
subtitle:   ""
date:       2018-08-31
author:     "Lgn"
tags:
    - ios
    - swift
---

>最近要给App添加支持打开特定类型文件，在参考相关博文时感觉其中的设置都有些过时了，现在特整理下当前可用的实现如下
* 环境
Xcode 9.4.1
Swift 4.1

# 设置支持文本文件类型
![img](/img/in-post/ios_doc_type.png)

# info.plist
```xml
<key>CFBundleDocumentTypes</key>
<array>
    <dict>
        <key>CFBundleTypeIconFiles</key>
        <array/>
        <key>CFBundleTypeName</key>
        <string>json</string>
        <key>LSItemContentTypes</key>
        <array>
            <string>public.data</string>
            <string>public.content</string>
        </array>
    </dict>
</array>
```

# 在AppDelegate.swift中接收
```swift
func application(_ application: UIApplication, open url: URL, sourceApplication: String?, annotation: Any) -> Bool {
    print("open url", url)
    
    let isJson = url.absoluteString.hasSuffix(".json")
    if !isJson  {
        return true
    }
    
    do {
        var content:String = try String(contentsOf: url, encoding: .utf8)
        print(content)
    } catch {
        print("read file error", error)
    }
    
    return true
}
```

# 参考
[Registering the File Types Your App Supports](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)

[System-Declared Uniform Type Identifiers](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html#//apple_ref/doc/uid/TP40009259-SW1)