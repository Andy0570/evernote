### 20171101 更新
```
// base64编码
- (NSString *)base64Encode:(NSString *)string {
    // 1.将字符串转换为二进制
    NSData *data = [string dataUsingEncoding:NSUTF8StringEncoding];
    // 2.二进制数据进行编码
    return [data base64EncodedStringWithOptions:0];
}

// base64解码
- (NSString *)base64Decode:(NSString *)string {
    // 1.字符串转换为二进制
    NSData *data = [[NSData alloc] initWithBase64EncodedString:string options:0];
    // 2.解码
    return [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
}
```

### 声明方法
``- (NSString *)base64;``
### 实现方法
```
- (NSString *)base64 {
    NSData *data = [NSData dataWithBytes:[self UTF8String] length:[self lengthOfBytesUsingEncoding:NSUTF8StringEncoding]];
    NSUInteger length = [data length];
    NSMutableData *mutableData = [NSMutableData dataWithLength:((length + 2) / 3) * 4];

    uint8_t *input = (uint8_t *)[data bytes];
    uint8_t *output = (uint8_t *)[mutableData mutableBytes];

    for (NSUInteger i = 0; i < length; i += 3) {
        NSUInteger value = 0;
        for (NSUInteger j = i; j < (i + 3); j++) {
            value <<= 8;
            if (j < length) {
                value |= (0xFF & input[j]);
            }
        }

        static uint8_t const kAFBase64EncodingTable[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";

        NSUInteger idx = (i / 3) * 4;
        output[idx + 0] = kAFBase64EncodingTable[(value >> 18) & 0x3F];
        output[idx + 1] = kAFBase64EncodingTable[(value >> 12) & 0x3F];
        output[idx + 2] = (i + 1) < length ? kAFBase64EncodingTable[(value >> 6)  & 0x3F] : '=';
        output[idx + 3] = (i + 2) < length ? kAFBase64EncodingTable[(value >> 0)  & 0x3F] : '=';
    }

    return [[NSString alloc] initWithData:mutableData encoding:NSASCIIStringEncoding];
}
```

### 参考文章
* [iOS 几种加密方法](http://www.cnblogs.com/dannygao/p/6958762.html)
* [iOS常见又通用的一些小方法集合（不定时更新，欢迎补充）](http://www.jianshu.com/p/f795e3609514)
