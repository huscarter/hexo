---
title: flutter接入友盟社会化分享
date: 2020-04-17 14:42:44
categories: flutter
tags:
     - flutter
description: 做了一阵子flutter开发，此前产品需求有社会化分享，在项目的发开过程中遇到了一些坑，特此记录。
---

### 社会化分享前期技术调研
目前社会化分享可以采取自行接入各个平台的分享 SDK，也可以采用UMeng和MobTech的集成 SDK。
1. 自己集成各个平台的sdk就需要自己封装各个平台的接口，统一提供给flutter调用，优点是sdk小，缺点就是自己的开发代码量大，自行踩坑多。
2. UMeng Share sdk和MobTech Share SDK 两者相比UMeng的包略小一点，但是MobTech的微博分享直接支持LinkCard分享形式，在功能上具有优势。
我在项目中选择了UMeng Share SDK，原因是项目中有使用UMeng统计，所以趋向于选用一套。不过个人建议，如果项目中之前没有使用UMeng的东西，我推荐选用MobTech实现社会化分享。
下面就是自己在项目中接入UMeng Share SDK的踩坑过程，后面有机会我将补上MobTech Share SDK 的接入部分。

### UMeng Share SDK 接入
1. 创建UMeng 应用，flutter开发需呀分别创建Android和iOS应用。
2. 创建各个平台的账号。（一般需要提供公司的营业执照）
3. 创建需要分享的各个平台的应用。（这里因为项目中只用到了微信、QQ和微博，所以我只以这三个平台为例）
3.1. [微信开放平台](https://open.weixin.qq.com/)
3.2. [腾讯开放平台](https://open.tencent.com/)
3.3. [微博开放平台](https://open.weibo.com/)
一般需要填写一下应用信息
```
- 创建APP
- [iOS]填写应用下载地址
- [iOS]填写Bundle ID
- [iOS]填写Universal Links

- [Androi]填写应用下载地址
- [Androi]填写应用签名
- [Androi]填写包名
```

### Android 端接入步骤
1. 初始化UMeng Share SDK。（可在Application的onCreate中初始化）
```
// 初始化UMmeng
UMConfigure.init(context,"UMengAndroid应用的AppKey","umeng",UMConfigure.DEVICE_TYPE_PHONE,"");
// 初始化微信
PlatformConfig.setWeixin("微信AppKey", "微信AppSecret");
// 初始化新浪。注意：最后一项填写auth认证的回调地址，不填写app无法在没有安装新浪客户端的情况通过h5分享
PlatformConfig.setSinaWeibo("新浪AppKey", "新浪AppSecret","https://api.weibo.com/oauth2/default.html");
// 初始化QQ
PlatformConfig.setQQZone("腾讯AppKey", "腾讯AppSecret");
```

2. 封装分享接口类
```
public class UMShareManager {
    // 单例代码部分省略
    ···

    // 初始化
    public void init(Activity context) {
        this.activity = context;
    }

    /**
     * 分享带有连接的内容
     *
     * @param call
     * @param result
     */
    public void shareWeb(MethodCall call, MethodChannel.Result result) {
        Map<String, String> map = (Map<String, String>) call.arguments;
        String url = map.get("url");
        String title = map.get("title");
        String thumb = map.get("thumb");
        String desc = map.get("desc");
        String platform = map.get("platform");
        UMWeb web = new UMWeb(url);

        web.setTitle(title);//标题
        web.setThumb(new UMImage(activity, thumb));  //缩略图
        web.setDescription(desc);//描述

        new ShareAction(activity).withMedia(web).setPlatform(getPlatform(platform)).share();
    }

    /**
     * 分享只有图片的内容
     *
     * @param call
     * @param result
     */
    public void shareImage(MethodCall call, MethodChannel.Result result) {
        Map<String, String> map = (Map<String, String>) call.arguments;
        String url = map.get("url");
        String platform = map.get("platform");
        new ShareAction(activity).withMedia(new UMImage(activity, url)).setPlatform(getPlatform(platform)).share();
    }

    /**
     * 是否安装了App
     *
     * @return
     */
    public boolean hasInstallApp(MethodCall call, MethodChannel.Result result) {
        Map<String, String> map = (Map<String, String>) call.arguments;
        String platform = map.get("platform");
        boolean hasInstall = false;
        if (platform.startsWith("wechat")) {
            hasInstall = canOpenWeChat();
        } else if (platform.equals("sina")) {
            hasInstall = canOpenSina();
        } else if (platform.equals("QQ")) {
            hasInstall = canOpenQQ();
        }
        result.success(hasInstall);
        return hasInstall;
    }

    private boolean canOpenSina() {
        final PackageManager packageManager = activity.getPackageManager();// 获取packagemanager
        List<PackageInfo> infos = packageManager.getInstalledPackages(0);// 获取所有已安装程序的包信息
        if (infos != null) {
            for (int i = 0; i < infos.size(); i++) {
                String pn = infos.get(i).packageName;
                if (pn.equals("com.sina.weibo")) {
                    return true;
                }
            }
        }

        return false;
    }

    private boolean canOpenWeChat() {
        final PackageManager packageManager = activity.getPackageManager();
        List<PackageInfo> infos = packageManager.getInstalledPackages(0);
        if (infos != null) {
            for (int i = 0; i < infos.size(); i++) {
                String pn = infos.get(i).packageName;
                if (pn.equalsIgnoreCase("com.tencent.qqlite") || pn.equalsIgnoreCase("com.tencent.mobileqq")) {
                    return true;
                }
            }
        }
        return false;
    }

    private boolean canOpenQQ() {
        final PackageManager packageManager = activity.getPackageManager();
        List<PackageInfo> infos = packageManager.getInstalledPackages(0);
        if (infos != null) {
            for (int i = 0; i < infos.size(); i++) {
                String pn = infos.get(i).packageName;
                if (pn.equalsIgnoreCase("com.tencent.qqlite") || pn.equalsIgnoreCase("com.tencent.mobileqq")) {
                    return true;
                }
            }
        }
        return false;
    }

    private SHARE_MEDIA getPlatform(String platform) {
        if (platform.equals("wechat")) {
            return SHARE_MEDIA.WEIXIN;
        } else if (platform.equals("sina")) {
            return SHARE_MEDIA.SINA;
        } else if (platform.equals("QQ")) {
            return SHARE_MEDIA.QQ;
        }
        return SHARE_MEDIA.WEIXIN;
    }
}
```

3. flutter 通过插件调用Android端的接口（java）
3.1 flutter端的代码
```
// 创建本地插件channel
static const MethodChannel channel = const MethodChannel("本地插件通道名");

// 通过本地插件通道调用原生应用的方法
channel.invokeMethod("shareWebFunc", {"url": url,"title": "标题","thumb": "缩略图地址","desc": "描述","platform":"wechat"});
```

3.2 原生端代码
```
// 此类可以在MainActivit:FlutterActivity的configureFlutterEngine方法初始化
public class LocalPluginXX implements FlutterPlugin{
    // Other methods
    ...
        
    // 重写加载本地插件方法
    @Override
    public void onAttachedToEngine(FlutterPlugin.FlutterPluginBinding binding) {
        channel = new MethodChannel(binding.getFlutterEngine().getDartExecutor(), "本地插件通道名");
        channel.setMethodCallHandler(new MethodChannel.MethodCallHandler() {
            @Override
            public void onMethodCall(MethodCall call, MethodChannel.Result result) {
                // 分享url
                if (call.method.equals("shareWebFunc")) {
                    UMShareManager.getInstance().shareWeb(call,result);
                }else if(call.method.equals("shareImageFunc")){
                    UMShareManager.getInstance().shareImage(call,result);
                }
            }
        });
    }
    
    // Other methods
    ...
}
```

### iOS 端接入步骤
1. 初始化UMeng Share SDK。（可在Application的onCreate中初始化
```
// 开启日志
UMSocialManager.default().openLog(true)
// 设置UMeng的AppKey
UMSocialManager.default().umSocialAppkey = "UMengIOS应用的AppKey"
// 设置微信的appKey和appSecret
UMSocialManager.default().setPlaform(UMSocialPlatformType.wechatSession, appKey: "微信AppKey", appSecret: "微信AppSecret", redirectURL: "配置的URLScheme")

// QQ
UMSocialManager.default().setPlaform(UMSocialPlatformType.QQ, appKey: "QQ的AppKey", appSecret: "QQ的AppSecret", redirectURL: "配置的URLScheme")

// 微博
UMSocialManager.default().setPlaform(UMSocialPlatformType.sina, appKey: "新浪的AppKey", appSecret:  "新浪的AppKey", redirectURL: "https://api.weibo.com/oauth2/default.html")
```

2. 封装分享接口类
```
class UMShareManager{
    // Other methods
    ...
    
    // 社会化分享
    // 此新浪处有坑：新版的新浪SDK
    public func shareWeb(result: FlutterResult,dic:Dictionary<String,Any>) {
        let url:String = dic["url"] as! String;
        let title:String = dic["title"] as! String;
        let thumb:String = dic["thumb"] as! String;
        let desc:String = dic["desc"] as! String;
        let platform:String = dic["platform"] as! String;
        
        let msgObject:UMSocialMessageObject = UMSocialMessageObject.init();
        if(platform != "sina"){
            let data = try! Data(contentsOf: URL.init(string:thumb)!)
            let thumn:UIImage? = UIImage.init(data: data)
            let shareObject: UMShareWebpageObject = UMShareWebpageObject.shareObject(withTitle: title, descr: desc, thumImage: thumn!);
            shareObject.webpageUrl = url;
            
            msgObject.shareObject = shareObject;
        }else{
            // 新浪网页版支持text
            msgObject.text = "\(title)-\(desc) \(url)"
        }
        
        UMSocialManager.default()?.share(to: getSharePlatform(platform: platform), messageObject: msgObject, currentViewController: nil, completion: { (result, code) in
            //
        })
    }
    
    // 社会化分享
    public func shareImage(result: FlutterResult,dic:Dictionary<String,Any>) {
        let url:String = dic["url"] as! String;
        let platform:String = dic["platform"] as! String;
        
        let canOpen:Bool = canOpenSina()
        print("shareWeb canOpen:\(String(describing: canOpen))");
        
        let msgObject:UMSocialMessageObject = UMSocialMessageObject.init();
        if(canOpen || platform != "sina"){
            let data = try! Data(contentsOf: URL.init(string:url)!)
            let thumb:UIImage? = UIImage.init(data: data)
            let shareObject: UMShareImageObject = UMShareImageObject.shareObject(withTitle: "分享", descr: "分享海报", thumImage: thumb)
            shareObject.shareImage = thumb
            
            msgObject.shareObject = shareObject;
        }else{
            msgObject.text = "分享图片 \(url)"
        }
        
        UMSocialManager.default()?.share(to: getSharePlatform(platform: platform), messageObject: msgObject, currentViewController: nil, completion: { (result, code) in
            //
        })
    }
    
    // 是否安装了app
    public func hasInstallApp(result: FlutterResult,dic:Dictionary<String,Any>)->Bool{
        let platform:String = dic["platform"] as! String;
        var hasInstall:Bool = false
        if(platform.contains("wechat")){
            hasInstall = canOpenWeChat()
        }else if(platform == "QQ"){
            hasInstall = canOpenQQ()
        }else if(platform == "sina") {
            hasInstall = canOpenSina()
        }
        result(hasInstall);
        return hasInstall;
    }
    
    // 是否安装了sina
    private func canOpenSina()->Bool{
        if UIApplication.shared.canOpenURL(URL.init(string: "sinaweibohd://")!) {
            return true
        }
        if UIApplication.shared.canOpenURL(URL.init(string: "sinaweibo://")!) {
            return true
        }
        if UIApplication.shared.canOpenURL(URL.init(string: "sinaweibosso://")!) {
            return true
        }
        if UIApplication.shared.canOpenURL(URL.init(string: "weibosdk://")!) {
            return true
        }
        if UIApplication.shared.canOpenURL(URL.init(string: "weibosdk2.5://")!) {
            return true
        }
        return false;
    }
    
    // 是否安装了微信
    private func canOpenWeChat()->Bool{
        if UIApplication.shared.canOpenURL(URL.init(string: "weixin://")!) {
            return true
        }
        if UIApplication.shared.canOpenURL(URL.init(string: "wechat://")!) {
            return true
        }
        if UIApplication.shared.canOpenURL(URL.init(string: "weixinULAPI://")!) {
            return true
        }
        return false;
    }
    
    // 是否安装了QQ
    private func canOpenQQ()->Bool{
        if UIApplication.shared.canOpenURL(URL.init(string: "mqqapi://")!) {
            return true
        }
        if UIApplication.shared.canOpenURL(URL.init(string: "mqq://")!) {
            return true
        }
        return false;
    }
    
    private func getSharePlatform(platform:String) ->UMSocialPlatformType{
        if(platform=="wechat"){
            return UMSocialPlatformType.wechatSession
        }else if(platform=="wechats"){
            return UMSocialPlatformType.wechatTimeLine
        }else if(platform=="QQ"){
            return UMSocialPlatformType.QQ
        }else {
            return UMSocialPlatformType.sina
        }
    }
}

```

3. flutter 通过插件调用iOS端的接口（swift）
3.1 flutter端的代码
```
同Android部分，flutter代码为同一套。
```

3.2 原生端代码
```
// AppDelegate 类本地插件
@objc class AppDelegate: FlutterAppDelegate{
    // Other methods
    ...
    
    //
    override func application(_ application: UIApplication,didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        let controller : FlutterViewController = window?.rootViewController as! FlutterViewController;
        LocalPluginXX.shared.initData(controller: controller);
    }
}

// 本地插件
class LocalPluginXX{
    // Other methods
    ...
    
    // 业务逻辑代码 
    public func initData(controller:FlutterViewController) {
        channel = FlutterMethodChannel.init(name: CHANNEL_NAME, binaryMessenger: controller as! FlutterBinaryMessenger);
        
        // 处理plugin调用
        channel?.setMethodCallHandler({(call: FlutterMethodCall, result: FlutterResult) -> Void in
            if("shareWebFunc" == call.method){
                UMShareManager.init().shareWeb(result: result, dic: call.arguments as! Dictionary<String,Any>);
            }else if("shareImageFunc" == call.method){
                UMShareManager.init().shareImage(result: result, dic: call.arguments as! Dictionary<String,Any>);
            }else {
                result(FlutterMethodNotImplemented);
            }
        });
    }
}
```

### MobTech Share SDK 接入
暂略。

### 问题记录
1. iOS需要配置各个平台的白名单(直接粘贴复制UMeng的文档)
```

<key>LSApplicationQueriesSchemes</key>
<array>
    <!-- 微信 URL Scheme 白名单-->
    <string>wechat</string>
    <string>weixin</string>
    <string>weixinULAPI</string>

    <!-- 新浪微博 URL Scheme 白名单-->
    <string>sinaweibohd</string>
    <string>sinaweibo</string>
    <string>sinaweibosso</string>
    <string>weibosdk</string>
    <string>weibosdk2.5</string>

    <!-- QQ、Qzone URL Scheme 白名单-->
    <string>mqqopensdklaunchminiapp</string>
    <string>mqqopensdkminiapp</string>
    <string>mqqapi</string>
    <string>mqq</string>
    <string>mqqOpensdkSSoLogin</string>
    <string>mqqconnect</string>
    <string>mqqopensdkdataline</string>
    <string>mqqopensdkgrouptribeshare</string>
    <string>mqqopensdkfriend</string>
    <string>mqqopensdkapi</string>
    <string>mqqopensdkapiV2</string>
    <string>mqqopensdkapiV3</string>
    <string>mqqopensdkapiV4</string>
    <string>mqzoneopensdk</string>
    <string>wtloginmqq</string>
    <string>wtloginmqq2</string>
    <string>mqqwpa</string>
    <string>mqzone</string>
    <string>mqzonev2</string>
    <string>mqzoneshare</string>
    <string>wtloginqzone</string>
    <string>mqzonewx</string>
    <string>mqzoneopensdkapiV2</string>
    <string>mqzoneopensdkapi19</string>
    <string>mqzoneopensdkapi</string>
    <string>mqqbrowser</string>
    <string>mttbrowser</string>
    <string>tim</string>
    <string>timapi</string>
    <string>timopensdkfriend</string>
    <string>timwpa</string>
    <string>timgamebindinggroup</string>
    <string>timapiwallet</string>
    <string>timOpensdkSSoLogin</string>
    <string>wtlogintim</string>
    <string>timopensdkgrouptribeshare</string>
    <string>timopensdkapiV4</string>
    <string>timgamebindinggroup</string>
    <string>timopensdkdataline</string>
    <string>wtlogintimV1</string>
    <string>timapiV1</string>

    <!-- 支付宝 URL Scheme 白名单-->
    <string>alipay</string>
    <string>alipayshare</string>

    <!-- 钉钉 URL Scheme 白名单-->
      <string>dingtalk</string>
      <string>dingtalk-open</string>

    <!--Linkedin URL Scheme 白名单-->
    <string>linkedin</string>
    <string>linkedin-sdk2</string>
    <string>linkedin-sdk</string>

    <!-- 点点虫 URL Scheme 白名单-->
    <string>laiwangsso</string>

    <!-- 易信 URL Scheme 白名单-->
    <string>yixin</string>
    <string>yixinopenapi</string>

    <!-- instagram URL Scheme 白名单-->
    <string>instagram</string>

    <!-- whatsapp URL Scheme 白名单-->
    <string>whatsapp</string>

    <!-- line URL Scheme 白名单-->
    <string>line</string>

    <!-- Facebook URL Scheme 白名单-->
    <string>fbapi</string>
    <string>fb-messenger-api</string>
    <string>fb-messenger-share-api</string>
    <string>fbauth2</string>
    <string>fbshareextension</string>

    <!-- Kakao URL Scheme 白名单-->  
    <!-- 注：以下第一个参数需替换为自己的kakao appkey--> 
    <!-- 格式为 kakao + "kakao appkey"-->    
    <string>kakaofa63a0b2356e923f3edd6512d531f546</string>
    <string>kakaokompassauth</string>
    <string>storykompassauth</string>
    <string>kakaolink</string>
    <string>kakaotalk-4.5.0</string>
    <string>kakaostory-2.9.0</string>

   <!-- pinterest URL Scheme 白名单-->  
    <string>pinterestsdk.v1</string>

   <!-- Tumblr URL Scheme 白名单-->  
    <string>tumblr</string>

   <!-- 印象笔记 -->
    <string>evernote</string>
    <string>en</string>
    <string>enx</string>
    <string>evernotecid</string>
    <string>evernotemsg</string>

   <!-- 有道云笔记-->
    <string>youdaonote</string>
    <string>ynotedictfav</string>
    <string>com.youdao.note.todayViewNote</string>
    <string>ynotesharesdk</string>

   <!-- Google+-->
    <string>gplus</string>

   <!-- Pocket-->
    <string>pocket</string>
    <string>readitlater</string>
    <string>pocket-oauth-v1</string>
    <string>fb131450656879143</string>
    <string>en-readitlater-5776</string>
    <string>com.ideashower.ReadItLaterPro3</string>
    <string>com.ideashower.ReadItLaterPro</string>
    <string>com.ideashower.ReadItLaterProAlpha</string>
    <string>com.ideashower.ReadItLaterProEnterprise</string>

   <!-- VKontakte-->
    <string>vk</string>
    <string>vk-share</string>
    <string>vkauthorize</string>

   <!-- Twitter-->
    <string>twitter</string>
    <string>twitterauth</string>
</array>
```

2. [iOS需要配置URL Scheme](https://developer.umeng.com/docs/128606/detail/129443#h3--url-scheme)

3. sina分享SDK的坑
```
1、sina完整版SDK3.2.5在iphone6上不支持（调用sinaSDK初始化崩溃）

2、sina分享在无客户端，只能分享文本，（图片及其他分享会异常）。原因是需要申请新浪的LinkCard权限，解决方案是将url连接和图片改成文本格式分享。
```

