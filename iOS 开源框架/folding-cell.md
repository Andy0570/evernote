* GitHub: [FOLDING CELL](https://github.com/Ramotion/folding-cell)
* Star: 9000+
* Language: Swift

以动画的方式扩展内容单元，灵感来自纸卡折叠的材料设计。

## 安装

Just add the FoldingCell.swift file to your project.

or use [CocoaPods](https://cocoapods.org) with Podfile:
```
pod 'FoldingCell'
```
or [Carthage](https://github.com/Carthage/Carthage) users can simply add Mantle to their `Cartfile`:
```
github "Ramotion/folding-cell"
```

or just drag and drop FoldingCell.swift file to your project

## 解决方案
[站外图片上传中...(image-d36a78-1568602604084)]



## 用法

1) 创建一个新的继承自  `FoldingCell` 的 cell 类。

2) 在你的 cell 类的 storyboard 或者 nib 文件中添加一个 UIView，该 UIView 继承自 `RotatedView`。

将此视图的插座连接到 cell 的  `foregroundView` 属性。

设置此视图与超视图的位置约束，如下所示：

[站外图片上传中...(image-7ec36b-1568602604084)]

(约束的常量值可能不同). Connect the outlet from this top constraint to the cell property `foregroundViewTop`
. (当 cell 处于正常折叠状态时，该视图会显示).

3) Add other UIViews to your cell, connect the outlet from this view to the cell
property `containerView`. Add constraints from this view to the superview like in the picture:

![1.2](https://upload-images.jianshu.io/upload_images/2648731-8e03ef6f53a3b05e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(constants of constraints may be different). Connect the outlet from this top constraint to the cell property `containerViewTop`.
(当 cell 处于打开状态时，该视图会显示)

Your result should be something like this picture:

[站外图片上传中...(image-505648-1568602604084)]


4) Set ``` @IBInspectable var itemCount: NSInteger ``` property is a count of folding (it IBInspectable you can set in storyboard). range 2 or greater. Default value is 2

Ok, we've finished configuring the cell.

5) 添加代码到 UITableViewController 中。

5.1) 设置常量值:

``` swift
fileprivate struct C {
  struct CellHeight {
    static let close: CGFloat = *** // equal or greater foregroundView height
    static let open: CGFloat = *** // equal or greater containerView height
  }
}
```
5.2) 添加属性用于计算 cell 高度

``` swift
     var cellHeights = (0..<CELLCOUNT).map { _ in C.CellHeight.close }
```

5.3) 覆盖方法:

``` swift
    override func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
        return cellHeights[indexPath.row]
    }
```

5.4) 添加代码到以下方法中:

``` swift
    override func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
        guard case let cell as FoldingCell = tableView.cellForRowAtIndexPath(indexPath) else {
          return
        }

        var duration = 0.0
        if cellIsCollapsed {
            cellHeights[indexPath.row] = Const.openCellHeight
            cell.unfold(true, animated: true, completion: nil)
            duration = 0.5
        } else {
            cellHeights[indexPath.row] = Const.closeCellHeight
            cell.unfold(false, animated: true, completion: nil)
            duration = 0.8
        }

        UIView.animateWithDuration(duration, delay: 0, options: .CurveEaseOut, animations: { _ in
            tableView.beginUpdates()
            tableView.endUpdates()
        }, completion: nil)
    }
```
5.5) 控制 cell 是处于打开还是关闭状态

``` swift
  override func tableView(tableView: UITableView, willDisplayCell cell: UITableViewCell, forRowAtIndexPath indexPath: NSIndexPath) {

        if case let cell as FoldingCell = cell {
            if cellHeights![indexPath.row] == C.cellHeights.close {
                foldingCell.selectedAnimation(false, animated: false, completion:nil)
            } else {
                foldingCell.selectedAnimation(true, animated: false, completion: nil)
            }
        }
    }
```

6) 添加以下代码到你的新的 cell 类中

``` swift
    override func animationDuration(itemIndex:NSInteger, type:AnimationType)-> NSTimeInterval {

        // durations count equal it itemCount
        let durations = [0.33, 0.26, 0.26] // timing animation for each view
        return durations[itemIndex]
    }
```

## 如果你不使用 storyboard 和 xib 文件

以手写代码的方式创建 foregroundView 和 containerView （步骤 2 - 3）请查看如下 demo 示例：

[Folding-cell-programmatically](https://github.com/ober01/Folding-cell-programmatically)


