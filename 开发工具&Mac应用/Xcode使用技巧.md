## Xcode菜单

* [Xcode(v 8.3.3)菜单栏选项及作用介绍 @楚简约](http://www.jianshu.com/p/9012033d2a1a)
* [[Xcode基本操作 @曾梦想仗剑走天涯](http://blog.csdn.net/phunxm/article/details/17044337)](http://blog.csdn.net/phunxm/article/details/17044337)

## Xcode快捷键

* [Mac 键盘快捷键 - Apple 支持](https://support.apple.com/zh-cn/HT201236)
* [提升 iOS 开发效率的一些 keys（持续更新)](https://www.jianshu.com/p/46621caaf8ba) ⭐️⭐️⭐️
* [让您的Xcode键字如飞](http://www.cocoachina.com/ios/20170217/18713.html)

| 常用快捷键 | 功能 |
| ---- | ---- |
|command + shift + N |新建项目|
|command + N |新建文件|
|command + delete |删除一行|
|command + Z |撤销上一步操作|
|command + /| 注释|
|command + ] 或者 [ |前后缩进|
|command + option + ] 或者 [ |一行代码上下移动|
|command + control + 上下方向键 |.h .m 文件切换|
|command + 左右方向键| 光标移动到行头或行尾|
|command + control + 左右方向键| 后退和前进|
|command + R |运行|
|command + B| 编译|
|command + shift + k |clean缓存|
|shift + 上下⽅方向键| 选中代码|
|command + , |打开偏好设置|
|command + option + Enter |显示辅助编辑器|
|command + Enter |返回标准编辑器|
|command + 0 |打开/隐藏左侧导航区域|
|command + option + 0 |打开/隐藏右侧工具区域|
|command + shift + Y | 打开/隐藏下方调试区域|
|command + shift + o | 弹出快速查找文件窗口|
|command + shift + 0 | 打开官方文档窗口|






## 日常开发技巧

### 修改工程名、类名

[Xcode里修改工程名、类名☀️](http://www.jianshu.com/p/789e7740176c)

### Xcode 环境配置
[Xcode 环境配置最佳实践](https://github.com/xitu/gold-miner/blob/master/TODO/effective-environment-switching-in-ios.md)

### 代码区样式设置

进入 Xcode 偏好设置（快捷键 `⌘`+`,` ），切换至字体颜色与设置面板（**Fonts & Colors**），你可以选择预设的几个风格，也可以尝试自定义配色，比如我们要把背景色换成苹果绿，则需要把 **Background** 这一项的 Hex color 值设置为**#C7EDCC** ：

 ![](http://upload-images.jianshu.io/upload_images/2648731-4f6f09e5a6579938.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)



### 快速查找、切换文件

当你工作在一个庞大的项目中时，使用【**快速打开**】功能可以为你提供很大的便利。

具体方法是：打开 **File** 菜单，选择 **Open Quickly** 子菜单（快捷键：**⌘+Shift+O** ），输入关键字即可过滤文件，按回车键（⏎）即可快速打开切换到该文件：

![](http://upload-images.jianshu.io/upload_images/2648731-b78774a05a0ec201.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 快速更改同名变量

1. 在代码编辑区域，点击定位需要更改名称的变量；
2. 打开 **Edit** 菜单，选择 **Refactor** 子菜单中的 **Rename…** 命令。 此命令允许您统一修改文档中的某个变量。
3. 输入该变量的新名称 —> 预览 —> 保存。


### 统一修改某一方法内某一变量名

快捷键：`Cmd + Control +e`

1. 在方法中，点击选择变量名；
2. 打开 **Editor** 菜单，选择 **Edit All in Scope** 命令。 在目标区域内编辑全部子菜单。
3. 点击键盘修改变量名，所有同名变量就会被同步更改。



### 选中整个方法体

打开 **Editor** 菜单，选择 **Structure** 子菜单中的 **Balance Delimiter…** 命令。平衡界定符命令能帮助你选择整个方法体。



### 代码的查找和替换

1. 打开 **Find** 菜单，选择 **Find and Replace** 子菜单。 
2. 输入要查找的关键词和要替换的新名称。
3. 你可以选择替换当前的代码和所有符合的代码。

![](http://upload-images.jianshu.io/upload_images/2648731-6eb38f2aa93ea95b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 将代码段提取为方法

1. 拖动选中需要提取的代码；
2. 打开 **Edit** 菜单，选择 **Refactor** 子菜单中的 **Extract…** 命令。此命令允许您将一段代码转化为一个方法。
3. 输入新建的方法名称 —> **Preview**预览 —> 保存。

![](http://upload-images.jianshu.io/upload_images/2648731-e9875eeae3066304.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

提取后的结果：

```objectivec
- (void)addImage {
    UIImage *image = [UIImage imageNamed:@"github"];
    UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
    [self.view addSubview:imageView];
}

- (void)viewDidLoad {
    [super viewDidLoad];
    [self addImage];
}

- (void)didReceiveMemoryWarning {
    [super didReceiveMemoryWarning];
    // Dispose of any resources that can be recreated.
}
```



### 代码自动补全

这个是最常用的方法了：

1. 打开 **Editor** 菜单，选择 **Show Completions** 命令（快捷键 **ESC** ）。 
2. Xcode就会列出所有可能的方法供我们选择。




### 代码方法块的折叠

 先定位到某一方法体中，打开 **Edit** 菜单，选择 **Code Folding** 子菜单:

![](http://upload-images.jianshu.io/upload_images/2648731-5f0f76fb08cfde2b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **Fold  / Unfold** :折叠／显示当前方法体中的所有代码行；
* **Fold   / Unfold Methods & Functions** :折叠／显示所有方法体中的所有代码行；
* **Fold    / Unfold Comment Blocks** :折叠／显示注释块；
* **Focus Follows Selection** :专注于当前方法体。




## 代码整洁小技巧#pragma mark

pragma 预处理指令的两个作用：

1. 整理代码；
2. 防止编译器警告；

**#pragam mark** 一般是写在方法与方法之间的预处理指令，类似于起注释说明作用。

但是，重要的是，使用 **#pragam mark** 预处理指令可以将方法按照功能分组，使代码更整洁、更具有逻辑性。而且查找起来也非常方便。

 **#pragam mark** 不会对代码本身起任何作用，但是Xcode会根据 **#pragam mark** 组织文件中的方法。使用 **#pragam mark** 后，代码编辑器中的跳转栏是这样显示的：

 ![代码编辑器中的跳转栏](http://upload-images.jianshu.io/upload_images/2648731-c450f803ff172a48.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### 使用方法
#### * 添加分割线
>  #pragma mark — 

#### * 添加功能说明
>  #pragma mark 自定义的方法

#### * 同时添加分割线和功能说明
>  #pragma mark — 自定义的方法（注意留空格）

### 推荐的代码组织方式：
![](http://upload-images.jianshu.io/upload_images/2648731-75835938d9c5a2fb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

经常使用 **#pragam mark** ，可以使代码更加清晰有条理。一旦之后需要重新审查代码，就会感受到事先使用了 **#pragam mark** 的好处。

### 推荐阅读
* [nshipster: #pragma](http://nshipster.cn/pragma/) 
  [纽约时报 移动团队 Objective-C 规范指南](https://github.com/NYTimes/objective-c-style-guide)
* [Objective-C编码规范：26个方面解决iOS开发问题](http://www.imooc.com/article/1216)
* [禅与 Objective-C 编程艺术 （Zen and the Art of the Objective-C Craftsmanship 中文翻译）](https://github.com/oa414/objc-zen-book-cn/)
* [OC代码规范总结](https://github.com/Tim9Liu9/TimLiu-iOS/blob/master/OC代码规范总结.md)
* [iOS中书写代码规范35条小建议](http://www.cocoachina.com/ios/20170324/18950.html)



## 使用代码片段库

> ### `代码片段`，顾名思义，就是一“块”嵌入的代码框架，提前将所需的代码框架放入代码片段库，可能发生改动的地方用代码补全占位符代替，使用时，用自定义的字符串触发相应代码片段，在占位符处填写所需代码即可完成高效率的开发。



代码片段使用示例 `init:`：

![](http://upload-images.jianshu.io/upload_images/2648731-b68e27ca63afa67e.gif?imageMogr2/auto-orient/strip)

如果在某个 **Objective-C** 的实现文件中输入 `init` ，Xcode 就会自动列出一组备选项，其中包括 `init`  方法。选择后，Xcode 就会在当前位置加入一段 `init` 方法的默认实现代码。



**代码片段库**（Code Snippet Library）位于 Xcode 的工具区域（快捷键：⌥⌘0）库面板选择条中的第二项。

直接打开工具区域和代码片段库面板的快捷键：`⌘⌃⌥2`

在代码片段库中，Xcode 自带的代码片段是只读的，不能编辑，但是我们可以创建自定义的代码片段。



### 添加自定义代码片段

#### 编辑窗口

编写好你的代码片段之后，将选中的代码段拖拽至代码片段库 **Code Snippet Library** 中的编辑窗口如下：

![](http://upload-images.jianshu.io/upload_images/2648731-67f49af7d8e98a10.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



| 标题                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| Title               | 标题                                                         |
| Summary             | 描述概要说明                                                 |
| Platform            | 平台：All/ iOS/ Mac OS/ TV OS/ Watch OS                      |
| Language            | 代码语言：Objective-C 等                                     |
| Completion Shortcut | 代码片段的触发字符串（在源代码文件中输入该字符串后，会触发 **Xcode** 自动加入相应的代码片段） |
| Completion Scopes   | 代码使用范围：可选类接口部分、类实现部分等                   |



### 代码补全占位符:`<#expression#>`





### 自定义的代码片段

#### UITableViewDataSource

把如下一段通用的 **UITableViewDataSource** 方法选中拖拽入代码片段库：

```objectivec
#pragma mark - UITableViewDataSource

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return <#number of section#>;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return <#number of rows#>;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:<#@"reuseIdentifier"#> forIndexPath:indexPath];
    
    // Configure the cell...
    
    return cell;
}
```

编辑如下：

![](http://upload-images.jianshu.io/upload_images/2648731-604104ee308b97d9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以后在使用 Objective-C 语言编写 iOS 项目时，在类的实现文件中，输入 `UITableViewDataSource` ，就会列出该代码片段备选项。



#### UITableViewDelegate

把 **UITableView** 委托协议的两个实现方法加入代码片段库：

```objectivec
#pragma mark - UITableViewDelegate

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    return <#expression#>;
}

- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    // when selected row of indexPath
    
}
```

#### cellReusreIdentifier

```objectivec
static NSString * const cellReusreIdentifier = @"<#UITableViewCellStyleDefault#>";
```
![](http://upload-images.jianshu.io/upload_images/2648731-fad8e26799c8b940.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)


处理循环引用经常要用到的两行代码，也可以加入代码片段库：

#### weakSelf

```objectivec
__weak __typeof(self)weakSelf = self;
```

#### strongSelf

```objectivec
__strong __typeof(weakSelf)strongSelf = weakSelf;
```



#### 注释代码

你也可以根据你的喜好将注释代码添加进代码片段库

```objectivec
/** <#注释#> */
```

编辑窗口：

![](http://upload-images.jianshu.io/upload_images/2648731-1dc9f4c8816c4b0d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

声明了一个属性，要写注释？当然你可以输入 `//说明 `，使用代码片段就可以这么玩了， 输入 `xx`

![](http://upload-images.jianshu.io/upload_images/2648731-81a690b70eb26e7a.gif?imageMogr2/auto-orient/strip)

### pragma mark

这个代码片段可以配合上面刚刚提到的 **代码整洁小技巧#pragma mark** 一起使用，美滋滋🙂

```objectivec
#pragma mark - <#string#>
```
![](http://upload-images.jianshu.io/upload_images/2648731-57691e7ecd91c880.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)



### NSException

子类有自己的指定初始化方法，但又不想覆盖父类的指定初始化方法 `init`,可以写如下的方法，主要是用来防止开发创建对象时用错初始化方法。
```objectivec
- (instancetype)init {
    @throw [NSException exceptionWithName:@"Method Undefined"
                                   reason:@"Use Designated Initializer Method"
                                 userInfo:nil];
    return nil;
}
```
![](http://upload-images.jianshu.io/upload_images/2648731-85796fdbd0cd120c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


### 编辑自定义代码片段

选中代码片段库中需要重新编辑的代码片段，在弹出的编辑窗口点击 `Edit` 按钮，完成后点击 `Done` 按钮。



### 删除自定义代码片段

选中代码片段库中需要删除的代码片段，键盘 `Backspace` 或者 `Del` 并确认即可删除代码片段。 



### 参考

* [Xcode Snippets](https://nshipster.cn/xcode-snippets/)
* [iOS开发－学会使用代码块，提高你的开发效率](https://shevakuilin.github.io/2016/05/10/iOS开发－学会使用代码块，提高你的开发效率/)





