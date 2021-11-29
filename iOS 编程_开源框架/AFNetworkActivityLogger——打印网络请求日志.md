[AFNetworkActivityLogger](https://github.com/AFNetworking/AFNetworkActivityLogger) 是基于 [AFNetworking](http://github.com/AFNetworking/AFNetworking/) 3.0 的扩展，可在发送和接收网络请求时记录网络请求，并在控制台打印输出。

> `AFNetworkActivityLogger` 会监听 `AFNetworkingTaskDidStartNotification` 和 `AFNetworkingTaskDidFinishNotification` 通知, 这两个通知会随着 AFNetworking 会话任务的开始和结束而被发送。为了进一步定制日志输出，建议用户通过创建新的遵守 `AFNetworkActivityLoggerProtocol` 代理协议的对象来实现所需的功能。

## 用法

### 1. 通过 Cocoapods 导入

```bash
pod 'AFNetworkActivityLogger', '~> 3.0.0'
```

### 2. 配置 AFNetworkActivityLogger
添加以下代码到项目的 `AppDelegate.m -application:didFinishLaunchingWithOptions:` 方法中：
``` objectivec
[[AFNetworkActivityLogger sharedLogger] startLogging];
```
现在，所有通过 `AFURLSessionManager` 创建的 `NSURLSessionTask` 对象都会在控制台打印请求和响应日志，例如：
```objectivec
GET http://example.com/foo/bar.json
200 http://example.com/foo/bar.json [0.1860 s]
```

如果你觉得默认的日志级别太过于啰嗦，比如说，你只想知道请求失败时的日志，你可以简单作如下修改：
```objectivec
// 设置只有当请求失败时，才会打印日志
[[AFNetworkActivityLogger sharedLogger] setLevel:AFLoggerLevelError];
[[AFNetworkActivityLogger sharedLogger] startLogging];
```

默认日志只会打印请求和响应的状态码和 URL，如果想打印 HTTP 请求和响应的内容，需要修改日志级别如下：
```objectivec
[[AFNetworkActivityLogger sharedLogger] setLogLevel:AFLoggerLevelDebug];
[[AFNetworkActivityLogger sharedLogger] startLogging];
```


## 日志级别

默认情况下，单例日志记录器（`[AFNetworkActivityLogger sharedLogger]`）是通过 `AFNetworkActivityConsoleLogger` 对象配置的，它的默认日志级别是 `AFLoggerLevelInfo `。要更改日志级别，只需要通过 `loggers` 属性访问日志记录器，然后调整日志级别。提供的日志级别有：

* `AFLoggerLevelOff`：不记录请求或响应日志。
* `AFLoggerLevelDebug`：记录请求的 HTTP 方法，URL路径，请求头和请求正文内容，以及响应的状态代码，URL路径，响应头，响应字符串和已用时间。
* `AFLoggerLevelInfo `：记录请求的 HTTP 方法，URL路径，以及响应的状态代码，URL路径和已用时间。
* `AFLoggerLevelError `：记录请求的 HTTP 方法，URL路径，以及响应的状态代码，URL路径和已用时间。，但仅仅是在请求失败时记录。

## 过滤日志

```objectivec
AFNetworkActivityConsoleLogger *testLogger = [AFNetworkActivityConsoleLogger new];
NSPredicate *predicate = [NSPredicate predicateWithBlock:^BOOL(NSURLRequest *  _Nonnull request, NSDictionary<NSString *,id> * _Nullable bindings) {
    return !([[request URL] baseURL] isEqualToString:@"httpbin.org"])
}];
[testLogger setFilterPredicate:predicate];
```

## 自定义日志

默认情况下，单例日志记录器（`[AFNetworkActivityLogger sharedLogger]`）是通过 `AFNetworkActivityConsoleLogger` 对象配置的。
要创建自定义记录器，请创建遵守 `AFNetworkActivityLoggerProtocol` 代理协议的新的对象，并将其添加到共享记录器。确保并配置正确的默认日志记录级别。
