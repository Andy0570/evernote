> 更新日期：20200522
> 微信支付SDK 版本：1.8.6

## iOS 接入指南
本文讲解 iOS 接入微信SDK，仅记录关键部分代码，其余在官方文档或网络文章中都已详述备尽，此处不再赘述。

### 1. 向微信注册你的应用程序 id

（略。。。）

### 2. 下载微信终端 SDK 文件

集成微信 SDK 有两种方式：

1. 手动集成，[下载 SDK ](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=11_1)然后拖拽相关库文件到你的项目中；
2. 通过 CocoaPods 集成：
   ```ruby
   pod 'WechatOpenSDK', '~> 1.8.6'
   ```

### 3. 开发环境搭建

#### 3.1 设置 TARGETS ，在 URL Types 中添加应用程序 id。

（略。。。）

#### 3.2 设置微信白名单

在 Xcode 中，选择你的工程设置项，选中 “TARGETS” 一栏，在 “info” 标签栏的 “LSApplicationQueriesSchemes“ 添加 `weixin`、`wechat` 和 `weixinULAPI` 字段：

![](https://upload-images.jianshu.io/upload_images/2648731-370dd0eedd2811ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


⚠️ 设置这个步骤的作用是用于判断微信是否安装（`[WXApi isWXAppInstalled]`）

详细可以参见 [LSApplicationQueriesSchemes-- 关于 info.plist 第三方登录 添加 URL Schemes 白名单](https://www.jianshu.com/p/a1895c766400)



### 4. 在项目中添加代码

为了使项目代码更清晰简洁（解耦合），我创建了一个范畴（Category）类专门存放微信支付相关的配置代码：

#### 4.1 AppDelegate+WechatPayService.h

```objectivec
#import "AppDelegate.h"

NS_ASSUME_NONNULL_BEGIN

/**
 集成微信支付
 */
@interface AppDelegate (WechatPayService)

// 配置方法，用于向微信终端注册应用 id
- (void)hql_configureForWechatPay;

@end

NS_ASSUME_NONNULL_END
```



#### 4.2 AppDelegate+WechatPayService.m

```objectivec
#import "AppDelegate+WechatPayService.h"
#import <WechatOpenSDK/WXApi.h> // 引入微信 SDK

@interface AppDelegate () <WXApiDelegate> // 遵守微信 SDK 协议

@end

@implementation AppDelegate (WechatPayService)

#pragma mark - Public

- (void)hql_configureForWechatPay {
    //向微信注册
    //注：WechatAppId 是常量，已经写在了预编译配置文件中
    //#define WechatAppId @"wx12345...67890"
    [WXApi registerApp:WechatAppId];
}

#pragma mark - WeChatPay

// NOTE: 9.0以后使用新API接口
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString*, id> *)options {
    return [WXApi handleOpenURL:url delegate:(id<WXApiDelegate>)self];
}

// NOTE: 20200522 更新，发现集成最新的 1.8.6 SDK 时，只有通过以下的方法才能获取到回调！
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray *))restorationHandler
{
    [WXApi handleOpenURL:userActivity.webpageURL delegate:(id<WXApiDelegate>)self];
    return YES;
}

/*! @brief 发送一个sendReq后，收到微信的回应
 *
 * 收到一个来自微信的处理结果。调用一次sendReq后会收到onResp。
 * 可能收到的处理结果有SendMessageToWXResp、SendAuthResp等。
 * @param resp具体的回应内容，是自动释放的
 */
-(void) onResp:(BaseResp*)resp {
    // 微信支付成功/失败，发起通知查询
    // 注：HQLWechatPayOnResponceNotification 通知方法名是常量，统一写在配置文件中
    // #define HQLWechatPayOnResponceNotification @"HQLWechatPayOnResponceNotification"
    [[NSNotificationCenter defaultCenter] postNotificationName:HQLWechatPayOnResponceNotification
                                                        object:(BaseReq *)resp];
}

@end
```



#### 4.3 AppDelegate.m

**AppDelegate+WechatPayService** 文件写好后，这里可以直接使用了。

```objectivec
#import "AppDelegate.h"
#import "AppDelegate+WechatPayService.h"

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [self hql_configureForWechatPay]; // 是的，只有一行配置代码
    return YES;
}
```

#### 4.4 开启调试模式

如果你是首次集成微信 SDK，现在还可以使用 SDK 中的自检函数，用于排查问题：

```objectivec
- (void)hql_configureForWechatPay {
    
    //在 register 之前打开 log, 后续可以根据 log 排查问题
    [WXApi startLogByLevel:WXLogLevelDetail logBlock:^(NSString * _Nonnull log) {
        NSLog(@"WeChat Log Info: %@",log);
    }];
    
    //向微信注册
    [WXApi registerApp:@"微信返回给你的 App ID" universalLink:@"Universal Link"];
    
    //调用自检函数
    /*! @brief 测试函数，用于排查当前 App 通过 Universal Link 方式分享到微信的流程
       注意1:  调用自检函数之前必须要先调用 registerApp:universalLink 接口, 并确认调用成功
       注意2:  自检过程中会有 Log 产生，可以先调用 startLogByLevel 函数，根据 Log 排查问题
       注意3:  会多次回调 block
       注意4:  仅用于新接入 SDK 时调试使用，请勿在正式环境的调用
    *
    *  当completion回调的 step 为 WXULCheckStepFinal 时，表示检测通过，Universal Link 接入成功
    *  @param completion 回调Block
    */
    [WXApi checkUniversalLinkReady:^(WXULCheckStep step, WXCheckULStepResult* result) {
        
        NSDictionary *resultInfo = @{
            @"当前检查步骤": @(step),
            @"当前检查结果": [NSString stringWithFormat:@"%u",result.success],
            @"Error Info": result.errorInfo,
            @"修复建议": result.suggestion
        };
        NSLog(@"%@",resultInfo);
    }];
}
```



### 5. 发起微信支付代码

#### 交互数据模型

先来看一下商户服务端返回的 JSON 数据格式：

```json
{
    appid = "wx12345...678906"; （应用 ID）
    noncestr = "FS7JBpYQ5TzkyfcV"; （随机字符串）
    package = "Sign=WXPay"; （扩展字段，默认值）
    partnerid = "12345674321"; （商户号）
    prepayid = "wx2530149711249kd7b531c00a2446020522"; （预支付交易会话 ID）
    sign = "BEE9FA6FCD14D286CCCD4EE7DC74578B"; （签名）
    timestamp = 1545704053 （时间戳，精确到秒，10位！！！）
}
```

然后写一个模型类来映射 JSON 数据，如下，JSON 解析可以使用 [YYModel](https://github.com/ibireme/YYModel) 库。

#### HQLWechatPayRequestModel.h

```objectivec
#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN

/**
 微信支付，服务器返回支付参数模型
 */
@interface HQLWechatPayRequestModel : NSObject

/** 应用 ID */
@property (nonatomic, copy, readonly) NSString *appid;
/** 商家向财付通申请的商家 ID */
@property (nonatomic, copy, readonly) NSString *partnerid;
/** 预支付订单 */
@property (nonatomic, copy, readonly) NSString *prepayid;
/** 商家根据财付通文档填写的数据和签名，默认值为 Sign=WXPay */
@property (nonatomic, copy, readonly) NSString *package;
/** 随机串，防重发 */
@property (nonatomic, copy, readonly) NSString *noncestr;
/** 时间戳，防重发 */
@property (nonatomic, assign, readonly) UInt32 timestamp;
/** 商家根据微信开放平台文档对数据做的签名 */
@property (nonatomic, copy, readonly) NSString *sign;

@end

NS_ASSUME_NONNULL_END
```

#### HQLWechatPayRequestModel.m

```objective-c
#import "HQLWechatPayRequestModel.h"

@interface HQLWechatPayRequestModel ()

@property (nonatomic, copy, readwrite) NSString *appid;
@property (nonatomic, copy, readwrite) NSString *partnerid;
@property (nonatomic, copy, readwrite) NSString *prepayid;
@property (nonatomic, copy, readwrite) NSString *package;
@property (nonatomic, copy, readwrite) NSString *noncestr;
@property (nonatomic, assign, readwrite) UInt32 timestamp;
@property (nonatomic, copy, readwrite) NSString *sign;

@end

@implementation HQLWechatPayRequestModel

#pragma mark - NSObject

- (NSString *)description {
    return [self modelDescription];
}

@end
```



#### 发起微信支付

```objective-c
#import "HQLWeChatPayViewController.h"

// Frameworks
#import <WechatOpenSDK/WXApi.h>
#import <WechatOpenSDK/WXApiObject.h>

// Models
#import "HQLWechatPayRequestModel.h"

@implementation HQLThirdPartyPayViewController

#pragma mark - Lifecycle

- (void)dealloc {
    // 移除微信支付通知
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

- (void)viewDidLoad {
    [super viewDidLoad];
  
    // 添加微信支付通知
    [[NSNotificationCenter defaultCenter] addObserver:self
                                             selector:@selector(respondsToWechatPayNotification:)
                                                 name:HQLWechatPayOnResponceNotification
                                               object:nil];
}

#pragma mark - Private

// 判断用户设备是否支持微信支付
- (BOOL)isSupportWechatPay {
    // 1.判断是否安装微信
    if (![WXApi isWXAppInstalled]) {
        // 业务代码，提示微信未安装...
        return NO;
    }
    // 2.判断微信的版本是否支持最新API
    if (![WXApi isWXAppSupportApi]) {
        // 业务代码，提示微信当前版本不支持此功能...
        return NO;
    }
    return YES;
}

// 响应微信支付回调通知
- (void)respondsToWechatPayNotification:(NSNotification *)notification {
    BaseResp *responds = notification.object;
    switch (responds.errCode) {
        case WXSuccess: {
            // 1. 微信支付成功
            // ...
            break;
        }
        case WXErrCodeCommon: {
            // 2. 微信支付失败
            // ...
            break;
        }
        case WXErrCodeUserCancel: {
            // 3. 用户点击取消并返回
            // ...
            break;
        }
        default: {
            break;
        }
    }
}

// 发起支付请求
- (void)dealWithOrderPay {
    // 业务逻辑，先向服务器发起预支付请求，服务器返回支持订单信息。
    // ...
    
    // 先判断是否支持微信支付
    if ([self isSupportWechatPay]) {
        // 解析服务端返回的支付参数
        NSDictionary *payDictionary;
        // 使用 YYModel 将 JSON 数据转化为数据模型 HQLWechatPayRequestModel
        HQLWechatPayRequestModel *payRequestModel = [HQLWechatPayRequestModel modelWithJSON:payDictionary];
        // 向微信终端发起支付的消息结构体
        PayReq *request = [[PayReq alloc] init];
        request.partnerId = payRequestModel.partnerid;
        request.prepayId = payRequestModel.prepayid;
        request.package = payRequestModel.package;
        request.nonceStr = payRequestModel.noncestr;
        request.timeStamp = payRequestModel.timestamp;
        request.sign = payRequestModel.sign;
        // 发起微信支付
        [WXApi sendReq:request];
    }
}

@end
```

iOS 应用中集成微信支付的交互流程：

1. 「你的应用」在「支付页面」通过 `sendReq:` 方法向「微信 APP」发送支付消息；
2. 系统判断「微信 APP」已经安装，并自动跳转到「微信 APP」引导用户支付；
3. 用户支付完成后，「微信 APP」自动跳回「你的应用」，「你的应用」通过实现 `WXApiDelegate` 代理中的 `onResp:` 方法接收微信返回的支付消息；
4. 「你的应用」收到的返回消息是通过 `AppDelegate` 来接收的， `AppDelegate` 需要通过 `NSNotificationCenter` 通知中心将返回的数据发送给 「支付页面」，「支付页面」再进行相关的业务处理。


That's all.

## 参考

* [微信支付开发文档](https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=8_1) ⭐️
* [微信开放平台 - iOS接入指南](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=1417694084&token=&lang=zh_CN) ⭐️⭐️⭐️
* [微信支付APP商户截图标准示范](https://kf.qq.com/faq/161226bymQ3y161226yQbmQv.html)
* [简书 - iOS-微信支付总结](https://www.jianshu.com/p/867563a1f080)
* [简书 - 微信支付](https://www.jianshu.com/p/162ece335b31)
* [掘金 - iOS 微信支付接入以及工具类封装](https://juejin.im/post/5ac32a7c51882555677ebeb3)
* [GitHub：WXPay](https://github.com/renzifeng/WXPay) - 微信支付讲解示例 ⭐️⭐️⭐️
* [MobTech：一、什么是 Universal Link](http://www.mob.com/wiki/detailed?wiki=Moblink_ios_UL_three&id=34)
* [MobTech：Universal Link 相关问题](http://www.mob.com/wiki/detailed?wiki=Moblink_ios_UL_related_issues_four&id=34)
* [iOS 通用链接（Universal Links）使用教程](http://blog.hudongdong.com/ios/1013.html)
* [简书：Universal Links-- 微信支付 (1.8.6)](https://www.jianshu.com/p/128d09cff44b)
