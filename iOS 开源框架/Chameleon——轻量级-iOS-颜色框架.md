* GitHub地址：[chameleon](https://github.com/ViccAlexander/Chameleon)
* star: 12000+



  ![Chameleon by Vicc Alexander](http://upload-images.jianshu.io/upload_images/2648731-c080748897b5bfc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Swift 3

To use the Swift 3 version, add this to your Podfile (until 2.2 or higher is released):
```ruby
pod 'ChameleonFramework/Swift', :git => 'https://github.com/ViccAlexander/Chameleon.git'
```



## Introduction 介绍

**Chameleon** is a lightweight, yet powerful, color framework for iOS (Objective-C & Swift). It is built on the idea that software applications should function effortlessly while simultaneously maintaining their beautiful interfaces.

With Chameleon, you can easily stop tinkering with RGB values, wasting hours figuring out the right color combinations to use in your app, and worrying about whether your text will be readable on the various background colors of your app. 

**Chameleon** 是一个轻量却功能强大的 iOS （支持 Objective-C 和 Swift）颜色框架软件。它基于的理念是：软件应用程序应该可以不费力气且同时保持多个漂亮的界面。 

使用 Chameleon ，你不再需要调配 RGB 值，不用浪费时间去找你的 APP 合适的颜色组合，也不用担心你 APP 的文字是否能在不同背景色上可读。



### Features 特性

  ![Features](http://upload-images.jianshu.io/upload_images/2648731-eaea8a8c44e846ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### App Showcase 应用程序展示

###### In an upcoming update we'll begin showcasing some of the best apps and companies making use of Chameleon. If you'd like to see your app featured in this section, make sure to add it [here](https://airtable.com/shrr1WK6dLQBZfXV0).

## Table of Contents 目录
[● Product Features](https://github.com/ViccAlexander/Chameleon#-product-features)  
[● Requirements](https://github.com/ViccAlexander/Chameleon#%EF%B8%8F-requirements)  
[● License](https://github.com/ViccAlexander/Chameleon#-license)  
[● Contributions](https://github.com/ViccAlexander/Chameleon#-contributions)   
[● Documentation](https://github.com/ViccAlexander/Chameleon#-documentation)  
[● Storyboard Add-On](https://github.com/ViccAlexander/Chameleon#storyboard-add-on)  
[● Author](https://github.com/ViccAlexander/Chameleon#-author)   
[● To Do List](https://github.com/ViccAlexander/Chameleon#-to-do-list)  
[● Change Log](https://github.com/ViccAlexander/Chameleon#-change-log)  

## 🌟 Product Features 特性

### 100% Flat & Gorgeous

Chameleon features over 24 hand-picked colors that come in both light and dark shades. 

**Chameleon** 拥有超过24种手工选择的颜色，可以设置浅色和深色色调。

  ![Swatches](http://upload-images.jianshu.io/upload_images/2648731-758b9db39d5e2421.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Flat Color Schemes 扁平颜色方案

Chameleon equips you with 3 different classes of flat color schemes that can be generated from a flat or non-flat color. *In the examples below, the white stars indicate the color used to generate the schemes.*

Chameleon 配备3种不同类型的平面颜色方案，可以从扁平或非扁平颜色生成。 *在下面的示例中，白色星星表示用于生成方案的颜色*。



###### Analogous Flat Color Scheme 类似扁平颜色方案


  ![Analogous Scheme](http://upload-images.jianshu.io/upload_images/2648731-0f42a0b2380a7994.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###### Complementary Flat Color Scheme 互补扁平颜色方案

  ![Complementary Scheme](http://upload-images.jianshu.io/upload_images/2648731-7bf5b646a3314b1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###### Triadic Flat Color Scheme 三元群扁平颜色方案

  ![Triadic Scheme](http://upload-images.jianshu.io/upload_images/2648731-94e30db60a7c5441.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Contrasting Text 对比文本
With a plethora of color choices available for text, it's difficult to choose one that all users will appreciate and be able to read. Whether you're in doubt of your text and tint color choices, or afraid to let users customize their profile colors because it may disturb the legibility or usability of the app, you no longer have to worry. With Chameleon, you can ensure that all text stands out independent of the background color.

Oh... Chameleon works with the status bar as well. : )

随着文字的色彩选择过多，很难选择所有用户将会欣赏并能够阅读的颜色。 无论您是否怀疑您的文字和色彩的颜色选择，或害怕让用户自定义自己的个人资料颜色，因为它可能会打扰应用程序的易读性或可用性，您不用担心。 使用 Chameleon，您可以确保所有文本独立于背景颜色。

哦，Chameleon 还可以与状态栏一起工作。 ：）

  ![Status Bar](http://upload-images.jianshu.io/upload_images/2648731-9c9df5c74a8c163c?imageMogr2/auto-orient/strip)


### Themes ![Beta](http://upload-images.jianshu.io/upload_images/2648731-826418f2ebe45b75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 主题

Chameleon now allows you easily theme your app with as little as **one line of code**. You can set a theme for all your views, and for specific views as well.


  ![Themes](http://upload-images.jianshu.io/upload_images/2648731-ea155f2fd3a808b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### Colors From Images 从图片中生成颜色 

Chameleon allows you to seamlessly extract non-flat or flat color schemes from images without hassle. You can also generate the average color from an image with ease. You can now mold the UI colors of a profile, or product based on an image!


  ![Colors from images](http://upload-images.jianshu.io/upload_images/2648731-f39ff74980c152cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Gradient Colors 渐变色
With iOS 7 & 8, Apple mainstreamed flat colors. Now, with the release of iOS 9, Chameleon strives to elevate the game once more. Say hello to gradient colors. Using one line of code, you can easily set any object's color properties to a gradient (background colors, text colors, tint colors, etc). Other features, like Chameleon's contrasting feature, can also be applied to create a seamless product. Experimentation is encouraged, and gutsiness is applauded!

随着iOS 7和8的发布，苹果推崇扁平色。 现在，随着iOS 9的发布，变色龙致力于提升游戏次数。 对渐变颜色问好。 使用一行代码，您可以轻松地将任何对象的颜色属性设置为渐变（背景颜色，文本颜色，色调颜色等）。 其他功能，如变色龙的对比功能，也可以应用于创建一个无缝的产品。 鼓励实验，鼓掌！

  ![Gradients](http://upload-images.jianshu.io/upload_images/2648731-4b352ad2a979c0a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](http://upload-images.jianshu.io/upload_images/2648731-00e449854161eaf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Xcode Quick Help Documentation Xcode 快速帮助文档

Chameleon's documentation, while written as clearly and concisely as possible may still render some slightly confused. But don't fret! Staying true to our vision of simplifying the entire color process, we added Xcode Quick Help's Documentation Support! Simply highlight a Chameleon method or tap it with three fingers to find out more about what it is and what it does!


  ![Xcode Quick Help Documentation](http://upload-images.jianshu.io/upload_images/2648731-59ba5d57a76e9355.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### Palettes 调色板

If you're like us and love to use storyboards, Chameleon's got you covered. We've provided you with a quick and easy way to access Chameleon colors right from Storyboard, and any other app that uses the color picker (i.e. TextEdit). In addition you can even import the palette directly into Photoshop Android Ske

如果你像我们一样喜欢使用 storyboards，变色龙的让你得到了保护。 我们为您提供了一个快速简单的方法来从storyboards 和任何其他使用颜色选择器（即TextEdit）的应用程序访问Chameleon颜色。 此外，您甚至可以将调色板直接导入 **Photoshop** 和 **Sketch** 。




  ![Chameleon Palette](http://upload-images.jianshu.io/upload_images/2648731-83518e00c70dcc56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



  ![Chameleon Palette](http://upload-images.jianshu.io/upload_images/2648731-a944a9fecbc42a29.gif?imageMogr2/auto-orient/strip)


## ⚠️ Requirements 最低配置需求

* Objective-C or Swift
* Requires a minimum of iOS 7.0 for Objective-C (No active development for anything earlier, but may work with 6.0) and a minimum of iOS 8.0 for Swift.
* Requires Xcode 6.3 for use in any iOS Project

## 🔑 License
Chameleon is released and distributed under the terms and conditions of the [MIT license](https://github.com/ViccAlexander/Chameleon/blob/master/LICENSE.md).

## 👥 Contributions
If you run into problems, please open up an issue. We also actively welcome pull requests. By contributing to Chameleon you agree that your contributions will be licensed under its MIT license.

If you use Chameleon in your app we would love to hear about it! Drop Vicc a line on [twitter](http://twitter.com/viccsmind).

## 📗 Documentation 文档
All methods, properties, and types available in Chameleon are documented below.   

##### Documentation Table of Contents  
[● Installation](https://github.com/ViccAlexander/Chameleon#installation)  
[● Palettes](https://github.com/ViccAlexander/Chameleon#palettes)  
[● Usage](https://github.com/ViccAlexander/Chameleon#usage)  
[● UIColor Methods](https://github.com/ViccAlexander/Chameleon#uicolor-methods)  
[● Colors From Images](https://github.com/ViccAlexander/Chameleon#colors-from-images)  
[● UIStatusBarStyle Methods](https://github.com/ViccAlexander/Chameleon#uistatusbarstyle-methods)  
[● Color Scheme Methods](https://github.com/ViccAlexander/Chameleon#color-schemes-methods)  
[● Theme Methods](https://github.com/ViccAlexander/Chameleon#theme-methods)  



### Installation 安装

###### Note: Swift 3 version maintained in a separate branch until it's release.

#### CocoaPods Installation
Chameleon is now available on [CocoaPods](http://cocoapods.org). Simply add the following to your project Podfile, and you'll be good to go.

###### Objective-C
```ruby
use_frameworks!

pod 'ChameleonFramework'
```
###### Swift
```ruby
use_frameworks!

pod 'ChameleonFramework/Swift'
```

=======
#### Carthage Installation
Add this to your Cartfile:
```ruby
github "ViccAlexander/Chameleon"
```

=======
#### Manual Installation
If you rather install this framework manually, just drag and drop the Chameleon folder into your project, and make sure you check the following boxes. Note: Don't forget to manually import the *QuartzCore* & *CoreGraphics* framework if you plan on using gradient colors!

<p align="center">
  ![Manual Installation](http://upload-images.jianshu.io/upload_images/2648731-6800ba32fe19819f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
</p>

If you're working with Swift and are manually installing Chameleon, there's an additional step. Make sure to download and drag the following file, [ChameleonShorthand.swift](https://github.com/ViccAlexander/Chameleon/blob/master/Pod/Classes/Swift/ChameleonShorthand.swift), into your project, and you'll be good to go.



### Palettes 调色板

#### Storyboard Add-On 添加到 Storyboard
Using Chameleon's awesome palette in Storyboard is easy! Simply download and install [Chameleon Palette](https://github.com/ViccAlexander/Chameleon/blob/master/Extras/Chameleon.dmg?raw=true).

Once installed, make sure to restart Xcode. You'll find all of Chameleon's colors in the Palette Color Picker whenever they're needed! :)


  ![Chameleon Palette](http://upload-images.jianshu.io/upload_images/2648731-fbc8d5402b3e5e6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



  ![Chameleon Palette](http://upload-images.jianshu.io/upload_images/2648731-a944a9fecbc42a29.gif?imageMogr2/auto-orient/strip)


#### Photoshop Add-On 添加到 Photoshop
Using Chameleon's awesome palette in Sketch is easy! Simply download and install [Photoshop Palette](https://github.com/ViccAlexander/Chameleon/blob/master/Extras/Chameleon_Photoshop.aco?raw=true).

#### Sketch Add-On 添加到 Sketch
Using Chameleon's awesome palette in Sketch is easy! Simply download and install [Sketch Palette](https://github.com/ViccAlexander/Chameleon/blob/master/Extras/Chameleon.sketchpalette?raw=true).



### Usage 使用

To use the myriad of features in Chameleon, include the following import:

要使用 **Chameleon** 中的无数功能，请导入：

###### If you installed Chameleon using CocoaPods:

###### Objective-C

``` objective-c
#import <ChameleonFramework/Chameleon.h>
```

###### Swift:
``` Swift
import ChameleonFramework
```

###### If you installed Chameleon using Carthage:

``` Swift
import Chameleon
```

###### If you installed Chameleon manually:
``` objective-c
#import "Chameleon.h"
```


### UIColor 方法

[● Flat Colors](https://github.com/ViccAlexander/Chameleon#flat-colors)   
[● Random Colors](https://github.com/ViccAlexander/Chameleon#random-colors)  
[● Complementary Colors](https://github.com/ViccAlexander/Chameleon#complementary-colors)  
[● Contrasting Colors](https://github.com/ViccAlexander/Chameleon#contrasting-colors)  
[● Flattening Non-Flat Colors](https://github.com/ViccAlexander/Chameleon#flattening-non-flat-colors)  
[● Gradient Colors](https://github.com/ViccAlexander/Chameleon#gradient-colors-1)   
[● Hex Colors](https://github.com/ViccAlexander/Chameleon#hex-colors-)  
[● Lighter & Darker Colors](https://github.com/ViccAlexander/Chameleon#lighter-and-darker-colors-)



#### Flat Colors 扁平色

Using a flat color is as easy as adding any other color in your app (if not easier). For example, to set a view's background property to a flat color with a dark shade, you simply have to do the following:

使用扁平色与在应用中添加任何其他颜色一样简单（如果不容易）。 例如，要将视图的背景属性设置为带有阴影的平面颜色，您只需执行以下操作：

##### Normal Convention: 正常语法

###### Objective-C
``` objective-c
self.view.backgroundColor = [UIColor flatGreenColorDark];
```
###### Swift
``` Swift
view.backgroundColor = UIColor.flatGreenDark
```

##### Chameleon Shorthand: 速写语法

###### Objective-C
``` objective-c
self.view.backgroundColor = FlatGreenDark;
```
###### Swift
``` Swift
view.backgroundColor = FlatGreenDark()
```

Setting the color for a light shade is the same, except without adding the *Dark* suffix. (By default, all colors without a *Dark* suffix are light shades). For example:

设置 light shade 颜色是一样的，除了不添加 *Dark* 后缀。 （默认情况下，所有没有 *Dark* 后缀的颜色都是浅色调）。 例如：

##### Normal Convention:
###### Objective-C
``` objective-c
self.view.backgroundColor = [UIColor flatGreenColor];
```
###### Swift
``` Swift
view.backgroundColor = UIColor.flatGreen
```

##### Chameleon Shorthand:

###### Objective-C
``` objective-c
self.view.backgroundColor = FlatGreen;
```
###### Swift
``` Swift
view.backgroundColor = FlatGreen()
```



#### Random Colors 随机色

There are four ways to generate a random flat color. If you have no preference as to whether you want a light shade or a dark shade, you can do the following:

有四种方法来生成随机扁平色。 如果您没有偏好是否需要遮光或暗色，可以执行以下操作：

##### Normal Convention:
###### Objective-C
``` objective-c
self.view.backgroundColor = [UIColor randomFlatColor];
```
###### Swift
``` Swift
view.backgroundColor = UIColor.randomFlat
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
self.view.backgroundColor = RandomFlatColor;
```

###### Swift
``` Swift
view.backgroundColor = RandomFlatColor()
```

Otherwise, you can perform the following method call to specify whether it should return either a light or dark shade:

否则，您可以执行以下方法调用以指定是否应返回亮或暗的阴影：

##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithRandomFlatColorOfShadeStyle:UIShadeStyleLight];
```

###### Swift
``` Swift
UIColor(randomFlatColorOfShadeStyle:.Light)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
RandomFlatColorWithShade(UIShadeStyleLight);
```
###### Swift
``` Swift
RandomFlatColorWithShade(.Light)
```

**UIShadeStyles:**  
- `UIShadeStyleLight` (`UIShadeStyle.Light` in Swift)
- `UIShadeStyleDark` (`UIShadeStyle.Dark` in Swift)



##### Choosing A Random Color From a List of Colors ![New](http://upload-images.jianshu.io/upload_images/2648731-d0300b1ffed0b18a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 从颜色列表中选择随机颜色

If you need to be a bit more selective and only display a random color from a set list of colors, you can use the following method:

如果您需要更有选择性，并且只能从设置的颜色列表中显示随机颜色，则可以使用以下方法：

##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithRandomColorInArray:@[FlatWhite, FlatRed, FlatBlue]];
```

###### Swift
``` Swift
TBA
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
RandomFlatColorInArray(@[FlatWhite, FlatRed, FlatBlue]) 
```
###### Swift
``` Swift
TBA
```



##### Choosing A Random Flat Color But Excluding A Few ![New](http://upload-images.jianshu.io/upload_images/2648731-d0300b1ffed0b18a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)选择随机扁平色，但不包括少量

Last but certainly not least, you can also choose form the list of random colors and exclude the ones you don't want. For example say you want to randomly select a flat color for a user's profile, but don't want to use any blacks, grays, or whites. You can simply do:

##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithRandomFlatColorExcludingColorsInArray:@[FlatBlack, FlatBlackDark, FlatGray, FlatGrayDark, FlatWhite, FlatWhiteDark]];
```

###### Swift
``` Swift
TBA
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
RandomFlatColorExcluding(@[FlatBlack, FlatBlackDark, FlatGray, FlatGrayDark, FlatWhite, FlatWhiteDark]) 
```
###### Swift
``` Swift
TBA
```



#### Complementary Colors 互补色

To generate a complementary color, perform the following method call, remembering to specify the color whose complement you want:

##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithComplementaryFlatColorOf:(UIColor *)someUIColor];
```

###### Swift
``` Swift
UIColor(complementaryFlatColorOf:someUIColor)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
ComplementaryFlatColorOf(color);
```

###### Swift
``` Swift
ComplementaryFlatColorOf(color)
```



#### Contrasting Colors 对比颜色

The contrasting color feature returns either a dark color a light color depending on what the Chameleon algorithm believes is a better choice. You can specify whether the dark or light colors are flat: *`([UIColor flatWhiteColor]` & `[UIColor flatBlackColorDark]`)* or non-flat *(`[UIColor whiteColor]` & `[UIColor blackColor]`).*

If you're trying to set a `UILabel's textColor` property, make sure you provide the `UILabel's backgroundColor`. If your label has a clear `backgroundColor`, just provide the `backgroundColor` property of the object directly behind the `UILabel`.

Here's an example:

##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithContrastingBlackOrWhiteColorOn:(UIColor *)backgroundColor isFlat:(BOOL)flat];
```

###### Swift
``` Swift
UIColor(contrastingBlackOrWhiteColorOn:UIColor!, isFlat:Bool)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
ContrastColor(backgroundColor, isFlat);
```

###### Swift
``` Swift
ContrastColor(backgroundColor, isFlat)
```



#### Flattening Non-Flat Colors 扁平的非平面颜色

As mentioned previously, this feature is unique to Chameleon. While this feature is in its early stages of operation and can be improved, it is accurate in finding the nearest flat version of any color in the spectrum, and very simple to use:

如前所述，这个功能是变色龙独有的。 虽然此功能处于早期操作阶段，可以改进，但是在找到最近的平面版本的任何颜色的频谱是准确的，并且使用非常简单：

##### Normal Convention:
###### Objective-C
``` objective-c
[(UIColor *)color flatten];
```

###### Swift
``` Swift
UIColor.pink.flatten()
```



#### Gradient Colors 渐变颜色

Using a gradient to color an object usually requires a couple of lines of code plus many more lines to superimpose smart contrasting text. Thankfully, Chameleon takes care of that for you. We've introduced a new way to have multicolored objects, and that's with gradients!

##### Gradient Styles 渐变样式
Chameleon provides three simple gradient styles. Gradients can be created from any number of colors you desire as long as at least two colors are provided. Don't forget that the contrasting text feature is also compatible with gradient colors!

**UIGradientStyles:**
* `UIGradientStyleLeftToRight` (UIGradientStyle.LeftToRight in Swift)
* `UIGradientStyleTopToBottom` (UIGradientStyle.TopToBottom in Swift)
* `UIGradientStyleRadial` (UIGradientStyle.Radial in Swift)

##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithGradientStyle:(UIGradientStyle)gradientStyle withFrame:(CGRect)frame andColors:(NSArray<UIColor *> *)colors];
```

###### Swift
``` Swift
UIColor(gradientStyle:UIGradientStyle, withFrame:CGRect, andColors:[UIColor])
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
GradientColor(gradientStyle, frame, colors);
```

###### Swift
``` Swift
GradientColor(gradientStyle, frame, colors)
```

**Objective-C Note**: If you use the Chameleon Shorthand, and use the `NSArray` literal ```@[]``` to set the array of colors, make sure you add parenthesis around it, or else you'll get an error.

Note: `UIGradientStyleRadial` only uses a maximum of 2 colors at the moment. So if more colors are provided, they will not show.

如果你使用Chameleon Shorthand 短语法，并使用 **NSArray** 的字面量语法```@[ ]```设置颜色数组，请确保在其周围添加圆括号，否则您将收到错误。



#### Hex Colors 十六进制颜色

One of the most requested features, *hex colors*, is now available. You can simply provide a hex string with or without a *#* sign:

##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithHexString:(NSString *)string];
```

###### Swift
``` Swift
UIColor(hexString:string)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
HexColor(hexString)
```

###### Swift
``` Swift
HexColor(hexString)
```


#### Hex Values ![New](http://upload-images.jianshu.io/upload_images/2648731-d0300b1ffed0b18a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 十六进制值

Retrieving the `hexValue` of a UIColor is just as easy.

检索 `UIColor` 的“十六进制值”同样简单。

###### Objective-C
``` objective-c
[FlatGreen hexValue]; //Returns @"2ecc71"
```

###### Swift
``` Swift
FlatGreen.hexValue //Returns @"2ecc71"
```



#### Lighter and Darker Colors 淡化和暗化颜色

Sometimes all you need is a color a shade lighter or a shade darker. Well for those rare, but crucial moments, Chameleon's got you covered. You can now lighten any color the following way:

##### Normal Convention:
###### Objective-C
``` objective-c
[color lightenByPercentage:(CGFloat)percentage];
```

###### Swift
``` Swift
color.lightenByPercentage(percentage: CGFloat)
```

You can also generate a darker version of a color:

##### Normal Convention:
###### Objective-C
``` objective-c
[color darkenByPercentage:(CGFloat)percentage];
```

###### Swift
``` Swift
color.darkenByPercentage(percentage: CGFloat)
```



### Colors From Images 从图片中提取颜色

Chameleon now supports the extraction of colors from images. You can either generate both flat and non-flat color schemes from an image, or easily extract the average color.

To generate a color scheme simply do the following:
##### Normal Convention:
###### Objective-C
``` objective-c
[NSArray arrayOfColorsFromImage:(UIImage *)image withFlatScheme:(BOOL)flatScheme];
```

###### Swift (**Array extension missing**)
``` Swift
NSArray(ofColorsFromImage: UIImage, withFlatScheme: Bool)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
ColorsFromImage(image, isFlatScheme)
```

###### Swift
``` Swift
ColorsFromImage(image, isFlatScheme)
```

To extract the average color from an image, you can also do:
##### Normal Convention:
###### Objective-C
``` objective-c
[UIColor colorWithAverageColorFromImage:(UIImage *)image];
```

###### Swift
``` Swift
UIColor(averageColorFromImage: UIImage)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
AverageColorFromImage(image)
```

###### Swift
``` Swift
AverageColorFromImage(image)
```



### UIStatusBarStyle Methods 状态栏样式方法

#### Contrasting UIStatusBarStyle
Many apps on the market, even the most popular ones, overlook this aspect of a beautiful app: the status bar style. Chameleon has done something no other framework has... it has created a new status bar style: `UIStatusBarStyleContrast`. Whether you have a `ViewController` embedded in a `NavigationController`, or not, you can do the following:

##### Normal Convention:
###### Objective-C
``` objective-c
[self setStatusBarStyle:UIStatusBarStyleContrast];
```

###### Swift
``` Swift
self.setStatusBarStyle(UIStatusBarStyleContrast)
```
###### **Note**: Make sure that the key *View controller-based status bar appearance* in **Info.plist** is set to `YES`.
参考：[iOS状态栏的管理](https://www.jianshu.com/p/006c2d663d2d)
![](http://upload-images.jianshu.io/upload_images/2648731-0d77101776d1faef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### Color Schemes Methods 颜色方案方法

###### **Note**: *Due to the limited number of flat colors currently available, color schemes may return results that reuse certain flat colors. Because of this redundancy, we have provided an option to return either a flat color scheme or a non-flat color scheme until more flat colors are added to the inventory.*

The initial color can be either a non-flat color or flat color. Chameleon will return an `NSArray` of 5 `UIColors` in which the original color will be the third object of the scheme. This allows for Chameleon to designate the colors of the color scheme (2 colors counter-clockwise and 2 clockwise from the initial color), and thus, the chosen colors are aligned specifically in that order. 



#### Analogous Color Scheme 类比颜色方案

An analogous color scheme uses three adjacent colors on the color wheel. According to Wikipedia, it’s best used with either warm or cool colors, creating a cohesive collection with certain temperature qualities as well as proper color harmony; however, this particular scheme lacks contrast and is less vibrant than complementary schemes. Within the scheme, choose one color to dominate and two to support. The remaining two colors should be used (along with black, white or gray) as accents.



#### Complementary Color Scheme 互补配色方案

A complementary color scheme uses opposite colors on the color wheel. To put into slightly more technical terms, they are two colors that, when combined, produce a neutral color. Complementary colors are tricky to use extensively, but work well when you want a point of emphasis. Complementary colors are generally not favorable to use for text. 



#### Triadic Color Scheme 三色配色方案

A triadic scheme uses evenly spaced colors on the color wheel. The colors tend to be richly vivid and offer a higher degree of contrast while, at the same time, retain color harmony. Let one color dominate and use the two others for accent.



#### Getting Colors in a Color Scheme 在颜色方案中获得颜色

To retrieve an array of colors, first make sure to initialize an NSMutableArray (in case you want to use the same array to replace with different colors later):

##### Normal Convention:
###### Objective-C
``` objective-c
NSMutableArray *colorArray = [NSMutableArray alloc] initWithArray:[NSArray arrayOfColorsWithColorScheme:(ColorScheme)colorScheme 
                                                                                                    with:(UIColor *)color 
                                                                                             flatScheme:(BOOL)isFlatScheme]];
```

###### Swift
``` Swift
var colorArray = NSArray(ofColorsWithColorScheme:ColorScheme, with:UIColor!, flatScheme:Bool)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
NSMutableArray *colorArray = [[NSMutableArray alloc] initWithArray:ColorScheme(colorSchemeType, color, isFlatScheme)];
```

###### Swift
``` Swift
var colorArray = ColorSchemeOf(colorSchemeType, color, isFlatScheme)
```

##### Example:
Assuming you want to generate an analogous color scheme for the light shade of Flat Red, perform the following method call:

##### Normal Convention:
###### Objective-C
``` objective-c
NSMutableArray *colorArray = [NSMutableArray alloc] initWithArray:[NSArray arrayOfColorsWithColorScheme:ColorSchemeAnalogous
                                                                                                    with:[UIColor flatRedColor] 
                                                                                             flatScheme:YES]];
```

###### Swift
``` Swift
var colorArray = NSArray(ofColorsWithColorScheme:ColorScheme.Analogous, with:UIColor.flatRed, flatScheme:true)
```

##### Chameleon Shorthand:
###### Objective-C
``` objective-c
NSMutableArray *colorArray = [[NSMutableArray alloc] initWithArray:ColorScheme(ColorSchemeAnalogous, FlatRed, YES)];
```

###### Swift
``` Swift
var colorArray = ColorSchemeOf(ColorScheme.Analogous, FlatRed(), true)
```

You can then retrieve each individual color the same way you would normally retrieve any object from an array:

###### Objective-C
```objective-c
UIColor *firstColor = colorArray[0];
```

###### Swift
``` Swift
var firstColor = colorArray[0] as! UIColor
```



### Theme Methods 主题色

With Chameleon, you can now specify a global color theme with simply one line of code (It even takes care of dealing with the status bar style as well)! Here's one of three methods to get you started. `ContentStyle` allows you to decide whether text and a few other elements should be white, black, or whichever contrasts more over any UI element's `backgroundColor`. 

使用变色龙，您现在可以使用简单的一行代码指定全局颜色主题（甚至可以处理状态栏样式）！ 这是启动您的三种方法之一。 `ContentStyle` 允许您决定文本和其他几个元素是否应该是白色，黑色或任何UI元素的 `backgroundColor` 之间的对比。

To set a global theme, you can do the following in your app delegate:

##### Normal Convention:
###### Objective-C
``` objective-c
[Chameleon setGlobalThemeUsingPrimaryColor:(UIColor *)color withContentStyle:(UIContentStyle)contentStyle];
```

But what if you want a different theme for a specific `UIViewController?` No problem, Chameleon allows you to override the global theme in any `UIViewController` and `UINavigationController`, by simply doing the following:

##### Normal Convention:
###### Objective-C
```objective-c
//This would go in the controller you specifically want to theme differently
[self setThemeUsingPrimaryColor:FlatMint withSecondaryColor:FlatBlue andContentStyle:UIContentStyleContrast];
```

###### **Note:** In order for the status bar style to automatically be set using a theme, you need to make sure that the *View controller-based status bar appearance* key in **Info.plist** is set to `NO`.



#### Navigation Bar Hairline 导航栏线

![No Hairline](http://upload-images.jianshu.io/upload_images/2648731-0f127777114dbd12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

As of `2.0.3` the navigation bar hairline view is no longer hidden by default. However, if you're seeking a true flat look (like the image above), you can hide the hairline at the bottom of the navigation bar by doing the following: 

###### Objective-C
```objective-c
[self.navigationController setHidesNavigationBarHairline:YES];

//or

self.navigationController.hidesNavigationBarHairline = YES;
```

###### Swift
```Swift
self.navigationController?.hidesNavigationBarHairline = true
```

## 👑 Author
Chameleon was developed by **Vicc Alexander** [(@ViccsMind)](https://twitter.com/viccsmind) in 2014 using Objective-C. In 2015, full Swift support was implemented by [@Bre7](https://github.com/bre7). Currently, it is being maintained by both [@ViccAlexander](https://github.com/ViccAlexander) and [@Bre7](https://github.com/bre7).

## 📝 To Do List 
* ~~CocoaPods Support~~ ![1.0.1](http://upload-images.jianshu.io/upload_images/2648731-49da681a0446b343.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Table of Contents~~ ![1.0.1](http://upload-images.jianshu.io/upload_images/2648731-49da681a0446b343.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Storyboard Color Picker Add-On~~ ![1.1.0](http://upload-images.jianshu.io/upload_images/2648731-28b91f266239e40e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Xcode In-App Documentation~~ ![1.1.0](http://upload-images.jianshu.io/upload_images/2648731-28b91f266239e40e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Switch from RGB values over to HSB and LAB~~ ![1.1.0](http://upload-images.jianshu.io/upload_images/2648731-28b91f266239e40e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Gradient Colors~~ ![1.1.0](http://upload-images.jianshu.io/upload_images/2648731-28b91f266239e40e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Update GradientStyle & ShadeStyle Syntax~~ ![1.1.1](http://upload-images.jianshu.io/upload_images/2648731-6fd1a044f9b29a41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Add Radial Gradient Support~~ ![1.1.1](http://upload-images.jianshu.io/upload_images/2648731-6fd1a044f9b29a41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Fix Swift Conflict with `initWithArray:for:flatScheme:` method~~  ![1.1.12](http://upload-images.jianshu.io/upload_images/2648731-103e0449c06ca392.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Swift Support~~ ![1.1.3](http://upload-images.jianshu.io/upload_images/2648731-c558e57d6e4d2690.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)     
* ~~Color Scheme From Images~~ ![2.0.0](http://upload-images.jianshu.io/upload_images/2648731-db5c8f6d432c1100.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~UIAppearance Convenience Methods~~  ![2.0.0](http://upload-images.jianshu.io/upload_images/2648731-db5c8f6d432c1100.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
* ~~Add option to hide `NavigationBar` hairline~~ ![2.0.3](http://upload-images.jianshu.io/upload_images/2648731-614b7b80a20c4440.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* ~~Add support for App Extensions hairline~~ ![2.2.0](http://upload-images.jianshu.io/upload_images/2648731-815b9c3af44c7824.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* Add Swift Support for Random Colors
* Allow Gradient Colors to Adapt To Frame Changes

## 📄 Change Log

### See [Changelog.md](https://github.com/ViccAlexander/Chameleon/blob/master/CHANGELOG.md) 👀tch.
