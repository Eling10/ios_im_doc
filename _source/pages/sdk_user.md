# 用户管理 `ELUserManager`

管理当前登录用户相关的信息

<br />

## API介绍

### 获取当前登录用户
```objc
/// 当前登录用户
@property (strong, nonatomic) ELUserInformation *currentUser;

/// 调用 
ELUserInformation *currentUser = [ELClient sharedClient].userManager.currentUser;
```

<br />

### 清空当前登录用户的缓存
当用户退出登录或者登录失效后，SDK内部会自动调用此方法来清除本地的用户数据，外部不应该主动调用此方法。
```objc
/// 清空当前登录的用户（缓存的数据会被移除）
- (void)clearCurrentUser;

/// 调用 
[[ELClient sharedClient].userManager clearCurrentUser];
```

<br />

### 获取指定用户信息
此方法是用来获取指定用户的信息
```objc
/**
 *  获取用户信息
 *
 *  @param userId 用户ID
 *  @param aCompletionBlock 回调
 */
- (void)getUserInformation:(NSString *)userId
                completion:(void(^)(NSError *error, ELUserInformation *information))aCompletionBlock;
                
/// 调用
[[ELClient sharedClient].userManager getUserInformation:@"zhangsan" completion:^(NSError *error, ELUserInformation *information) {
    if (!error) {
        NSLog(@"请求成功");
    } else {
        NSLog(@"请求成功");
    }
}];
```

<br />

### 修改当前用户信息

```objc
/**
 *  修改用户信息
 *
 *  @param avatar 头像地址（传 nil 则不做修改）
 *  @param nickName 昵称（传 nil 则不做修改）
 *  @param aCompletionBlock 回调
 */
- (void)updateUserInformationWithAvatar:(NSString *)avatar
                               nickName:(NSString *)nickName
                             completion:(void(^)(NSError *error))aCompletionBlock;
                             
/// 调用 
[[ELClient sharedClient].userManager updateUserInformationWithAvatar:nil nickName:@"zhangsan" completion:^(NSError *error) {
    if (!error) {
        NSLog(@"修改成功");
    } else {
        NSLog(@"修改失败");
    }
}];
```

<br />

### 发送邮箱验证码

```objc
/**
 *  验证码类型
 */
typedef NS_ENUM(NSUInteger, ELVerificationCodeType) {
    /// IM修改密码
    ELVerificationCodeTypeUpdatePassword    = 4,
    /// 绑定邮箱
    ELVerificationCodeTypeBindEmail         = 6
};

/**
 *  发送邮箱验证码
 *
 *  @param email 邮箱
 *  @param type 验证码类型
 *  @param aCompletionBlock 回调
 */
- (void)sendCodeToEmail:(NSString *)email
                   type:(ELVerificationCodeType)type
             completion:(void(^)(NSError *error))aCompletionBlock;

/// 调用 
/// 发送邮箱验证码 - 绑定邮箱
[[ELClient sharedClient].userManager sendCodeToEmail:@"xxx@xxx.com" type:ELVerificationCodeTypeBindEmail completion:^(NSError *error) {}];
/// 发送邮箱验证码 - 修改密码
[[ELClient sharedClient].userManager sendCodeToEmail:@"xxx@xxx.com" type:ELVerificationCodeTypeUpdatePassword completion:^(NSError *error) {}];
```

> 注：**绑定邮箱** 或者 **修改密码** 时，需要先调用此API来获取对应的验证码。

<br />

### 绑定邮箱
```objc
/**
 *  绑定邮箱
 *
 *  @param email 邮箱地址
 *  @param code 邮箱验证码
 *  @param aCompletionBlock 回调
 */
- (void)bindEmail:(NSString *)email
             code:(NSString *)code
       completion:(void(^)(NSError *error))aCompletionBlock;

/// 调用
[[ELClient sharedClient].userManager bindEmail:@"xxx@xxx.com" code:@"8888" completion:^(NSError *error) {}];
```
<br />

### 修改密码
```objc
/**
 *  修改密码
 *
 *  @param newPassword 新密码（6~18位，字母+数字组合）
 *  @param account 账号
 *  @param code 邮箱验证码
 *  @param aCompletionBlock 回调
 */
- (void)updatePassword:(NSString *)newPassword
               account:(NSString *)account
                  code:(NSString *)code
            completion:(void(^)(NSError *error))aCompletionBlock;
            
/// 调用 
[[ELClient sharedClient].userManager updatePassword:@"abc123" account:@"zhangsan" code:@"8888" completion:^(NSError *error) {}];
```
