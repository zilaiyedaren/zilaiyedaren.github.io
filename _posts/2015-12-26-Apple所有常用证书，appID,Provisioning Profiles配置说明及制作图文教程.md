---
layout: post
title:  "Apple所有常用证书，appID,Provisioning Profiles配置说明及制作图文教程"
author: 自来也大人
feature-img: "img/awakening_of_utumn.jpg"
category: ios
tag: 转载
---

##苹果所有常用证书，appID,Provisioning Profiles配置说明及制作图文教程##

###概述###

Apple的证书繁琐而复杂，制作管理比较麻烦，由于某个项目中所有的证书重置，便记录下整个过程，方便日后查阅。

首先，描述下各个证书的定义和作用，这样在制作的时候能够更加快捷，也能更加准确无误：

1、开发者证书（分为开发和发布两种，类型为iOS Development，iOS Distribution）,这是最基础的，不论真机调试（注：从iOS 7开始真机调试是未付费的开发者账号）还是上传到App Store都是需要的，是一个基本证书，用来证明自己的开发者身份。

2、App ID，这是每一个应用程序的独立标识，在配置中可以配置该应用的权限，比如是否用到了PassBook,GameCenter，以及更常用的push Notification服务，那么就可以创建生成下面第3条所提到的推送证书，所以，在所有和推送相关的配置中，首先要做的就是先开通推送服务的App ID。

3、推送证书（分为开发和发布两种，类型分别为APNS Development iOS,APNs Distribution iOS），该证书在App ID配置中创建生成，和开发者证书一样，安装到开发者电脑中。

4、Provisioning Profile，这个是我们一般称之为PP文件，该文件将App ID,开发者证书，硬件Device绑定到一起，在开发者中心配置好后，可以添加到Xcode上，也可以直接在Xcode上连接开发者中心生成，真机调试时需要在PP文件中添加真机的UDID,是真机调试和上架所必须的。

平常我们的制作流程一般都是按以上序列进行，先利用开发者帐号登陆开发者中心，创建开发者证书，appID,在appID中开通推送服务，在开通推送服务的选项下面创建推送证书（服务器端的推送证书见下文），之后在PP文件中绑定所有的证书id,添加调试真机等；

###具体操作流程如下：

1、开发者证书的制作，首先登陆到[开发者中心](https://developer.apple.com/account/ios/certificate/certificateList.action)，找到证书配置的版块，点进证书，会显示如下界面，点击右上角的加号

![](http://img.blog.csdn.net/20130701203155046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

会出现以下界面，该操作重复两次，分别创建开发测试证书和发布证书，开发测试证书用于真机调试，发布证书用于提交到appStore,我们以开发测试证书为例，选择第一个红框中的内容；

![](http://img.blog.csdn.net/20130701203638765?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

然后下一步，会提示创建CSR文件，也就是证书签名请求文件，会有很详细的操作说明，可以参考下图；

![](http://img.blog.csdn.net/20130701203947078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![](http://img.blog.csdn.net/20130701204012078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

之后将该CSR文件保存到一处；
备注：**CSR文件尽量每个证书都制作一次，将常用名称区分开来，因为该常用名称是证书中的密钥的名字；**

之后在开发者中心将该CSR文件提交；

![](http://img.blog.csdn.net/20130701204202421?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

提交上去后就会生成一个cer证书，如图所示，有效期为一年；

![](http://img.blog.csdn.net/20130701204343750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

利用同样的方法配置一下Distribution发布证书，下载保存，双击安装；在钥题串登陆证书中可以查看，其中专用密钥的名字即为CSR请求文件中的常用名称；

2、以上开发者证书的配置完成了，下面我们来**配置appID和推送证书**；在左边栏中选择appID,勾选右边的push可选项，为该appID所对应的应用添加推送功能，下面会看到创建证书的按钮，分别为开发证书和发布证书，下面的流程就和上述1中创建证书一样了，都是先建立证书请求文件，然后提交生成就行了，需要注意的是，虽然在左边栏证书栏中也可以直接创建推送证书，但是还是建议在appID中，勾选了push服务后在此处创建，这样会避免因为忘了开通push服务而导致推送不可用的情况发生；

![](http://img.blog.csdn.net/20130701205824796?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

证书创建完成后，下载保存，双击安装即可；

3、最后我们来进行PP文件的制作

![](http://img.blog.csdn.net/20130701212132640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

该流程进行两次，分别创建开发测试用PP文件和发布PP文件，前者用于真机测试，后者用于提交发布；Ad Hoc格式一般用于企业帐号，此处我们忽略；

选择后提交

![](http://img.blog.csdn.net/20130701212312703?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

会自动检测匹配appID,另外下拉项中还可以选择wildCard格式，该格式为自动生成，使用*通配符，适用于批量的，没有推送，PassCard等服务的应用；我们选择我们刚刚创建的appID,之后下一步选择证书；

![](http://img.blog.csdn.net/20130701212501078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

继续，这里有一个区别，因为PP文件的开发测试版需要真机调试，所以我们需要绑定真机，这里因为之前我添加过一些设备，所以这里就可以直接全选添加，如果没有的话，需要将真机的udid复制出来在此添加，在发布PP文件中，是没有这一步的；

![](http://img.blog.csdn.net/20130701212737265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

之后就是输入一个PP文件的名字了，然后生成，下载保存，双击添加到Xcode库中，这样在真机调试或者发布时，就可以分别有不同的PP文件与其对应；

![](http://img.blog.csdn.net/20130701213031265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

添加到Xcode中的效果如下：

![](http://img.blog.csdn.net/20130701213056453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaG9seWRhbmNlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

