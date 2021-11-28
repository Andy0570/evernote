# UINavigationController

**UINavigationController** 类实现了一个用于管理分层内容导航的专用视图控制器。 该导航界面可以有效地呈现您的数据，并使用户更容易浏览该内容。 您通常按照原样使用此类，但您也可以使用子类来自定义类的行为。



## 预览

导航界面呈现的屏幕通常会模拟您的数据层次结构。 在层次结构的每个级别中，您提供一个适当的屏幕（由自定义视图控制器管理）来显示该级别的内容。 图1显示了iOS模拟器中“设置”应用程序提供的导航界面的示例。 第一个屏幕向用户显示包含首选项的应用程序列表。 选择应用程序会显示该应用程序的各个设置和设置组。 选择组会产生更多设置等等。 除了根视图之外，导航控制器提供一个后退按钮，以允许用户返回到上一层次结构。

![图 1](http://upload-images.jianshu.io/upload_images/2648731-f1934c0b4c9f6515.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



导航控制器对象使用（由视图控制器数组表示的）导航堆栈（**navigation stack**）管理当前显示的屏幕。 数组中的第一个视图控制器是根视图控制器。 数组中的最后一个视图控制器是当前正在显示的视图控制器。 您可以使用 segue 或使用此类中的方法从堆栈中添加和删除视图控制器。 用户还可以使用导航栏中的后退按钮或使用左侧滑动手势来移除最上面的视图控制器。

导航控制器管理界面顶部的导航栏和界面底部的可选工具栏。 导航栏始终存在，并由导航控制器本身管理，导航控制器本身使用导航堆栈上的视图控制器提供的内容来更新导航栏。 当 `toolbarHidden` 属性为 `NO` 时，导航控制器将以最上层视图控制器提供的内容类似地更新工具栏。

导航控制器使用委托（delegate）对象协调其行为。 委托对象可以覆盖视图控制器的推送或弹出，提供自定义的转换动画，并指定导航界面的首选方向。 您提供的委托对象必须遵守 `UINavigationControllerDelegate` 协议。

图2显示了导航控制器与其管理对象之间的关系。 使用导航控制器的指定属性访问这些对象。

![](http://upload-images.jianshu.io/upload_images/2648731-1f290d905bab1415.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 导航控制器视图

导航控制器是一个容器视图控制器——也就是说，它将其他视图控制器的内容嵌入到其中。 您可以从其 `view` 属性访问导航控制器的视图。 此视图包含导航栏，可选的工具栏以及与最上层视图控制器对应的内容视图。 图3显示了如何组合这些视图来呈现整个导航界面。 （在该图中，导航界面进一步嵌入到 tab bar 界面中。）虽然导航栏和工具栏视图的内容发生变化，但视图本身不会。 实际更改的唯一视图是导航堆栈上最上层视图控制器提供的自定义内容视图。

![](http://upload-images.jianshu.io/upload_images/2648731-dfce2ad0b97f6cfb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 注意
>
> 在iOS 7及更高版本中，因为内容视图在导航栏下方，您必须在设计视图控制器内容时考虑该空间。

导航控制器管理导航栏和可选导航工具栏的创建，配置和显示。 允许自定义导航栏的外观相关属性，但是千万不要直接更改其 `frame`，`bounds`或 `alpha`值。 如果您将 **UINavigationBar** 子类化，则必须使用`initWithNavigationBarClass：toolbarClass：` 方法初始化导航控制器。 要隐藏或显示导航栏，请使用`navigationBarHidden` 属性或 `setNavigationBarHidden：animated：` 方法。

导航控制器使用与导航堆栈上的视图控制器相关联的导航项目对象（**UINavigationItem** 类的实例）动态构建导航栏的内容。 要定制导航栏的整体外观，请使用 **UIAppearance** API。 要更改导航栏的内容，您必须配置自定义视图控制器的导航项。 有关导航项的更多信息，请参阅 **UINavigationItem**。



## 更新导航栏（Navigation Bar）

每次顶层的视图控制器更改时，导航控制器会相应地更新导航栏。 具体地，导航控制器更新显示在三个导航栏位置中的每一个的栏按钮项：左，中，右。 按钮项是 **UIBarButtonItem** 类的实例。 您可以使用自定义内容创建按钮项，或根据需要创建标准系统按钮项。

导航栏的 **tint** 由导航栏本身的属性控制。 使用 `tintColor` 属性更改导航栏中 Item 的颜色，并使用 `barTintColor` 属性来更改导航栏本身的颜色。 导航栏不会从当前显示的视图控制器中继承其 **tintColor**。

有关导航栏的更多信息，请参阅 **UINavigationBar**。 有关如何创建条形按钮项的更多信息，请参阅 **UIBarButtonItem**。



### The Left Item

除了导航堆栈上的根视图控制器之外，导航栏左侧的 Item 可以导航返回到上一个视图控制器。 这个最左边按钮的内容确定如下：

* 如果新创建的最顶层视图控制器具有自定义的左栏按钮Item，则显示该Item。 要指定自定义左栏按钮，请设置视图控制器导航项目的 `leftBarButtonItem`  属性。
* 如果最顶层视图控制器没有自定义左栏按钮项，但是上一个视图控件的导航项在其 `backBarButtonItem` 属性中有一个对象，导航栏将显示该项。
* 如果任何一个视图控制器都没有指定自定义导航栏栏按钮项，则使用默认的后退按钮，并将该按钮的标题设置为上一个视图控制器的 `title` 属性的值，即视图控制器下一级堆栈。 （如果导航堆栈中只有一个视图控制器，则不显示后退按钮。）

> 注释
>
> 假设通过VC1`push`VC2，那么如果设置`VC1.navigationItem.backBarButtonItem`就会显示在VC2的左上角返回按钮；如果再设置`VC2.navigationItem.leftBarButtonItem`则会覆盖VC1的设置；如果VC1和VC2都没有设置，则会显示默认的`backBarButtonItem`，它的标题是VC1的`title`。

> 注意
>
> 如果后退按钮的标题太长而不能放在可用空间中，则导航栏可以将字符串“后退”替换实际的按钮标题。 只有后退按钮由前一个视图控制器提供时，导航栏才会执行此操作。 就算新的顶级视图控制器自定义了左栏返回按钮项 - 该导航项的 `leftBarButtonItem` 或 `leftBarButtonItems` 属性中的对象 - 导航栏也不会更改按钮标题。



### The Middle Item

导航控制器更新导航栏的中间项，如下所示：

* 如果新的顶级视图控制器具有自定义标题视图，则导航栏将显示该视图以替代默认标题视图。 要指定自定义标题视图，请设置视图控制器导航项的 `titleView` 属性。
* 如果没有设置自定义标题视图，则导航栏将显示包含视图控制器默认标题的标签。 该标签的字符串通常从视图控制器本身的 `title` 属性获取。 如果要显示与视图控制器相关的标题不同的标题，请改为设置视图控制器导航项的标题属性。



### The Right Item

导航控制器更新导航栏的右侧项，如下所示：

* 如果新的顶级视图控制器具有自定义右键按钮项目，则显示该项目。 要指定自定义右键按钮项目，请设置视图控制器导航项目的 `rightBarButtonItem` 属性。
* 如果没有指定自定义的右栏按钮项目，则导航栏在该栏的右侧不显示。



## 展示Toolbar

导航控制器对象在其视图层次结构中管理可选工具栏（Toolbar）。显示时，此工具栏从活动视图控制器的`toolbarItems` 属性中获取其当前的一组项目。当前视图控制器更改时，导航控制器更新工具栏项目以匹配新的视图控制器，在适当时将新项目动画化。

导航工具栏默认隐藏，但您可以通过调用导航控制器对象的 `setToolbarHidden：animated：`方法来显示导航界面。如果不是所有的视图控制器都支持工具栏项，您的委托对象可以调用此方法在随后的推送和弹出操作期间切换工具栏的可见性。要使用自定义 **UIToolbar** 子类，请使用 `initWithNavigationBarClass：toolbarClass：`方法初始化导航控制器。如果您使用自定义工具栏和导航栏子类来创建导航控制器，请注意，在将导航控制器显示在屏幕上之前，请注意您需要按下并设置视图控制器。



## 适应不同环境

导航界面在水平紧凑和水平正常的环境中保持不变。 当在两个环境之间切换时，只有导航控制器视图的大小发生变化。 导航控制器不会更改其视图层次结构或其视图的布局。

当在导航堆栈上配置视图控制器之间的 **segues** 时，标准的 **Show** 和 **Show Detail** 行为如下：

* Show segue—导航控制器将指定的视图控制器 push 入其导航堆栈。
* Show Detail segue—导航控制器以模态方式呈现指定的视图控制器。

其他 segues类型的行为不变。



## Interface Behaviors

导航控制器为其界面支持以下行为：

* **Supported interface orientations** （支持的接口方向） - 确定支持的接口方向时，导航控制器对象不会在其导航堆栈上查看视图控制器。 在iPhone上，导航控制器支持除肖像上下颠倒之外的所有方向。 在iPad上，导航控制器支持所有方向。 如果导航控制器具有委托对象，则委托可以使用`navigationControllerSupportedInterfaceOrientations：`方法指定一组不同的支持方向。
* **Presentation context** (演示文稿上下文) - 导航控制器定义了模态呈现视图控制器的呈现上下文。 当模态转换样式为 `UIModalPresentationCurrentContext` 或 `UIModalPresentationOverCurrentContext` 时，导航堆栈中的视图控制器的模态呈现覆盖整个导航界面。



## 保存状态

在iOS 6及更高版本中，如果为此视图控制器的 `restoreIdentifier` 属性分配值，则会尝试在其导航堆栈上保留子视图控制器。 导航控制器从堆栈底部开始向上移动，对每个具有有效恢复标识符字符串的视图控制器进行编码。 在下一个启动周期中，导航控制器将保留的视图控制器按照保留的顺序恢复到导航堆栈。

推送到导航堆栈的子视图控制器可能使用相同的恢复标识符。 导航控制器自动存储附加信息，以确保每个孩子的恢复路径是唯一的。



### 参考
* [UINavigationController与UINavigationBar详解](http://www.jianshu.com/p/b2ae4d211499)
* [iOS导航控制器——UINavigationController使用详解 @时间已静止](http://www.jianshu.com/p/319cbc53f0ba)
*  [UINavigationBar]( http://www.jianshu.com/p/f0d3df54baa6)
* [UINavigationController 视图控制器 @独木舟的木](http://www.jianshu.com/p/9a7f7c8ac766)
