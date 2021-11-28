## NSURL 路径拼接 Tips

通过 baseURL 和关联字符串来构造 `NSURL` 路径的注意事项。

Apple 的 [NSURL](https://developer.apple.com/documentation/foundation/nsurl?language=objc) 类中有一个方法：

```objectivec
+ (instancetype)URLWithString:(NSString *)URLString 
                relativeToURL:(NSURL *)baseURL;
```

该方法使用一个 baseURL 和 `NSString` 类型的字符串进行拼接，构造并返回一个新的 `NSURL` 对象。使用示例：
```objectivec
NSURL *baseURL = [NSURL URLWithString:@"http://example.com/"];
NSURL *requestURL = [NSURL URLWithString:@"/api/v1/users" relativeToURL:baseURL];
NSLog(@"%@",requestURL.absoluteString);
// http://example.com/api/v1/users
```
以上示例通过一个服务器主机域名（即 `http://example.com/`）和一个请求路径（即 `/api/v1/users`）进行拼接，创建了一个 URL 请求路径。

但有的人可能会觉得这样的构造方式有些多余，因为你完全可以通过：
```objectivec
NSURL *requestURL = [NSURL URLWithString:@"http://example.com/api/v1/users"];
```
实现一步到位岂不是更好！

URL 拼接的场景常见于服务器主机域名需要切换的情况，比如说项目有三套服务器：

服务器描述 | 地址
-------------- | -------------- 
开发服务器 | http://192.168.0.1:80/
测试服务器 | https://112.112.112.112/
生产服务器 | https://example.com/

这样你就可以把这三套服务器地址写在全局宏定义中，然后随时实现动态切换，如：

```objectivec
#pragma mark - 服务器地址
#define DevelopServer   0
#define TestServer      0
#define ProductServer   1

#if DevelopServer
/** 开发服务器 */
#define URL_HOST    @"http://192.168.0.1:80/"
#elif TestServer
/** 测试服务器 */
#define URL_HOST    @"https://112.112.112.112/"
#elif ProductServer
/** 生产服务器 */
#define URL_HOST    @"https://example.com/"
#endif
```

em...拉回本文想说的话题，当使用路径拼接方法构造 `URL` 路径需要注意的事项，就是神奇的反斜杠 `/`。

### 当 baseURL 末尾存在反斜杠时

```objectivec
NSURL *baseURL = [NSURL  URLWithString:@"http://example.com/v1/"];
[NSURL URLWithString:@"foo" relativeToURL:baseURL];                  
// http://example.com/v1/foo

[NSURL  URLWithString:@"foo?bar=baz"  relativeToURL:baseURL];          
// http://example.com/v1/foo?bar=baz

[NSURL URLWithString:@"/foo" relativeToURL:baseURL];                 
// http://example.com/foo

[NSURL URLWithString:@"foo/" relativeToURL:baseURL];                 
// http://example.com/v1/foo

[NSURL  URLWithString:@"/foo/"  relativeToURL:baseURL];                
// http://example.com/foo/

[NSURL  URLWithString:@"[http://example2.com/](http://example2.com/)"  relativeToURL:baseURL]; 
// http://example2.com/
```
💡 **当 baseURL 末尾有反斜杠，构造字符串开头也有反斜杠时，创建的 `NSURL` 中 baseURL 中 host 之后的路径（`/v1`）会被自动删除**！

### 当 baseURL 末尾没有反斜杠时

```objectivec
NSURL *baseURL = [NSURL URLWithString:@"http://example.com/v1"];
[NSURL URLWithString:@"foo" relativeToURL:baseURL];
// http://example.com/foo

[NSURL URLWithString:@"foo?bar=baz" relativeToURL:baseURL];
// http://example.com/foo?bar=baz

[NSURL URLWithString:@"/foo" relativeToURL:baseURL];
// http://example.com/foo

 [NSURL URLWithString:@"foo/" relativeToURL:baseURL];
// http://example.com/foo/

[NSURL URLWithString:@"/foo/" relativeToURL:baseURL];
// http://example.com/foo/

[NSURL URLWithString:@"http://example2.com/" relativeToURL:baseURL];
// http://example2.com/
```
💡 **当 baseURL 末尾没有反斜杠时，不管构造字符串开头有无反斜杠，创建的 `NSURL` 中 baseURL 中 host 之后的路径（`/v1`）一定会被自动删除**！

### 推荐的写法

这是 [官方](https://developer.apple.com/documentation/foundation/nsurl?language=objc) 文档中的一个示例：

```objectivec
NSURL *baseURL = [NSURL fileURLWithPath:@"file:///path/to/user/"];
NSURL *URL = [NSURL URLWithString:@"folder/file.html" relativeToURL:baseURL];
NSLog(@"absoluteURL = %@", [URL absoluteURL]);
// file:///file:/path/to/folder/file.html
```
统一在 baseURL 末尾添加反斜杠（`/`）,然后在拼接的字符串开头不要添加反斜杠，以防止各种令人错愕的情况发生。

还有就是 baseURL 中只放一个主机地址，带路径的参数统一添加到字符串中，这样不管 baseURL 中有没有反斜杠都不用管了。

### `URLByAppendingPathExtension:` 和 `URLByAppendingPathComponent:` 的区别

* `URLByAppendingPathExtension:` 表示追加扩展名；
* `URLByAppendingPathComponent:` 表示追加路径；

具体可以看以下示例：
```objectivec
NSURL *baseURL = [NSURL URLWithString:@"http://example.com"];
NSURL *appendExtension = [baseURL URLByAppendingPathExtension:@"hello"];
NSURL *appendComponent = [baseURL URLByAppendingPathComponent:@"hello"];
        
NSLog(@"%@",appendExtension); // http://example.com.hello
NSLog(@"%@",appendComponent); // http://example.com/hello
```


### 参考

* [NSURL /NSURLComponents](http://nshipster.cn/nsurl/)


