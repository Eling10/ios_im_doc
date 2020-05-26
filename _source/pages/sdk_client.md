# 客户端 `ELClient`

## 1.1 初始化
1. 引入相关头文件。
2. 在工程的 `AppDelegate` 中调用 SDK 对应的方法。

```objc
#import "ELClient.h"

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    /// 客户端相关配置
    ELClientOptions *o = [ELClientOptions new];
    o.appId = @"您应用的AppId";
    o.appSecret = @"您应用的AppSecret";
    
    /// 初始化 SDK 客户端
    [[ELClient sharedClient] initializeSDKWithOptions:o];
    return YES;
}
```

<br />

## 1.2 配置类

### `ELClientOptions` 
客户端配置类，在初始化SDK的时候传入的配置。

| 属性 | 类型 | 描述 | 
| --- | --- | --- |
| `appId` | `NSString` | 应用的 `AppId` |
| `appSecret` | `NSString` | 应用的 `AppSecret` |

> 注：`AppId` 与 `AppSecret` 是您在平台上创建应用时生成的应用标识，参考：[开发者注册及管理后台](./app_register.md)。 

<br />

### `ELOSSOptions`
阿里云OSS配置类，消息中的附件（音频、视频、图片等）是存储在阿里云OSS服务器上的。

| 属性 | 类型 | 描述 | 
| --- | --- | --- |
| `bucketName` | `NSString` | 默认：`celery-master` |
| `endPoint` | `NSString` | 默认：`http://oss-cn-shenzhen.aliyuncs.com/` |
| `directoryName` | `NSString` | 服务器上存储文件的一级目录名，默认：`starRtc` |
| `fileDirectoryName` | `NSString` | 文件目录名（二级目录），默认：`file` |
| `imageDirectoryName` | `NSString` | 图片目录名（二级目录），默认：`images` |
| `voiceDirectoryName` | `NSString` | 语音目录名（二级目录），默认：`voice` |
| `videoDirectoryName` | `NSString` | 视频目录名（二级目录），默认：`video` |

<br />

## 1.3 API介绍

### 获取 SKD 实例
```objc
/**
 *  获取 SKD 实例
 */
+ (instancetype)sharedClient;

/// 调用
ELClient *client = [ELClient sharedClient];
```
<br />
### 初始化 SDK
```objc
/**
 *  初始化 SDK，启动App时需要调用此方法
 *
 *  @param options 配置选项
 */
- (void)initializeSDKWithOptions:(ELClientOptions *)options;

/// 调用 
[[ELClient sharedClient] initializeSDKWithOptions:o];
```