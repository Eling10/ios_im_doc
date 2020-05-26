# 好友管理 `ELContactManager`

管理好友的基本功能，比如添加、移除代理，添加、删除好友等。

非好友关系的双方是不能进行单聊的。

<br />

## 协议 `ELContactManagerDelegate`
好友管理类的代理，用来监听接收好友请求、删除等回调。

```objc
/**
 *  用户B同意用户A的加好友请求后，用户A会收到这个回调
 *
 *  @param userId 用户B
 */
- (void)friendRequestDidApproveByUser:(NSString *)userId;

/**
 *  用户B申请加A为好友后，用户A会收到这个回调
 *
 *  @param userId 用户B
 *  @param message 消息
 */
- (void)friendRequestDidReceiveFromUser:(NSString *)userId
                                message:(NSString *)message;

/**
 *  用户A解除与用户B的好友关系后，用户B会收到这个回调
 *
 *  @param userId 用户A
 */
- (void)friendDidRemoveByUser:(NSString *)userId;
```

<br />

## API介绍

### 获取好友列表

```objc
/**
 *  获取好友列表
 *
 *  @param aCompletionBlock 完成的回调
 */
- (void)getContactsWithCompletion:(void (^)(NSArray<ELUserInformation *> *aList, NSError *aError))aCompletionBlock;

/// 调用
[[ELClient sharedClient].contactManager getContactsWithCompletion:^(NSArray<ELUserInformation *> *aList, NSError *aError) {}];
```

> 注：好友数据都是从服务器上获取的，`SDK` 内部不会对好友数据做存储。

<br />

### 查找联系人

```objc
/**
 *  搜索联系人
 *
 *  @param username 用户名
 *  @param aCompletionBlock 完成的回调
 */
- (void)searchContactWithUsername:(NSString *)username
                       completion:(void (^)(NSArray<ELUserInformation *> *aList, NSError *aError))aCompletionBlock;
                       
/// 调用
[[ELClient sharedClient].contactManager searchContactWithUsername:@"zhangsan" completion:^(NSArray<ELUserInformation *> *aList, NSError *aError) {}];
```

<br />

### 添加好友

```objc
/**
 *  添加好友
 *
 *  @param userId 用户ID
 *  @param aMessage 邀请信息
 */
- (void)addContact:(NSString *)userId
           message:(NSString *)aMessage
        completion:(void (^)(NSError *aError))aCompletionBlock;
        
/// 调用
[[ELClient sharedClient].contactManager addContact:@"zhangsan" message:text completion:^(NSError *aError) {}];
```

<br />

### 删除好友
好友删除成功后，会删除与该好友之间的会话数据。

```objc
/**
 *  删除好友
 *
 *  @param userId 用户ID
 */
- (void)deleteContact:(NSString *)userId
          completion:(void (^)(NSError *aError))aCompletionBlock;
          
/// 调用
[[ELClient sharedClient].contactManager deleteContact:@"zhangsan" completion:^(NSError *aError) {}];
```

<br />

### 同意好友申请

```objc
/**
 *  同意好友申请
 *
 *  @param userId 用户ID
 */
- (void)approveFriendRequestFromUser:(NSString *)userId
                          completion:(void (^)(NSError *aError))aCompletionBlock;
                          
/// 调用
[[ELClient sharedClient].contactManager approveFriendRequestFromUser:@"zhangsan" completion:^(NSError *aError) {}];
```

<br />

### 添加好友管理代理
```objc
/// 添加代理
- (void)addDelegate:(id<ELContactManagerDelegate>)aDelegate;
/// 移除代理
- (void)removeDelegate:(id<ELContactManagerDelegate>)aDelegate;

/// 调用
[[ELClient sharedClient].contactManager addDelegate:self];
[[ELClient sharedClient].contactManager removeDelegate:self];
```
