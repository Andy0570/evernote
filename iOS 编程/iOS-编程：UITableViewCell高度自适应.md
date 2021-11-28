### 第三方框架
* [GitHub: UITableView-FDTemplateLayoutCell](https://github.com/forkingdog/UITableView-FDTemplateLayoutCell) ⭐️8000+


### ✨方法一：使用 Auto Layout 并缓存高度
该方法参考：[UITableView自动计算cell高度并缓存，再也不用管高度啦](http://www.jianshu.com/p/64f0e1557562)
大致分4步实现：
#### 第一步
自定义 **UITableViewCell** 子类对象，**xib** 文件中的控件使用 **Auto Layout**【自上而下】加好约束：

![](http://upload-images.jianshu.io/upload_images/2648731-3f18ccac25b7d6ab.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 第二步
Controller 对象中添加一个 **NSMutableDictionary** 属性，用于保存缓存下来的cell高度值，这样可以解决点击状态栏不会自动滚动到顶部的问题。

```objective-c
@property (nonatomic, strong) NSMutableDictionary *heightAtIndexPath;
```



#### 第三步

实现代理方法：```-(void)tableView: willDisplayCell: forRowAtIndexPath:```

用第2️⃣步添加的 **NSMutableDictionary** 属性作为容器，在cell将要显示的时候在字典中保存这行cell的高度。

```objective-c
- (void)tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPath {
  
    NSNumber *height = @(cell.frame.size.height);
    [self.heightAtIndexPath setObject:height forKey:indexPath];
  
}
```

#### 第四步

实现代理方法：```-(CGFloat)tableView:estimatedHeightForRowAtIndexPath:```

```objective-c
-(CGFloat)tableView:(UITableView *)tableView estimatedHeightForRowAtIndexPath:(NSIndexPath *)indexPath {
    // 先去字典查看有没有缓存高度，有就返回，没有就返回一个大概高度
    NSNumber *height = [self.heightAtIndexPath objectForKey:indexPath];
    return (height ? height.floatValue : 100);
}
```
结果：
cell 的行高会自适应内容文本的高度：
![](http://upload-images.jianshu.io/upload_images/2648731-e86752b195e61807.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### ✨方法二：使用默认 UITableViewCellStyleValue1 实现自适应高度
上面的方法是通过自定义 **UITableViewCell** 子类对象实现高度自适应的。
某天发现了一篇博文号称能使用系统默认Cell 多行显示 detailTableView
参考：[Multi-line UITableViewCell using UILabel](http://the-lost-beauty.blogspot.jp/2009/11/multi-line-uitableviewcell-using.html)

1️⃣设置 `detailTextLabel.numberOfLines = 0` 实现多行显示
```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    
    static NSString *cellReusreIdentifier = @"UITableViewCellStyleValue1";
    UITableViewCell *cell =
        [tableView dequeueReusableCellWithIdentifier:cellReusreIdentifier];
    if (!cell) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleValue1
                                      reuseIdentifier:cellReusreIdentifier];
    }
    
    NSArray *allkey = [self.dataSourceDictionary allKeys];
    NSString *oneKey = allkey[indexPath.row];

    cell.textLabel.text = oneKey;
    cell.detailTextLabel.text = self.dataSourceDictionary[oneKey];
    cell.detailTextLabel.numberOfLines = 0;
    cell.detailTextLabel.lineBreakMode = NSLineBreakByWordWrapping;
    return cell;
}
```

2️⃣计算 `detailTextLabel` 中的字符串大小，并返回高度。

> You will also need to return a suitable height for the multi-line cell. A height of (44.0 + (numberOfLines - 1) * 19.0) should work fine.

文章作者称，还需要为多行 cell 返回一个合适的高度，高度的计算方式=
(44.0 + （detailTextLabel 的行数 - 1）*  19.0）
 
而网上大多数获取高度的方法是，通过计算（包含给定字符串的）矩形大小来获得高度和宽度：
```
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    
    // 参数一：最大宽高 44+19*4
    CGSize maxSize = CGSizeMake(355, MAXFLOAT);
    // 参数二：Font:
    UIFont *defaultFont = [UIFont systemFontOfSize:17.0f];
    // 参数三：String:
    NSArray *allKeys = [self.dataSourceDictionary allKeys];
    NSString *oneKey = allKeys[indexPath.row];
    NSString *string = self.dataSourceDictionary[oneKey];
    
    CGSize resultSize = [self sizeOfText:string font:defaultFont maxSize:maxSize];
    NSLog(@"result Size:(%f,%f) \n",resultSize.width,resultSize.height);
    
//    return resultSize.height + 23; // 23即消息上下的空间，可自由调整
    
    // 总高度 / 1行文本的高度 = 行数
    CGFloat labelNum = resultSize.height / 20.5;
    CGFloat height = 44.0 + (labelNum - 1) * 19.0;
    return height;
    
}

// 封装计算多行文本数据size方法
- (CGSize)sizeOfText:(NSString *)text font:(UIFont *)font maxSize:(CGSize)maxSize {
    NSDictionary *attrs = @{NSFontAttributeName:font};
    CGSize size = [text boundingRectWithSize:maxSize
                                     options:NSStringDrawingUsesLineFragmentOrigin
                                  attributes:attrs
                                     context:nil].size;
    return size;
}
```

这里会用到 **UIKit** 框架中 **NSString** 类的一个方法以计算包含字符串的矩形区域大小:

```
/**
 计算并返回(在当前图形上下文中)(使用给定选项和显示特征)的指定矩形内绘制的接收器的边界。
 
 @param size 要绘制的矩形的大小。
 @param options 字符串绘图选项。
 @param attributes 要应用于字符串的文本属性字典。 这些属性可以应用于NSAttributedString对象，但在NSString对象的情况下，这些属性适用于整个字符串，而不是字符串中的范围。
 @param context 用于接收器的字符串绘制上下文，指定最小比例因子和跟踪调整。
 @return 使用给定选项和显示特征绘制的接收器的边界。 从该方法返回的直线起点是第一个字形起点。
 */
- (CGRect)boundingRectWithSize:(CGSize)size
                       options:(NSStringDrawingOptions)options
                    attributes:(nullable NSDictionary<NSString *, id> *)attributes   
                       context:(nullable NSStringDrawingContext *)context NS_AVAILABLE(10_11, 7_0);
```
参数二是一个枚举类型：
类型为 `NSStringDrawingOptions`，用来指明绘制字符串时的渲染选项。各个选项如下：
```
typedef NS_OPTIONS(NSInteger, NSStringDrawingOptions) {
  // The specified origin is the line fragment origin, not the base line origin
  // 整个文本将以每行组成的矩形为单位计算整个文本的尺寸
  NSStringDrawingUsesLineFragmentOrigin = 1 << 0, 

  // Uses the font leading for calculating line heights
  // 使用字体的行间距来计算文本占用的范围，即每一行的底部到下一行的底部的距离计算
  NSStringDrawingUsesFontLeading = 1 << 1, 

  // Uses image glyph bounds instead of typographic bounds
  // 将文字以图像符号计算文本占用范围，而不是排版的边界
  NSStringDrawingUsesDeviceMetrics = 1 << 3,

  // Truncates and adds the ellipsis character to the last visible line if the text doesn't fit into the bounds specified.
  // Ignored if NSStringDrawingUsesLineFragmentOrigin is not also set.
  // 如果文本内容超出指定的矩形限制，文本将被截去并在最后一个字符后加上省略号。
  // 如果 NSStringDrawingUsesLineFragmentOrigin 没有设置，则该选项不生效
  NSStringDrawingTruncatesLastVisibleLine NS_ENUM_AVAILABLE(10_5, 6_0) = 1 << 5, 
} NS_ENUM_AVAILABLE(10_0, 6_0);
```

![](http://upload-images.jianshu.io/upload_images/2648731-3a0a3a98815f86d3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

测试结果并不是很好，你会发现返回多行 Label  行高的时候会少计算了一行，猜测原因就在于 `boundingRectWithSize:options:attributes: context:`传进去的第一个参数，该参数代表指定矩形区域的大小，但是 `detailLabel` 的宽度不是固定的，而是根据 `textLabel`的宽度动态变化的。因此要继续往下做的话就要先计算出 `textLabel` 的宽度，再用 屏幕宽度-textLabel 宽度-左右边距-`textLabel` 与 `detailLabel`之间的距离 算出 `detailLabel`的实际宽度传进去。。。😂😂😂，越想越复杂，因此该方法目前来看还是更加适用于固定多行的时候吧。

### 参考

* [优化UITableViewCell高度计算的那些事 @ForkingDog](http://blog.sunnyxx.com/2015/05/17/cell-height-calculation/)
* [UITableView自动计算cell高度并缓存，再也不用管高度啦](http://www.jianshu.com/p/64f0e1557562)
* [理解NSStringDrawingOptions每个选项的用法与意义](http://www.itdadao.com/articles/c15a197037p0.html)
* [iOS－－UILabel字体默认宽度和高度](http://blog.csdn.net/gauss_li/article/details/51013284)
* [UI高级控件入门2——UITableView](http://www.jianshu.com/p/7bd87054d6ac)
* [UILabel中一个很多人都会踩的坑](https://mp.weixin.qq.com/s?__biz=MzI5MjEzNzA1MA==&mid=2650264215&idx=1&sn=bce53cc5b98eef3f839760a4b9b5002c&chksm=f40683a2c3710ab4af58fa5e39a053c5997c4780da3df0f5acca189b6f8fc15a5c7cc26076f1&scene=0#rd)
