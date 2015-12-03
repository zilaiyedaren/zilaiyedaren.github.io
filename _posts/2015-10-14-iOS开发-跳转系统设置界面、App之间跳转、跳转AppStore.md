---
layout: post
title:  "iOS开发-跳转系统设置界面、App之间跳转、跳转AppStore"
author: 自来也大人
feature-img: "img/touring.jpg"
category: ios
tag: 整合
#tags: [ios, github]
---

## 1.跳转系统设置界面 ##

### 跳到设置界面 ###

比如：定位服务、FaceTime、蓝牙、音乐、iCloud设置等

**定位服务**

定位服务有很多APP都有，如果用户关闭了定位，那么，在APP里面可以提示用户打开定位服务。点击到设置界面设置，直接跳到定位服务设置界面。代码如下：

    //定位服务设置界面

    NSURL *url = [NSURL URLWithString:@"prefs:root=LOCATION_SERVICES"];

    if ([[UIApplication sharedApplication] canOpenURL:url])
    {
    	[[UIApplication sharedApplication] openURL:url];
    }

其他的跳转Facetime、墙纸设置界面、蓝牙设置界面、音乐、iCloud设置界面界面都是同样的原理。

**参数配置**
首先设置一个跳转的URL，URL里面有需要跳转页面的设置字符串`@prefs:root=XXX`,想跳到哪个设置界面只需要prefs:root=后面的值即可,所以统一的代码格式如下：

	NSURL *url = [NSURL URLWithString:@"prefs:root=XXX"];

	if([[UIApplication sharedApplication] canOpenUrl;url])
	{
		[[UIApplication sharedApplication] openURL:url];
	}


下面列出可以跳到这些界面的参数配置：

	Wi-Fi — prefs:root=WIFI
	Bluetooth — prefs:root=General&path=Bluetooth
	FaceTime — prefs:root=FACETIME
    General — prefs:root=General
	Safari — prefs:root=Safari
    Keyboard — prefs:root=General&path=Keyboard
    About — prefs:root=General&path=About
    Accessibility — prefs:root=General&path=ACCESSIBILITY
    Airplane Mode On — prefs:root=AIRPLANE_MODE
    Auto-Lock — prefs:root=General&path=AUTOLOCK
    Brightness — prefs:root=Brightness
    Date & Time — prefs:root=General&path=DATE_AND_TIME
    iCloud — prefs:root=CASTLE
    iCloud Storage & Backup — prefs:root=CASTLE&path=STORAGE_AND_BACKUP
    International — prefs:root=General&path=INTERNATIONAL
    Location Services — prefs:root=LOCATION_SERVICES
    Music — prefs:root=MUSIC
    Music Equalizer — prefs:root=MUSIC&path=EQ
    Music Volume Limit — prefs:root=MUSIC&path=VolumeLimit
    Network — prefs:root=General&path=Network
    Nike + iPod — prefs:root=NIKE_PLUS_IPOD
    Notes — prefs:root=NOTES
    Notification — prefs:root=NOTIFICATIONS_ID
    Phone — prefs:root=Phone
    Photos — prefs:root=Photos
    Profile — prefs:root=General&path=ManagedConfigurationList
    Reset — prefs:root=General&path=Reset
    Siri — prefs:root=General&path=Assistant
    Sounds — prefs:root=Sounds
    Software Update — prefs:root=General&path=SOFTWARE_UPDATE_LINK
    Store — prefs:root=STORE
    Twitter — prefs:root=TWITTER
    Usage — prefs:root=General&path=USAGE
    VPN — prefs:root=General&path=Network/VPN
    Wallpaper — prefs:root=Wallpaper
   
	类似的还有：

    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"http://www.baidu.com"]];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"sms://158********"]];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"tel://158********"]];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"mailto://362****@qq.com"]];

## 2.App之间跳转 ##
在开发IOS项目的时候，有可能会遇到两个APP应用相互调用的需求，比如说：支付宝支付、使用第三方登录，分享到微信等等。

**下面来详细介绍实现的步骤：**
1，在App1中添加URL Types项

打开项目中info.plist文件，在infomation property list项下面增加一项URL Typs

2，配置URL Scheme

展开URL types，再展开Item0，将Item0下的URL identifier修改为URL Scheme

**展开URL Scheme**，将Item0的内容修改为myApp

(其他应用可通过”myApp://“来访问此自定义URL的应用程序)

这里最关键的部分在于URL Schemes数组里的Item 0，后面的填写的字符串就是你用来通信的命令前缀“myApp”，URL identifier只是一个标示符，随意填写,然后再AppDelegate里处理重载下面的回调方法

		-(BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url
		{
    		if ([[url scheme] isEqualToString:@"myApp"])
    		{
        		NSLog(@"%@",url);
    		}
    		return YES;
		}

可以看见[url scheme]这个命令是为了拿到url的scheme，就是命令前缀“myApp”
新建app2，这个app什么都不用操作，只需要去唤醒app1即可，于是我们在viewDidLoad里写上这一句

	[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"myApp://hello"]];

"myApp"就是app1里的url scheme，我叫它命令前缀（可能apple的应用程序装上过后有个像通知中心一样的应用程序来统一管理，而每个应用程序的url scheme都会在那里被记录，以供其他app来调用该app，至于url scheme属于哪个应用程序，当然是和app的Bundle identifier相关的），格式采用`“前缀://..."`

我们关闭app1，app2，然后再启动app2，发现app2启动过后唤醒了app1，并且成功跳转；我们再关闭app1，app2，然后我们打开app1进行监测，发现app1被启动后，进入了`-(BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url` 方法，这就实现了两个app之间的唤醒和通信。

3，其他应用的跳转

作为调用者，需要通过：

	NSString *paramStr = [NSString stringWithFormat:@"myAppDemo://username=%@&address=%@", @"testdemo", @"上海市"];
    NSURL *url = [NSURL URLWithString:[paramStr stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]];
    [[UIApplication sharedApplication] openURL:url];

这段代码来跳转目标应用并传递参数。

4，参数的接收

那么作为一个Provider怎么去接收Customer传递过来的参数呢？
首先，在找到项目中的AppDelegate.m文件，然后找到openURL方法(如果没有就去实现它),实现过程如下：
	
		- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
		{
    		NSString *urlStr = [url absoluteString];
    		if ([urlStr hasPrefix:@"myAppDemo://"]) {
        	NSLog(@"TestAppDemo1 request params: %@", urlStr);
        	urlStr = [urlStr stringByReplacingOccurrencesOfString:@"myAppDemo://" withString:@""];
        	NSArray *paramArray = [urlStr componentsSeparatedByString:@"&"];
        	NSLog(@"paramArray: %@", paramArray);
       	 	NSMutableDictionary *paramsDic = [[NSMutableDictionary alloc] initWithCapacity:0];
        	for (int i = 0; i < paramArray.count; i++) {
            	NSString *str = paramArray[i];
            	NSArray *keyArray = [str componentsSeparatedByString:@"="];
            	NSString *key = keyArray[0];
            	NSString *value = keyArray[1];
            	[paramsDic setObject:value forKey:key];
            	NSLog(@"key:%@ ==== value:%@", key, value);
        	}
 
    	}
    	return NO;
	}

通过本身自定的参数拼接规则，来解析参数。


## 3.跳转AppStore ##

应用内直接跳转到Appstore

**1.进入appstore中指定的应用**

	NSString *str = [NSString stringWithFormat: 
                         @"itms-apps://ax.itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?type=Purple+Software&id=%d", 
                         myAppID ];  
        [[UIApplication sharedApplication] openURL:[NSURL URLWithString:str]];

其中myAppID为itunesconnect中的应用程序id

**2.进入首页**

	NSString *str = [NSString stringWithFormat: 
                         @"itms-apps://itunes.apple.com/WebObjects/MZStore.woa/wa/viewSoftware?id=%@",
                         myAppID ];  
        [[UIApplication sharedApplication] openURL:[NSURL URLWithString:str]];



## 4.ios应用内跳转到appstore里评分 ##
**在ios6.0前跳转到appstore评分一般是直接跳转到appstore评分**

	NSString *evaluateString = [NSString stringWithFormat:@"itms-apps://ax.itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?type=Purple+Software&id=%d",myAppID];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:evaluateString]];

**在ios6.0，APPle增加了一个新功能，当用户需要给APP评分时候，不再跳转到appstore了，可以在应用内实现打开appstore，苹果提供了一个框架StoreKit.framework,实现步骤如下:**
  1:导入StoreKit.framework,在需要跳转的控制器里面添加头文件#import

  2:实现代理SKStoreProductViewControllerDelegate

  3:
		- (void)evaluate{
			static NString *appId =@"111111"; 
		    //初始化控制器
		    SKStoreProductViewController *storeProductViewContorller = [[SKStoreProductViewController alloc] init];

		    //设置代理请求为当前控制器本身
		    storeProductViewContorller.delegate = self;

		    //加载一个新的视图展示
		    [storeProductViewContorller loadProductWithParameters:
		     //appId唯一的
		     @{SKStoreProductParameterITunesItemIdentifier : appId}
			 completionBlock:^(BOOL result, NSError *error) {
		         //block回调
		        if(error){
		            NSLog(@"error %@ with userInfo %@",error,[error userInfo]);
		        }else{
		            //模态弹出appstore
		            [self presentViewController:storeProductViewContorller animated:YES completion:^{
		               
		            }
		             ];
		        }
		    }];
		}


    //取消按钮监听
		- (void)productViewControllerDidFinish:(SKStoreProductViewController *)viewController{
    		[self dismissViewControllerAnimated:YES completion:^{   
    		}];
		}
就可以实现了应用内置appstore评分功能。