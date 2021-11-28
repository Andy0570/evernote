> 关于 **TextKit** 框架中的动态类型和字体描述符特性。


### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 12 :: Dynamic Type and Font Descriptors](http://blog.kingiol.com/2014/01/14/ios7-day-by-day-day12-dynamic-type-and-font-descriptors/)



### Dynamic Type - 动态类型

动态类型允许用户指定他们设备中应用程序内部的字体类型大小。

#### UIFontTextStyle 类型

```objective-c
// Font text styles
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleTitle1 NS_AVAILABLE_IOS(9_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleTitle2 NS_AVAILABLE_IOS(9_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleTitle3 NS_AVAILABLE_IOS(9_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleHeadline NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleSubheadline NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleBody NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleCallout NS_AVAILABLE_IOS(9_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleFootnote NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleCaption1 NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN UIFontTextStyle const UIFontTextStyleCaption2 NS_AVAILABLE_IOS(7_0);
```

#### Interface Builder 设置

![](http://upload-images.jianshu.io/upload_images/2648731-5a807907124c3e8b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 代码设置

```objective-c
// 设置子标签上的字体
self.subHeadingLabel.font = [UIFont preferredFontForTextStyle:UIFontTextStyleSubheadline];
```
#### 样式大小

当应用支持动态字体后，我们可以到【设置】- 【显示与亮度】-【文字大小】中修改：

![](http://upload-images.jianshu.io/upload_images/2648731-bad3a77a20bd5988.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 最大号字体（滑块位于最右边）

![](http://upload-images.jianshu.io/upload_images/2648731-88c6185ca1f699a7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 中等字体（滑块位于正中间）

![](http://upload-images.jianshu.io/upload_images/2648731-418c17e7dc7712fb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 最小号字体（滑块位于最左边）

![](http://upload-images.jianshu.io/upload_images/2648731-dffc57f4748d1009.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### Font Descriptors - 字体描述符

可以利用字体描述符改变字体样式，而不需要获取并设置当前字体大小：

#### 修改字体描述符信息

```objective-c
// 用描述符修改字体
// 1.获得 UIFontTextStyleBody 风格的字体描述符信息。
UIFontDescriptor *bodyFontDesciptor = [UIFontDescriptor preferredFontDescriptorWithTextStyle:UIFontTextStyleBody];
// 2.覆盖字体特征。
UIFontDescriptor *boldBodyFontDescriptor = [bodyFontDesciptor fontDescriptorWithSymbolicTraits:UIFontDescriptorTraitBold];
// 3.设置与字体描述符匹配的字体。
// 如果 fontSize 大于0.0，则它在 fontDescriptor 中优先于UIFontDescriptorSizeAttribute。
self.boldBodyTextLabel.font = [UIFont fontWithDescriptor:boldBodyFontDescriptor size:0.0];
```

##### UIFontDescriptorSymbolicTraits 部分选项

```objective-c
UIFontDescriptorTraitItalic = 1u << 0,
UIFontDescriptorTraitBold = 1u << 1,
UIFontDescriptorTraitExpanded = 1u << 5, // expanded and condensed traits are mutually exclusive
UIFontDescriptorTraitCondensed = 1u << 6,
UIFontDescriptorTraitMonoSpace = 1u << 10, // Use fixed-pitch glyphs if available. May have multiple glyph advances (most CJK glyphs may contain two spaces)
UIFontDescriptorTraitVertical = 1u << 11, // Use vertical glyph variants and metrics
UIFontDescriptorTraitUIOptimized = 1u << 12, // Synthesize appropriate attributes for UI rendering such as control titles if necessary
UIFontDescriptorTraitTightLeading = 1u << 15, // Use tighter leading values
UIFontDescriptorTraitLooseLeading = 1u << 16, // Use looser leading values
```



#### 通过传入字典的方式创建字体描述符

```objective-c
// 创建一个字体描述符
UIFontDescriptor *scriptFontDescriptor = [UIFontDescriptor
    fontDescriptorWithFontAttributes:@{
                                       UIFontDescriptorFamilyAttribute: @"Zapfino",
                                         UIFontDescriptorSizeAttribute: @15.0
                                       }];
self.scriptTextLabel.font = [UIFont fontWithDescriptor:scriptFontDescriptor size:0.0];
```

其他可以设置的 Attributes 属性：

```objective-c
UIKIT_EXTERN NSString *const UIFontDescriptorFamilyAttribute NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN NSString *const UIFontDescriptorNameAttribute NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN NSString *const UIFontDescriptorFaceAttribute NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN NSString *const UIFontDescriptorSizeAttribute NS_AVAILABLE_IOS(7_0);
UIKIT_EXTERN NSString *const UIFontDescriptorVisibleNameAttribute NS_AVAILABLE_IOS(7_0);
```

字体描述符效果：

![](http://upload-images.jianshu.io/upload_images/2648731-7dd956bfeece322d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



The End.
