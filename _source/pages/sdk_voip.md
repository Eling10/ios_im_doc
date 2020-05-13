# 实时音视频 `ELCallManager`

目前只支持1对1音视频通话，包括语音通话和视频通话。

<br />

## 通话配置 `ELCallOptions`

| 属性 | 类型 | 描述 | 
| --- | --- | --- |
| `openGLEnable` | `BOOL` | 是否使用 `openGL` 渲染，默认 `YES` |
| `hwEncodeEnable` | `BOOL` | 是否使用硬编码，默认 `NO` |
| `dynamicBitrateAndFPSEnable` | `BOOL` | 是否动态调节帧率码率，默认 `NO`|
| `bigVideoBitrate` | `int` | 大图码率，默认 `500` |
| `bigVideoFPS` | `int` | 大图帧率，默认 `15` |
| `smallVideoBitrate` | `int` | 小图码率，默认 `100` |
| `smallVideoFPS` | `int` | 小图帧率，默认 `15` |
| `resolution` | `ELCallVideoResolution` | 视频分辨率，默认 `ELCallVideoResolution_360BW_640BH_180SW_320SH` |
| `videoCodecType` | `ELCallVideoCodecType` | 视频编码格式，默认 `ELCallVideoCodecTypeH264` |
| `audioCodecType` | `ELCallAudioCodecType` | 音频编码格式配置，默认 `ELCallAudioCodecTypeAAC` |

<br />

## 协议 `ELCallManagerDelegate`

监听通话状态的回调。

```objc
/**
 *  用户A拨打用户B，用户B会收到这个回调
 *
 *  @param aMessage 消息对象
 */
- (void)callDidReceive:(ELMessage *)aMessage;

/**
 *  连接成功，双方都会收到此回调
 *
 *  @param aMessage 消息对象
 */
- (void)callDidConnect:(ELMessage *)aMessage;

/**
 *  通话关闭，双方都会收到此回调
 *
 *  @param aMessage 消息对象
 */
- (void)callDidEnd:(ELMessage *)aMessage;

/**
 *  收到实时数据流，此方法会调用多次
 *
 *  @param data 二进制流
 */
- (void)onReceiveRealtimeData:(NSData *)data;
```

<br />

## API介绍

**主叫方**：通话发起方。

**被叫方**：通话接收方

<br />

### 主叫方调用

#### 发起音视频通话

```objc
/**
 *  发起音视频通话
 *
 *  @param toId 接收人ID
 *  @param type 通话类型
 *  @param aCompletionBlock 回调
 */
- (void)call:(NSString *)toId
        type:(ELCallType)type
  completion:(void(^)(ELMessage *callSession, NSError *error))aCompletionBlock;
  
/// 调用
// 语音通话
ELCallType type = ELCallTypeAudio;
// 视频通话
// ELCallType type = ELCallTypeAudio;
[[ELClient sharedClient].callManager call:@"001" type:type completion:nil];
```

<br />

#### 取消通话

```objc
/**
 *  取消通话
 *
 *  @param toId 接收人ID
 */
- (void)cancel:(NSString *)toId
    completion:(void(^)(NSError *error))aCompletionBlock;
    
/// 调用
[[ELClient sharedClient].callManager cancel:@"001" completion:nil];
```

> 注：取消通话指对方收到通话邀请后，还没有同意通话，主叫方就取消呼叫，即通话连接还没有完成。

<br />

### 被叫方调用

#### 同意通话

```objc
/**
 *  同意通话
 *
 *  @param fromId 拨叫方ID
 */
- (void)accept:(NSString *)fromId
    completion:(void(^)(NSError *error))aCompletionBlock;
    
/// 调用 
[[ELClient sharedClient].callManager accept:@"001" completion:^(NSError *error) {}];
```

<br />

#### 拒绝通话

```objc
/**
 *  拒绝通话
 *
 *  @param fromId 拨叫方ID
 */
- (void)refuse:(NSString *)fromId
    completion:(void(^)(NSError *error))aCompletionBlock;
    
/// 调用
[[ELClient sharedClient].callManager refuse:@"001" completion:nil];
```

### 双方调用 

#### 挂断

```objc
/**
 *  挂断
 *
 *  @param callId 通话Id
 */
- (void)hangup:(NSString *)callId
    completion:(void(^)(NSError *error))completion;
    
/// 调用 
[[ELClient sharedClient].callManager hangup:@"001" completion:nil];
```

> 注：挂断是指通话已经连接成功后，双方正常挂断通话。

<br />

### 其它API

#### 添加通话管理代理

```objc
/// 添加代理
- (void)addDelegate:(id<ELCallManagerDelegate>)aDelegate;
/// 移除代理
- (void)removeDelegate:(id<ELCallManagerDelegate>)aDelegate;

/// 调用
[[ELClient sharedClient].callManager addDelegate:self];
[[ELClient sharedClient].callManager removeDelegate:self];
```

<br />

#### 配置通话参数

```objc
/// 切换摄像头方向，返回切换后的摄像头方向
- (ELCameraDirection)switchCamera;

/// 开关音频
- (void)setAudioEnable:(BOOL)enable;

/// 开关视频
- (void)setVideoEnable:(BOOL)enable;

/// 设置主方和对方的视频画面
- (void)setupView:(UIView *)selfPreview targetView:(UIView *)targetView;

/// 配置通话项
- (void)setCallOptions:(ELCallOptions *)options;
```