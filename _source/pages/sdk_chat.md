# 消息管理 `ELChatManager`

管理所有与聊天相关的逻辑，包括单聊和群聊消息的查询、发送、更新、删除的功能。

<br />

## 消息 `ELMessage`

**消息：**`IM` 交互的实体，由消息体（ `ELMessageBody` ）组成，目前的消息体的类型有：`文本消息`、`图片消息`、`文件消息`、`语音消息`、`视频消息`、`音频通话`、`视频通话`。


```objc
/**
 *  初始化消息实例
 *
 *  @param conversationId 会话ID（接收方的ID）
 *  @param toName 接收方名称
 *  @param toAvatar 接收方头像
 *  @param body 消息体
 *  @param ext 拓展信息
 */
- (instancetype)initWithConversationId:(NSString *)conversationId
                                toName:(NSString *)toName
                              toAvatar:(NSString *)toAvatar
                                  body:(ELMessageBody *)body
                                   ext:(NSDictionary *)ext;
```

**`conversationId`：**会话 `id`，消息接收方的 `id`。如果是单聊，会话 `id` 就是对方的 `userId`，如果是群聊，会话 `id` 就是群的 `groupId`。

<br />

### 构造文本消息

```objc
/// 调用
ELTextMessageBody *body = [ELTextMessageBody new];
body.text = @"要发送的消息";

/// 生成消息
ELMessage *message = [[ELMessage alloc] initWithConversationId:@"001" toName:@"zhangsan" toAvatar:nil body:body ext:nil];
// 单聊
message.chatType = ELChatTypeChat;
// 群聊
// message.chatType = ELChatTypeGroupChat;
```

<br />

### 构造图片消息

```objc
/*!
 *  初始化文件消息体（数据将会写入本地）
 *
 *  @param aData        附件数据
 *  @param aDisplayName 附件显示名（不包含路径）
 *
 *  @result 消息体实例
 */
- (instancetype)initWithData:(NSData *)aData
                 displayName:(NSString *)aDisplayName;

/// 调用
ELImageMessageBody *body = [[ELImageMessageBody alloc] initWithData:aData displayName:@"image.png"];

/// 生成消息
ELMessage *message = [[ELMessage alloc] initWithConversationId:@"001" toName:@"zhangsan" toAvatar:nil body:body ext:nil];
// 单聊
message.chatType = ELChatTypeChat;
// 群聊
// message.chatType = ELChatTypeGroupChat;
```

<br />

### 构造语音消息

```objc
/*!
 *  初始化文件消息体
 *
 *  @param aLocalPath   附件本地路径
 *  @param aDisplayName 附件显示名（不包含路径）
 *
 *  @result 消息体实例
 */
- (instancetype)initWithLocalPath:(NSString *)aLocalPath
                      displayName:(NSString *)aDisplayName;
                      
/// 调用 
ELVoiceMessageBody *body = [[ELVoiceMessageBody alloc] initWithLocalPath:@"xxx/xxx.amr" displayName:@"audio"];
// 语音时长，10 秒
body.duration = 10;

/// 生成消息
ELMessage *message = [[ELMessage alloc] initWithConversationId:@"001" toName:@"zhangsan" toAvatar:nil body:body ext:nil];
// 单聊
message.chatType = ELChatTypeChat;
// 群聊
// message.chatType = ELChatTypeGroupChat;
```

<br />

### 构造视频消息

```objc
/*!
 *  初始化文件消息体
 *
 *  @param aLocalPath   附件本地路径
 *  @param aDisplayName 附件显示名（不包含路径）
 *
 *  @result 消息体实例
 */
- (instancetype)initWithLocalPath:(NSString *)aLocalPath
                      displayName:(NSString *)aDisplayName;
                      
/// 调用 
ELVideoMessageBody *body = [[ELVideoMessageBody alloc] initWithLocalPath:@"xxx/xxx.mp4" displayName:@"video.mp4"];

/// 生成消息
ELMessage *message = [[ELMessage alloc] initWithConversationId:@"001" toName:@"zhangsan" toAvatar:nil body:body ext:nil];
// 单聊
message.chatType = ELChatTypeChat;
// 群聊
// message.chatType = ELChatTypeGroupChat;
```

<br />

### 构造音频通话消息

```objc
/// 调用
ELAudioCallMessageBody *body = [ELAudioCallMessageBody new];
// 通话结束的原因
body.reason = ELCallEndReasonHangup;
// 通话时长
body.duration = 10;

/// 生成消息
ELMessage *message = [[ELMessage alloc] initWithConversationId:@"001" toName:@"zhangsan" toAvatar:nil body:body ext:nil];
// 单聊
message.chatType = ELChatTypeChat;
// 群聊
// message.chatType = ELChatTypeGroupChat;
```

<br />

### 构造视频通话消息

```objc
/// 调用
ELVideoCallMessageBody *body = [ELVideoCallMessageBody new];
// 通话结束的原因
body.reason = ELCallEndReasonHangup;
// 通话时长
body.duration = 10;

/// 生成消息
ELMessage *message = [[ELMessage alloc] initWithConversationId:@"001" toName:@"zhangsan" toAvatar:nil body:body ext:nil];
// 单聊
message.chatType = ELChatTypeChat;
// 群聊
// message.chatType = ELChatTypeGroupChat;
```

<br />

### 获取历史消息

```objc
/**
 *  获取指定会话的消息，如果数据库中不存在，则从服务器中取，按时间顺序 升序 排列（服务器中的数据会同步到本地数据库）
 *
 *  @param conversationId 会话ID
 *  @param page 第几页（从 1 开始）
 *  @param size 每页显示的条数
 */
- (void)getMessages:(NSString *)conversationId
               page:(NSInteger)page
               size:(NSInteger)size
         completion:(void(^)(NSArray<ELMessage *> *messages, NSError *aError))aCompletionBlock;
         
/// 调用
[[ELClient sharedClient].chatManager getMessages:@"001" page:1 size:10 completion:^(NSArray<ELMessage *> *messages, NSError *aError) {}];
```

<br />

### 删除单条消息

```objc
/**
 *  删除某一条消息记录，数据将会从服务器和本地数据库中移除
 *
 *  @param messageId 消息ID
 *  @param aCompletionBlock 完成的回调（回调结果以本地数据库的操作结果为准）
 */
- (void)removeMessage:(NSString *)messageId
           completion:(void (^)(NSError *aError))aCompletionBlock;
           
/// 调用 
[[ELClient sharedClient].chatManager removeMessages:@"001" completion:^(NSError *aError) {}];
```

<br />

### 删除多条消息

```objc
/**
 *  删除某一条会话下的所有消息（会话记录不会被删除），数据将会从服务器和本地数据库中移除
 *
 *  @param conversationId 会话ID
 *  @param aCompletionBlock 完成的回调（回调结果以本地数据库的操作结果为准）
 */
- (void)removeMessages:(NSString *)conversationId
            completion:(void (^)(NSError *aError))aCompletionBlock;

/// 调用 
[[ELClient sharedClient].chatManager removeMessages:@"001" completion:^(NSError *aError) {}];
```

<br />

## 会话 `ELConversation`

**会话：**操作聊天消息 `ELMessage` 的容器，`SDK` 内部会对消息进行分类，将 `conversationId` 相同的消息归为一类，作为一条会话。每条会话中存储了最新的一条消息，以及该会话下所有未读消息的数量。

<br />

### 获取会话列表

```objc
/**
 *  获取当前用户的所有会话数据，会按照最新的那一条消息进行 降序 排列（聊天室的会话除外）
 */
- (void)getAllConversations:(void(^)(NSArray<ELConversation *> *conversations, NSError *aError))aCompletionBlock;

/// 调用 
[[ELClient sharedClient].chatManager getAllConversations:^(NSArray<ELConversation *> *conversations, NSError *aError) {}];
```

> 注：会话列表中不包含聊天室的会话消息。

<br />

### 删除会话

当会话被删除后，它底下的所有消息数据都会被删除。

```objc
/**
 *  删除某一条会话（同时会清空该会话中的所有消息记录），数据将会从服务器和本地数据库中移除
 *
 *  @param conversationId 会话ID
 *  @param aCompletionBlock 完成的回调（回调结果以本地数据库的操作结果为准）
 */
- (void)removeConversation:(NSString *)conversationId
                completion:(void (^)(NSError *aError))aCompletionBlock;
                
/// 调用 
[[ELClient sharedClient].chatManager removeConversation:@"001" completion:^(NSError *aError) {}];
```

<br />

### 获取单个会话的未读消息数

```objc
ELConversation *conversation = self.conversations[0];
// 未读数
NSInteger unreadMessagesCount = conversation.unreadMessagesCount;
```

<br />

### 获取所有会话的未读消息数

```objc
[[ELClient sharedClient].chatManager getAllConversations:^(NSArray<ELConversation *> *conversations, NSError *aError) {
    NSInteger unreadCount = 0;
    for (ELConversation *conversation in conversations) {
        unreadCount += conversation.unreadMessagesCount;
    }
}];
```

<br />

### 查询会话是否是好友会话

对于单聊而言，聊天双方必须是好友关系，才能进行聊天。

```objc
/**
 *  查询该会话是否是好友会话（只针对单聊）
 *
 *  @param conversationId 会话ID
 */
- (void)getConversationFriendStatus:(NSString *)conversationId
                         completion:(void (^)(BOOL isFriend, NSError *aError))aCompletionBlock;
                         
/// 调用
[[ELClient sharedClient].chatManager getConversationFriendStatus:self.conversation.conversationId completion:^(BOOL isFriend, NSError *aError) { }];
```

<br />

## 聊天

登录成功之后才能进行聊天操作。发消息时，单聊和群聊调用的是统一接口，区别只是消息的 `chatType` 不同。

<br />

### 发送消息

发送消息时，对于带附件的消息体（图片、语音、视频等），SDK 内部会自动将附件上传到服务器上。

```objc
/**
 *  发送消息，SDK内部会自动上传消息的附件（图片、视频、语音、文件）
 *
 *  @param aMessage 消息实例
 *  @param aCompletionBlock 完成的回调
 */
- (void)sendMessage:(ELMessage *)aMessage
         completion:(void (^)(NSError *aError))aCompletionBlock;
         
/// 调用 
[[ELClient sharedClient].chatManager sendMessage:message completion:^(NSError *aError) {}];
```

<br />

### 接收消息

```objc
协议：ELChatManagerDelegate
/**
 *  收到一条消息，消息会被写入数据库，对于图片、语音、视频、文件类的消息，文件数据会被缓存到本地
 *
 *  @param aMessages 消息
 */
- (void)messageDidReceive:(ELMessage *)aMessages;

代理：
// 添加代理
[[ELClient sharedClient].chatManager addDelegate:self];
// 移除代理
[[ELClient sharedClient].chatManager removeDelegate:self];
```

<br />

### 上传消息附件

发送消息时，`SDK` 内部会自动上传消息的附件。如果不是特殊情况，外界不需要调用此方法。

```objc
/**
 *  上传附件到到服务器，上传完成后会同步更新 aMesage 对象
 *
 *  @param aMessage 消息对象
 *  @param progress 进度
 *  @param aCompletionBlock 回调
 */
- (void)uploadMessageAttachment:(ELMessage *)aMessage
                       progress:(ELFileProgressBlock)progress
                     completion:(void (^)(NSError *error))aCompletionBlock;
                     
/// 调用
[[ELClient sharedClient].chatManager uploadMessageAttachment:message progress:nil completion:^(NSError *error) {}];
```

### 下载消息附件

```objc
/**
 *  下载消息附件（语音，视频、文件），下载完成后会同步更新 aMesage 对象
 *
 *  @param aMessage 消息对象
 *  @param progress 进度
 *  @param aCompletionBlock 下载完成的回调
 */
- (void)downloadMessageAttachment:(ELMessage *)aMessage
                         progress:(ELFileProgressBlock)progress
                       completion:(void (^)(NSError *error))aCompletionBlock;
                     
/// 调用
[[ELClient sharedClient].chatManager downloadMessageAttachment:message progress:nil completion:^(NSError *error) {}];
```