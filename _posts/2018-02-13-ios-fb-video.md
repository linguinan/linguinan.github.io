---
layout:     post
title:      "iOS分享视频到Facebook"
subtitle:   "坑坑更健康"
date:       2018-02-13
author:     "Lgn"
tags:
    - Unity
    - iOS
    - Facebook
---

>在实践中积累知识点

fb提供的[Unity插件](https://developers.facebook.com/docs/unity/)
在分享这块做的不好，只提供了分享网络url的方法，无法直接分享本地image或video。

遂自行封装了原生的ios分享以做调用，其中在做视频分享时遇到个坑：

直接把app保存到persistentDataPath的视频分享fb时，出错：
````objc
Error Domain=com.facebook.sdk.share Code=2 "(null)" UserInfo={com.facebook.sdk:FBSDKErrorArgumentValueKey=file:///var/mobile/Containers/Data/Application/3F5A4911-038B-4510-91CD-CB0C4488A0B2/Documents/Cache/icolor_5b1c524ac9174b0894fac9452a42ca47.mp4, com.facebook.sdk:FBSDKErrorArgumentNameKey=videoURL, com.facebook.sdk:FBSDKErrorDeveloperMessageKey=Invalid value for videoURL: file:///var/mobile/Containers/Data/Application/3F5A4911-038B-4510-91CD-CB0C4488A0B2/Documents/Cache/5b1c524ac9174b0894fac9452a42ca47.mp4}
````

videoURL不可用，查官方文档：
视频网址 videoURL 必须是**资产网址**。例如，您可以从 UIImagePickerController 获取视频资产网址。
按意思url必须是asset url，不死心的想把本地路径改为这个，还是不行，最后采用论坛里的一种做法实现：

````objc
void _FBShareVideo(char *readAddr)
{
	NSString *videoPath = [NSString stringWithUTF8String:readAddr];
	NSURL *videoURL = [NSURL fileURLWithPath:videoPath];
	
	if(delegate == NULL)
		delegate = [[FBDelegate alloc] init];
	
    //保存视频到相册
	ALAssetsLibrary *library = [[ALAssetsLibrary alloc] init];
	if([library videoAtPathIsCompatibleWithSavedPhotosAlbum:videoURL])
	{
		[library writeVideoAtPathToSavedPhotosAlbum:videoURL completionBlock:^(NSURL *assetURL, NSError *error) {
			if(error)
			{
				NSLog(@"write video error : %@", error);
			}else{
				FBSDKShareVideo *video = [[FBSDKShareVideo alloc] init];
				video.videoURL = assetURL;//对应相册中的资产地址
                //调用fb的弹窗分享
				FBSDKShareVideoContent *content = [[FBSDKShareVideoContent alloc] init];
				content.video = video;
				FBSDKShareDialog *dialog = [FBSDKShareDialog showFromViewController:UnityGetGLViewController() withContent:content delegate:delegate];
				dialog.mode = FBSDKShareDialogModeNative;
				BOOL state = [dialog show];
				NSLog(@"assetURL is %@", assetURL);
				NSLog(@"FBSDKShareDialog state %i", state);
			}
		}];
	}
}
````

以上的做法是把视频文件保存到相册后在发布的
````objc
assetURL is assets-library://asset/asset.mp4?id=9243C095-3AB9-4AB9-9065-5D96AFA86FF4&ext=mp4
````

实现中一个不太熟悉的知识点：**AssetsLibrary**

[摘录自](https://www.jianshu.com/p/c321861ef6fa)
* AssetsLibrary：代表iPhone中的资源库（包含所有的photo、video），可以这么认为，AssetsLibrary就代表iPhone中的相册应用。可以通过AssetsLibrary获取所有的AssetsGroup，同时可以向资源库中添加相册。
* 在AssetsLibrary框架之下，所有的遍历操作都是异步（Asynchronous）执行的。因为用户的资源库、相册可能很大，这种条件之下，异步遍历不至于堵塞主线程。
* AssetsLibrary 获取的资源只是个引用，如果要存储在数组中读取，则实例需要强引用，以保证不会ARC
* AssetsLibrary 遵循写入优先原则，即如果在写入时有其他进程进行读取操作则会抛出异常