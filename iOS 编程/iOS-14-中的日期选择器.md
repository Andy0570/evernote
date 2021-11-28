原文：[New UIDatePicker in iOS 14 @Andy lbanez](https://www.andyibanez.com/posts/new-uidatepicker-ios14/?__cf_chl_captcha_tk__=ac35d33f6e5ba6dedb397a8f86f30b9c5f41922b-1601358178-0-AUsWXOk4_C8voDhbp9HRDEnFbfyLlyVr4mh0yD0GkUZgkh3lVdfFlvwBmcM9d9KBsEmieozOWWp3_16Gk5MxsXaDZcJQwqcrBU45LGnScgntIhC7YDac6G44jBLgq5_zPI7P4OPf07O458VvWItUZsa7geDed8m9x6jwMtynIpAt1vur-nWvih_uPcDSH15tgChZjudLoHNVEpGwrWAZ79o9S4bW1o7KZZGD3JHVRKA-IQLL4vWNDZ4MhNvK8Nt2EYaih-QEJEVhuiE37y8k3vAUdTKTeoKx2OF2Qz2BHb770Uz-cwJ6RSz7xitsWVESHmAHtzDH40Vy8of9nKjLrcw4RhB6UpA4do56CdLkRtvE6ixV4gx9S2PQ784EAUYpFAFT6i1DEl6cdQayMKf75Shr8zTfmGZXhL-68e4rOKeCPKxfnWEskPu6CEdVlSjIWCKn8-X4rQ8DpyWBHdDn4aE85eM_ZmGGq78z4aUTq-nBEnwMerzck33vBSw879LNm876hDMxoEvappn-WzRMJsT2Izt6DLZSHm6D_fSAS3M-)

WWDC 2020 为很多旧的、众所周知的 API 带来了许多有趣且出乎意料的更新。 在本文中，我们将探讨 iOS 上 `UIDatePicker` 的新增功能，该 API 自 iOS 发布之初就存在，并且自引入以来一直没有太大的变化。

## 选择器简史

`UIDatePicker` 是自 iOS SDK 诞生以来就存在的 API，它最早可以追溯到 iOS 2.0。如果你已经为 iOS 开发了一段时间，想必一定记得它的样子：

![](https://upload-images.jianshu.io/upload_images/2648731-fe575b3785149048.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在 iOS 7 中，整个系统进行了全面的重新设计。 看看我们的老朋友 `UIDatePicker` 经过的巨大更新：

![](https://upload-images.jianshu.io/upload_images/2648731-af8d9e51d6c7331f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

…… 虽然对于我们的老朋友来说机会不多。 但是 iOS 14 引入了我们可以使用的全新的日期选择器。 对于用户而言，它使用起来更加轻松，灵活，而且我们无需花太多时间就可以使用它。

## iOS 14 中的 UIDatePicker

### UIDatePicker 样式

首先，老式滚轮选择器的样式实际上并没有消失。 相反，UIDatePicker 现在具有一个名为 `datePickerStyle` 的属性，你可以在其中让系统使用 `.automatic` 以自动选择最佳样式，或者您可以在 `.compact` 和 `.inline` 之间进行选择 - 这两者都是 iOS 14 的新功能 - 或 `.wheel` 是旧的十多年来我们所熟知的风格。

### `.compact` 紧凑样式

`.compact` 紧凑样式的日期选择器会呈现为用户可以点击的小 UI。

![](https://upload-images.jianshu.io/upload_images/2648731-1db5ab5fc035b72d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个小 UI 占用更少的空间，而最好的部分是它是可交互的。 当用户点击它时，他们将以全屏样式显示一个完整的日历视图：

![](https://upload-images.jianshu.io/upload_images/2648731-63135f704ee8d385.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这个完整的日历视图中，用户可以更加灵活地选择时间和日期，并且可以点击箭头指示器在几个月或更长时间之间移动。

选择器仍然像以往一样可自定义。 例如，你可以显示提示，以仅选择日期或时间，而不是默认两者都提供。

![](https://upload-images.jianshu.io/upload_images/2648731-dee3073d9dc32700.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/2648731-68bebcf43af95dc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### `.inline` 内嵌样式

这种样式本质上与 `.compact` 相同，不同之处在于用户将永远不会看到他们必须点击的小界面。 取而代之的是，日历或时间选择器组件将完全呈现。

![](https://upload-images.jianshu.io/upload_images/2648731-7b3e2c5f28fe2fa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后，在 `.inline` 或 `.compact` 模式下，用户可以点击左上角显示月份和年份的标签，然后系统会显示一个滚轮，让他们快速跳转到不同的月份和年份：

![](https://upload-images.jianshu.io/upload_images/2648731-b3e965fae8a480d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 总结

旧的和已知的 API 在 iOS 14 中得到了应有的更新。新的日期选择器易于实现，它为用户提供了一种更快的日期检索方法。如果你发现本文有任何不正确之处（包括错别字）或问题，请向我发送推特（@AndyIbanezK）或向我发送电子邮件至 `andy.andyibanez.com`。 感谢您帮助我提高博客质量！如果您希望我涉及到与 Swift，iOS 或其他 Apple 平台有关的任何内容，请随时与我联系，我将在以后的文章中尝试介绍。
