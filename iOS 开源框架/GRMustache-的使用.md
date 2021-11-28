[GRMustache](https://github.com/groue/GRMustache) 是用 Objective-C 编写的 [Mustache](http://mustache.github.io) 模板引擎，适用于 MacOS Cocoa 和 iOS。



## 1. 项目中使用 GRMustache

通过 Cocoapods 导入 GRMustache 时，在 Podfile 文件中添加如下代码：

```ruby
pod 'GRMustache', '~> 7.3.2'
```



## 2. 创建模版文件

创建并编写一个 HTML 模版文件，命名为 `template.html`  并保存到项目中。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
    <H1> {{name}} </H1>
    <P> {{content}} </P>
</body>
</html>
```

模版文件中的标签用双圆括（`{{tag}}`）号表示。在以上的模版文件，`{{name}}` 和 `{{content}}` 都是标签，你可以把标签理解为占位符，在渲染模版文件时，我们会用动态的文本内容来替换它。



## 3. 渲染 HTML 内容

首先在代码中将该文件读取到内存中，再使用 `GRMustache` 的 `renderObject` 方法生成渲染后的 HTML 内容：

```objectivec
- (NSString *)renderTemplateWithName:(NSString *)name value:(NSString *)value {
    // 1.读取 template.html 文件
    NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
    NSURL *url = [bundleURL URLByAppendingPathComponent:@"template.html"];
    NSString *template = [NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
    
    // 2.要渲染的数据
    NSDictionary *renderObject = @{ @"name": name, @"content": value };
    
    // 3.生成渲染数据
    NSString *content = [GRMustacheTemplate renderObject:renderObject fromString:template error:nil];
    return content;
}
```



## 4. 通过 WKWebView 加载 HTML 内容

使用 GRMustache 模版引擎渲染并生成 HTML 内容后，即可通过 `WKWebView` 加载 HTML 内容了：

```objectivec
// 通过模版引擎渲染得到内容
NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
NSString *htmlString = [self renderTemplateWithName:@"我是一级标题名称" value:@"我是页面内容"];
[self.webView loadHTMLString:htmlString baseURL:bundleURL];
```

执行上述代码前，你还需要在视图控制器中添加一个 `WKWebView`  作为当前视图控制器的子视图。



## 5. 总结

GRMustache 到底实现了什么功能？？？

这是我们的 HTML 模版文件：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
    <H1> {{name}} </H1>
    <P> {{content}} </P>
</body>
</html>
```

然后给定以下一个简单的哈希表（JSON 字典）：

```json
{
  "name": "我是一级标题名称",
  "value": "我是页面内容"
}
```

GRMustache 渲染并生成了以下文件：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
    <H1> 我是一级标题名称 </H1>
    <P> 我是页面内容 </P>
</body>
</html>
```
最后，我们再通过 WKWebView 显示该 HTML 文件：

![](https://upload-images.jianshu.io/upload_images/2648731-8a9640a557928d82.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


## 6. 续

以上示例中，使用 GRMustache 中的类方法 `renderObject: fromString: error:` 时，我们提前将模版文件（即 `template.html` 文件）作为字符串加载进内存作为模版字符串，然后通过 GRMustache 渲染此模板字符串的对象，最后通过 WKWebView 显示。

这里多了一个预加载模版字符串文件的步骤，那我们能不能省略这一个步骤，直接渲染加载呢？

通过 GRMustache 中的另一个类方法 `renderObject: fromResource: bundle: error:` 即可实现。而且模版文件也可以写得非常简单，不必完整地声明一个 HMTL 文件。

首先，创建一个名为 `template.mustache ` 的文件，内容如下：
```html
<H1> {{name}} </H1>
<P> {{content}} </P>
```

然后，修改 GRMustache 引擎渲染方法：

```objectivec
- (NSString *)renderTemplateWithName:(NSString *)name  value:(NSString *)value {
    
    // 1.要渲染的数据
    NSDictionary *renderObject = @{ @"name": name, @"content": value };
        
    // 2.生成渲染数据
    NSError *error = nil;
    NSString *content = [GRMustacheTemplate renderObject:renderObject
                                            fromResource:@"template"
                                                  bundle:nil
                                                   error:&error];
    if (!content) {
        NSLog(@"%@",error);
    }
    return content;
}
```

最后，通过 WKWebView 加载 HTML 的方法是一样的。重要的是，通过该方法可以达到加载模版字符串一样的效果。
