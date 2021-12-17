本文是构建 iOS 界面系列的第1部分，它解决了如何在没有原生编程经验的情况下实现iOS设计的方法和原因，这对Web设计人员和开发人员来说是完美的。 

为Web设计教会了我们设计师编写HTML / CSS的价值，以及它在提高整体工作质量方面的作用。 在现代网页设计过程中，涉及静态模型和数百兆分片资产的瀑布工作流程早已不复存在。

不幸的是，对于移动设计来说也是一样。 大量的设计者发现Xcode令人望而生畏，他们更倾向于使用其他工具来设计原型，然后交给开发者来实现。 平台制造商和开发商没有把这个任务变得更容易。 文档和工具都是以开发人员为中心的，而社区普遍认为UI编程是开发人员工作的一部分。 现在是时候改变这一点。

## Xcode? Swift? UIKit? Oh My!

当我和设计师学习iOS的时候，我听到的最常见的问题是“我从哪里开始？”。 考虑到这个过程涉及的概念相对较多，这是一个有效的问题。 无法得到这个问题的明确答案往往导致在开始之前放弃。 在学习方面，没有比处理未知的未知物更令人沮丧的事情 - 我们不知道我们不知道的事情。 这个介绍旨在解决这个问题，然后再讨论更具体的话题。

### Frameworks

让我们从基础开始。 iOS操作系统是由苹果公司建立的一个封闭的系统，为每个iPhone，iPad和iPod Touch提供电源。 它从曾在Mac电脑上运行十多年的祖父OS X大量借用。 反过来，手机操作系统严重影响了苹果公司开发工具带的最新成员watchOS（苹果手表）和tvOS（苹果电视）。

iOS最基本的构建块之一就是框架。 框架是一个独立的API集合，旨在解决诸如控件，音频，视频，动画等特定问题。 框架可以驻留在其他框架中，导致一个层次结构，如果一个人不注意，可能很容易混淆。

iOS中的顶级框架称为Cocoa Touch，与Cocoa for OS X相反.Cocoa Touch托管您将作为UI设计者处理的所有框架，包括Foundation，UIKit和Core Animation。

![image](http://upload-images.jianshu.io/upload_images/2648731-fe9d2f784b22e0aa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**How will this affect my day-to-day work?**

您需要将这些框架中的一些导入到您的文件中才能访问某些API。 对于iOS，UIKit将是最常见的导入。

### Languages

现在我们已经确定了我们要使用的框架，我们需要一种方法来与他们沟通。 这就是编程语言的地方。多年来，开发人员已经能够使用诸如C，Objective-C甚至C ++等语言来调用内置的API。

在2014年，苹果公司将Swift作为一种现代化的官方编程语言，大部分框架都进行了更新以利用它。

**How will this affect my day-to-day work?**

你需要熟悉Swift，并且对于面向对象的编程如何工作有一些基本的了解。 这些听起来可能听起来令人望而生畏，但实际上并非如此。 不是如果你已经处理了 CSS 和 JavaScript。 这是一个Swift文件的例子：

```swift
import UIKit

class MyButton: UIButton {
  override func awakeFromNib() {
    super.awakeFromNib()
    backgroundColor = UIColor.yellowColor()
  }
}
```

上面的代码片段定义了一个新的按钮子类，并使其默认背景颜色为黄色。 这里介绍的概念包括类和继承，超出了这篇介绍性文章的范围，但仍然是充分利用框架API的基础。



### Software Tools

到目前为止，我们已经有框架和语言与他们沟通。 我们现在需要的是将这些成分结合在一起来构建我们的用户界面。 输入Xcode。

不论好坏，Xcode是唯一官方支持的用于构建iOS应用程序的编辑器。 它是功能丰富的，包括您需要开始的所有第一方框架。 这个名字实际上是误导性的，因为你花费大部分时间的部分甚至不需要编写代码：Interface Builder。

![image](http://upload-images.jianshu.io/upload_images/2648731-5fcc098e52bb623c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Interface Builder允许您以可视化的方式构建界面，包括将不同的控件放入画布中，并在某种程度上对其进行造型，而无需编写任何代码。 不是所有的东西都像你最喜欢的图形编辑器一样工作，但是相似之处在那里。

我们在设计界面时所做的很大一部分是布局。 为此，苹果已经构建了自动布局，一个基于约束的系统，描述视图之间的关系，而不是绝对的框架。

这将如何影响我的日常工作？

您将花费大部分时间在图形编辑器和Xcode之间。 此外，自动布局将成为您可能触及的一切的重要组成部分。

### Recap

我们已经了解了您需要熟悉的工具和技术，以便更好地实施您的iOS设计。你可能仍然想知道你应该如何处理这些问题 - 一个合理的问题，如果没有答案，可能会阻碍学习过程，或者更糟的是阻止它。先让我们看看坏消息：

* 熟悉上面介绍的所有概念和工具，才能充分发挥平台的潜力。
* 除Swift之外，很难在学习过程中将这些技术相互分离。

现在好消息是：

* Basic Swift is a low-hanging fruit to go for.
* 了解框架的最好方法就是使用它们。
* 当你有一个明确的目标时，学习顺序就会减少。
* 你将要使用的框架是有据可查的。
* 所有考虑的事情，学习中最根本的事情之一就是理解为什么。唉，那里的大部分资源都是完全侧重于如何做的饼干食谱。希望本系列能够为您解决这个问题找到正确的道路。

### 原文
* [Building iOS Interfaces: A Primer](https://robots.thoughtbot.com/building-ios-interfaces-swift-primer)
* [Building iOS Interfaces: Views](https://robots.thoughtbot.com/building-ios-interfaces-views)
* [Building iOS Interfaces: A Custom Button](https://robots.thoughtbot.com/building-ios-interfaces-custom-button)
