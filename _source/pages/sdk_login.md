<!-- toc -->
# 登录 `ELLoginManager`

登录管理类，负责IM用户的注册、登录、登出以及监听登录状态相关的逻辑。

<br />

## 协议 `ELLoginManagerDelegate`
监听登录状态的协议

```objc
/**
 *  连接状态发生改变的回调
 *
 *  @param state 状态
 */
- (void)connectionStateDidChange:(ELConnectionState)state;

/**
  登录状态失效的回调
 */
- (void)loginStatusDidInvalid;
```

<br />

## API介绍

### 获取登录管理实例
`SDK` 内部已经将登录管理对象挂载到客户端实例上，因此外部无需再实例化登录管理类，直接通过客户端实例来获取。
```objc
/// 获取登录实例
ELLoginManager *login = [ELClient sharedClient].loginManager;
```
<br />

### 用户注册

```objc
/**
 *  用户注册
 *
 *  @param username 用户名
 *  @param password 密码（6~18位，字母+数字组合）
 *  @param aCompletionBlock 完成的回调
 */
- (void)registerWithUsername:(NSString *)username
                    password:(NSString *)password
                  completion:(void (^)(NSError *aError))aCompletionBlock;
   
/// 调用 
[[ELClient sharedClient].loginManager registerWithUsername:self.phoneF.text password:self.passwordF.text completion:^(NSError *aError) {
    if (aError) { 
        NSLog(@"注册失败");
    } else {
        NSLog(@"注册成功");
    }
}];
```
> 注：SDK对注册用户的 **密码** 做了限制，密码长度在 **6~18** 位之间，并且只能是 **数字** + **字母** 的组合。

<br />

### 登录IM服务器
登录成功后，用户信息会被缓存在本地，可以通过 [ELUserManager](./sdk_user.md) 类来获取当前登录的用户信息。

```objc
/**
 *  用户登录
 *
 *  @param username 用户名
 *  @param password 密码（6~18位，字母+数字组合）
 *  @param aCompletionBlock 完成的回调
 */
- (void)loginWithUsername:(NSString *)username
                 password:(NSString *)password
               completion:(void (^)(NSString *userId, NSError *aError))aCompletionBlock;
   
/// 调用    
[[ELClient sharedClient].loginManager loginWithUsername:@"abc" password:@"abc123" completion:^(NSString *userId, NSError *aError) {
    if (aError) { 
        NSLog(@"登录失败");
    } else {
        NSLog(@"登录成功");
    }
}];
```

<br />

### 登出
退出登录后，`SDK` 会清空缓存在本地的用户数据。

```objc
/**
 *  退出登录
 *
 *  @param aCompletionBlock 回调
 */
- (void)logout:(void (^)(NSError *aError))aCompletionBlock;

/// 调用 
[[ELClient sharedClient].loginManager logout:^(NSError *aError) {
    if (aError) { 
        NSLog(@"退出失败");
    } else {
        NSLog(@"退出成功");
    }
}];
```

<br />

### 监听登录状态

```objc
/// 添加代理
- (void)addDelegate:(id<ELLoginManagerDelegate>)delegate;
/// 移除代理
- (void)removeDelegate:(id<ELLoginManagerDelegate>)delegate;

/// 调用
[[ELClient sharedClient].loginManager addDelegate:self];
[[ELClient sharedClient].loginManager removeDelegate:self];
```