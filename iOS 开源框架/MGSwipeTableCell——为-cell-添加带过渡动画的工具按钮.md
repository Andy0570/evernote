* GitHub: [MGSwipeTableCell](https://github.com/MortimerGoro/MGSwipeTableCell)
* Star: 6.8k+

MGSwipeTableCell 是一个易于使用的 UITableViewCell 子类，允许显示具有各种过渡效果的可滑动按钮。

该库与创建 UITableViewCell 的所有方法兼容：系统预定义样式，以编程方式创建的 cell，从 xib 加载的单元格以及故事板中的原型 cell。 如果需要，你也可以使用 autolayout。

适用于 iOS> = 5.0 版本。 在 iPhone 和 iPad 上的所有 iOS 版本上进行测试：iOS 7，iOS 8，iOS 9，iOS 10，iOS 11，iOS 12，iOS 13。

## 过渡动画演示

### Border transition - 边缘过渡

![](https://upload-images.jianshu.io/upload_images/2648731-16d07f1db4794d5a.gif?imageMogr2/auto-orient/strip)


### Clip transition - 裁剪过渡

![](https://upload-images.jianshu.io/upload_images/2648731-daf5e70e4f434f96.gif?imageMogr2/auto-orient/strip)


### 3D transition - 3D 过渡

![](https://upload-images.jianshu.io/upload_images/2648731-007ee3a15483e8fe.gif?imageMogr2/auto-orient/strip)


### Static transition - 静态过渡

![](https://upload-images.jianshu.io/upload_images/2648731-b0dba03319d11c9b.gif?imageMogr2/auto-orient/strip)


### Drag transition - 拖动过渡

![](https://upload-images.jianshu.io/upload_images/2648731-5c123fca311978ea.gif?imageMogr2/auto-orient/strip)


## API 参考
参见 [`MGSwipeTableCell.h`](MGSwipeTableCell/MGSwipeTableCell.h) 头文件查看有关该类功能的完整概述。
参见 [`MailAppDemo`](demo/MailAppDemo) 查看一个模仿 Apple 的邮件应用程序的完整项目（用 Objective-C 编写）。
参见 [`MailAppDemoSwift`](demo/MailAppDemoSwift) 查看一个模仿 Apple 的邮件应用程序的完整项目（用 Swift 编写）。
参见 [`SpotifyDemo`](demo/SpotifyDemo) 查看一个模仿 Spotify App 滑动样式的完整项目。
参见 [`MGSwipeDemo`](demo/MGSwipeDemo) 查看一个完整的项目示例，您可以在真实设备 / 模拟器上测试各种过渡。

## 项目设置

You can use CocoaPods to include MGSwipeTableCell into you project:
```ruby
pod 'MGSwipeTableCell'
```
You can use Carthage to include MGSwipeTableCell into your project. Just add this dependency to your Cartfile:
```ruby    
github "MortimerGoro/MGSwipeTableCell"
```
## 用法

### 概述
在项目中集成 `MGSwipeTableCell` 非常简单。 基本上，你只需继承 `MGSwipeTableCell` 而不是 `UITableViewCell`，或直接使用 iOS 预定义单元格样式实例化 `MGSwipeTableCell` 实例。 你可以按照以前的方式布置单元格内容，`MGSwipeTableCell` 不会强制您更改布局。

以下是使用 iOS 预定义样式的 `MGSwipeTableCell` 的示例。 您可以将按钮数组设置为 `cell.leftButtons` 或 `cell.rightButtons` 属性。 `MGSwipeButton` 是一个便捷类，你不必被迫使用它。 你可以使用自己的 `UIButtons` 或 `UIViews`。 你也可以使用 `leftSwipeSettings` 和 / 或 `rightSwipeSettings` 属性配置转场（和滑动阈值）

##### Objective-C
```objectivec
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    static NSString * reuseIdentifier = @"programmaticCell";
    MGSwipeTableCell * cell = [self.tableView dequeueReusableCellWithIdentifier:reuseIdentifier];
    if (!cell) {
        cell = [[MGSwipeTableCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:reuseIdentifier];
    }

    cell.textLabel.text = @"Title";
    cell.detailTextLabel.text = @"Detail text";
    cell.delegate = self; //optional

    //配置左边按钮
    cell.leftButtons = @[[MGSwipeButton buttonWithTitle:@"" icon:[UIImage imageNamed:@"check.png"] backgroundColor:[UIColor greenColor]],
                          [MGSwipeButton buttonWithTitle:@"" icon:[UIImage imageNamed:@"fav.png"] backgroundColor:[UIColor blueColor]]];
    // 按钮过渡样式,3D转换
    cell.leftSwipeSettings.transition = MGSwipeTransition3D;

    //配置右边按钮
    cell.rightButtons = @[[MGSwipeButton buttonWithTitle:@"Delete" backgroundColor:[UIColor redColor]],
                           [MGSwipeButton buttonWithTitle:@"More" backgroundColor:[UIColor lightGrayColor]]];
    cell.rightSwipeSettings.transition = MGSwipeTransition3D;
    return cell;
}
```
##### Swift
```swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
{
    let reuseIdentifier = "programmaticCell"
    var cell = tableView.dequeueReusableCell(withIdentifier: reuseIdentifier, for: indexPath) as! MGSwipeTableCell

    cell.textLabel!.text = "Title"
    cell.detailTextLabel!.text = "Detail text"
    cell.delegate = self //optional

    //configure left buttons
    cell.leftButtons = [MGSwipeButton(title: "", icon: UIImage(named:"check.png"), backgroundColor: .green),
                        MGSwipeButton(title: "", icon: UIImage(named:"fav.png"), backgroundColor: .blue)]
    cell.leftSwipeSettings.transition = .rotate3D

    //configure right buttons
    cell.rightButtons = [MGSwipeButton(title: "Delete", backgroundColor: .red),
                         MGSwipeButton(title: "More",backgroundColor: .lightGray)]
    cell.rightSwipeSettings.transition = .rotate3D

    return cell
}
```

为了监听按钮点击事件，你可以实现可选的 MGSwipeTableCellDelegate 协议，或者如果您懒得这样做，MGSwipeButton 类也附带一个便捷 block 回调;）

##### Objective-c
```objectivec
// 创建按钮时，同时设置 block 回调
[MGSwipeButton buttonWithTitle:@"More" backgroundColor:[UIColor lightGrayColor] callback:^BOOL(MGSwipeTableCell *sender) {
      NSLog(@"Convenience callback for swipe buttons!");
}]
```
##### Swift
```swift
MGSwipeButton(title: "Delete", backgroundColor: .red) {
      (sender: MGSwipeTableCell!) -> Bool in
      print("Convenience callback for swipe buttons!")
      return true
    }
```

### Delegate - 代理协议

MGSwipeTableCellDelegate 是一个可选的委托协议，用于配置滑动按钮或接收触发的操作或其他事件。 按钮可以在cell 被创建时内联配置，如果你不想使用此委托方法。但是使用委托可以提高内存使用率，因为按钮仅按需创建。

```objectivec
@protocol MGSwipeTableCellDelegate <NSObject>

@optional
/**
 * Delegate method to enable/disable swipe gestures
 * 开关
 * @return YES if swipe is allowed
 **/
-(BOOL) swipeTableCell:(MGSwipeTableCell*) cell canSwipe:(MGSwipeDirection) direction;
/**
 * Delegate method invoked when the current swipe state changes
 * 当前滑动状态更改时调用的 Delegate 方法
 @param state the current Swipe State
 @param gestureIsActive YES if the user swipe gesture is active. No if the uses has already ended the gesture
 **/
-(void) swipeTableCell:(MGSwipeTableCell*) cell didChangeSwipeState:(MGSwipeState) state gestureIsActive:(BOOL) gestureIsActive;
/**
 * Called when the user clicks a swipe button or when a expandable button is automatically triggered
 * 当用户单击滑动按钮或自动触发可展开按钮时调用
 * @return YES to autohide the current swipe buttons
 **/
-(BOOL) swipeTableCell:(MGSwipeTableCell*) cell tappedButtonAtIndex:(NSInteger) index direction:(MGSwipeDirection)direction fromExpansion:(BOOL) fromExpansion;
/**
 * Delegate method to setup the swipe buttons and swipe/expansion settings
 * 配置按钮协议方法
 * Buttons can be any kind of UIView but it's recommended to use the convenience MGSwipeButton class
 * Setting up buttons with this delegate instead of using cell properties improves memory usage because buttons are only created in demand
 * @param swipeTableCell the UITableViewCell to configure. You can get the indexPath using [tableView indexPathForCell:cell]
 * @param direction The swipe direction (left to right or right to left)
 * @param swipeSettings instance to configure the swipe transition and setting (optional)
 * @param expansionSettings instance to configure button expansions (optional)
 * @return Buttons array
 **/
-(NSArray*) swipeTableCell:(MGSwipeTableCell*) cell swipeButtonsForDirection:(MGSwipeDirection)direction
             swipeSettings:(MGSwipeSettings*) swipeSettings expansionSettings:(MGSwipeExpansionSettings*) expansionSettings;

@end
```

### Expandable buttons - 可扩展按钮

默认情况下，按钮不可扩展。 你可以使用 cell.leftExpansion 和 cell.rightExpansion 属性设置可扩展按钮

当用户结束滑动手势并且扩展处于活动状态时（可通过阈值配置），将自动触发可扩展按钮事件。 触发的可扩展按钮可以弹回到其初始位置或填充整个 UITableViewCell，您可以使用 fillOnTrigger 属性选择所需的动画。

```objectivec
@interface MGSwipeExpansionSettings: NSObject
/** index of the expandable button (in the left or right buttons arrays) */
@property (nonatomic, assign) NSInteger buttonIndex;
/** if true the button fills the cell on trigger, else it bounces back to its initial position */
@property (nonatomic, assign) BOOL fillOnTrigger;
/** Size proportional threshold to trigger the expansion button. Default value 1.5 */
@property (nonatomic, assign) CGFloat threshold;
@end
```
### Rounded corners and swipe buttons - 圆角和滑动按钮
MGSwipeTableCell 支持圆角。 例：

```objectivec
cell.layer.cornerRadius = 50
cell.backgroundColor = UIColor.gray
cell.clipsToBounds = true
cell.swipeBackgroundColor = UIColor.gray
```
![RoundTableViewCell](http://upload-images.jianshu.io/upload_images/2648731-36ea5c3bd3daba49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## License - 许可证

The MIT License (MIT)

Copyright (c) 2014 Imanol Fernandez @MortimerGoro

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

### 示例代码
```
#pragma mark - MGSwipeTableCellDelegate

/**
 * Delegate method to setup the swipe buttons and swipe/expansion settings
 * Buttons can be any kind of UIView but it's recommended to use the convenience MGSwipeButton class
 * Setting up buttons with this delegate instead of using cell properties improves memory usage because buttons are only created in demand
 * @param cell the UITableViewCell to configure. You can get the indexPath using [tableView indexPathForCell:cell]
 * @param direction The swipe direction (left to right or right to left)
 * @param swipeSettings instance to configure the swipe transition and setting (optional)
 * @param expansionSettings instance to configure button expansions (optional)
 * @return Buttons array
 **/
-(nullable NSArray<UIView*>*) swipeTableCell:(nonnull MGSwipeTableCell*) cell swipeButtonsForDirection:(MGSwipeDirection)direction
                               swipeSettings:(nonnull MGSwipeSettings*) swipeSettings expansionSettings:(nonnull MGSwipeExpansionSettings*) expansionSettings {
    
    if (direction == MGSwipeDirectionRightToLeft) {
        // MGSwipeSettings 按钮设置
        swipeSettings.transition = MGSwipeTransitionBorder;
        swipeSettings.threshold = 1.5; // 工具按钮被显示出来的阈值 （手势拉伸的长度/按钮自身宽度）
        swipeSettings.keepButtonsSwiped = YES; // 手势停止时，按钮是否自动隐藏
        
        // MGSwipeExpansionSettings 拉伸设置
        expansionSettings.buttonIndex = 0; // 被拉伸的按钮索引
        expansionSettings.fillOnTrigger = NO; // 拉伸时是否填充整个 cell
        expansionSettings.threshold = 2.0; // 拉伸动作被触发的阈值（与按钮的宽度相关）
        
        // 删除按钮
        CGFloat padding = 20; // 按钮右侧 - contentView 右侧边距
        MGSwipeButton *trashButton = [MGSwipeButton buttonWithTitle:@"" icon:[UIImage imageNamed:@"trash"] backgroundColor:[UIColor clearColor] padding:padding];
        return @[trashButton];
    }
    return nil;
}

/**
 * 当用户单击滑动按钮或可展开按钮被自动自动触发时调用
 * Called when the user clicks a swipe button or when a expandable button is automatically triggered
 * @return YES to autohide the current swipe buttons
 **/
-(BOOL) swipeTableCell:(nonnull MGSwipeTableCell*) cell tappedButtonAtIndex:(NSInteger) index direction:(MGSwipeDirection)direction fromExpansion:(BOOL) fromExpansion {
    
    // 删除按钮触发事件
    if (direction == MGSwipeDirectionRightToLeft && index == 0) {
        // 这里直接删除，业务层还需要做删除前的提示和判断的！！！
        NSIndexPath *indexPath = [self.tableView indexPathForCell:cell];
        [self.dataSourceArray removeObjectAtIndex:indexPath.row];
        [self.tableView deleteRowAtIndexPath:indexPath withRowAnimation:UITableViewRowAnimationLeft];
        return NO;
    }
    return YES;
}
```

### 参考

* [GitHub：SWTableViewCell](https://github.com/CEWendel/SWTableViewCell) ⭐️ 7000+，但是这个库好像很久没有更新了（最后更新是4年前。。。）
* [GitHub：MGSwipeTableCell](https://github.com/MortimerGoro/MGSwipeTableCell) ⭐️6500+



