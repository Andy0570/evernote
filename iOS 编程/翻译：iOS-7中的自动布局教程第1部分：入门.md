本文内容：参考外文学习并使用**Auto Layout**，顺便翻译记录。

### 原文

* [Beginning Auto Layout Tutorial in iOS 7: Part 1](https://www.raywenderlich.com/50317/beginning-auto-layout-tutorial-in-ios-7-part-1) @Matthijs Hollemans on October 16, 2013
* [Beginning Auto Layout Tutorial in iOS 7: Part 2](https://www.raywenderlich.com/50319/beginning-auto-layout-tutorial-in-ios-7-part-2) @Matthijs Hollemans on October 16, 2013
	#### 作者 : Matthijs Hollemans
	**Matthijs Hollemans**是一位独立的开发人员和设计师。 [访问他的网站](http://www.matthijshollemans.com)。   
![](http://upload-images.jianshu.io/upload_images/2648731-29ee036c74ec6fba?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

* 注：第一篇文章翻译到一半的时候发现早就有人都翻译好了，就是下面的这两篇，但咱也不能烂尾啊不是，干脆就自己再翻译着再啃一遍吧。
* [开始iOS 7中自动布局教程(一)](http://www.cocoachina.com/industry/20131203/7462.html)
* [开始iOS 7中自动布局教程(二)](http://www.cnblogs.com/zer0Black/p/3977288.html)  


---
### iOS 9, Xcode 7, Swift 2 环境下可以参考:

* [Auto Layout Tutorial in iOS 9 Part 1: Getting Started](https://www.raywenderlich.com/115440/auto-layout-tutorial-in-ios-9-part-1-getting-started-2) @ Bradley Johnson on September 22, 2015
* [Auto Layout Tutorial in iOS 9 Part 2: Constraints](https://www.raywenderlich.com/115444/auto-layout-tutorial-in-ios-9-part-2-constraints)

	#### 作者 : Bradley Johnson
	一名位于西雅图 Getty Images Inc.公司的移动开发人员。具有大约4年的iOS开发经验。   
	![](http://upload-images.jianshu.io/upload_images/2648731-3a34e658ecd58bce?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)   
	   

# iOS 7中的自动布局教程第1部分：入门

来自Ray的注释:教程团队成员 **Matthijs Hollemans**（iOS Apprentice系列作者）已将此教程移植到iOS 7作为 [iOS 7系列](https://www.raywenderlich.com/49762/introducing-the-ios-7-feast) 的一部分。我们希望你会喜欢！

设想这样一个场景，如果你想让你的应用程序能够在手机竖屏和横屏状态下都能正常显示，但是调试起来却是一件足够令人崩溃的事情。另外，想要约束屏幕布局让APP同时支持iPhone和iPad，是否是一件足以把你逼到绝望边缘的事情？ 别灰心，这篇文章会给你带来好消息！   

为屏幕设计一个总是能保证大小相同的用户界面并不困难，但如果屏幕的frame是可以改变的，UI元素的位置和大小也必须适应这些新的尺寸变化。

到目前为止，如果你的设计是相当复杂的，你就必须编写大量的代码来支持这种自适应布局。 你会很高兴听到这再也不是个麻烦了——iOS 6给iPhone和iPad带来了一个非常棒的新功能：**自动布局（Auto Layout）**。 Xcode 5和iOS 7让它实现得更好！如果你尝试在Xcode 4中实现自动布局并想放弃时，那么你真的应该给Xcode 5一个机会了。

Auto Layout不仅可以轻松地在应用程序中支持不同的屏幕的尺寸，而且还可以使国际化实现变得非常方便。你不再需要为希望支持的每种语言创建新的nib文件或storyboard文件，这包括从右到左的语言，如希伯来语（Hebrew）或阿拉伯语（Arabic）。

此自动布局教程将向您介绍如何在**Interface Builder**中开始使用自动布局。 在[iOS 6 by Tutorials Third Edition](https://www.raywenderlich.com/store/ios-6-by-tutorials)教程中，我们将本教程向深层次推进，然后会有一个新的章节，基于这些知识，并展示如何通过代码实现Auto Layout的全部功能。

> 提示：我们正在将基于iOS 6上的所有教程到更新到iOS 7 上——这是一个预览版本！ 当我们完成所有的更新后，更新将免费下载到所有iOS 6的Tutorials PDF客户手中。

所以吃着点心和你最喜欢的咖啡饮料，准备好成为一个自动布局（Auto Layout）大师吧！

## 弹簧和支柱（Autosizing）的问题
毫无疑问你一定对**autosizing masks**非常熟悉 - 它也常被称为“弹簧和支柱”模型。 **autosizing masks**确定了当父视图更改大小时，相应子视图发生的改变。 它是否有灵活或固定的边距（**margins**）（支柱），以及它的宽度和高度（弹簧）发生的变化？

例如，如果子视图使用灵活可变的宽度，当父视图变得更宽时，则子视图将成比例地变宽。 如果使用固定的右边距，子视图的右边缘将始终贴着父视图（superview）的右边缘。

**Autosizing** 系统适用于简单的情况，但是当你的布局变得更复杂时，它很快就会失效。 让我们来看一个弹簧和支柱完全失效的例子。

打开Xcode 5并创建一个新的基于 **Single View Application** 模板的iPhone项目。 设置应用程序名为：StrutsProblem

![1](http://upload-images.jianshu.io/upload_images/2648731-497cd3b315c408b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击 **Main.storyboard** 以打开Interface Builder（界面生成器）界面。在做任何操作之前，先禁用该storyboard的Auto Layout功能。你可以在右侧工具栏的六个标签中的第一个 **File Inspector** (文件检视器)中执行该操作。

![2](http://upload-images.jianshu.io/upload_images/2648731-cae9307740861995.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

取消勾选**Use Autolayout**。 现在storyboard就会使用旧的支柱和弹簧(struts-and-springs)模型.

>译者注：我在Xcode 8环境下取消勾选的对话框界面如下：

![](http://upload-images.jianshu.io/upload_images/2648731-d038ab730bae7ee9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

取消勾选**Use Auto Layout**后，会弹出如下对话框：

![](http://upload-images.jianshu.io/upload_images/2648731-f1933a1cdb5c97be.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   
**Trait Variations**:用来声明APP在不同设备上的不同特征。使用**特征变量(Trait Variations)**需要 **Autolayout** 支持，因此如果取消勾选**Use Autolayout**，那么**特征变量(Trait Variations)**也会不可用。
   
禁用特征变量(Trait Variations)将文档限制为存储单个设备的数据。用于表示目标设备系列的性状集合的数据将被保留，并且所有其他数据将被删除。此外，segues 将被转换为它们的非适应性等效。

这里我们点击 **Disable Trait Variations**。

* 关于[特征变量](http://www.skyfox.org/ios-use-trait-variations.html)

> 注意：使用Xcode 4.5或更高版本创建的任何新的nib或storyboard文件将默认开启Auto Layout。 因为Auto Layout是iOS 6及以上系统的新特性，如果你想使用最新的Xcode来开发与iOS 5兼容的应用程序，你需要通过取消选中“Use Autolayout“复选框,在任何新创建的nib或storyboard上禁用Auto layout。

拖动三个新视图UIView到主视图上，并按照这样的方式排列：

![](http://upload-images.jianshu.io/upload_images/2648731-76b1c2df704a4dc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了便于清晰理解，给每一个新视图设置它们自己的颜色以便于区分是哪个。

每个视图距离窗口的边框20pt; 视图之间的距离也是20pt。 底部视图是280pt宽，顶部的两个视图都是130 pt宽。 所有的视图都是254pt高。

运行APP在 **iPhone Retina 4-inch 模拟器**（没有的话也可以用iPhone SE代替）上，旋转模拟器至横屏状态。这时APP看起来是这样子的，并不是我所期待的样子：

![](http://upload-images.jianshu.io/upload_images/2648731-a9ea86d3d61316d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注意：你可以使用菜单栏中的 **Hardware\Rotate Left** 和 **Rotate Right ** 选项来旋转模拟器，或者通过按住键盘 **⌘** 同时按向左或者向右的方向键来实现屏幕旋转。

相反，你想要APP在横屏状态下应该是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-9c52bd1497af653c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

显然，autosizing masks为所有三个视图留了一些需要设置的东西。 将左上角视图的 autosizing设置更改为：

![](http://upload-images.jianshu.io/upload_images/2648731-838924b76530312a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该操作会使视图贴着顶部和左边缘（但不是底部和右边缘），并且当父视图更改其大小时会调整子视图的宽和高。同样，将右上角视图的autosizing设置为：

![](http://upload-images.jianshu.io/upload_images/2648731-b414aba8bd6a93af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

底部视图设置：

![](http://upload-images.jianshu.io/upload_images/2648731-fd689f7f27c38983.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再次运行APP并且旋转至横屏状态，它应该看起来是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-44bc9d8df119c304.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

已经很接近我们想要的样式了，但还不完全匹配。视图之间的距离不正确。或者用另一种说法：视图的大小比例并不是100%正确的。问题就在于 **autosizing masks** 告诉子视图当父视图调整大小时，子视图大小随之改变，但是它无法告诉子视图**以何种方式**调整大小。

你或许可以这样设置 autosizing masks  - 例如，调整可变的宽度和高度设置（弹簧） - 但是你不会得到三个视图之间的距离正好完全是20pt的结果。

![这是为什么呢](http://upload-images.jianshu.io/upload_images/2648731-98d2214ba50694e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

要用弹簧和支柱（springs and struts）的方法解决这个布局问题，不幸的是，你将不得不写一点代码。

在旋转UI界面之前，之中和之后，UIKit会向您的视图控制器发送几个消息。 您可以截取这些消息以更改UI的布局。 通常，你需要覆写 ``viewWillLayoutSubviews`` 方法以更改需要重新排列的任何视图的大小。

在这样做之前，你首先必须声明插座属性来引用需要调整的视图。

切换到辅助编辑器模式（ Assistant Editor mode）（Xcode工具栏上的编辑器工具集上的中间按钮），然后鼠标右击拖动三个视图的每个视图到 **ViewController.m** 中：

![](http://upload-images.jianshu.io/upload_images/2648731-3a2292159f926f28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分别连接每个视图到这三个属性：


```
@property (weak, nonatomic) IBOutlet UIView *topLeftView;    
@property (weak, nonatomic) IBOutlet UIView *topRightView;      
@property (weak, nonatomic) IBOutlet UIView *bottomView;     
```


添加以下代码到**ViewController.m** 中：


	- (void)viewWillLayoutSubviews
	{	
	// 译者注：原文中if的判断方法在我最新的Xcode 8中尝试时发现被弃用了。
    ~~if(UIInterfaceOrientationIsLandscape(self.interfaceOrientation))~~
    // 换用下面这个
    if(UIInterfaceOrientationIsLandscape([[UIApplication sharedApplication] statusBarOrientation]))
    {	 
    	// 判断设备方向：如果是横屏模式
        CGRect rect = self.topLeftView.frame;
        rect.size.width = 254;
        rect.size.height = 130;
        self.topLeftView.frame = rect;
 
        rect = self.topRightView.frame;
        rect.origin.x = 294;
        rect.size.width = 254;
        rect.size.height = 130;
        self.topRightView.frame = rect;
 
        rect = self.bottomView.frame;
        rect.origin.y = 170;
        rect.size.width = 528;
        rect.size.height = 130;
        self.bottomView.frame = rect;
    }
    else
    {
        CGRect rect = self.topLeftView.frame;
        rect.size.width = 130;
        rect.size.height = 254;
        self.topLeftView.frame = rect;
 
        rect = self.topRightView.frame;
        rect.origin.x = 170;
        rect.size.width = 130;
        rect.size.height = 254;
        self.topRightView.frame = rect;
 
        rect = self.bottomView.frame;
        rect.origin.y = 295;
        rect.size.width = 280;
        rect.size.height = 254;
        self.bottomView.frame = rect;
    }
	}


当视图控制器旋转到新方向时，就会回调这段方法。 它先判断视图控制器旋转到的方向，并适当调整视图大小——在这种情况下，使用基于iPhone的已知屏幕尺寸的硬编码偏移(将可变变量用一个固定值来代替的方法叫做hard-code)。 此回调发生在动画块中，因此会以动画的方式调整视图大小。

先别运行APP,首先你需要恢复刚才所作的所有三个视图的 **autosizing masks** 设置，否则自动调整机制将与您在``viewWillLayoutSubviews``中的设置的视图位置和大小相冲突：

![](http://upload-images.jianshu.io/upload_images/2648731-64591d061ca99d84.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

三个都还原后就可以了。 运行APP，旋转到横屏状态。 现在的视图排列得很好。 旋转回竖屏状态，验证一切看起来也很好。

这奏效了。代价就是，你必须为一个看起来很简单的布局写很多的代码。 想象一下调整一个真正复杂的布局需要花费的工夫，尤其是单个视图以动态的方式更改大小，或者子视图的数量是不确定的情况下。

现在试着在3.5寸的模拟器上运行程序。糟糕。视图的位置和大小又错了，因为``viewWillLayoutSubviews``的硬编码坐标是基于4英寸大小的手机(320x568取代320x480)。你可以增加另一个if语句判断屏幕大小，并使用不同的坐标集，但是你可以看到这个方法很快变得不切实际。

![](http://upload-images.jianshu.io/upload_images/2648731-2bbe5f7c63ac4412.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注意：您可以采取的另一种方法是为竖屏和横屏设置单独的nib文件。 当设备旋转时，您从另一个nib加载视图，并替换出现有的nib。 但这仍然需要非常大的工作量，它也增加了许多麻烦，必须设置两个nib文件，而不是一个。 当你使用storyboards而不是nibs时，这种方法是不切实际的。

# Auto Layout （自动布局）拯救猿！
现在，你将看到如何使用自动布局实现相同的效果。 首先，从**ViewController.m**中删除``viewWillLayoutSubviews``方法，因为该方法不用写任何代码。

选择**Main.storyboard**，并在**File inspector**中勾选**Use Autolayout**为该storyboard文件开启自动布局：

![](http://upload-images.jianshu.io/upload_images/2648731-64ee4a669608b21d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注意：自动布局始终作用于整个nib或storyboard文件。 如果选中该框，那么所有插入nib或storyboard中的视图都将使用自动布局。

运行APP并且旋转为横屏状态，它现在看起来是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-a16d1c0f76063262.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

让我们把Auto Layout放到Action中。 按住⌘键的同时单击顶部的两个视图（绿色和黄色视图），以便同时选中这两个视图。 从Xcode的编辑器菜单中，选择 **Pin \ Widths Equal（相等宽度）：**

> 还是译者提示：这一步操作在 Xcode 5 环境下应该入下图所示，但我在 Xcode 8 的环境下操作发现情况已经不一样了，菜单界面都发生了变化，Editor 里 没有 Pin 选项了。

![](http://upload-images.jianshu.io/upload_images/2648731-30f9649b6260b744.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 所以选中两个View之后不妨可以在画板右下角设置，之后的很多操作步骤在新Xcode环境下找不到的话你也可以在这里试试：

![](http://upload-images.jianshu.io/upload_images/2648731-ae85fcb041e9d6cb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再次选中相同的两个视图，然后选择 **Editor\Pin\Horizontal Spacing(水平距离)**。（即使在执行第一次Pin操作后两个视图看起来是仍然选中，请注意这只是它们处于特殊的布局关系的一种显示模式，因此你必须重新选中这两个视图。）

现在storyboard看起来是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-af7764265b456ba5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

橙色的“T条”形状的东西代表视图之间的约束。 到目前为止，你添加了两个约束：两个视图的 **Equal Widths** （相同宽度）约束（由带有等号"="的T条表示）和位于两个视图之间的 **Horizontal Space**（水平距离）约束。约束（Constraints ）代表了视图之间的关系，它们是使用Auto Layout设置布局的主要工具。它可能看起来有点可怕，但它实际上是很直接的描述，一旦你知道这一切意味着什么时。

要继续构建此屏幕的布局，请执行以下步骤。 每个步骤都会添加更多的橙色T条。

对于左边的视图，从 **Editor\Pin** 菜单选择：

* Top Space to Superview ——上边界到父视图的距离
* Leading Space to Superview ——左边界到父视图的距离

对于右边的视图，选择：

* Top Space to Superview ——上边界到父视图的距离
* Trailing Space to Superview ——右边界到父视图的距离

对于底部那张大视图：

* Leading Space to Superview ——左边界到父视图的距离
* Trailing Space to Superview ——右边界到父视图的距离
* Bottom Space to Superview ——下边界到父视图的距离

现在你应该有了如下的约束：

![](http://upload-images.jianshu.io/upload_images/2648731-49b29d416073df07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意，T形条仍然是 **橙色** 的。这就意味着你的布局是不完整的;也就是说自动布局没有足够的约束来计算视图的位置和大小。解决方案是添加更多的约束，直到它们变成 **蓝色** 。

按住⌘并选中所有三个视图。从 **Editor** 菜单中，选择 **Pin\Heights Equally（相等高度）**

现在选中左上角视图和底部视图（像先前一样使用⌘），并选择 **Editor\Pin\Vertical Spacing（垂直距离）**。

Interface Builder 应该看起来是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-bb37cbe68ab0f836.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在T条已经变为**蓝色**了。自动布局现在有足够的信息来计算有效的布局了。 它看起来有点凌乱，不过这是因为**Equal Widths**和**Equal Heights**约束占据了很大空间的缘故。

运行APP并且...哇噢，不需要写一行代码便运行的很好了。不管你在哪个模拟器上运行它；在3.5英寸和4英寸设备上，布局效果都运行良好。

![](http://upload-images.jianshu.io/upload_images/2648731-492d1c96857d5fbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

真酷，但是实际上你到底做了什么你？Auto Layout 允许你表达：**布局中的视图如何相互关联**，而不是要求你对视图的大小和位置进行硬编码设置。

你已经把以下关系——也就是所谓的约束——放入了布局中：

*  左上角和右上角的视图总是保持相同的宽度（也就是pin中第一个widths equally命令）
*  左上角和右上角视图的水平距离是20pt（也就是pin中的horizontal spacing命令）
*  所有的视图总是有相同的高度（pin中heights equally命令）
*  顶部两个视图和底部视图的垂直距离是20pt（pin中的vertical spacing命令）
*  视图和屏幕边缘的距离是20pt （上边界、下边界、左边界和右边界到父视图的距离）

这些就足以表达出让 **Auto Layout** 怎么放置视图，以及当屏幕大小改变时该如何处理了。

![](http://upload-images.jianshu.io/upload_images/2648731-23762c2e1cae43fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你可以在左侧的 **Document Outline** 中查看所有的约束(constraints)。 当你为storyboard开启**Auto Layout**时，就添加了名为**Constraints**的部分。 （如果没有看到大纲窗格，请单击Interface Builder窗口底部的箭头按钮。）

如果你选中了**Document Outline**中的一个约束(constraints)，Interface Builder界面就会通过在约束周围画白色轮廓的方式，高亮显示约束在视图上的位置，并且还会为其添加阴影以使其突出显示。

![](http://upload-images.jianshu.io/upload_images/2648731-9eea894b7b5e523a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

约束是真实的对象（**NSLayoutConstraint** 类），它们也有属性。例如，选择在两个顶部视图之间创建填充的约束（名“为Horizontal Space（20）”，请务必选择正确），然后切换到**Attributes inspector**。 你就可以通过编辑**Constant**字段来更改边距的大小。

![](http://upload-images.jianshu.io/upload_images/2648731-295fba89d07a8b5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

把值设置为100，然后再次运行APP，现在边距变得更宽了：

![](http://upload-images.jianshu.io/upload_images/2648731-ffdf7d85fd3be83e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当你在应用程序中描述视图时，**Auto Layout** 比 **springs and struts** 更具表达力。 在本教程的其余部分中，您将了解所有约束以及如何在Interface Builder中应用它们以创建不同类型的布局。

# Auto Layout 是如何工作的

正如你在上面的测试中所看到的，**Auto Layout**中的基本工具是约束（**constraint**）。 约束描述了两个视图之间的几何关系。 例如，您可能有一个约束：   
“label A右边缘和button B左边缘有20点的空白空间”

自动布局采用所有这些约束，并进行一些数学计算，计算出所有视图的理想位置和大小。 您不再需要自己设置视图的frame——自动布局会帮你实现它，完全基于你在这些视图上设置的约束。

自动布局以前，你总是需要为视图的frames设置硬编码，要么在Interface Builder中将他们放置在特定的坐标中，或通过传递一个rectangle到``initWithFrame：``，或者设置视图的**frame**，**bounds**或者**center**属性。

对于你刚刚设置的APP来说，你尤其需要像这样设置frames：

![](http://upload-images.jianshu.io/upload_images/2648731-ac48baee6fc0cab6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还需要为这些视图设置自动调整大小的masks：

![](http://upload-images.jianshu.io/upload_images/2648731-54aa95cda946df0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这再也不是你需要为屏幕设计所考虑的东西了。有了**Auto Layout**（自动布局），你需要做的是这些：

![](http://upload-images.jianshu.io/upload_images/2648731-a8c0954e6191b8a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

视图的大小和位置不再重要了; 只有约束才是最重要的。当然，当你拖动一个新的button或label到画布上时，它将有一定的大小，并且你会把它放在一个特定的位置，但这只是一个设计帮助，用来告诉Interface Builder在哪里放置约束 。

# 想你所想，如你所愿

使用约束的最大优点是，你不再需要调整坐标以使你的视图显示在正确的位置。 相反，你可以向自动布局描述视图如何相互关联，自动布局将为您做所有的辛苦工作。 这就是所谓的根据目的设计（designing by intent）。

当你根据目的设计时，你表达的是你想要实现什么，而不是是如何实现。你不应该说：“该按钮的左上角坐标是（20,230）”，你应该说：

按钮在其父视图中垂直居中，并且放置在距离父视图的左边缘固定的距离处。

使用此描述，自动布局可以自动计算按钮应显示的位置，不用管父视图的大小。

其他根据目的设计的示例（自动布局可以处理所有这些指令）：

* 这两个textField应该总是保持相同大小；
* 这两个button应该总是同时移动；
* 这四个label应该总是保持右对齐；

这使得你的用户界面的设计更具描述性。你只需定义约束，系统就会自动计算其frame。

在第一部分你看到，即使为几个视图在横竖方向上正确的布局都需要做大量的工作。有了自动布局，你可以绕过这些麻烦。如果你正确的设置了约束，那么在横竖屏方向上，布局将不需要做任何改变。

使用自动布局另一个重要的好处就是国际化。比如德语中的文本，出了名的比老奶奶的裹脚布还要长，适配起来是一件很麻烦的事。再次，自动布局释放了程序员的双手，因为它能根据label需要显示的内容自动改变label的大小。

添加对德语，法语或任何其他语言的支持现在只是一个设置约束的问题，翻译文本...然后就完了！

![](http://upload-images.jianshu.io/upload_images/2648731-7c468dfb91effe5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

学习自动布局最好的方法就是使用它，所以这正是剩下教程中你会学到的东西。


> 注意：自动布局不仅对旋转有作用；它还能轻易的缩放你UI的大小从而适应不同尺寸的屏幕。这并不是巧合，当iPhone5拥有更高屏幕的同时，这个技术也同时加到了iOS中！自动布局能轻易的拉伸你程序的用户界面，从而充满iPhone5垂直方向上多出来的空间。随着iOS7中的动态类型的出现，自动布局变得更加重要了。用户现在可以改变全局字体大小设置--有了自动布局，这将变得非常简单。

# 拥抱约束（courting constraints）

关闭你当前的项目并用 **Single View Application** 模板创建一个新的iPhone项目。叫做"Constraints"。任何用Xcode5创建出来的项目都会自动假定你会使用自动布局，所以你并不需要额外做任何事情。

点击**Main.storyboard**打开Interface Builder。 将一个新按钮拖动到画布上。 请注意，当您拖动时，会出现蓝色虚线。 这些线用来做向导：

![](http://upload-images.jianshu.io/upload_images/2648731-9938cf4bf70e8ac5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将控件拖动到在屏幕边缘以及中心的时候，都会有向导线出现：

![](http://upload-images.jianshu.io/upload_images/2648731-33d4c005354c567a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果之前你已经使用过Interface Builder，那么你肯定看到过这些向导线。这些对我们对齐控件有很大的帮助。

在启用自动布局的Xcode 4中，指南有不同的用途。你仍然使用它们来对齐，但他们也告诉你新的约束会在哪里。如果你将button沿着向导线反方向拖拽到左上角时，Xcode 4中的storyboard会是这样的：

![](http://upload-images.jianshu.io/upload_images/2648731-dd91e27c1edc5bb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有两个蓝色的东西附属在button上面。这些T-bar形状的对象便是约束了。在Xcode 4的Interface Builder中不管你将UI控制器放在哪儿，它总是会给出有效的约束。理论上这听起来是个好主意，但是实际上，在Interface Builder中使用自动布局却非常困难。

幸运的是，在Xcode 5中使用的情况变好了。当你将按钮放到画布上后，没有任何T形条可见了：


![](http://upload-images.jianshu.io/upload_images/2648731-d509131594ecbc77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意到**Document Outline**窗格中也没有“约束”部分。结论就是：这个按钮没有设置任何约束。

但是这又是如何奏效的呢？你刚刚也有了解到，Auto Layout总是需要足够的约束来确定所有视图的大小和位置，但在这里你根本没有设置任何约束。很明显这难道不是一个不完整的布局吗？

这就是Xcode 5比Xcode 4有所提升的地方：它不再强迫你总是去设置一个有效的布局。

> 注意：
> 
> * 运行带有无效布局的应用程序是一个坏主意，因为自动布局无法正确计算视图应该在哪里。 要么视图的位置将是不可预测的（如果没有足够的约束）或是应用程序将会崩溃（如果有太多的约束）。
> * Xcode 4试图通过确保总是有足够的约束来创建有效的布局来防止这种情况的发生。 不幸的是，它通常通过删除你自己的约束并用你实际上不想要的约束来替换它们。 这是非常令人沮丧的，这也是许多开发人员放弃使用Auto Layout的原因。
Xcode5中，当你编辑Storyboard时它允许你有不完整的布局，但它也会指出哪些地方你还需要修改。使用Interface Builder创建的自动布局驱动用户界面变得更有趣了，使用Xcode5也消耗更少的时间。
> * 如果你根本不提供任何约束，Xcode自动分配一套默认的约束，正是我们所知的**automatic constraints**(自动约束)。它会在程序built的编译时间中去完成这些事，而不是设计时间。当你设计你的用户界面时，Xcode5中的自动布局为了不参与你的设计方法而努力工作，这这是我们喜欢它的原因。


自动约束为你的视图提供一个固定尺寸和位置。换句话说，视图总是拥有跟你在storyboard中看到的一样的坐标。这是非常方便的，因为这就意味着你可以大量的忽视自动布局。你可以为那些拥有充分约束的控件不增加约束，只为那些需要特殊规则的视图创建约束。

好吧，让我们来使用一下约束，看看他们能做些什么。现在，按钮位于左上角并且没有约束。请确保按钮是与两个角导轨对齐的。

使用**Editor\Pin**菜单为按钮增加两个新的约束，看起来像这样：

![](http://upload-images.jianshu.io/upload_images/2648731-2bce0e726ca3f648.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果你还没有猜到，这就是**Leading Space to Superview** 和 **Top Space to Superview** 选项。

所有的约束也列在Interface Builder窗口左侧的Document Outline窗格中：

![](http://upload-images.jianshu.io/upload_images/2648731-1393992ffb98a156.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

目前有两个约束，即按钮和主视图的左边缘之间的水平距离，以及按钮和主视图的顶部边缘之间的垂直距离。 描述这些约束表达的关系就是：

该按钮始终位于其父视图中左上角的20点位置。

> 注意：这些实际上并不是非常有用的约束，因为它们与自动约束所做的事相同。 如果你总是希望你的按钮位于它父视图的左上角，那么你可能不需要提供任何约束，你可以让Xcode帮你完成。

现在拖动button并将它放到屏幕的右上角，再次和蓝色向导线对齐：

![](http://upload-images.jianshu.io/upload_images/2648731-8d9bc5ccfaff4ec6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

哇，这里发生了什么？在Xcode 4中，这将破坏旧的约束并基于蓝色引导线分配新的约束，但在Xcode 5中，按钮保留现有的约束。这里的问题是，【Interface Builder中按钮的大小和位置】与【Auto Layout中基于约束所设置的大小和位置】不一致。我们称之为 **misplaced view**(错位视图)。

运行APP。该按钮仍将显示在屏幕的左上角：

![](http://upload-images.jianshu.io/upload_images/2648731-c187e81a028df06a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于Auto Layout来说，橙色代表着问题所在。 Interface Builder绘制了两个橙色框：一个带有虚线的边框，一个带有实线的边框。 虚线框展示的是根据Auto Layout约束显示的视图边框。 实心橙色框是根据你对视图的拖动显示在屏幕中的边框。 这两个边框应该是匹配一致的，但在这里他们并不匹配。

如何解决这个问题取决于你想实现什么：

* 如果你想要将按钮放在屏幕的左边，而左边边缘距离拖放的视图234点位置， 在这种情况下，你已经使现有视图的水平空间约束增加了234pt。 这就是橙色线条用“+234”的意思。
* 如果你要将按钮放在屏幕的右边，那么，你需要删除现有的约束并创建一个新的约束。

删除水平空间约束。首先在画布或 **Document Outline** 中选择它，然后按键盘上的**Delete**键。

![](http://upload-images.jianshu.io/upload_images/2648731-9d76eeb34268db7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

请注意，这步操作会将垂直空间约束变为橙色，而刚才它是蓝色的。这个特定的约束并没有错误;它只是意味着没有足够的约束来确定按钮的完整位置。你仍然需要为X位置添加约束。

> 注意：你可能想知道为什么Xcode不为X位置添加一个自动约束。 规则是：只有当你没有人为设置任何约束时，Xcode才会创建自动约束。而一旦只要你添加了一个约束，这就意味着你告诉Xcode，这个视图的约束由你管理，不需要Xcode插手来自动添加。因此，Xcode将不再进行任何自动约束，并希望你添加此视图所需要的任何其他约束。

选中按钮，选择 **Editor\Pin\Trailing Space to Superview**。 这个步骤会在按钮的右边缘和屏幕的右边缘之间产生新的约束。 该约束表达的关系是：   
“这个按钮总是位于它的superview的右上角20px。”   
运行APP并旋转模拟器到横屏状态。 请注意查看按钮与右边屏幕边缘的距离是否始终相同：

![](http://upload-images.jianshu.io/upload_images/2648731-e184db0c51d4d032.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当你将按钮（或任何其他视图）参照引导并设置约束时，你将获得一个由Apple的iOS人机交互指南 （即**HIG**）定义的标准尺寸的间距约束。 对于边框周围的边缘距离，标准大小为20px的空间。

现在，将按钮向左拖动一点：

![](http://upload-images.jianshu.io/upload_images/2648731-61fd30ee489fdde3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同样你又得到了一个橙色的虚线框，因为视图产生了**misplaced view**(错位视图)。如果说这个新的按钮位置确实是你想要的。这是常见的约束设置，只要微调视图的几个像素，橙色框就会出现。解决该问题的一种方法是删除旧约束并创建一个新的约束，但有一个更简单的解决方案。

**Editor**菜单有一个**Resolve Auto Layout Issues**子菜单。 从该菜单中，选择**Update Constraints**更新约束。在我的这种情况下，这就告诉了Interface Builder，它的约束需要增大64px，如下所示：

![](http://upload-images.jianshu.io/upload_images/2648731-0bef699b21d4109e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

太棒了，T条又变成蓝色并且布局也是有效的。在**Document Outline**（文档大纲）中，你可以看到水平空间约束不再有标准空间的那个约束了：

![](http://upload-images.jianshu.io/upload_images/2648731-1de131aa6d4626dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

到目前为止，你已经尝试使用了水平空间和垂直空间约束。 还有一个“中心”约束。将一个新的Button对象拖动到画布的底部中心，以便它与指引位置相同：

![](http://upload-images.jianshu.io/upload_images/2648731-9151869a263bf602.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了使按钮始终相对于父视图居中对齐，在水平轴上，你需要添加 Center X Alignment（中心对齐）约束。 从Editor菜单中选择**Align\Horizontal Center in Container**。 这个步骤会增加了一条长的橙色线条：
![](http://upload-images.jianshu.io/upload_images/2648731-b42ce1fe7e781f0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该线是橙色的，因为你只指定了按钮的X坐标，而没有指明其Y坐标发生了什么。 使用Editor \ Pin菜单在视图的按钮和底部之间添加垂直空间约束。 它应该看起来像这样：

![](http://upload-images.jianshu.io/upload_images/2648731-75e441178e1b8bfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果你不知道如何操作，它是**Bottom Space to Superview**选项。 垂直空间约束使按钮远离视图的底部（再次使用标准边距20px）。

运行APP并将其旋转到横屏状态。 即使在横屏模式下，按钮仍停留在屏幕的底部中心：
![](http://upload-images.jianshu.io/upload_images/2648731-ecffc2a2d06a96f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这就是你所表达的意图：“这个按钮应该始终在底部中心。”注意，你并没有告诉Interface Builder该按钮的坐标是什么，你只是希望它被锚定在视图底部中心。

 有了 **Auto Layout**，你不再需要关心在画布上放置视图的位置的确切坐标或它们的大小。相反，**Auto Layout** 会根据你设置的约束中导出这两个东西。

您可以在按钮的Size inspector（大小检视器）中看到这种模式转换，现在已经非常不同了：

![](http://upload-images.jianshu.io/upload_images/2648731-7bdc17850fbeeb0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

禁用自动布局后，输入X，Y，宽度或高度字段将更改所选视图的位置和大小。 启用自动布局后，您仍然可以在这些字段中键入新值，但是如果您已经在视图上设置了约束，它现在可能会放错位置。 您还必须更新约束以使其与新值匹配。

例如，将按钮的宽度值更改为100.画布变成这样：

![](http://upload-images.jianshu.io/upload_images/2648731-8288ee0173af52ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Xcode 4 会用水平空间的新约束替换掉中心X对齐约束，强制它具有100点的宽度。然而，Xcode 5简单地说，“如果你想要的宽度是100点，对我来说无所谓，但是你要知道约束并不是这么说的。

在这种情况下，你希望按钮是100点宽。 对此有一种特殊类型的约束：**the Fixed Width constraint**（固定宽度约束）。 首先按撤消，使按钮再次居中，T条全部为蓝色。 选择按钮并选择**Editor \ Pin \ Width**。 这会在按钮下方放置一个新的T形条：

![](http://upload-images.jianshu.io/upload_images/2648731-890ef4e4d6c02f20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择该T条，并在**Attributes inspector**（属性检视器）中将常数更改为100.这将强制按钮总是100点宽，无论它的标题多大或小。 要看到这一点，你可以给按钮一个背景颜色：

![](http://upload-images.jianshu.io/upload_images/2648731-a52cbcea41364e93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

您还可以在左侧的Document Outline中看到此新宽度约束：

![](http://upload-images.jianshu.io/upload_images/2648731-ab041d340078e94c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

与按钮及其父视图之间的其他约束不同，宽度约束仅适用于按钮本身。 你可以认为这是按钮和...按钮之间的约束。

你可能想知道为什么按钮之前没有宽度约束。 Auto Layout如何知道按钮有多宽？

这里有一条就是：按钮本身是知道它自己必须有多宽的。 它是基于其标题文本和一些填充来计算的。 如果你在按钮上设置了一个背景图片，它也会考虑到这一点。

这称之为**intrinsic content size（固有内容大小）**。 不是所有的控件都有这个，但很多（UILabel是另一个例子）。 如果视图可以计算自己的应有大小，那么您不需要在为其设置特定的宽度或高度约束。 以后你会看到更多的。

![](http://upload-images.jianshu.io/upload_images/2648731-4ed8bec5c49ccf3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

要将按钮恢复到最佳大小，请先删除宽度约束。 然后选择按钮，从编辑器菜单中选择**Size to Fit Content**（大小适应内容）。这将恢复按钮的固有内容大小。

# 孤掌难鸣

引导线不仅仅呈现视图及其父视图之间的关系，而且也呈现视图层次结构的同一级别的视图之间。 为了演示这一点，将一个新按钮拖到画布上。 如果将此按钮拖动到其他按钮附近，则它们的引导线也会开始交互。

将新按钮放在现有按钮旁边，使其卡入到位：

![](http://upload-images.jianshu.io/upload_images/2648731-b743cc13ad5727b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里有很多虚线的引导线。 Interface Builder认为这两个按钮可以以不同的方式排列 - 在它们的顶部，中心和基线。
Xcode 4将把这些贴紧引导下中的一个转换成新的约束。 但是使用Xcode 5，如果你想在这两个按钮之间有一个约束，你必须自己做。 您已经看到，您可以使用**Editor \ Pin**菜单在两个视图之间创建约束，但也有一种更简单的方法。
选择新的按钮，然后**Ctrl-drag**到另一个按钮，像这样：

![](http://upload-images.jianshu.io/upload_images/2648731-ad8eec6c9faf83fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当您松开鼠标按钮时，会出现一个弹出窗口。 选择第一个选项，**Horizontal Spacing**（水平间距）。

![](http://upload-images.jianshu.io/upload_images/2648731-9127fdd7e02b531a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

它会创建一个像这样的新约束：

![](http://upload-images.jianshu.io/upload_images/2648731-71fdc5b6d8aa34f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

它是橙色的，意味着此按钮需要至少一个其他约束。 按钮的大小是已知的 - 它使用固有内容大小确定 - 并且按钮的X位置已经有一个约束了。 只留下Y位置没有约束。

这里缺少的约束很容易确定，但对于更复杂的设计，它可能不会总是立即显而易见的。 幸运的是，你不必猜测。 Xcode会一直保持计算，可以准确地告诉你缺少什么。
在文档大纲中有一个小的红色箭头，在**View Controller Scene**旁边。 单击该箭头以查看所有自动布局问题的列表：

![](http://upload-images.jianshu.io/upload_images/2648731-766aae86dedda18e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

真贴心！让我们添加那个缺少Y位置的约束吧。选择新按钮向下拖动：

![](http://upload-images.jianshu.io/upload_images/2648731-59b26064860bbe9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时弹出菜单有不同的选项。此菜单中的项目取决于上下文（您在其间拖动的视图）和移动鼠标的方向。 选择**Bottom Space to Bottom Layout**（底部空间到底部布局）。

新按钮现在有一个到屏幕底部的垂直空间，还有一个水平空间，它与其他按钮链接。 因为这个空间很小（只有8分），T条可能有点难以看到，但它绝对存在。

单击文档大纲中的**Horizontal Space (8)**约束将其选中：
![](http://upload-images.jianshu.io/upload_images/2648731-c9e4f149baab9b31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当选择约束时，它会点亮其所属的控件。 这个特定约束位于两个按钮之间。 你在这里做的是说：

“第二个按钮总是出现在第一个按钮的左边，无论第一个按钮位于何处或有多大。

选择黄色的按钮，并在其标签中键入一些长的“A longer label”。 完成后，按钮会调整大小，为新文本腾出空间，而另一个按钮则会移动。毕竟，它被附加到第一个按钮的左边缘，所以这是你打算发生的事情：

![](http://upload-images.jianshu.io/upload_images/2648731-63ff2f6eef7b9c8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

只是为了获得【这是如何工作的】更好感觉，再试一次。 将另一个按钮拖动到画布中，并将其放在黄色的上方，以便它们垂直卡入到位（但不要尝试对齐两个按钮的左边缘）：

![](http://upload-images.jianshu.io/upload_images/2648731-0b7db0ee45d776b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

给新按钮设置一个背景颜色（绿色），这样你可以更容易地看到它的范围。

因为你把两个按钮扣在一起，根据HIG的推荐现在它们之间有一个标准的8pt空间。 通过在两个按钮之间按住Ctrl键并拖动，将其转换为约束。 从弹出菜单中选择**Vertical Spacing**（垂直间距）。

> 注意：“HIG”是iOS人机交互指南的缩写，包含Apple关于设计良好用户界面的建议。 这是任何iOS开发人员的必须要阅读的。 HIG解释了哪些UI元素适合在哪些情况下使用，以及使用它们的最佳做法。 您可以在[此处](https://developer.apple.com/ios/human-interface-guidelines/overview/design-principles/)找到此文档。

但是，您并不受限于控件之间的标准间距。 约束是完整的对象，就像视图一样，因此它们具有可以更改的属性。

选择两个按钮之间的垂直空间约束。 你可以通过点击T条在画布中做到这一点，虽然这往往是有点过于挑剔 到目前为止，最简单的方法是单击文档大纲中的约束。 一旦选择它，切换到**Attributes inspector**（属性检查器）：

![](http://upload-images.jianshu.io/upload_images/2648731-c5836645ae2797bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在Constant字段中输入40以更改约束的大小。现在两个按钮分开了，但它们仍然连接：

![](http://upload-images.jianshu.io/upload_images/2648731-53790e91214e5947.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行应用程序并翻转到横屏模式以查看效果：

![](http://upload-images.jianshu.io/upload_images/2648731-3d2779bec6566a0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

按钮当然仍然保持着垂直排列，但他们不是在同一个水平位置！ 原因应该很明显：绿色按钮还没有X位置的约束。

从绿色按钮向画布的左边缘添加水平空间约束将无法解决此问题。 有了这样的约束，绿色按钮总是保持相同的X坐标，即使在横屏模式下也同样。 这看起来不是很好，所以你要表达以下意图：

“黄色按钮将始终水平居中，并且绿色按钮的左边缘要和黄色按钮的左边缘对齐。

您已经有第一个约束了，但还没有第二个的约束。 Interface Builder有显示对齐的指引线，因此您可以拖动顶部按钮，直到其左边缘与黄色按钮对齐：

![](http://upload-images.jianshu.io/upload_images/2648731-f23e94351701e62c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果您也拖动了按钮的垂直距离，按钮的边框和垂直空间约束可能不在正确的距离下。 您将在T形条上看到橙色标识：

![](http://upload-images.jianshu.io/upload_images/2648731-ea10bad262b32f7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果发生这种情况，只需使用方向键将按钮推入到位，直到标识消失。

最后，在两个按钮之间按住Ctrl键并拖动，然后从弹出菜单中选择**Left**。 这创建了一个对齐约束，它的意思是说：“这两个视图的左边缘总是对齐的”。 换句话说，两个按钮将总是具有完全相同的X位置。 这步解决了布局问题，同时T条转为蓝色：

![](http://upload-images.jianshu.io/upload_images/2648731-08a8f775f8c516ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行APP，并旋转到横屏模式以验证其是否有效：
![](http://upload-images.jianshu.io/upload_images/2648731-3acad973c3d3fe4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 接下来做什么？

现在你已经对 **Auto Layout** 有了第一次尝试，你喜欢它吗？ 适应它可能还要花些功夫，但可以使你的生活很容易，你的应用程序也会更灵活！

想了解更多？ 继续阅读本[part 2 of this Auto Layout tutorial](https://www.raywenderlich.com/50319/beginning-auto-layout-tutorial-in-ios-7-part-2)，您将继续使用Interface Builder中的按钮来更好地了解**Auto Layout**提供的功能以及您可能遇到的问题。最好就是 - 你也将使用Auto Layout 在你真正的APP中创建一个真正的布局。

在此期间，如果您有任何问题或意见，请加入下面的论坛讨论！


### 参考文章

* [iOS AutoLayout与AutoSizing：自动布局，想说爱你真的好难](http://blog.lessfun.com/blog/2014/10/17/ios-autolayout-vs-autosizing/)
* [从此爱上iOS Autolayout](https://segmentfault.com/a/1190000000646452)
* [使用 Auto Layout 的典型痛点和技巧](http://www.jianshu.com/p/0f031606e5f2)
* [代码手写UI，xib和StoryBoard间的博弈，以及Interface Builder的一些小技巧](https://onevcat.com/2013/12/code-vs-xib-vs-storyboard/)
* [iOS AutoLayout 进阶: Aspect Ratio](https://www.jianshu.com/p/b3fb77f95ec8)
