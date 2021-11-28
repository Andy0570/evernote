推荐的开源库： [soffes/SAMKeychain](https://github.com/soffes/SAMKeychain) 

新建KeyChain类，用于实现向keyChain存储、读取和修改用户名密码。代码如下：
### KeyChain.h文件

```objectivec
#import <Foundation/Foundation.h>
#import <Security/Security.h>

@interface KeyChain : NSObject

+ (NSMutableDictionary *)getKeychainQuery:(NSString *)service;

// save username and password to keychain
+ (void)save:(NSString *)service data:(id)data;

// load username and password from keychain
+ (id)load:(NSString *)service;

// delete username and password from keychain
+ (void)delete:(NSString *)serviece;

@end
```
### KeyChain.m文件

```objectivec
#import "KeyChain.h"

@implementation KeyChain

+ (NSMutableDictionary *)getKeychainQuery:(NSString *)service {
    return [NSMutableDictionary dictionaryWithObjectsAndKeys:
            (id)kSecClassGenericPassword,(id)kSecClass,
            service, (id)kSecAttrService,
            service, (id)kSecAttrAccount,
            (id)kSecAttrAccessibleAfterFirstUnlock,(id)kSecAttrAccessible,
            nil];
}

#pragma mark 写入
+ (void)save:(NSString *)service data:(id)data {
    //Get search dictionary
    NSMutableDictionary *keychainQuery = [self getKeychainQuery:service];
    //Delete old item before add new item
    SecItemDelete((CFDictionaryRef)keychainQuery);
    //Add new object to search dictionary(Attention:the data format)
    [keychainQuery setObject:[NSKeyedArchiver archivedDataWithRootObject:data] forKey:(id)kSecValueData];
    //Add item to keychain with the search dictionary
    SecItemAdd((CFDictionaryRef)keychainQuery, NULL);
}

#pragma mark 读取
+ (id)load:(NSString *)service {
    id ret = nil;
    NSMutableDictionary *keychainQuery = [self getKeychainQuery:service];
    //Configure the search setting
    //Since in our simple case we are expecting only a single attribute to be returned (the password) we can set the attribute kSecReturnData to kCFBooleanTrue
    [keychainQuery setObject:(id)kCFBooleanTrue forKey:(id)kSecReturnData];
    [keychainQuery setObject:(id)kSecMatchLimitOne forKey:(id)kSecMatchLimit];
    CFDataRef keyData = NULL;
    if (SecItemCopyMatching((CFDictionaryRef)keychainQuery, (CFTypeRef *)&keyData) == noErr) {
        @try {
            ret = [NSKeyedUnarchiver unarchiveObjectWithData:(__bridge NSData *)keyData];
        } @catch (NSException *e) {
            NSLog(@"Unarchive of %@ failed: %@", service, e);
        } @finally {
        }
    }
    if (keyData)
        CFRelease(keyData);
    return ret;
}

#pragma mark 删除
+ (void)delete:(NSString *)service {
    NSMutableDictionary *keychainQuery = [self getKeychainQuery:service];
    SecItemDelete((CFDictionaryRef)keychainQuery);
}

@end
```

### PasswordManager

```objectivec
// -------------------- PasswordManager.h --------------------
#import <Foundation/Foundation.h>

/**
 管理用户密码
 */
@interface PasswordManger : NSObject

+ (void)savePassword:(NSString *)password;

+ (NSString *)loadPassword;

+ (void)deletePassword;

@end


// -------------------- PasswordManager.h --------------------
#import "PasswordManger.h"
#import "AppKeyChain.h"

static NSString *const PASSWORD = @"com.CompanyName.ProjectName.password";

@implementation PasswordManger

+ (void)savePassword:(NSString *)password {
    [AppKeyChain saveData:password forKey:PASSWORD];
}

+ (NSString *)loadPassword {
    NSString *password = (NSString *)[AppKeyChain loadForKey:PASSWORD];
    return password;
}

+ (void)deletePassword {
    [AppKeyChain deleteKey:PASSWORD];
}

@end
```
