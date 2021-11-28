Retina 显示屏拥有很高的分辨率——其中 4 英寸屏幕是 640 像素 × 1136 像素（640 x 1136 pixels），3.5 英寸屏幕是 640 像素 × 960 像素（640 x 960 pixels）。 Retina 技术之前的屏幕分辨率是 320 像素 × 480 像素（320 x 480 pixels）。

对于矢量图像，不用做任何处理就能够在两种设备上有最好的显示效果。

如果应用是通过Core Graphics函数绘图的，那么画出的图像在不同的设备上会有不同的显示效果。Core Graphics以点为单位描述线、曲线和文字等。对于非Retina显示屏，一个点是一个像素。对于Retina显示屏，一个点是2像素×2像素。

开发者需要为高分辨率的图片文件加上后缀名 `@2×`、或者 `@3x`。

### iPhone 屏幕尺寸

开发尺寸    | 倍率 | 状态栏高度 | 导航栏高度 | 标签栏高度 |设备型号
---------- | --- | -------- | --------- | -------- | -----
414x896 pt | @3x | 44 pt    | 44pt      | 49 pt    | iPhone 11 Pro Max, XS Max
414x896 pt | @2x | 44 pt    | 44pt      | 49 pt    | iPhone 11, XR
375x812 pt | @3x | 44 pt    | 44pt      | 49 pt    | iPhone 11 Pro, X, XS
414x736 pt | @3x | 20 pt    | 44pt      | 49 pt    | iPhone 6P, 6SP, 7P, 8P
375x667 pt | @2x | 20 pt    | 44pt      | 49 pt    | iPhone 6, 6S, 7, 8
320x568 pt | @2x | 20 pt    | 44pt      | 49 pt    | iPhone 5, 5S, 5C, SE
320x480 pt | @2x | 20 pt    | 44pt      | 49 pt    | iPhone 4, 4S
320x480 pt | @1x | 20 pt    | 44pt      | 49 pt    | iPhone 2G, 3S, 3GS

注：

* 在 iOS 开发中，绘制图形以 `point` 为单位。所以我们更多关注的 iPhone 屏幕参数是以上列出的开发尺寸。
* 像素尺寸(pixels) = 开发尺寸(point) * 倍率(Scale)


### LaunchImage 启动图分辨率

| iPhone 设备型号        | 所属组                    | 启动图分辨率       |
| -------------------- | ------------------------- | ---------------- |
| iPhone Xs Max        | Portrait iOS 12+          | 1242px × 2688px  |
| iPhone XR            | Portrait iOS 12+          | 828px × 1792px   |
| iPhone Xs Max        | Landspace iOS 12+         | 2688px × 1242px  |
| iPhone XR            | Landspace iOS 12+         | 1792px × 828px   |
| iPhone X / iPhone XS | Portrait iOS 11+          | 1125px × 2436px  |
| iPhone X / iPhone XS | Landspace iOS 11+         | 2436px × 1125px  |
| Retina HD 5.5        | iPhone Portrait iOS 8+    | 1242px × 2208px  |
| Retina HD 4.7        | iPhone Portrait iOS 8+    | 750px × 1334px   |
| Retina HD 5.5        | Landspace Portrait iOS 8+ | 2208px × 1242px  |
| 2x                   | iPhone Portrait iOS 7+    | 640px × 960px    |
| Retina 4             | iPhone Portrait iOS 7+    | 640px × 1136px   |
| 1x                   | iPhone Portrait iOS 5,6   | 320px ×480px     |
| 2x                   | iPhone Portrait iOS 5,6   | 640px × 960px    |
| Retina 4             | iPhone Portrait iOS 5,6   | 640px × 1136px   |


### 参考

* [Apple - Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
* [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) - 👍 iPhone 设备型号参考
* [Medium: Xcode LaunchImage Size](https://medium.com/@furkancelik/xcode-launchimage-size-47589835859) - 👍 iPhone 启动图尺寸
* [The Ultimate Guide To iPhone Resolutions](https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions) 👍 iPhone 分辨率终极指南
* [图翼网: iPhone 尺寸规范（界面 & 图标）](http://www.pinmie.com/cc/) 👍
* [图翼网:【iOS 9】史诗级规范！全新 Apple iOS 设计规范指南！](http://www.pinmie.com/v/45421.html)
* [iPhone 各个型号屏幕分辨率总结 @2018-02-17](https://blog.csdn.net/amyloverice/article/details/79389357)
* [看过那么多UI 配色指南，这篇可能是最全面的（附大量实例）](http://www.pinmie.com/v/50355.html)
* [小专栏 - 字体指南「让内容清晰可读」](https://xiaozhuanlan.com/topic/6935142078)
* [小专栏 - 色彩指南「如何使用颜色」](https://xiaozhuanlan.com/topic/3094517286)
* [金融（支付宝）APP 原型资源分享](http://m.ui.cn/details/254556)
* [UI 中国: UI设计基础概念系列文章](https://i.ui.cn/ucenter/270387.html)
