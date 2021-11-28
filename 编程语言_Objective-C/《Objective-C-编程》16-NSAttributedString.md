###  NSFontAttributeName(字体)

> UIFont, default Helvetica(Neue) 12

该属性所对应的值是一个 **UIFont** 对象。该属性用于改变一段文本的字体。



###  NSParagraphStyleAttributeName（段落）

>  NSParagraphStyle, default defaultParagraphStyle

该属性所对应的值是一个 **NSParagraphStyle** 对象。该属性在一段文本上应用多个属性（居中，换行模式，间距等样式）。如果不指定该属性，则默认为 NSParagraphStyle 的 defaultParagraphStyle 方法返回的默认段落属性。



### NSForegroundColorAttributeName(字体颜色)

> UIColor, default blackColor

该属性所对应的值是一个 **UIColor** 对象。该属性用于指定一段文本的字体颜色。如果不指定该属性，则默认为黑色。

> 注意：
> NSForegroundColorAttributeName 设置的颜色与 UILabel 的 textColor 属性设置的颜色在地位上是相等的，与 NSBackgroundColorAttributeName 地位上也相等，谁最后赋值，最终显示的就是谁的颜色，但是textColor属性可以与 NSBackgroundColorAttributeName 属性可叠加。



### NSBackgroundColorAttributeName(字体背景色)

> UIColor, default nil: no background

该属性所对应的值是一个 **UIColor** 对象。该属性用于指定一段文本的背景颜色。如果不指定该属性，则默认无背景色。



### NSLigatureAttributeName(连字符)

> NSNumber containing integer, default 1: default ligatures, 0: no ligatures

该属性所对应的值是一个 **NSNumber** 对象(整数)。连体字符是指某些连在一起的字符，它们采用单个的图元符号。0 表示没有连体字符。1 表示使用默认的连体字符。2表示使用所有连体符号。默认值为 1（注意，iOS 不支持值为 2）。



### NSKernAttributeName(字符间距)

> NSNumber containing floating point value, in points; amount to modify default kerning. 0 means kerning is disabled.

**NSKernAttributeName** 设定字符间距，取值为 **NSNumber** 对象（整数），正值间距加宽，负值间距变窄



### NSStrikethroughStyleAttributeName(删除线)

> NSNumber containing integer, default 0: no strikethrough

**NSStrikethroughStyleAttributeName** 设置删除线，取值为 **NSNumber** 对象（整数），枚举常量 **NSUnderlineStyle** 中的值：

```objectivec
typedef NS_ENUM(NSInteger, NSUnderlineStyle) {
    NSUnderlineStyleNone                                    = 0x00,// 不设置删除线
    NSUnderlineStyleSingle                                  = 0x01,// (──────)细单实线
    NSUnderlineStyleThick NS_ENUM_AVAILABLE(10_0, 7_0)      = 0x02,// (━━━━━━━)粗单实线
    NSUnderlineStyleDouble NS_ENUM_AVAILABLE(10_0, 7_0)     = 0x09,// (══════)细双实线

    NSUnderlinePatternSolid NS_ENUM_AVAILABLE(10_0, 7_0)      = 0x0000,// (────────)
    NSUnderlinePatternDot NS_ENUM_AVAILABLE(10_0, 7_0)        = 0x0100,// (‑ ‑ ‑ ‑ ‑ ‑)
    NSUnderlinePatternDash NS_ENUM_AVAILABLE(10_0, 7_0)       = 0x0200,// (— — — —)
    NSUnderlinePatternDashDot NS_ENUM_AVAILABLE(10_0, 7_0)    = 0x0300,// (— ‑ — ‑ — ‑)
    NSUnderlinePatternDashDotDot NS_ENUM_AVAILABLE(10_0, 7_0) = 0x0400,// (— ‑ ‑ — ‑ ‑)

    NSUnderlineByWord NS_ENUM_AVAILABLE(10_0, 7_0)            = 0x8000// (••••••••••••)
} NS_ENUM_AVAILABLE(10_0, 6_0);
```



### NSUnderlineStyleAttributeName(下划线)

> NSNumber containing integer, default 0: no underline

该属性所对应的值是一个 **NSNumber** 对象(整数)。该值指定是否在文字上加上下划线，该值参考“Underline Style Attributes”。默认值是 **NSUnderlineStyleNone**。





### NSStrokeColorAttributeName(文字描边颜色)

> UIColor, default nil: same as foreground color

设置文字描边颜色，需要和**NSStrokeWidthAttributeName**设置描边宽度，这样就能使文字空心.





### NSStrokeWidthAttributeName(文字描边宽度)

> NSNumber containing floating point value, in percent of font point size, default 0: no stroke; positive for stroke alone, negative for stroke and fill (a typical value for outlined text would be 3.0)

**NSStrokeWidthAttributeName** 这个属性所对应的值是一个 **NSNumber** 对象(小数)。该值改变笔画宽度（相对于字体 size 的百分比），负值填充效果，正值中空效果，默认为 0，即不改变。正数只改变描边宽度。负数同时改变文字的描边和填充宽度。例如，对于常见的空心字，这个值通常为 3.0。
同时设置了空心的两个属性，并且 **NSStrokeWidthAttributeName** 属性设置为整数，文字前景色就无效果了。





### NSShadowAttributeName(阴影)

> NSShadow, default nil: no shadow

该属性所对应的值是一个 **NSShadow** 对象。默认为 nil。单独设置不好使，和这三个任一个都好使，**NSVerticalGlyphFormAttributeName**，**NSObliquenessAttributeName**，**NSExpansionAttributeName**





### NSTextEffectAttributeName（效果属性）

> NSString, default nil: no text effect

该属性所对应的值是一个 **NSString** 对象，默认为 nil，表示无文字效果。
NSTextEffectLetterpressStyle：图形文字凸版印刷效果。

### NSAttachmentAttributeName(附件属性)

> NSTextAttachment, default nil



### NSLinkAttributeName（链接属性）

> NSURL (preferred) or NSString



### NSBaselineOffsetAttributeName（基线偏移量）

> NSNumber containing floating point value, in points; offset from baseline, default 0



### NSUnderlineColorAttributeName（下划线颜色）

> UIColor, default nil: same as foreground color

**NSUnderlineColorAttributeName** 设置下划线颜色，取值为 **UIColor** 对象，默认值为黑色





### NSStrikethroughColorAttributeName(删除线颜色)

> UIColor, default nil: same as foreground color

**NSStrikethroughColorAttributeName** 设置删除线颜色，取值为 UIColor 对象，默认值为黑色





### NSObliquenessAttributeName(字体倾斜)

> NSNumber containing floating point value; skew to be applied to glyphs, default 0: no skew



### NSExpansionAttributeName (字体扁平化)

> NSNumber containing floating point value; log of expansion factor to be applied to glyphs, default 0: no expansion



### NSWritingDirectionAttributeName（书写方向属性）

> NSArray of NSNumbers representing the nested levels of writing direction overrides as defined by Unicode LRE, RLE, LRO, and RLO characters.  The control characters can be obtained by masking NSWritingDirection and NSWritingDirectionFormatType values.  LRE: NSWritingDirectionLeftToRight|NSWritingDirectionEmbedding, RLE: NSWritingDirectionRightToLeft|NSWritingDirectionEmbedding, LRO: NSWritingDirectionLeftToRight|NSWritingDirectionOverride, RLO: NSWritingDirectionRightToLeft|NSWritingDirectionOverride,



### NSVerticalGlyphFormAttributeName(横竖排版)

> An NSNumber containing an integer value.  0 means horizontal text.  1 indicates vertical text.  If not specified, it could follow higher-level vertical orientation settings.  Currently on iOS, it's always horizontal.  The behavior for any other value is undefined.

设置文字垂直方向、水平方向显示，该属性所对应的值是一个 NSNumber 对象(整数)。0 表示横排文本。1 表示竖排文本。在 iOS 中，总是使用横排文本，0 以外的值都未定义。



### 参考

* [NSAttributedString所有文本属性详解（多图）](http://www.devzhang.com/14708783388469.html)
