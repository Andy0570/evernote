### [在线AES加解密网站](http://www.seacha.com/tools/aes.html)
* 发现这个网站除了可以**在线AES加解密**，还有**MD5在线加密** 、**SHA1在线加密** 、**Base64在线加解密**  、**3DES在线加解密** 、**中文域名在线转码** 、**在线生成二维码** 等其它功能。

### 加密算法

> iOS加密算法参考了[AES加密 - iOS与Java的同步实现 @WelkinXie](http://www.jianshu.com/p/df828a57cb8f)，然后根据自己的需求作了一下修改，传入的是**NSDictionary**类型，并且嵌套进了 **NSDictionary** <-> **NSString** 相互转换的两个方法。

#### AESCipher.h

```
#import <Foundation/Foundation.h>

@interface AESCipher : NSObject

/**
 加密算法

 @param dictionary 待加密字典
 @param key 密钥
 @return 加密后字符串
 */
+ (NSString *)encryptAES:(NSDictionary *)dictionary key:(NSString *)key;

/**
 解密算法

 @param content 待解密字符串
 @param key 密钥
 @return 解密后字典
 */
+ (NSDictionary *)decryptAES:(NSString *)content key:(NSString *)key;

@end
```

#### AESCiphser.m
* 需要注意的是AES加密算法中的这几个设置参数一定要和服务器端一致，否则会有各种错误。
	* 密钥长度（Key Size）
	* 加密模式（Cipher Mode）
	* 填充方式（Padding）
	* 初始向量（Initialization Vector）


```
#import "AESCipher.h"
#import <CommonCrypto/CommonCryptor.h>

/** 初始向量*/
NSString *const kInitVector = @"16-Bytes--String";
/** 密钥长度：AES-128*/
size_t const kKeySize = kCCKeySizeAES128;

@implementation AESCipher

#pragma mark - 加密算法

+ (NSString *)encryptAES:(NSDictionary *)dictionary key:(NSString *)key {
    
    // NSDictionary -> NSString
    NSString *content = [self convertToJsonData:dictionary];
    
    // 待加密数据: NSString -> NSData
    NSData *contentData = [content dataUsingEncoding:NSUTF8StringEncoding];
    NSUInteger dataLength = contentData.length;
    
    // 密钥长度:kCCKeySizeAES128
    char keyPtr[kKeySize + 1]; // 为结束符'\\0' +1
    memset(keyPtr, 0, sizeof(keyPtr));
    [key getCString:keyPtr maxLength:sizeof(keyPtr) encoding:NSUTF8StringEncoding];
    
    // 密文长度 <= 明文长度 + BlockSize（填充数据）
    size_t encryptSize = dataLength + kCCBlockSizeAES128;
    void *encryptedBytes = malloc(encryptSize);
    size_t actualOutSize = 0;
    
    NSData *initVector = [kInitVector dataUsingEncoding:NSUTF8StringEncoding];
    
    // 设置加密参数
    // 系统默认使用 CBC 加密模式，然后指明使用 PKCS7Padding 补码方式(填充方式)
    CCCryptorStatus cryptStatus = CCCrypt(kCCEncrypt,
                                          kCCAlgorithmAES,
                                          kCCOptionPKCS7Padding,
                                          keyPtr,
                                          kKeySize,
                                          initVector.bytes,
                                          contentData.bytes,
                                          dataLength,
                                          encryptedBytes,
                                          encryptSize,
                                          &actualOutSize);
    
    if (cryptStatus == kCCSuccess) {
        // 加密结果编码方式: base64 编码
        return [[NSData dataWithBytesNoCopy:encryptedBytes length:actualOutSize] base64EncodedStringWithOptions:NSDataBase64EncodingEndLineWithLineFeed];
    }
    free(encryptedBytes);
    return nil;
}

#pragma mark - 解密算法

+ (NSDictionary *)decryptAES:(NSString *)content key:(NSString *)key {
    // 把 base64 String 转换成 Data
    NSData *contentData = [[NSData alloc] initWithBase64EncodedString:content options:NSDataBase64DecodingIgnoreUnknownCharacters];
    NSUInteger dataLength = contentData.length;
    
    char keyPtr[kKeySize + 1];
    memset(keyPtr, 0, sizeof(keyPtr));
    [key getCString:keyPtr maxLength:sizeof(keyPtr) encoding:NSUTF8StringEncoding];
    
    size_t decryptSize = dataLength + kCCBlockSizeAES128;
    void *decryptedBytes = malloc(decryptSize);
    size_t actualOutSize = 0;
    
    NSData *initVector = [kInitVector dataUsingEncoding:NSUTF8StringEncoding];
    
    CCCryptorStatus cryptStatus = CCCrypt(kCCDecrypt,
                                          kCCAlgorithmAES,
                                          kCCOptionPKCS7Padding,
                                          keyPtr,
                                          kKeySize,
                                          initVector.bytes,
                                          contentData.bytes,
                                          dataLength,
                                          decryptedBytes,
                                          decryptSize,
                                          &actualOutSize);
    
    if (cryptStatus == kCCSuccess) {
        NSString *content = [[NSString alloc] initWithData:[NSData dataWithBytesNoCopy:decryptedBytes length:actualOutSize] encoding:NSUTF8StringEncoding];
        return [self dictionaryWithJsonString:content];
    }
    free(decryptedBytes);
    return nil;
}

#pragma mark - NSDictionary <-> NSString

// NSDictionary 转换为 NSString
+ (NSString *)convertToJsonData:(NSDictionary *)dictionary {
    NSError *error;
    NSData *jsonData = [NSJSONSerialization dataWithJSONObject:dictionary options:NSJSONWritingPrettyPrinted error:&error];
    NSString *jsonString;
    if (!jsonData) {
        NSLog(@"%@",error);
    }else{
        jsonString = [[NSString alloc]initWithData:jsonData encoding:NSUTF8StringEncoding];
    }
    NSMutableString *mutStr = [NSMutableString stringWithString:jsonString];
    NSRange range = {0,jsonString.length};
    //去掉字符串中的空格
    [mutStr replaceOccurrencesOfString:@" " withString:@"" options:NSLiteralSearch range:range];
    NSRange range2 = {0,mutStr.length};
    //去掉字符串中的换行符
    [mutStr replaceOccurrencesOfString:@"\n" withString:@"" options:NSLiteralSearch range:range2];
    return mutStr;
}

// NSString 转换为 NSDictionary
+ (NSDictionary *)dictionaryWithJsonString:(NSString *)content {
    if (content == nil) {
        return nil;
    }
    
    NSData *jsonData = [content dataUsingEncoding:NSUTF8StringEncoding];
    NSError *err;
    NSDictionary *dic = [NSJSONSerialization JSONObjectWithData:jsonData
                                                        options:NSJSONReadingMutableContainers
                                                          error:&err];
    if(err)
    {
        NSLog(@"json解析失败：%@",err);
        return nil;
    }
    return dic;
}

@end
```
### 调用
1. 先在需要使用AES加密方法的文件中先导入包文件。
``#import "AESCipher.h" ``
2. 设置好需要传入的**NSDictionary对象**和**key值**（一定要确保是16位的，且和服务器端一致）。
```
NSDictionary *dictionary = @{
                               @"key":@"value"
                                };
NSString *key = @"1234567890123456";
NSString *cipherString = [AESCipher encryptAES:dictionary key:key];
```
3. 解密调用时，设置一个**NSDictionary对象**传入需要解密的字符串接受解密后的数据。
```
NSDictionary *dictionary = [AESCipher decryptAES:cipherString key:key];
```


### 参考资料

* [AES加密 - iOS与Java的同步实现 @WelkinXie](http://www.jianshu.com/p/df828a57cb8f)
* [GitHub框架：**AESCrypt-ObjC**](https://github.com/Gurpartap/AESCrypt-ObjC)
* [iOS数据加密方式 MD5 & RSA](http://www.jianshu.com/p/3a4f47cb86f8)
* [iOS开发--字典(NSDictionary)和JSON字符串(NSString)之间互转](http://www.cnblogs.com/ming1025/p/6186171.html)
* [如何正确对用户密码进行加密？](http://www.infoq.com/cn/articles/how-to-encrypt-the-user-password-correctly)
