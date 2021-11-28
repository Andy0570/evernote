### 导读

> 该Demo展示了一个可展开折叠的TableView，类似于QQ好友分组列表，我参考了@zhenwenming 写的博客，通过研读，在实现原理不变的前提下，少放了几个不必要的对象，自己写了一个改进版的Demo。

### 对比差异

#### 参考的Demo：

* 博客： [UITableView的折叠收缩和QQ好友分组效果 @zhenwenming](http://blog.csdn.net/wenmingzheng/article/details/50483502) 
* [GitHub源码](https://github.com/zhengwenming/ExpandTableView)  

* 博主的Demo：

 ![](http://upload-images.jianshu.io/upload_images/2648731-dbe3d41f260b6fec?imageMogr2/auto-orient/strip)

* 从 **视图层次**上看   
 通过分析代码，可以发现，该 **ExpandTableView** 折叠收缩的实现方式是：   
 * **分组名**：section 1 ——> 显示在 **UITableView对象** 的 **Header View** 上；
 * **Header View** 上 ——> 用code画了一个 **UIButton**
 * **UIButton** 上 ——> 用code分别画了 2个**UIImageView** 显示左右两边的图片，一个**UILabel**显示文字，底部又放了一张 **Line** 的图片显示分割线
 * **每组可展开/收缩的内容**：section 11/12/13 ——> 显示在 **UITableView对象** 的 **UITableViewCell** 上；然后**UITableViewCell**又是用Xib重新绘制了。
 * 另外，我发现，该**ExpandTableView**中列表视图的父视图是基于**UIViewController**的，**TableView**也是code画上去的，而我自己创建的时候就直接把父视图设置为**UITableViewController**,这样就不用手动创建**TableView**了。

![](http://upload-images.jianshu.io/upload_images/2648731-e95237787f785317.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 模仿实现

![](http://upload-images.jianshu.io/upload_images/2648731-5dc149365ebf4e45.gif?imageMogr2/auto-orient/strip)

* **视图层次**上的优化点,
 * **UIButton** 上,通过设置 **UIButton** 自带的 **ImageView** 和 **Label** 属性设置UI，通过设置两者的偏移量让其水平左右显示。
 * **Header View** 底部的分割线也没有通过放图片来设置，而是通过设置 **UIButton** 的背景为灰色，设置 **Footer View** 的高度为1，巧妙的显示了分割线。

![](http://upload-images.jianshu.io/upload_images/2648731-b1ea2bef831007e6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 实现原理
该可展开折叠的 **TableView** 通过 **Header View** 上的 **Button** 点击来实现 ，而每组的展开/折叠状态用1/0标记并存放在Flag数组中，每按一下**Button**，就修改一下Flag值，然后重新加载**section**,就实现分组展开收缩的动作了。
更多细节先参考源码吧： [GitHub源码](https://github.com/Andy0570/HQLTableViewDemo)

### 参考文章
* [TableView的cell折叠和展开](http://www.jianshu.com/p/9eed61eb6a64)
