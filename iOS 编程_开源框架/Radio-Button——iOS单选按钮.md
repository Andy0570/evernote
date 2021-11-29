* GitHub源码: [RadioButton](https://github.com/onegray/RadioButton-ios)
* star: 200+

>                                 ⭐️⭐️⭐️ 
以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

# Radio Button for iOS——iOS单选按钮
## README.md文档描述

继承自 **UIButton** 的一个非常简单的类，扩展了标准的 **UIButton** 的功能。

可以为每个按钮配置默认和选中状态。

[图片上传失败...(image-49831d-1587702980658)]

### 非常容易使用
它不需要任何 central manager。 只需在Interface Builder中直接链接按钮：



[图片上传失败...(image-9a7c7a-1587702980658)]


或者，使用单行代码对按钮进行分组：

	radio1.groupButtons = @[radio1, radio2, radio3];


设置任意按钮为选中状态，同一组中的所有其他按钮将自动取消选中状态：

	radio2.selected = YES; // radio1 and radio3 become deselected


组中的任何一个按钮都知道哪一个被选中了：

	RadioButton* r1 = radio1.selectedButton;
	RadioButton* r2 = radio2.selectedButton;
	RadioButton* r3 = radio3.selectedButton;
	NSAssert (r1==r2 && r2==r3, @"Must be equal");

另一个通过标签选择按钮的好方法：

	[radio1 setSelectedWithTag:kTagRadio3];


### 资源
[sample.zip](https://github.com/onegray/RadioButton-ios/archive/sample.zip) - Sample app  
[radio-res-ios.zip](https://raw.github.com/onegray/RadioButton-ios/data/radio-res-ios.zip) - Radio Button images used in the sample  

### 许可
RadioButton 基于 MIT许可协议，—你可以基于你的意愿fork、覆盖和使用



## 实践使用

参照官方Demo，学习使用 **RadioButton**。

首先需要导入RadioButton.h 和 RadioButton.m 文件；

其次还需要两张图片用于显示按钮选中（check）和未选中（unchecked）状态。



### 1. 一组中有三个单选按钮

[图片上传失败...(image-b0c222-1587702980658)]

1. 首先在**Interface Builder**界面中拖入3个 **UIButton** 控件、1个 **UILabel** 控件；

![](http://upload-images.jianshu.io/upload_images/2648731-403dcda2a78bfd18.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 选中第一个 **UIButton** 控件，修改 **Identity Inspector** 工具栏中的**class**属性为：**Radio Button**；

![](http://upload-images.jianshu.io/upload_images/2648731-4ce3464529fe9eec.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 还是在选中第一个 **UIButton** 控件下，切换到 **Attributes Inspector** 工具栏中修改相关属性如下：

其中你需要分开设置 **Button**按钮在不同状态下显示的图片（蓝框所示）：

* **Default **默认状态下设置的 **Image** 是：unchecked.png;
* **Selected**选中状态下设置的 **Image** 是：checked.png;

另外勾选设置第一个 **Button** 按钮的初始化状态是默认选中的（绿框所示）。

![](http://upload-images.jianshu.io/upload_images/2648731-c40dd4cfb485c823.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)![](http://upload-images.jianshu.io/upload_images/2648731-125da448b6bb9bd8.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 剩下两个 **Button** 按钮也相同设置，需要区分设置的是Title标题、另外两个按钮都不用勾选默认选中状态，设置完成后应该是这样的：

   ![](http://upload-images.jianshu.io/upload_images/2648731-ac67df9338a7c4c4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5. 设置群组，在 **Radio Button1** 上鼠标右击，找到 **groupButtons** 属性，长按➕连接到 **Radio Button2** ，再如法炮制，将  **Radio Button2** 的 **groupButtons **连接到 **Radio Button3**；

![](http://upload-images.jianshu.io/upload_images/2648731-e691dba3d37afd62.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6. 修改 **UILabel** 控件的默认**Text**属性显示为：Radio Button1，因为Radio Button1是默认选中的，所以默认文本也应该显示这个：

7. 关联到代码：

   * 在**viewController.h** 文件中先声明**Radio Button**类;

   ````
   @class RadioButton;
   ````

   * 关联以上几个控件属性；

   ```
   @property (strong, nonatomic) IBOutlet RadioButton *radioButton1;
   @property (strong, nonatomic) IBOutlet RadioButton *radioButton2;
   @property (strong, nonatomic) IBOutlet RadioButton *radioButton3;
   @property (strong, nonatomic) IBOutlet UILabel *statusLabel;
   ```

   * 在 **viewController.m** 文件中导入``import "RadioButton.h"``

8. 将3个**Radio Button**按钮同时关联到一个方法上：

   ```
   - (IBAction)onRadioBtn:(RadioButton *)sender {
       _statusLabel.text = [NSString stringWithFormat:@"%@",sender.titleLabel.text];
   }
   ```

9. 构建运行。



### 2.一组中有两个按钮

![](http://upload-images.jianshu.io/upload_images/2648731-c01f7bcf46683b30.gif?imageMogr2/auto-orient/strip)

该例与1的类似，就不详细说了，只是【切换】是一个标准的 **UIButton** 按钮，该按钮关联的方法如下：

```
- (IBAction)radioBtnInvert:(UIButton *)sender {
    _man.selected = !_man.selected;
}
```



### 3.代码创建方法

![](http://upload-images.jianshu.io/upload_images/2648731-1c581d4fbfbfa4b8.gif?imageMogr2/auto-orient/strip)

点击按钮创建一个包含三个按钮的群组

代码方法：

```
- (IBAction)createMoreBtn:(UIButton *)sender {
    
    [sender setHidden:YES];
    
    NSMutableArray* buttonsArray = [NSMutableArray arrayWithCapacity:3];
    CGRect btnRect = CGRectMake(25, 320, 100, 30);
    for (NSString* optionTitle in @[@"Red", @"Green", @"Blue"]) {
        RadioButton* btn = [[RadioButton alloc] initWithFrame:btnRect];
        [btn addTarget:self action:@selector(onRadioButtonValueChanged:) forControlEvents:UIControlEventValueChanged];
        btnRect.origin.y += 40;
        [btn setTitle:optionTitle forState:UIControlStateNormal];
        [btn setTitleColor:[UIColor darkGrayColor] forState:UIControlStateNormal];
        btn.titleLabel.font = [UIFont boldSystemFontOfSize:17];
        [btn setImage:[UIImage imageNamed:@"unchecked.png"] forState:UIControlStateNormal];
        [btn setImage:[UIImage imageNamed:@"checked.png"] forState:UIControlStateSelected];
        btn.contentHorizontalAlignment = UIControlContentHorizontalAlignmentLeft;
        btn.titleEdgeInsets = UIEdgeInsetsMake(0, 6, 0, 0);
        [self.view addSubview:btn];
        [buttonsArray addObject:btn];
    }
    
    [buttonsArray[0] setGroupButtons:buttonsArray]; // 把按钮放进群组中
    
    [buttonsArray[0] setSelected:YES]; // 初始化第一个按钮为选中状态

}
```

### 其他

* [IOS单选框RadioButton实现](http://www.jianshu.com/p/4971424c693b)
* [iOS开发复选框类库SSCheckBoxView](http://blog.csdn.net/duxinfeng2010/article/details/9071003)
* [GitHub:**SSCheckBoxView**](https://github.com/ardalahmet/SSCheckBoxView)
* [GitHub:**BEMCheckBox**](https://github.com/Boris-Em/BEMCheckBox#sample-app)
