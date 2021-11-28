功能：在视图中显示一个问题，用户点击视图下方的「显示答案」按钮，可以显示相应的答案，用户点击上方的「显示问题」按钮，则会显示一个新的问题 。

要点：Xcode、MVC 设计模式、Interface Builder、AppDelegate、模拟器&真机运行应用、应用图标、启动图片。

![Quiz](https://upload-images.jianshu.io/upload_images/2648731-5ad0f38dd8c24547.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

# 第1章 第一个简单的iOS应用

## 1.1 创建Xcode项目

* 位于工作空间窗口左侧的是导航面板区域（navigator area），负责显示各种不同的导航面板。
* 项目导航面板（project navigator）：显示项目中的文件。
* 项目导航面板中的组（Group）只用来整理文件，与文件系统无关（注：你也可以创建带文件系统的组，使用 New Group with Folder 项即可）。
* 一个类（class）表示一种对象（object）。iOS 开发是面向对象的，每个 iOS 应用都可以看成是由一系列协同工作的对象构成的。

## 1.2 模型-视图-控制器

模型-视图-控制器（Model-View-Controller），简称 MVC，是 iOS 开发中频繁使用的一种设计模式。

MVC 设计模式的含义：**应用创建的任何一个对象，其类型必定是模型对象、视图对象或控制器对象三种类型中的一种。**

* 【视图对象】是用户可以看见的对象，用来构建用户界面。
* 【模型对象】负责存储数据，与用户界面无关。通常情况下，【模型对象】表示真实世界中与用户相关的事物。
* 【控制器对象】扮演“管家”的角色，它用于控制【视图对象】为用户呈现的内容，以及负责确保【视图对象】和【模型对象】的数据保持一致。
* 【模型对象】和【视图对象】之间没有直接产生联系，而是由【控制器对象】负责彼此间的消息发送和数据传递。

![MVC 设计模式](https://upload-images.jianshu.io/upload_images/2648731-8ef617649c6f1e20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


## 1.3 设计Quiz

开发中需要使用的对象：

* 四个视图对象：`UILabel` 和 `UIButton` 对象各两个；
* 两个控制器对象：`AppDelegate`、`QuizViewControl` 的对象各一个；
* 两个模型对象：`NSArray` 的对象两个；

## 1.4 创建视图控制器
略...

## 1.5 创建界面

* Xcode 会使用 Interface Builder （界面创建工具）打开 XIB 文件。
* Interface Builder 是一种可视化编辑器，可以用拖动对象的方式创建图形用户界面。
* XIB 的全称就是 XML Interface Builder。

### 创建、设置视图对象

打开对象库（快捷键：cmd + shift + L），通过鼠标拖拽的方式将视图控件添加到 XIB 中。

通过编辑属性检视面板（attributes inspector）中的属性来修改相应的视图对象。

### NIB文件

构建项目时，所有 XIB 文件都会被编译成 NIB 文件（NIB 文件体积更小，更容易解析），然后 Xcode 会将 NIB 文件拷贝至应用的程序包（bundle）中。程序包其实就是目录，其中包含应用的可执行文件和其会用到的所有资源文件。

应用在运行时刻会按需载入 NIB 文件并激活文件中的对象。

## 1.6 创建关联

通过关联（connection），一个对象可以知道另一个对象在内存中的位置，从而使这两个对象可以协同工作。在 Interface Builder 中可以创建两种关联：

* **插座变量（outlets）**：指向对象的指针；
* **动作（actions）**：在视图对象和用户发生交互时被调用的方法。

## 1.7 创建模型对象

通过编写代码的方式创建模型对象。

## 1.8 大功告成

### 在屏幕上显示视图控制器

使用 Xcode 开发 iOS 应用时，所有应用模版都会自动帮助开发者创建一个应用程序委托（app delegate）。应用程序委托是每一个 iOS 应用都必须具备的启动入口。

应用程序委托负责管理应用的 `UIWindow` 对象。`UIWindow` 对象表示应用唯一的主窗口。为了在屏幕上显示 QuizViewController，需要将它设置为 `UIWindow` 对象的根视图控制器（root view controller）。

## 1.12 启动图片

iOS 中的启动图片（launch image）的作用：**向用户传达“应用正在启动”的信息，并描绘应用启动后的用户交互界面**。因此，好的启动图片应该是应用的空白（contentless）截图。
