原文：[A first look into UI Tests @20150616](http://www.mokacoding.com/blog/xcode-7-ui-testing/)

> **注**：本文中的代码基于 Xcode 7 Beta 1 和 Swift 2.0。我会尽力将代码更新为最新版本，如有遗漏，请留言指正。

WWDC 15 可能不像以前那样令人充满惊喜了，但它确实给我们带来了些许好消息。在 [The state of iOS testing in 2015](http://www.mokacoding.com/blog/ios-testing-in-2015/) 一文中，我曾经写道：

> 虽然在过去的两年中，iOS 和 OS X 中的单元测试变得越来越好了，但在验收测试方面并没有做出任何改进。

现在，验收测试已经随着 Xcode 7 的变化而变得越来越好了！

新的 IDE 引入了 “UI Testing Bundle”，我们可以用它来编写和运行验收测试。

但还有更多。臭名昭着的 `UIAutomation` 已被弃用，尽管仍可从 Instruments 获得。但现在，我们有一套新的 API 来进行 UI 测试了。

这些 API 在 `XCTest` 中有很好的 Swift 接口和插件，因此我们可以使用简单的键盘快捷键 **⌘ + U** 运行验收测试。

我们要编写的新测试在原理上是与我们之前用 KIF、Calabash、Appium 等编写的旧测试是相同的，他们会像真实用户一样与应用程序进行交互，并利用 iOS 的辅助（Accessibility）功能实现这一目标。iOS 的辅助（Accessibility）功能在 iOS 9 中得到了改进。

### 初见  Xcode 7 中的 UI 测试

在开始之前，我想要提醒你的是，要使用这些新特性，你需要 Xcode 7 和一个基于 iOS 9 的项目。

在这篇文章中，我们将使用 UI 测试框架重新编写 [Bench](https://github.com/mokacoding/Bench/tree/xcode-7) 中现有的验收测试。如果你想要了解更多关于编写这些测试的过程，请查看这[两个 post](http://www.mokacoding.com/blog/job-stories-acceptance-tests-with-kif-and-specta/)。

首先，我们需要添加一个新的 Target。你会很高兴看到 Xcode 7 终于有一个专门的测试 Target 模块了🎉

![](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/2015-06-16-xcode-7-add-test-target-1.png)

Target 语言可以选择 **Objective-C** 或者 **Swift**。由于 UI Test 是一个全新的 API，我觉得这可能是尝试 **Swift** 的最佳方式，所以本文我们选择 **Swift**。

自动生成的模版代码没有做任何事情，但建议 “*使用录制功能（UI recroding）来开始编写 UI 测试*”。

“Recording” 是 Xcode 7 提供的一个有趣的选项，它可以自动为我们生成大部分测试代码。只需将光标移动到任何 `test...()` 方法内，点击页面左底角的红色 **Recording** 按钮，然后当我们与应用程序进行交互时，你就会看到代码会被神奇地自动生成。

![](https://s3.amazonaws.com/mokacoding/2015-06-16-xcode-7-record-test.gif)

我们为 Bench 应用列出的第一个验收标准是 “*当我点击 'say hellow' 按钮时，页面显示一个问候弹窗*”。

为该交互自动生成的代码如下：

```Swift
testSayHello() {
  let app = XCUIApplication() // 启动应用
  app.buttons["say hello"].tap() // 点击按钮
  app.alerts["Hello"].collectionViews.buttons["Dismiss"].tap() // 显示弹窗
}
```

正如你所看到的，语法非常简单。如果运行 UI 测试，我们将在控制台中看到如下输出：

```
Test Case '-[BenchUITests.BenchUITests testSayHello]' started.
2015-06-15 19:36:13.494 XCTRunner[3123:9611325] Continuing to run tests in the background with task ID 1
  t =     1.60s     Wait for app to idle
  t =     1.81s     Tap the "say hello" Button
  t =     1.81s         Wait for app to idle
  t =     1.81s         Find the "say hello" Button
  t =     1.84s         Dispatch the event
  t =     2.08s         Wait for app to idle
  t =     2.10s     Tap the "Dismiss" Button
  t =     2.10s         Wait for app to idle
  t =     2.56s         Find the "Dismiss" Button
  t =     2.57s         Dispatch the event
  t =     2.81s         Wait for app to idle
Test Case '-[BenchUITests.BenchUITests testSayHello]' passed (3.215 seconds).
```

输出显示了自测试用例开始执行以来对 UI 执行的操作及其时间增量。在检查那些非常看重时间的应用程序中的故障时，这可能会非常有用，例如鸡蛋计数器。

你也可以从测试报告（Test reporter）中查看它，如果单击检查器图标，还能看到测试失败时应用程序状态的屏幕截图。

你可能还会注意到，除了屏幕中的某些元素之外，此测试不会测试任何其他内容。

### 断言应用状态

为 Bench 应用列出的第二个验收标准是 [Job Story](https://jtbd.info/replacing-the-user-story-with-the-job-story-af7cdee10c27)，“*当我点击 'show elements' 按钮时，我会看到一个元素列表*”。

此测试的录制代码如下：

```Swift
func testShowElements() {
  let app = XCUIApplication() // 启动应用
  app.buttons["show elements"].tap() // 点击按钮
  app.tables.staticTexts["[N] Nitrogen (7)"].swipeUp() // 向上轻扫
  app.tables.staticTexts["[Ir] Iridium (77)"].swipeUp()
  app.tables.staticTexts["[Tl] Thallium (81)"].swipeUp()
  app.tables.staticTexts["[Uut] Ununtrium (113)"].swipeUp()
}
```

这次自动生成的代码不是很有用，而且与单元格的内容相结合了。

我们可以通过编辑该测试代码来简单地验证：当我们点击 “show elements” 按钮时，屏幕显示一个列表，并且列表中只有 118 个元素。除非在物理学上取得重大突破，否则列表中的元素的数量总是会持续存在一段时间，这使得这个测试更具有确定性，而不是依赖于类似于设计师或撰稿人情绪那样不稳定的东西。

让我们一步步来编写测试代码。开始是相同的，点击 “show elements” 按钮时加载下一个页面。

```Swift
let app = XCUIApplication()
```

`XCUIApplication` 是应用运行的代理，也是我们用来查询和交互的实例。

```Swift
app.buttons["show elements"].tap()
```

`.buttons["show elements"]` 是由 `XCUIApplication` 提供的 `XCUIElementQuery` 类型实例。我们可以将它与下标组合使用，寻找名为 “show elements” 的按钮，除非找到有且只有一个与该名称匹配的按钮，否则查找失败。如果找到了这个按钮，查询将向该按钮返回一个 `XCUIElement` 类型的代理。然后，我们可以使用通过代理实现的 `tap()` 方法点击这个按钮。

`XCUIApplication`，`XCUIElementQuery` 和 `XCUIElement` 是 UI 测试中的三个核心 API。你可以通过 Xcode 文档查看更多信息。

接下来要做的是，确保屏幕只显示一个 Table 列表：

```Swift
XCTAssertEqual(app.tables.count, 1)
```

我们可以将新的 UI 测试 API 和 `XCTAssert...` 方法结合使用。

确保只有一个 Table 列表后，我们得以继续可靠地执行下一个步骤，并断言列表中的单元格数量与预期的元素数量相匹配：

```Swift
let table = app.tables.elementAtIndex(0) // 找到页面中的列表
XCTAssertEqual(table.cells.count, 118)
```

以上就是我们使用 Xcode 7 进行的第一个 UI 测试示例。

这个 UI 测试功能组合起来如下所示：

```Swift
func testShowElements() {
  let app = XCUIApplication() // 应用
  app.buttons["show elements"].tap() // 点击应用下的 "show elements" 按钮

  XCTAssertEqual(app.tables.count, 1) // 断言应用中的 Table 列表是不是只有一个

  let table = app.tables.elementAtIndex(0) // 找到应用中的列表
  let expectedNumberOfElements: UInt = 118 
  XCTAssertEqual(table.cells.count, expectedNumberOfElements) // 断言列表元素数量个数
}
```

到目前为止，我个人对该框架非常满意，我期待用它来测试真正的应用程序。我认为 Apple 表达的信息非常明确，他们关心的是全范围覆盖测试，而作为开发人员，我们也应如此。

### 接下来要做什么

首先，尝试在更复杂的场景下使用这个新的框架会很不错。这里，我们为 Bench 设置的验收标准其实非常简单，仅仅是看看如何设置 UI 测试框架，而没有提及如何去充分利用它各项强大的功能。

在真实场景下，应用程序中的测试必须包括某种形式的网络解耦、动画、处理应用程序启动时的状态，以及更深入地使用断言。

其他可探索的有趣场景：从命令行和 CI（持续集成）环境中启动 UI 测试的行为方式。

为此，请关注此博客，因为我们将在接下来的几周内尝试涵盖这些主题。提示：您可以订阅 [时事通讯](http://www.mokacoding.com/blog/xcode-7-ui-testing/#subscribe) 或 [RSS 推送](http://www.mokacoding.com/feed.xml)。

*Happy coding, and leave the codebase better than you found it.*

