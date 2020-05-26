# 群管理 `ELGroupManager`

## 群实例 `ELGroup`

| 属性 | 类型 | 描述 | 
| --- | --- | --- |
| `groupId` | `NSString` | 群id |
| `groupName` | `NSString` | 群名称 |
| `owner` | `NSString` | 群主id |
| `groupAvatar` | `NSString` | 群头像 |
| `memberList` | `ELUserInformation` | 群成员列表（包括群主） |

<br />

## 协议 `ELGroupManagerDelegate`

群管理协议，用于监听群操作的回调。

```objc
/**
 *  当群被解散的通知，所有群成员会收到此回调（群主除外）
 *
 *  @param groupId 群ID
 */
- (void)groupDidDissolution:(NSString *)groupId;

/**
 *  自己被移出群组的通知（自己主动退群，不会收到此回调）
 *
 *  @param aGroupId 群组ID
 */
- (void)userDidDeleteFromGroup:(NSString *)aGroupId;

/**
 *  用户主动退群的通知（只有群主会收到此回调）
 *
 *  @param aGroupId 群组ID
*/
- (void)userDidLeaveFromGroup:(NSString *)aGroupId;
```

<br />

## API介绍

### 获取群列表

```objc
/**
 *  获取用户已加入的群组
 *
 *  @param aCompletionBlock 完成的回调
 */
- (void)getGroups:(void (^)(NSArray<ELGroup *> *aList, NSError *aError))aCompletionBlock;

/// 调用 
[[ELClient sharedClient].groupManager getGroups:^(NSArray<ELUserInformation *> *aList, NSError *aError) {}];
```

<br />

### 查询群详情

```objc
/**
 *  获取群组详情信息
 *
 *  @param aGroupId 群组ID
 *  @param aCompletionBlock 完成的回调
 */
- (void)getGroupDetailWithId:(NSString *)aGroupId
                  completion:(void (^)(ELGroup *group, NSError *aError))aCompletionBlock;
                  
/// 调用 
[[ELClient sharedClient].groupManager getGroupDetailWithId:@"001" completion:^(ELGroup *group, NSError *aError) { }];
```

<br />

### 创建群组

```objc
/**
 *  创建群组
 *
 *  @param groupName 群组名称（最多20位字符长度）
 *  @param aCompletionBlock 完成的回调
 */
- (void)createGroupWithGroupName:(NSString *)groupName
                      completion:(void (^)(NSError *aError))aCompletionBlock;
                      
/// 调用 
[[ELClient sharedClient].groupManager createGroupWithGroupName:@"group01" completion:^(NSError *aError) {}];
```

> 注：群名称的字符长度 `SDK` 内部做了限制，必须在 20 个字符以内（包括 20 个字符）。 

<br />

### 退出群组

```objc
/**
 *  退出群组，owner不能退出群，只能解散群（数据库中与本群相关的会话数据会被清除）
 *
 *  @param aGroupId         群组ID
 *  @param aCompletionBlock 完成的回调
 */
- (void)leaveGroup:(NSString *)aGroupId
        completion:(void (^)(NSError *aError))aCompletionBlock;
        
/// 调用 
[[ELClient sharedClient].groupManager leaveGroup:@"001" completion:^(NSError *aError) {}];
```

> 注：只有群成员可以退群，群主不能退群，只能解散群。操作成功后，`SDK` 内部会删除与本群相关的会话数据。

<br />

### 解散群组

```objc
/**
 *  解散群组, 需要owner权限（数据库中与本群相关的会话数据会被清除）
 *
 *  @param aGroupId 群组ID
 *  @param aCompletionBlock 完成的回调
 */
- (void)destroyGroup:(NSString *)aGroupId
          completion:(void (^)(NSError *aError))aCompletionBlock;
          
/// 调用 
[[ELClient sharedClient].groupManager destroyGroup:@"001" completion:^(NSError *aError) { }];
```

> 注：只能群主有权限解散群。群被解散后，群内的所有成员（群主除外）都会收到通知，同时 `SDK` 内部会删除与本群相关的会话数据。

<br />

### 添加群成员

```objc
/**
 *  添加群组成员
 *
 *  @param aUsers 被邀请的用户名列表
 *  @param aGroupId 群组ID
 *  @param aCompletionBlock 完成的回调
 */
- (void)addMembers:(NSArray<NSString *> *)aUsers
           toGroup:(NSString *)aGroupId
        completion:(void (^)(NSError *aError))aCompletionBlock;
        
/// 调用
[[ELClient sharedClient].groupManager addMembers:memberList toGroup:@"001" completion:^(NSError *aError) {}];
```

<br />

### 删除群成员

```objc
/**
 *  将群成员移出群组, 需要owner权限
 *
 *  @param aUsers 要移出群组的用户列表
 *  @param aGroupId 群组ID
 *  @param aCompletionBlock 完成的回调
 */
- (void)removeMembers:(NSArray<NSString *> *)aUsers
            fromGroup:(NSString *)aGroupId
           completion:(void (^)(NSError *aError))aCompletionBlock;
           
/// 调用
[[ELClient sharedClient].groupManager removeMembers:memberList fromGroup:@"001" completion:^(NSError *aError) {}];
```

> 注：只有群主有权限删除群成员，且群主不能删除自己。

<br />

### 修改群信息

```objc
/**
 *  修改群信息（群名称、头像），传空则表示不修改此项
 *
 *  @param aGroupId 群组ID
 *  @param aGroupName 群名称
 *  @param aGroupAvatar 群头像
 *  @param aCompletionBlock 完成的回调
 */
- (void)updateGroupWithId:(NSString *)aGroupId
                     name:(NSString *)aGroupName
                   avatar:(NSString *)aGroupAvatar
               completion:(void (^)(NSError *aError))aCompletionBlock;
               
/// 调用
[[ELClient sharedClient].groupManager updateGroupWithId:@"001" name:@"group02" avatar:nil completion:^(NSError *aError) {}];
```

> 注：只有群主有权限修改群信息。

<br />

### 添加群组管理代理
```objc
/// 添加代理
- (void)addDelegate:(id<ELGroupManagerDelegate>)aDelegate;
/// 移除代理
- (void)removeDelegate:(id<ELGroupManagerDelegate>)aDelegate;

/// 调用
[[ELClient sharedClient].groupManager addDelegate:self];
[[ELClient sharedClient].groupManager removeDelegate:self];
```
