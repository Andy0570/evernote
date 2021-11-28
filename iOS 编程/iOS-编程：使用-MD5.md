MD5 只是密码学中的一个哈希算法，哈希算法通常用于校验数据完整性和数字签名算法中。

```
#import "ViewController.h"
// 导入加密功能的头文件
#import <CommonCrypto/CommonDigest.h>

/**
 演示系统自带的md5加密功能。
 */
@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 定义一个字符串对象
    NSString *str = @"Hello Apple!";
    // 将字符串对象转换为C语言字符
    const char *representation = [str UTF8String];
    
    // 创建一个标准长度的字符串
    unsigned char md5[CC_MD5_DIGEST_LENGTH];
    // 对C语言字符串进行加密，并将结果存入变量
    CC_MD5(representation, strlen(representation), md5);
    
    // 创建一个可变的字符串变量
    NSMutableString *mutableStr = [NSMutableString string];
    for (int i = 0; i < 16; i++) {
        // 通过遍历该变量，将加密后的结果，存入可变字符串
        [mutableStr appendFormat:@"%02X",md5[i]];
    }
    NSLog(@"%@",mutableStr);
}
```
