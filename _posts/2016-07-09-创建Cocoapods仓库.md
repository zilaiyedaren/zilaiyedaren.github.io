---
layout: post
title:  "创建Cocoapods仓库"
date:   2016-07-09
author: 自来也大人
feature-img: "img/touring.jpg"
category: iOS
tag: 原创
---
每种语言发展到一个阶段，就会出现相应的依赖管理工具, 或者是中央代码仓库。如

```
	Java: maven,Ivy
	Ruby: gems
	Python:pip,easy_install
	Nodejs:npm
	Objective-C(Swift):cocoapods
```
那么cocoapods就是objc和Swift的代码依赖管理工具。cocoapods非常适合代码的集中管理，和工程依赖的管理。

首先我们需要学会使用cocoapods拉取Github上面的共有库，后面才能一步步了解，cocoapods确实好用而且有必要学习怎么样去制作私有库，或者在Github上面分享自己的库。

####cocoapods安装和使用简介
***安装***

安装方式异常简单 , Mac下都自带ruby，使用ruby的gem命令即可下载安装,打开Mac的终端输入如下命令：

```
	sudo gem install cocoapods
	pod setup
	// 如果gem的版本过低，我们可以进行更新操作
	$sudo gem update --system
	(ruby 的软件源 https://rubygems.org 因为使用的是亚马逊的云服务，更新时非常慢估计被强，需要更新一下ruby的源，使用如下代码将官方的ruby 源替换成国内淘宝的源或者其他国内开放的源：此处已淘宝为例)
	gem sources --remove https://rubygems.org/
	gem sources -a https://ruby.taobao.org/
	gem sources -l
```

还有一点需要注意，pod setup在执行时，会输出Setting up CocoaPods master repo，但是会等待比较久的时间。这步其实是 Cocoapods 在将它的信息下载到 ~/.cocoapods目录下，如果你等太久，可以试着 cd 到那个目录，用du -sh *来查看下载进度。

所有的项目的 Podspec 文件都托管在https://github.com/CocoaPods/Specs。第一次执行pod setup时，CocoaPods 会将这些podspec索引文件更新到本地的 ~/.cocoapods/目录下，这个索引文件比较大。如果没有使用VPN的话，建议还是使用其他的cocoapods镜像索引。

```
	pod repo remove master
	pod repo add master https://gitcafe.com/akuandev/Specs.git 
	pod repo update
	（来自巧哥的技术博客）
```
***CocoaPods的使用***
使用时需要在项目文件夹中新建一个名为Podfile的文件无需指定后缀(在终端输入 touch Podfile即可创建),然后将依赖的库名字依次列在文件中即可，当然你可以指定拉取哪个库，也可以指定那个项目需要哪些库：

```
	#默认为Github的Specs索引库，所有的开源库索引都是在这里面
	source 'https://github.com/CocoaPods/Specs.git'
	#当然如果我们有自己的私库，是可以使用自己的私库的
	source '我们私库的git仓库地址'
	#指定的平台(版本号可缺省默认为最新版本)一般都是ios 
	platform:ios ,'7.0'

	#指定需要获取库的项目
	target '项目名' do 
	pod 'JSPatch'	
	pod 'Masonry'
	pod 'SDAutoLayout', '~> 1.53'
	
	end
```
然后到当前项目路径下面，执行如下命令即可：

```
	pod install
```
现在，所有需要的第三方库都已经下载完成并且设置好了编译参数和依赖，以后只需要注意如下3点即可：
1.使用CocoaPods生成的.xcworkspace文件来打开工程，而不是以前的.xcodeproj 文件。
2.每次更改了Podfile文件，你需要重新执行一次pod update命令。
3.每次查找需要的库版本或者基本信息，只需要执行：pod search + 名称


Cocoapods在执行pod install和pod update的时候，都会默认更新一次本地的podspec索引，使用如下命令可以禁止索引更新

```
	pod update --no-repo-update
```

我们也可以利用CocoaPods帮助我们生成第三方的文档，并集成到Xcode中：

```
	brew install appledoc
```
来自巧哥的总结（在项目中cocoapods切入我们的项目）

CocoaPods的基本原理，它是将所有的依赖库都放到另一个名为 Pods 项目中，然后让主项目依赖 Pods 项目，这样，源码管理工作都从主项目移到了 Pods 项目中。发现的一些技术细节有：
Pods 项目最终会编译成一个名为 libPods.a 的文件，主项目只需要依赖这个 .a 文件即可。
对于资源文件，CocoaPods 提供了一个名为 Pods-resources.sh 的 bash 脚本，该脚本在每次项目编译的时候都会执行，将第三方库的各种资源文件复制到目标目录中。
CocoaPods 通过一个名为 Pods.xcconfig 的文件来在编译时设置所有的依赖和参数。

####用Cocoapods创建私有podspec
创建一个私有的podspec包括如下主要几个步骤：
1.创建并设置一个私有的Spec Repo
2.创建Pod的所需要的项目工程文件，并且有可访问的项目版本控制地址。
3.创建Pod所对应的podspec文件。
4.本地测试配置好的podspec文件是否可用。
5.向私有的Spec Repo中提交podspec。
6.在个人项目中的Podfile中增加刚刚制作的好的Pod并使用。
7.更新维护podspec。

在步骤中需要有Git仓库（不一定要是Git仓库，只要是可以获取到相关代码文件就可以，也可以是SVN,zip包等，区别就是在podspec中的source项填写的内容不同，那个适合自己就有那个！）

***创建私有Spec Repo***
Spec Repo是所有的Pods的一个索引,所有公开的Pods都在这个里面，实际是一个Git仓库remote端
在GitHub上，但是当我们使用了Cocoapods后他会被clone到本地的~/.cocoapods/repos目录下，可以进入到这个目录看到master文件夹就是这个官方的Spec Repo了。master目录的结构：

```
	.
	├── Specs
    	└── [SPEC_NAME]
       		 └── [VERSION]
          		  └── [SPEC_NAME].podspec
```

因此我们需要创建一个类似于master的私有Spec Repo，我们可以fork官方的Repo，也可以自己创建，最好使用官方的，因为如果只是想添加自己的Pods，没有必要把现有的公开Pods都copy一份。所以创建一个Git仓库，这个仓库你可以创建私有的也可以创建公开的。
因为GitHub的私有仓库是收费的，所以我们可以使用了其他Git服务，可以选择开源中国、Bitbucket，CODING以及CSDN。

#####创建属于自己的私有库中心：

```
	#pod repo add [Private Repo name][Github HTTPS clone URL]
	pod repo add KentPods https://git.oschina.net/Kent/KentPods.git
```
如果成功的话，在~/.cocoapods/repos/目录下就可以看到KentSpecs目录文件。

#####通过.podspec可以找到我们想要的第三方库，那么.spec是什么呢？

```
	Pod::Spec.new do |s|
	   s.name         = "TestComponents" #名称
	   s.version      = "0.0.1" #版本号
	   s.summary      = "bruce TestComponents." #描述
	
	   s.homepage     = "https://zilaiyedaren.github.io" #描述页面
	   s.license      = "MIT" #版权声明
	   s.author       = { "Kent" => "yunduanyueying@163.com" } #作者信息
	
	   s.platform     = :ios, "7.0" #使用平台
	   s.source       = { :git => "https://git.oschina.net/zialiye/PodTestLib.git", :tag => "0.0.1" } #源码地址
	
	   s.source_files  = "Classes", "Classes/**/*.{h,m}" #源码文件
	   s.frameworks = "CoreGraphics", "CoreFoundation", "Foundation", "UIKit" #依赖的framework
	   s.requires_arc = true #是否支持ARC
	end
```

##### 制作自己的私有库
1.先创建出一个私有仓库，大家可以在OSChina上创建一个私有库（免费的）。
2.先到你要创建私有库的目录下面这里我创建的目录时MyTestPod，然后把刚才创建的私有库从remote端clone到本地(建议使用Git名进行操作，很方便)
3.在PodTestLib文件目录下，创建一个Classes文件，用来存放源码文件.最好在Classes下再创建一层目录显示自己自制组建的名称这个作为s.name(TestComponents)和(podspec配置文件的名称一致)TestComponents.podspec
4.在PodTestLib目录下创建.podspec文件，先在目录下，然后输入以下命令：

```
	pod spec create TestComponents	
```
其中TestComponents是自定义的spec name。然后打开TestComponents.podspec文件，进行编辑，在文件中加入如上显示的配置，按照自己的项目替换成对应的配置即可。具体可以查看[Podspec Syntax Reference](http://guides.cocoapods.org/syntax/podspec.html)。

6.编辑完成后，在终端可以输入
```
	pod lib lint
```
编辑成功后，会提示成功，根据终端提示的错误信息，进行修改，直到验证成功。

7.往OSChina上提交刚才的修改，并打上tag标签。***一定要记得打上tag标签***，且与刚才编辑.podspec里面写的版本号一致。否则体会是tag版本信息不对。

8.创建属于自己的私有库中心，敲入以下命令即可：

```
	pod repo add KentPods https://git.oschina.net/zialiye/PodTestLib.git
```
创建成功后，可以进入~/.cocoapods/repos目录下可以看到KentPods文件,然后就是把TestComponents.podspec添加到私有库中心，如下：

```
	pod repo push KentPods TestComponents.podspec
```
9.这个时候，就可以通过pod search命令搜索到刚才创建的私有库了.

10.到这个步骤我们的私有库就制作完成了。来测试一下，我们的私有库是否能够正常使用。我们创建一个新的工程，在Podfile文件中，写入以下内容：*****共有仓库和私有仓库的链接地址必须给出！*****

```
	#公有仓库
	source 'https://github.com/CocoaPods/Specs.git'
	#私有仓库
	source 'https://git.oschina.net/zialiye/PodTestLib.git'
	target 'TestDemo' do
	platform :ios, '7.0'
	pod 'TestComponents'
end
```
然后运行pod install --no-repo-update命令,就可以正常使用啦！

11.私有库的升级、分支
在对私有库进行升级维护的时候，只需要重新编辑.podspec文件，修改相应的版本号和对应的配置信息即可，再次执行下面命令即可：

```
	pod repo push BrucePods TestComponents.podspec
```
想创建分支的话，只需对subspec进行设置即可。

12.删除私有库
如果想要删除私有库，需要分两步，第一步删除OSChina上创建的私有库。第二部，到~/.cocoapods/repos目录下，通过以下命令行即可删除：

```
	rm -rf BrucePods
```

13.其他项目组成员如何使用私有库
首先在OSChina上面给其他成员添加相应的权限。另外，在其终端上执行以下命令即可：

```
	pod repo add BrucePods https://git.oschina.net/zialiye/PodTestLib.git
```

#####制作属于自己的公有库
公有库的制作和私有库的制作很多都是相同的，唯一不同的就是把.podspec文件提交到公有仓库里面了。以前Cocoapods组件的提交方式是通过pull request进行的，现在改成trunk自动化的提交方式。Trunk自动化提交有下面几个步骤：

1.首次使用trunk的时候，需要注册自己的电脑：

```
 // pod trunk register [E-mail] [User Name]
 $ pod trunk register yunduanyueying@163.com "Kent"
 ```
执行完成之后，会受到一封验证邮件，按邮件提示完成验证即可。
注册流程完成之后，可以使用

```
	pod trunk me
```
其他的操作不步骤和自作私有库时一样的流程。

***如下这种操作也可以进行配置***
首先我们在指定位置：

```
	#pod lib create [Your PodName]
	pod lib create PodTestLib
```
之后会有5个问题，需我们进行设置：
1.选需要选择的语言类型(Swift/Objc)
1.是否需要一个例子工程；
2.选择一个测试框架；
3.是否基于View测试；
4.类的前缀；
4个问题的具体介绍可以去看官方文档,之后会自动执行pod install命令创建项目并生成依赖。以下是项目生成的目录结构及相关介绍。
使用"pod lib create PodTestLib"

```
	PodTestLib
├── Example                                  #demo APP
│   ├── PodTestLib
│   ├── PodTestLib.xcodeproj
│   ├── PodTestLib.xcworkspace
│   ├── Podfile                       #demo APP 的依赖描述文件
│   ├── Podfile.lock
│   ├── Pods                          #demo APP的依赖文件
│   └── Tests
├── LICENSE                               #开源协议 默认MIT
├── PodTestLib                                       #组件的目录
│   ├── Assets                            #资源文件
│   └── Classes                              #类文件
├── PodTestLib.podspec           #第三步要创建的podspec文件
└── README.md                                #markdown格式的README

```
这就是我们需要的一个制作的demo，然后可以将除Example文件夹之外的其它的文件提交到我们私有Git库中。
其他的操作步骤还是相同的。

其他的参看文章:

[Using Pod Lib Create](https://guides.cocoapods.org/making/using-pod-lib-create.html)

[Cocoapods代码管理](https://blog.cnbluebox.com/blog/2014/03/31/cocoapodsdai-ma-guan-li/)

[使用Cocoapods创建私有podspec](http://blog.wtlucky.com/blog/2015/02/26/create-private-podspec/)
