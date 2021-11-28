* GitHub地址：[Colours](https://github.com/bennyguitar/Colours)
* star: 2800+

> **Colours** 颜色库包含一套漂亮的预定义颜色集和一系列颜色方法，可以简化颜色搭配相关的开发工作，使你的 iOS / OSX 开发更轻松。



![](https://github.com/bennyguitar/Colours/raw/master/Screenshots/banner.png)



> 下文是我对官方 **READ.ME** 文档的翻译。



## 安装

把 **Colours.h** 和 **Colours.m** 文件拖进你的工程里,在需要的地方引入头文件即可:

  ```objective-c
#import "Colours.h"
  ```

**CocoaPods**


  ```objective-c
pod 'Colours'
  ```

或者 **Swift** 版本的：

```objective-c
pod 'Colours/Swift'
```

**NSColor**

Colours 支持 **NSColor** ,只需要将 **AppKit** 框架导入到项目中（对于新的应用程序是这样的），然后你就可以使用了。**UIColor** 和 **NSColor** 的方法是一样的。**NSColor**是用于OS X 桌面应用开发，**UIColor** 是用于iOS 手机应用开发。

**Swift**

Colours 的 Swift 版本现在基本包含了 Objective-C 版本的一切除了以下内容：

* 颜色组件 **Dcitionary**（使用 tuples 代替）
* 排序/比较颜色
* 颜色值之间的距离

并且，tuples 也用来代替了颜色组件的字典和数组。因此，你将使用`someRedColor.rgba()`方法代替`[someRedColor rgbaArray]`方法，该方法给你了包含四个 `CFFloats`类型的元值，例如`(1.0, 0.0, 0.0, 1.0)`。想要只得到一个红色值，你应该这样写`someRedColor.rgba().r`。



## 目录
* 调色板
* 使用预定义的颜色
* 颜色辅助方法
  * 十六进制字符串
  * RGBA
  * HSBA
  * CIELAB
  * CMYK
  * 颜色组件
  * 调暗／调亮组件
  * 黑色或白色对比色
  * 互补颜色
* 两个颜色值之间的距离
* 生成颜色方案
* Android
* Xamarin
* Reap What I Sow!

## 调色板

一系列预定义颜色。

参考：[Colours](https://github.com/bennyguitar/Colours)




## 使用预定义的颜色

Colours 被设置成和使用 Apple 预设的系统颜色方法一样使用。例如，想要得到鲜红色，输入`[UIColor redColor]`.无需太多变化，虽然没有使用`colorWithRed:green:blue:alpha:`方法并且定制出该颜色来。于是，我自由地创建了100种颜色，使用的方式与Apple在iOS中使用的相同。所以，想要替代早期的红色颜色例子，只需要替换成上面的巨型调色板中的一种颜色，就像`[UIColor indigoColor]`。


## 颜色辅助方法

除了不遗余力地给你一大堆颜色的列表，这个类别还给你提供了一些允许不同颜色操作和转换的方法。 以下是你如何使用这些的方法：

#### 十六进制字符串（HexString）

你可以使用以下方法将常用的十六进制颜色字符串（啊哈，谢谢CSS）转换为 **UIColor**，反之亦然。

```objective-c
UIColor *newColor = [UIColor colorFromHexString:@"#f587e4"];
NSString *hexString = [newColor hexString];
```

#### RGBA

**UIColor 的RGBA数组**

color - > array 方法创建一个表示RGBA颜色值的4个 **NSNumbers** 的数组。 这些不在0-255范围内，而是在0-1范围内归一化。 所以如果你的R是230，那么它将在数组中表示为0.902。

```objective-c
NSArray *colorArray = [[UIColor seafoamColor] rgbaArray];
UIColor *newColor = [UIColor colorFromRGBAArray:colorArray];
```

**UIColor 的RGBA字典**

类似于上面的数组方法，它返回一个包含 **NSNumbers** 的 **NSDictionary** 字典。 静态键用于访问字典中的不同颜色组件。 这允许你使用返回的字典更快地自动更正。

* `kColoursRGBA_R`
* `kColoursRGBA_G`
* `kColoursRGBA_B`
* `kColoursRGBA_A`

```objective-c
NSDictionary *colorDict = [[UIColor seafoamColor] rgbaDictionary];
UIColor *newColor = [UIColor colorFromRGBADictionary:colorDict];

// 你也可以像这样获得一个单一的组件
NSNumber *r = colorDict[kColoursRGBA_R];
```

#### HSBA

像上面的 RGBA 方法一样，您还可以从 **UIColor** 获取色调（Hue），饱和度（Saturation）和亮度值（Brightness），并从中创建 数组 或 字典，反之亦然。 返回的 colorDictionary 也使用静态键，如此方法的RGBA 版本。 以下是使用的：

* `kColoursHSBA_H`
* `kColoursHSBA_S`
* `kColoursHSBA_B`
* `kColoursHSBA_A`

```objective-c
NSArray *colorArray = [[UIColor seafoamColor] hsbaArray];
NSDictionary *colorDict = [[UIColor seafoamColor] hsbaDictionary];

UIColor *newColor1 = [UIColor colorFromHSBAArray:colorArray];
UIColor *newColor2 = [UIColor colorFromHSBADictionary:colorDictionary];
```

#### CIELAB

像上述 RGBA 方法一样，您还可以从 **UIColor** 获取CIE_Lightness，CIE_a和CIE_b值，并从中创建数组或字典，反之亦然。 返回的 colorDictionary 也使用静态键，如此方法的 RGBA 版本。 以下是使用的：

* `kColoursCIE_L`
* `kColoursCIE_A`
* `kColoursCIE_B`
* `kColoursCIE_alpha`

```objective-c
NSArray *colorArray = [[UIColor seafoamColor] CIE_LabArray];
NSDictionary *colorDict = [[UIColor seafoamColor] CIE_LabDictionary];

UIColor *newColor1 = [UIColor colorFromCIE_LabArray:colorArray];
UIColor *newColor2 = [UIColor colorFromCIE_LabDictionary:colorDictionary];
```

#### CMYK

像上面的 RGBA 方法一样，您也可以从 **UIColor** 获取CMYKY值，并从中创建一个数组或字典，反之亦然。 返回的colorDictionary 也使用静态键，如此方法的 RGBA 版本。 以下是使用的：

* `kColoursCMYK_C`
* `kColoursCMYK_M`
* `kColoursCMYK_Y`
* `kColoursCMYK_K`

```objective-c
NSArray *colorArray = [[UIColor seafoamColor] cmykArray];
NSDictionary *colorDict = [[UIColor seafoamColor] cmykDictionary];

UIColor *newColor1 = [UIColor colorFromCMYKArray:colorArray];
UIColor *newColor2 = [UIColor colorFromCMYKDictionary:colorDictionary];
```

#### 颜色组件

该方法从上方返回包含每个键（RGBA，HSBA，CIE_LAB，CMYK）的值的字典。 这意味着您可以从同一来源获取色调值和亮度值。 以下是您如何使用此功能：

```objective-c
NSDictionary *components = [someColor colorComponents];
CGFloat H = components[kColoursHSBA_H];
CGFloat L = components[kColoursCIE_L];
```

您还可以通过在 **UIColor** 上调用以下任何方法来检索单个值而不是整个字典。 获得一种颜色的所有值，而不是仅检索一个颜色，这将显着更慢。 如果需要多个，请从上面调用特定的数组或字典方法。

```objective-c
CGFloat R = [[UIColor tomatoColor] red];
CGFloat G = [[UIColor tomatoColor] green];
CGFloat B = [[UIColor tomatoColor] blue];
CGFloat H = [[UIColor tomatoColor] hue];
CGFloat S = [[UIColor tomatoColor] saturation];
CGFloat B = [[UIColor tomatoColor] brightness];
CGFloat CIE_L = [[UIColor tomatoColor] CIE_Lightness];
CGFloat CIE_A = [[UIColor tomatoColor] CIE_a];
CGFloat CIE_B = [[UIColor tomatoColor] CIE_b];
CGFloat alpha = [[UIColor tomatoColor] alpha];
```

#### 调暗／调亮组件

你可以使用这些方法使颜色变暗或变亮。 唯一的参数是从0 - > 1的百分比浮点数，因此25％较浅的颜色将使用参数0.25。

```objc
UIColor *lighterColor = [[UIColor seafoamColor] lighten:0.25f];
UIColor *darkerColor = [[UIColor seafoamColor] darken:0.25f];
```

#### 黑色或白色对比色

很多时候，您可能希望将文本放置在某种颜色的视图之上，并且您希望确保它在外观上看起来很好。 使用这种方法，您将返回白色或黑色，具体取决于它们之间的对比度。 以下是您如何使用此功能：

```objc
UIColor *contrastingColor = [[UIColor seafoamColor] blackOrWhiteContrastingColor];
```

#### 互补颜色

This method will create a UIColor instance that is the exact opposite color from another UIColor on the color wheel. The same saturation and brightness are preserved, just the hue is changed.

该方法将创建一个在色值上与另一个 **UIColor** 完全相反的颜色的 **UIColor** 实例。 相同的饱和度和亮度被保留，只是色调被改变。

```objc
UIColor *complementary = [[UIColor seafoamColor] complementaryColor];
```

## 两个颜色值之间的距离

`5.1.0 +`

Detecting a difference in two colors is not as trivial as it sounds. One's first instinct is to go for a difference in RGB values, leaving you with a sum of the differences of each point. It looks great! Until you actually start comparing colors. Why do these two reds have a different distance than these two blues *in real life* vs computationally? Human visual perception is next in the line of things between a color and your brain. Some colors are just perceived to have larger variants inside of their respective areas than others, so we need a way to model this human variable to colors. Enter CIELAB. This color formulation is supposed to be this model. So now we need to standardize a unit of distance between any two colors that works independent of how humans visually perceive that distance. Enter CIE76,94,2000. These are methods that use user-tested data and other mathematically and statistically significant correlations to output this info. You can read the wiki articles below to get a better understanding historically of how we moved to newer and better color distance formulas, and what their respective pros/cons are.

**找出距离**

```objc
CGFloat distance = [someColor distanceFromColor:someOtherColor type:ColorDistanceCIE94];
BOOL isNoticablySimilar = distance < threshold;
```

**引用**

* [Color Difference](http://en.wikipedia.org/wiki/Color_difference)
* [Just Noticeable Difference](http://en.wikipedia.org/wiki/Just_noticeable_difference)
* [CIELAB Specification](http://en.wikipedia.org/wiki/CIELAB)

## 生成颜色方案

您可以使用以下方法创建一个基于 **UIColor** 的5色方案。 它包含一个 **UIColor** 和Color中定义的 **ColorScheme** 类型之一。 它返回一个4个新的UIColor对象的 **NSArray** 数组来创建一个相当不错的配色方案，以补充您传入的根颜色。

```objc
NSArray *colorScheme = [color colorSchemeOfType:ColorSchemeType];
```

**ColorSchemeTypes 颜色方案类型**

* ColorSchemeAnalagous —— 类似颜色方案
* ColorSchemeMonochromatic —— 单色颜色方案
* ColorSchemeTriad —— 三色方案
* ColorSchemeComplementary —— 互补配色方案

以下是基于`[UIColor seafoamColor]`的颜色方案开始的不同示例:

![seafoamColor](https://camo.githubusercontent.com/a425f8b01a4610c5d94e4f1be3eeed08aaabbcaa/68747470733a2f2f7261772e6769746875622e636f6d2f62656e6e796775697461722f436f6c6f7572732d666f722d694f532f6d61737465722f53637265656e73686f74732f436f6c6f72732f33352e706e67)

**ColorSchemeAnalagous**

![Analagous](https://raw.github.com/bennyguitar/Colours-for-iOS/master/Screenshots/analagous.png)

**ColorSchemeMonochromatic**

![Monochromatic](https://raw.github.com/bennyguitar/Colours-for-iOS/master/Screenshots/monochromatic.png)

**ColorSchemeTriad**

![Triad](https://raw.github.com/bennyguitar/Colours-for-iOS/master/Screenshots/triad.png)

**ColorSchemeComplementary**

![Complementary](https://raw.github.com/bennyguitar/Colours-for-iOS/master/Screenshots/complementary.png)

## Android

My friend, [Matt York](https://github.com/MatthewYork) ported a version of this repository over to Android, so you can use these exact same colors and color methods in your Android apps as well. You can find it here: [Colours for Android](https://github.com/MatthewYork/Colours).

## Xamarin

[akamud](https://github.com/akamud/) has graciously ported this library as a Xamarin Android component, which can be found at [https://github.com/akamud/Colours](https://github.com/akamud/Colours). An iOS Xamarin component is in the works as well.

Reap What I Sow!
================

This project is distributed under the standard MIT License. Please use this and twist it in whatever fashion you wish - and recommend any cool changes to help the code.


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/bennyguitar/colours/trend.png)](https://bitdeli.com/free "Bitdeli Badge")





# 参考

* [Colours颜色库：包含100种预定义的颜色和方法@伯乐在线](http://hao.jobbole.com/colours-ios/)
