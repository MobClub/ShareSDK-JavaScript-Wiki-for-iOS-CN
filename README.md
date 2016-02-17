# ShareSDK-JavaScript-Wiki-for-iOS-CN
## ShareSDK-iOS (v3.x) for JavaScript 
* JavaScript开发者方便的集成ShareSDK分享和授权功能。

## 目录
* Getting started
    * [1、下载 ShareSDK 及 ShareSDK for JavaScript 插件](#Download)
    * [2、初始化ShareSDK并设置社交平台](#init)
    * [3、ShareSDK接口的调用](#interface)
* Notice
    * [各个分享平台自字段参数配置描述](#SocialConfiguration)
    * [构造分享内容字段参数值](#ContentConfiguration)
    


## <a id="Download"></a>1、下载 ShareSDK 及 ShareSDK for JavaScript 插件
* 1、ShareSDK iOS版本的 JavaScript 插件是在ShareSDK iOS版本基础上对接口做JS桥接，是依赖ShareSDK for iOS的。(目前不支持浏览器打开的web应用，只支持App内置的Web页面,这个web页可以自己在本地也就是项目里创建html文件，这个html文件也可以网上得到)。所以下载ShareSDK就包含了以下2部分，下载下来的文件目录截图如下：

  （1）ShareSDK iOS版本的下载：：[网页链接](http://www.mob.com/#/downloadDetail/ShareSDK/ios)


  （2）JavaScript 插件的下载：[网页链接](https://github.com/MobClub/New-JS-For-ShareSDK) （包含Demo）
  
![初始化](http://wiki.mob.com/wp-content/uploads/2015/12/JS1.jpg)

* 将以上2个红色方框内的文件拖到新建的项目中。

## <a id="init"></a> 2、初始化ShareSDK并设置社交平台
## iOS部分
 * 1、下载的ShareSDK文件拖拽进项目
   
  
  ![img](https://camo.githubusercontent.com/c3ca885230b30187a1c7e29adcd95622b1c0d291/687474703a2f2f77696b692e6d6f622e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f31312f536861726553444b322e6a7067)
  注意：请务必在上述步骤中选择“Create groups for any added folders”单选按钮组。如果你选择“Create folder references for any added folders”，一个蓝色的文件夹引用将被添加到项目并且将无法找到它的资源。


* 2、添加必须的依赖库

 必须添加的依赖库如下 ( Xcode 7 下 *.dylib库后缀名更改为*.tbd )：
 
  ```objc
  libicucore.dylib
  libz.dylib
  libstdc++.dylib
  JavaScriptCore.framework
  ```

 以下依赖库根据社交平台添加：
  
  ```objc
  新浪微博SDK依赖库
  
  ImageIO.framework
  libsqlite3.dylib
  ```
  
  ```objc
  微信SDK依赖库 
  
  libsqlite3.dylib
  ```
  
  ```objc
  QQ好友和QQ空间SDK依赖库 
  
  libsqlite3.dylib
  ```
  
  ```objc
  短信和邮件需要依赖库 
  
  MessageUI.framework
  ```
  
  ```objc
  Google＋SDK依赖库 
  
  CoreMotion.framework
  CoreLocation.framework
  MediaPlayer.framework
  AssetsLibrary.framework
  ```

 添加依赖库的方法如下:

 ![img](http://wiki.mob.com/wp-content/uploads/2015/09/233D16A0-E241-4D4B-ACF2-4C03259F995A.png)

* 3、各个社交平台需要的配置（url schemes 等）可以参考文档中的可选配置项：[网页链接](http://wiki.mob.com/ios%E7%AE%80%E6%B4%81%E7%89%88%E5%BF%AB%E9%80%9F%E9%9B%86%E6%88%90/)

* 4、为UIWebView添加消息捕获
 (1) 在实现 UIWebView 控件的 ViewController 中导入 ShareSDK 头文件并挂载 UIWebViewDelegate
 
 ```
  #import "ShareSDKJSBridge.h"
 
 @interface ViewController ()<UIWebViewDelegate>
 {
    @private
     ShareSDKJSBridge *_bridge;
 }
 //...
 @end
 ```
 
 (2)给UIWebView设置一个委托对象，如：（注意：webView是UIWebView的对象实例。）
 
 ```
 // ...
 webView.delegate = self;
 ```
 
 (3)实现shouldStartLoadWithRequest委托方法，并在方法中加入JSBridge的captureRequest方法，代码如下：
 
 ```
 #pragma mark - UIWebViewDelegate
 - (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequ est *)request navigationType: (UIWebViewNavigationType)navigationType
 {
     return ![[ShareSDKJSBridge sharedBridge] captureRequest:request webVie w:webView];
 }
 ```
 
## html 部分


* 1、选择需要平台的SDK

  打开JSApi / iOS / ShareSDKJSBridge.m，按需注释掉已导入的原生SDK库，ShareSDK的 libraries / extends 也可以删除相应的不需要的第三方SDK

  ```
#define IMPORT_SINA_WEIBO_LIB               //导入新浪微博库，如果不需要新浪微博客户端分享可以注释此行
#define IMPORT_QZONE_QQ_LIB                 //导入腾讯开发平台库，如果不需要QQ空间分享、SSO或者QQ好友分享可以注释此行
#define IMPORT_RENREN_LIB                   //导入人人库，如果不需要人人SSO，可以注释此行
#define IMPORT_GOOGLE_PLUS_LIB              //导入Google+库，如果不需要Google+分享可以注释此行
#define IMPORT_WECHAT_LIB                   //导入微信库，如果不需要微信分享可以注释此行
#define IMPORT_ALIPAY_LIB                   //导入支付宝分享库，如果不需要支付宝分享可以注释此行
#define IMPORT_KAKAO_LIB                    //导入Kakao库，如果不需要KaKao分享可以注释此行
```

* 2、在html 页面 添加 JS 文件引用

  ```
   <script src="ShareSDK.js"></script>
  ```

* 3、添加初始化代码

  ```
  function init()
  {
        //1、配置平台信息，有开放平台账号系统的平台需要自行去申请账号
        var platformConfig = {};
        
        //以下是示例
        //新浪微博
        var sinaConf = {};
        sinaConf["app_key"] = "568898243";
        sinaConf["app_secret"] = "38a4f8204cc784f81f9f0daaf31e02e3";
        sinaConf["redirect_uri"] = "http://www.sharesdk.cn";
        platformConfig[$sharesdk.PlatformID.SinaWeibo] = sinaConf;
        
        //微信
        var weixinConf = {};
        weixinConf["app_id"] = "wx4868b35061f87885";
        weixinConf["app_secret"] = "64020361b8ec4c99936c0e3999a9f249";
        platformConfig[$sharesdk.PlatformID.WechatPlatform] = weixinConf;

        //QQ
        var qqConf = {};
        qqConf["app_id"] = "100371282";
        qqConf["app_key"] = "aed9b0303e3ed1e27bae87c33761161d";
        platformConfig[$sharesdk.PlatformID.QQPlatform] = qqConf;

        //腾讯微博
        var tencentWeiboConf = {};
        tencentWeiboConf["app_key"] = "801307650";
        tencentWeiboConf["app_secret"] = "ae36f4ee3946e1cbb98d6965b0b2ff5c";
        tencentWeiboConf["redirect_uri"] = "http://www.sharesdk.cn";
        platformConfig[$sharesdk.PlatformID.TencentWeibo] = tencentWeiboConf;
        
        //Facebook
        var facebookConf = {};
        facebookConf["api_key"] = "107704292745179";
        facebookConf["app_secret"] = "38053202e1a5fe26c80c753071f0b573";
        platformConfig[$sharesdk.PlatformID.Facebook] = facebookConf;
        
        //Twitter
        var twitterConf = {};
        twitterConf["consumer_key"] = "LRBM0H75rWrU9gNHvlEAA2aOy";
        twitterConf["consumer_secret"] = "gbeWsZvA9ELJSdoBzJ5oLKX0TU09UOwrzdGfo9Tg7DjyGuMe8G";
        twitterConf["redirect_uri"] = "http://mob.com";
        platformConfig[$sharesdk.PlatformID.Twitter] = twitterConf;
        
        //Mail
        var mailConf = {};
        platformConfig[$sharesdk.PlatformID.Mail] = mailConf;

        //2、初始化ShareSDK
        $sharesdk.initSDKAndSetPlatfromConfig("iosv1101", platformConfig);
}
```

* [各个分享平台字段参数配置描述](#SocialConfiguration) 


##<a id="interface"></a> 3、ShareSDK接口的调用
## 分享

* 1、构造分享内容参数，示例如下：

  ```
var params =
    {
        "text" : "测试的文字",  // 分享的文字
        "imageUrl" : "http://www.mob.com/mob/img/navproducts_03.png",  // 分享的图片 Url
        "title" : "测试的标题",  /分享的标题
        "titleUrl" : "http://www.mob.com",
        "description" : "测试的描述",
        "site" : "ShareSDK",
        "siteUrl" : "http://www.mob.com",
        "type" : $sharesdk.ContentType.Text
    };
```

* 2、调用分享方法：
  
 (1)、弹出分享菜单
 
  ```
   $sharesdk.showShareMenu(null, params, 100, 100, function (platform, state, shareInfo, error) {
                alert("state = " + state + "\n shareInfo = " + shareInfo + "\n error = " + error);
   });
```

 (2)、弹出分享编辑框分享
 
  ```
   $sharesdk.showShareView($sharesdk.PlatformID.SinaWeibo, params, function (platform, state, shareInfo, error) {
                alert("state = " + state + "\n shareInfo = " + shareInfo + "\n error = " + error);
    });
```

 (3)、直接分享
 
  ```
    $sharesdk.shareContent($sharesdk.PlatformID.SinaWeibo, params, function (platform, state, shareInfo, error) {
                alert("state = " + state + "\n shareInfo = " + shareInfo + "\n error = " + error);
    });
```

## 授权


* 1、调用授权方法，代码如下：

  ```
   $sharesdk.authorize($sharesdk.PlatformID.SinaWeibo, function (platform, state, error) {
                alert("state = " + state + "\n error = " + error);
    });
```


* 2、获取用户资料，代码如下：

  ```cpp
   $sharesdk.getUserInfo($sharesdk.PlatformID.SinaWeibo, function (platform, state, user, error) {
                alert("state = " + state + "\n user = " + user + "\n error = " + error);
   });
```

 
## <a id="SocialConfiguration"></a>各个分享平台参数配置描述
平台                 | 通用字段           | 通用字段               |通用字段             | iOS 特需           | Android 特需          |
--------------------|------------------|-----------------------|--------------------|------------------|-----------------------|
新浪微博              | app_key          | app_secret           |redirect_uri         | auth_type        |                      |
腾讯微博              | app_key          | app_secret           |redirect_uri         | ––               |                      |
豆瓣                 | api_key          | secret               | redirect_uri        | ––               |    | 
QQ系列               | app_id           | app_key              | ––                  | auth_type        |    |
人人网               |app_id            | app_key              |secret_key           | auth_type        |    |
开心网               | api_key          | secret_key           |redirect_uri         | ––               |    |
Facebook            | api_key          | app_secret           |  ––                 | auth_type        |    |
Twitter             | consumer_key     | consumer_secret      |redirect_uri         |  ––              |    |
GooglePlus          |client_id         | client_secret        |redirect_uri         | auth_type        |    |
微信系列              | app_id          | app_secret           | ––                   |  ––             |    |
Pocket              | consumer_key     | ––                   |redirect_uri         | auth_type        |    |
Instragram          | client_id        | client_secret        |redirect_uri         |  ––              |    |
LinkedIn            | api_key          | secret_key           |redirect_uri         |  ––              |    |
Tumblr              | consumer_key     | consumer_secret      |callback_url         |  ––              |    |
Flicker             | api_key          | api_secret           | ––                  |  ––              |    |
有道                 | consumer_key     | consumer_secret      |oauth_callback       |  ––              |    |
印象笔记Evernote      | consumer_key     |consumer_secret       |––                   | ––               |    |
支付宝好友            | app_id           | ––                   | ––                  | ––               |    |
Pinterest           | client_id         | ––                   |––                   | ––               |    |
Kakao系列            | app_key          | rest_api_key         |redirect_uri         | auth_type        |     |
Dropbox             | app_key           | app_secret           |oauth_callback      |  ––              |     |
Vkontakte           | application_id    | secret_key           |––                   | ––              |     |
明道                 | app_key           | app_secret           |redirect_uri        | ––              |     |
易信                 | app_id            | app_secret           |redirect_uri        | auth_type        |     |
Instapaper           |consumer_key      | consumer_secret      |––                 |––               |    |
