* GitHub地址：[Wonderful](https://github.com/dsxNiubility/Wonderful)
* star: 700+

>                                 ⭐️⭐️⭐️ 
以下内容来源于官方源码、 README 文档、测试 Demo或个人使用总结 ！

# Wonderful

[![](https://img.shields.io/badge/platform-iOS-red.svg)](https://developer.apple.com/iphone/index.action)
[![](http://img.shields.io/badge/language-OC-yellow.svg?style=flat
)](https://en.wikipedia.org/wiki/Objective-C)
[![](https://img.shields.io/badge/license-MIT-blue.svg)](http://mit-license.org)
![](https://img.shields.io/badge/pod-v1.2.1-brightgreen.svg)

	一个关于色彩的库

![image](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/1000.png)





#### ChangeLog 1.2.1

1.2.1版本修复了：

有marquee或是headline组件的页面，后台回到前台偶现错乱的问题

有marquee或是headline组件的页面，停留在当前页面，锁屏解锁偶现错乱的问题

允许marquee在运行中修改文字



####Demo程序截图

建议使用iPhone6运行Demo程序
![Drawing](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/1001.png)<br />

---
####一、UIColor+Wonderful
1.这个分类里扩充了更多的颜色扩展，以后随手写个小Demo再也不需要redColor buleColor了。

	self.view.backgroundColor = [UIColor khakiColor];
	self.view.backgroundColor = [UIColor chocolateColor];

2.平均每个色系有10种颜色，不仅可以使用名称直接敲出，还能使用颜色阶梯的宏敲出，在你想不起词的时候更加方便。 宏从1～10是颜色渐深，可以根据自己的感觉使用浅一级的宏或深一级的宏。<br />

	self.view.backgroundColor = Wonderful_YelloeColor4;
	self.view.backgroundColor = Wonderful_BrownColor4;
<br />
![Drawing](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/1003.png)
![Drawing](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/1005.png)<br />

---
####二、UIColor+Separate
1.提供颜色分离方案，可以将任何颜色的rgb喝alpha的值取出。<br />

	UIColor *testC = [UIColor salmonColor];
	float r= [testC red];
	float g= [testC green];
	float b= [testC blue];
	float alpha= [testC alpha];
	NSLog(@"******  %f,%f,%f,%f",r,g,b,alpha);

2.可以通过一个颜色算出此颜色的反色，使得背景无论被用户设置成什么色，文字颜色都是背景的反色。<br />

	self.showLbl1.backgroundColor = [UIColor peachRed];
	self.showLbl1.textColor = [[UIColor peachRed]reverseColor];

3.也可以直接打印这个颜色的各项详细数值。<br />

	[[UIColor salmonColor]printDetail];
	// 打印结果
	This Color's Red:250, Green:128, Blue:114, Alpha:1
	decimal red:0.9804 green:0.5020 blue:0.4471
	Hexadecimal 0xfa8072

4.提供了颜色微调方案，可以让一个已知颜色的rgb的某值上升或下降若干，可用于不管背景是什么色，边框都比背景深20。 也可以将认可颜色的详细值打印出来。<br />

	UIColor *navColor = [[UIColor redColor]up:SXColorTypeBlue num:30]; // 在红色上把蓝色色值提高30
	UIColor *barColor = [[UIColor blueColor]up:1 num:140]; // 可以直接用枚举对应的tag
	UIColor *bgColor = [[UIColor blackColor]down:SXColorTypeAlpha num:10]; // 取一个比黑色稍微淡的颜色
	UIColor *lineColor = [bgColor up:3 num:20]; // 不管背景是什么颜色 线都比背景浅20.

![image](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/003.gif)<br />

---
####三、SXColorGradientView
1.颜色渐变的view，可以设置任何颜色到透明的过渡。<br />

	SXColorGradientView *grv1 = [SXColorGradientView createWithColor:[UIColor paleGreen] frame:CGRectMake(10, 10, 80, 30) visible:YES direction:SXColorGradientToRight];

2.也可以设置两个颜色相互过渡。<br />

	SXColorGradientView *grv3 = [SXColorGradientView createWithFromColor:[UIColor peruColor] toColor:[UIColor ghostWhite] frame:CGRectMake(10, 50, 80, 30) direction:SXColorGradientToRight];

3.可以设置向上下左右四个过渡的方向。<br />

	typedef NS_ENUM(NSInteger, SXColorGradientDirection) {
		SXColorGradientToTop = 1,
		SXColorGradientToLeft = 2,
		SXColorGradientToBottom = 3,
		SXColorGradientToRight = 4,
	};

4.支持增加传入一个数组，然后搭建一个多个颜色过渡的view。<br />
![Drawing](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/1006.png)<br />

---
####四、SXMarquee
1.实现任何样式的跑马灯。<br />
跑马灯方法：
```
- (instancetype)initWithFrame:(CGRect)frame
                        speed:(SXMarqueeSpeedLevel)speed
                          Msg:(NSString *)msg
                      bgColor:(UIColor *)bgColor
                     txtColor:(UIColor *)txtColor;

// 点击动作，默认点击停止
- (void)changeTapMarqueeAction:(void(^)())action;

// 改变字体大小
- (void)changeMarqueeLabelFont:(UIFont *)font;

- (void)start;

- (void)stop;

- (void)restart;

```

Demo:

    SXMarquee *mar = [[SXMarquee alloc] initWithFrame:CGRectMake(20, 255, 335, 35)
                                                speed:4
                                                  Msg:@"重大活动，天猫的双十一，然而并没卵用"
                                              bgColor:[UIColor salmonColor]
                                             txtColor:[UIColor whiteColor]];
    // 默认字体：[UIFont fontWithName:@"HelveticaNeue" size:14.0f]
    [mar changeMarqueeLabelFont:[UIFont systemFontOfSize:26]];
    // 添加点击事件,源码实现方式：在该视图上添加了一个按钮
    [mar changeTapMarqueeAction:^{
        UIAlertView *alert = [[UIAlertView alloc]initWithTitle:@"点击事件" message:@"可以设置弹窗，当然也能设置别的" delegate:self cancelButtonTitle:@"取消" otherButtonTitles:@"确定", nil];
        [alert show];
    }];
    [mar start];

2.跑马灯的背景可以设置任何颜色，这个是基于颜色过渡view做的。<br />

3.跑马灯可以实现点击拖动，或者绑定更多点击事件。<br />


###### 📌 源码解析

```
/*
 // Label 的宽度是根据字符串长度计算而来
 UIFont *fnt = [UIFont fontWithName:@"HelveticaNeue" size:14.0f];
 CGSize msgSize = [_marqueeLbl.text sizeWithAttributes:@{NSFontAttributeName:fnt}];
 */

#pragma mark - 核心动画
- (void)moveAction {
    // 以 Label 的中心点运动
    // 贝塞尔曲线 起点
    CGPoint fromPoint = CGPointMake(self.frame.size.width + self.marqueeLbl.frame.size.width/2, self.frame.size.height/2);
    // 贝塞尔曲线 终点
    CGPoint toPoint = CGPointMake(-self.marqueeLbl.frame.size.width/2, self.frame.size.height/2);
    
    self.marqueeLbl.center = fromPoint;
    UIBezierPath *movePath = [UIBezierPath bezierPath];
    [movePath moveToPoint:fromPoint];
    [movePath addLineToPoint:toPoint];
    
    // 创建动画实例
    CAKeyframeAnimation *moveAnim = [CAKeyframeAnimation animationWithKeyPath:@"position"];
    // 设置动画路径
    moveAnim.path = movePath.CGPath;
    // 完成后移除
    moveAnim.removedOnCompletion = YES;
    // 设置延迟
    moveAnim.duration = self.marqueeLbl.frame.size.width * self.speedLevel * 0.01;
    // 委托协议，动画完成后重复该动画
    moveAnim.delegate = self;
    // 在图层上添加动画
    [self.marqueeLbl.layer addAnimation:moveAnim forKey:nil];
}

#pragma mark - CAAnimationDelegate

- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag{
    if (flag) {
        [self moveAction];
    }
}
```
##### SXHeadLine

方法：
```
- (void)setBgColor:(UIColor *)bgColor
         textColor:(UIColor *)textColor
          textFont:(UIFont *)textFont;

// 设置时间间隔
- (void)setScrollDuration:(NSTimeInterval)scrollDuration
             stayDuration:(NSTimeInterval)stayDuration;

// 改变点击动作，默认点击停止
- (void)changeTapMarqueeAction:(actionBlock)action;

- (void)start;

- (void)stop;
```

Demo:
 
    SXHeadLine *headLine3 = [[SXHeadLine alloc]initWithFrame:CGRectMake(100, 530, 250, 30)];
    headLine3.messageArray = @[@"1、库里43分，勇士吊打骑士",@"2、伦纳德死亡缠绕詹姆斯，马刺大胜骑士",@"3、乐福致命失误，骑士惨遭5连败",@"4、五小阵容发威，雄鹿吊打骑士", @"5、天猫的双十一，然而并没卵用"];
    [headLine3 setBgColor:[UIColor whiteColor] textColor:[UIColor orangeRed] textFont:[UIFont systemFontOfSize:13]];
    [headLine3 setScrollDuration:0.5 stayDuration:3];
    headLine3.hasGradient = YES;

    [headLine3 changeTapMarqueeAction:^(NSInteger index) {
        
        NSLog(@"你点击了第 %ld 个button！内容：%@", index, headLine3.messageArray[index]);
        
    }];
    [headLine3 start];



![image](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/004.gif)<br />

####五、SXColorLabel
1.把text里重要的内容用特殊符号包起来，就会特殊显示<br />
	
2.如果用<>包起来，就会显示高亮颜色。如果用[]包起来，就会显示高亮的字体。

	lbl.text = @"例1：今天要记得通知<Peter>和<Robin>去开会。";
	lbl2.text = @"例2：礼物很有[粪]量，你会大吃一[斤]！";

3.这个高亮颜色和高亮字体可以自行设置。

    // 高亮颜色
    [SXColorLabel setAnotherColor:[UIColor salmonColor]];
    // 高亮字体大小
    [SXColorLabel setAnotherFont:[UIFont boldSystemFontOfSize:18]];

4.两者也可以混合使用。 高亮的字体里有高亮颜色。

    lbl3.text = @"例3：一定要：[通知<Peter>和<Robin>去开会]";

完整方法：

    SXColorLabel *sxlbl3 = [[SXColorLabel alloc]initWithFrame:CGRectMake(40, 200, 350, 30)];
    sxlbl3.anotherColor = [UIColor salmonColor];
    sxlbl3.anotherFont = [UIFont systemFontOfSize:14];
    sxlbl3.text = @"一定要：[通知<Peter>和<Robin>去开会]";
    [self.bodyView addSubview:sxlbl3];

![Drawing](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/1002.png)<br />

##### 📌 源码解析

在同一个 Label 中设置不同字体颜色的方法参考如下，源码中只是在字符串中放了标记符号<>、[]、遍历删除这些标记符号的同时将他们的范围、长度记录下来设置不同的字体和颜色。
```
- (UILabel *)titleLabel {
    if (!_titleLabel) {
        _titleLabel = [[UILabel alloc] initWithFrame:CGRectMake((MaxX(self.imageView)+10), MinY(self.imageView), 245, 40)];
        _titleLabel.numberOfLines = 2;
        _titleLabel.lineBreakMode = NSLineBreakByWordWrapping;
        _titleLabel.font = [UIFont systemFontOfSize:16];
        
        NSString *strPrice = [NSString stringWithFormat:@"%d元 - %d元", (int)self.data.minPrice, (int)self.data.maxPrice];
        NSString *strTitle = self.data.title;
        NSMutableAttributedString *string = [[NSMutableAttributedString alloc] initWithString:[NSString stringWithFormat:@"%@ %@", strTitle, strPrice]];
        [string setAttributes:@{NSForegroundColorAttributeName: COLOR_RGB(34, 34, 39)} range:NSMakeRange(0, strTitle.length)];
        [string setAttributes:@{NSForegroundColorAttributeName: COLOR_RGB(255, 77, 100)} range:NSMakeRange((strTitle.length+1), strPrice.length)];
        _titleLabel.attributedText = string;
    }

    return _titleLabel;
}
```
_旧版demo_<br />
_颜色列表_<br />
![image](https://github.com/dsxNiubility/Wonderful/raw/master/screenshots/002.gif)
