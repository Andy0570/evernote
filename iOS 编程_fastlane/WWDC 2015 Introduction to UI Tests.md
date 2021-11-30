视频地址：[WWDC 2015 Introduction to UI Tests](https://developer.apple.com/videos/play/wwdc2015/406/)


# WWDC Session 406: UI Testing in Xcode

Xcode 7 引入了完全集成到 IDE 中的 UI 测试功能。了解新 API 以及 UI 测试如何适应 Xcode 中的现有测试功能。了解如何开始记录您的应用程序，以及如何有效地制作和维护 UI 测试。

## UI testing

【UI 测试、UI 录制、UI 测试报告】

通过 UI 测试，你可以找到用户界面中的元素，与它们交互，并验证 UI 的属性和状态。

除了 UI 测试，我们还引入了 UI 录制功能，可以让你真正快速地为项目设置 UI 测试。

最后，我们更新了 Xcode 中的测试报告，其中显示了测试中通过和失败的结果，以适应我们在 UI 测试中获得的新数据。

所以我想谈谈 UI 测试的核心技术，第一个是 XCTest。第二是 Accessibility。

## XCTest

【XCTest 是 Xcode 的测试框架】

* 在 XCTest 中，你需要为测试用例创建子类，实现测试方法，并使用断言来验证你的预期结果是否成立。
* XCtest 集成在 Xcode 中，这意味着你可以从代码自动补全、调试的 ID 中获得一切，以及直接从源代码运行测试，并且查看结果。
* 你还可以通过 Xcode Server 和 Xcodebuild 实现持续集成。
* XCTest 支持 Swift 和 Objective-C。

## Testing Matrix（测试矩阵）

XCTest 作为单元测试框架在 Xcode 5 中引入。

在 Xcode 6 中，我们扩展了它的性能测试特性。它让你可以捕获代码中的回退，并确保它继续以最佳方式执行，发布。

现在，在 Xcode 7 中，我们引入了 UI 测试，您可以将其用于正确性和性能测试。


## Accessibility（可访问性）

可访问性是我们平台上的技术，它为我们的设备和所有其他用户都能获得的应用程序提供了相同的体验。

为了使辅助功能工作，它提供了一组丰富的关于 UI 的语义数据，Voice Over 等技术可以用来引导用户完成应用程序。

UI 测试使用它，并且 Accessibility 与 UI 工具包和应用程序工具包集成在一起，因此，当您使用这些框架中的控件时，您可以免费获得大量的可访问性支持，开箱即用。它还提供 API，允许您微调公开的可访问性数据。关键是，通过 UI 测试，您的测试将以用户的方式与应用程序进行交互。

UI 测试有一些您应该了解的要求。首先，它取决于操作系统中的新功能。对于 iOS，您需要 iOS 9，对于 OS X，您需要 OS 10.11。

UI 测试可保护您的隐私。这样做意味着您的 iOS 设备需要启用开发功能并连接到运行 Xcode 的可信主机。
在 OS X 上，您需要授予特殊 Xcode 助手应用程序的权限，并且在第一次运行 UI 测试时将提示您这样做。

## Getting Start

让我们来看看您在项目中开始使用 UI 测试所需的内容。

【Xcode Target】

首先，你需要一个新的 Xcode Target 类型。传统上，单元测试是 Xcode 中的一个特殊 Target 类型，现在 UI 测试也是一个 Target 类型。我们还介绍了一组用于 UI 测试的大量新 API，当然还有 UI 录制，这将真正让您快速入门。因此，Xcode testing Target 可以满足 UI 测试的特殊要求。

它包括：您正在测试的应用程序是在单独的进程中执行的。它还处理在隐私保护中使用辅助功能的权限。Target 为 Cocoa 和 Cocoa Touch 提供了新的模板，提供的辅助功能将按照您需要的方式设置所有内容。

这里有一个需要 UI 测试的 Target 设置，它用于标识您正在测试的应用程序。

![](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20190803-175022%402x.png)

在新的 API 中有三个关键类：

* `XCUIApplication` - 应用类
* `XCUIElement` - 元素类
* `XCUIElementQuery` - 元素查询类


【UI Recroding】

UI 录制功能可让您与设备、模拟器或 OS X Mac 上的应用程序进行交互。在你与应用程序进行交互时，它会自动生成执行这些交互所需的代码。您可以执行此操作来创建新的测试或扩展现有测试。

## Demo

演示 Lister App：

* 从列表中添加、移除项；
* 通过显式断言的方式验证应用程序中状态的更改；

![](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20190804-012006%402x.png)


## UI Testing API 原理

```swift
// application 应用代理:
let app = XCUIApplication()
app.launch()

// element and query: 用元素和查询去寻找添加按钮
let addButton = app.buttons[“Add”]
addButton.tap()

// assertion: 添加断言
XCTAssertEqual(app.tables.cells.count, 1)
```

### XCUIApplication

`XCUIApplication` 是测试应用程序的代理，它的生命周期独立于应用程序启动和终止的生命周期。因为你的测试是在一个单独的进程中运行的。你可以明确控制应用程序何时启动以及何时终止。

当启动测试时，系统始终会生成一个新的进程。所以我们做的事情就是不会给你提供一个完全干净的 Slate，因为你的应用程序中有很多状态等待着你去控制。但是，我们通过每次启动时都清理进程，帮助您最大限度地减少测试中必须处理的变量数量。因此，如果测试进程之前已经在运行，则 launch 调用将终止先前正在运行的实例。

```objc
XCUIApplication *app = [[XCUIApplication alloc] init];
app.launchArguments = @[ @"USE_MOCK_SERVER" ];
[app launch];
```

### XCUIElement

`XCUIApplication` 是查找 `XCUIElement` 的起点。与 `XCUIApplication` 类似，`XCUIElement` 是一个代理对象，但这次是针对测试应用程序中的 UI 元素。

`XCUIElement` 有类型。类似于 button，cell 或 window 等类型。 它们具有我们可以从访问系统中获得的称之为标识符、字符串或数据。它们是标识符、标签或标题等。大多数情况下，你会使用 Type 类型和 label 标签的组合方式找到 `XCUIElement` 元素。

例如，如果你添加了一个 Button 按钮，通过寻找带有标识符 add 的按钮类型。`XCUIElement` 会在应用程序中的层次结构中被找到。

### Element Hierarchy 元素层次结构

`XCUIApplication` 是这个树形层次结构的根。 如果你回想起以前学习计算机科学的日子，你就会想起树形数据结构。

```
Application 
|-Navigation Bar
| |- Title Label
| |-“Add” Button 
|
|-View
  |-Table 
    |-Cell
    | |-“Groceries” Label
    |
    |-Cell
    | |-“TechToys”Label 
    |
    |-Cell
      |-“Today”Label
```

### 元素的唯一性

这个树形层次结构是用于寻找元素的。在测试中使用元素时，元素必须是唯一的。每个元素中都有查询支持。查询必须解析为单个实例。否则，当我们去合成你告诉我们的点击事件时，却有多个与之匹配的按钮，它就不是确定的。

因此，对于元素查询来说，精确地匹配到确定某个元素非常重要的。

### Event Synthesis 事件整合

元素是事件整合 API 所在的位置。事件整合就是我们如何模拟用户和应用程序的交互，并在系统的最底层执行此操作，因此所有内容都通过与用户交互时相同的渠道。 用于事件整合的 API 是特定于平台的，但有一些例外。 我们有按钮点击事件，例如在 OS X 和 iOS 中，相同的按钮点击：

```swift
button.click() // OS X
button.tap() // iOS

textField.typeText(“Hello, World!”) // iOS & OS X
```

## XCUIElementQuery

`XCUIElementQuery` 是我们指定的元素 API 接口。查询解析为可访问元素的集合。 他们只能找到可访问性可见的元素。 他们将解决这些问题。

这意味着您可以使用 `count` 属性获取匹配数。 您还可以使用标识符下标或使用索引 API 处的元素来指定该集合中的不同元素。 我们马上就会看到这些。

查询策略：

1. 查询中元素之间的关系：
    * Descendants 后代
    * Children 子代
    * Containment 包含
2. 过滤


```objc
[[app.tables.element.cells elementAtIndex:4] tap];

// 通过特定的标签访问堆栈中的对象
[app.tables.element.cells[@"Call Mom"].buttons[@"More"] tap];
```


### `descendantsMatchingType()` 后代匹配查询

```swift
let allButtons = app.descendantsMatchingType(.Button)
let allCellsInTable = table.descendantsMatchingType(.Cell)
let allMenuItemsInMenu = menu.descendantsMatchingType(.MenuItem)

// 便利方法
let allButtons = app.buttons
let allCellsInTable = table.cells
let allMenuItemsInMenu = menu.menuItems
```


### `childrenMatchingType()` 子代匹配查询


```swift
let allButtons = app.buttons // descendantsMatchingType(.Button)
let childButtons = navBar.childrenMatchingType(.Button)
```

### `containingType()` 包含类型


```swift
let cellQuery = cells.containingType(.StaticText,
                                   identifier:”Groceries”)
```

## Combining Queries 组合查询


```swift
let labelsInTable = app.tables.staticTexts
```

## 从查询中获取元素

* Subscripting：`table.staticTexts[“Groceries”]`
* Index：`table.staticTexts.elementAtIndex(0)`
* Unique：`app.navigationBars.element`



## Evaluating Queries 过滤查询



## 断言

检查某个元素是否存在：

```objc
// 在一个元素对象上进行非空断言测试
XCTAssertNotNil(app.buttons[@"Submit"]);


XCTAssert(app.buttons[@"Submit"].exists);
```



## 参考

* [OSCHINA: Xcode 7 中的 UI 测试功能](https://www.oschina.net/translate/ui-testing-xcode-7?cmp)




