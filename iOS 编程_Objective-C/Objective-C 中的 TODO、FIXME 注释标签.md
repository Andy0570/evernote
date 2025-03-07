## 导读

在 Xcode 中通过 Objective-C 语言编写代码时，你可能经常会使用 `#pragma mark` 预处理指令来实现代码注释，或者对类实现文件中的方法按照功能进行分组。其实，**在 Xcode 中，也可以实现 `TODO`、`FIXME` 等高亮语法**。

## `#pragma mark` 语法

之前我在 [Xcode 使用技巧/代码整洁小技巧 #pragma mark](https://www.jianshu.com/p/68d8ce9eed14) 部分也有探讨过 `#pragma mark` 语法的常见用法。

简单来说，`#pragma mark` 就是用来组织类实现文件中的代码，并对方法进行实现合理分组（分割代码段）的预处理指令。

使用方式：

* 添加分割线：`#pragma mark -`
* 添加功能说明：`#pragma mark 功能说明`
* 同时添加分割线和功能说明：`#pragma mark - 功能说明`

这里我推荐 [禅与 Objective-C 编程艺术#pragma](https://github.com/oa414/objc-zen-book-cn/#pragma) 中的分组形式对代码进行分组。

```objectivec
- (void)dealloc { /* ... */ }
- (instancetype)init { /* ... */ }

#pragma mark - Lifecycle （View 的生命周期方法）

- (void)viewDidLoad { /* ... */ }
- (void)viewWillAppear:(BOOL)animated { /* ... */ }
- (void)didReceiveMemoryWarning { /* ... */ }

#pragma mark - Custom Accessors （自定义访问器，即 Getter 和 Setter 方法）

- (void)setCustomProperty:(id)value { /* ... */ }
- (id)customProperty { /* ... */ }

#pragma mark - IBActions （按钮执行的动作方法）  

- (IBAction)submitData:(id)sender { /* ... */ }

#pragma mark - Public （类对外公开的方法，即在头文件中声明过的方法）

- (void)publicMethod { /* ... */ }

#pragma mark - Private （类内部的私有方法）

- (void)zoc_privateMethod { /* ... */ }

#pragma mark - UITableViewDataSource （遵守的代理协议方法）

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath { /* ... */ }

#pragma mark - Override （覆盖父类的方法）

// ... 重写来自 ZOCSuperclass 的方法

#pragma mark - NSObject （覆盖 NSObject 类的方法 ）

- (NSString *)description { /* ... */ }
```

> [!NOTE]
>
> 敲黑板！到这里，如果有人就是搞不明白，写这些花里胡哨的东西到底有啥用啊？请在类文件中输入键盘快捷键 **Control + 6**，然后等待奇迹的发生。

## `#warning` 和 `#error` 语法

当我们使用键盘快捷键 `Command + B` 编译项目时，编译器可能会显示很多黄色的**警告**（warning）或者红色的**错误**（error）信息。但很有可能我们比编译器更了解哪些代码中存在警告或者错误信息，只是暂时解决不了。这时候我们就可以自定义警告或者错误信息：

```objectivec
#warning 自定义警告信息
#error 自定义错误警告
```

## TODO、FIXME 语法

用过 VS Code 的人很有可能安装过这两个插件：[Better Comments](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments)、[TODO Highlight](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight)。

在 Xcode 中进行编码时，至少可以使用 6 个不同的注释标签在代码中方便地进行跳转：

```objectivec
// MARK: <#普通标记#>
// TODO: <#待办事项#>
// FIXME: <#待修复的 Bug#>
// ERROR: <#错误#>
// ???: <#疑问#>
// !!!: <#重要信息#>
```

* **MARK 注释**：标记需要关注的代码，普通注释，与 `#pragma mark` 效果相同。
* **TODO 注释**：标记某些日后需要修正的代码、待办事项、等待日后更新或者完善的代码。
* **FIXME 注释**：标记某些可能存在 Bug 的代码，以便日后修复。
* **ERROR  注释**：标记某些可能存在错误的代码，以便日后修复。
* **??? 注释**：当你不确定某些代码逻辑的原理，或者没看懂别人写的代码时，可以打上三个问号进行标记。
* **!!! 注释**：标记需要关注的代码，通常用来标记重要的步骤、方法、功能、核心算法。

实际编程时，通过以上几个标签编写注释代码：

![](https://upload-images.jianshu.io/upload_images/2648731-b55d55173abf1e37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

键盘快捷键  **Control + 6** 查看标记效果：

![](https://upload-images.jianshu.io/upload_images/2648731-43e073d4fa90d84c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 参考

* [stack overflow: How can I mark “To Do” comments in Xcode?](https://stackoverflow.com/questions/16913055/how-can-i-mark-to-do-comments-in-xcode)
* [medium: //TODO: — Make your notes on Xcode stand out](https://medium.com/@cboynton/todo-make-your-notes-on-xcode-stand-out-5f5124ec064c)
* [Objective-C: FIXME, TODO and ???](https://tempdoron.wordpress.com/2009/12/05/objective-c-fixme-todo-and/)

