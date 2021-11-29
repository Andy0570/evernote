---
title: iOS 编程：UITableView（列表视图）
comments: true
date: 2018-10-09 02:07:00
categories: iOS
tags: [iOS]
---

### 参考文献

* [Apple官方文档:Table View Programming Guide for iOS](https://developer.apple.com/library/content/documentation/UserExperience/Conceptual/TableView_iPhone/AboutTableViewsiPhone/AboutTableViewsiPhone.html#//apple_ref/doc/uid/TP40007451)
* [iOS学习笔记之UITableView（1）@yetcode](http://www.jianshu.com/p/d8957a0ef419)  （阅读难度：😂，入门、不全）
* [UITableView的基本使用 @Enrica](http://www.jianshu.com/p/c3906d2c4943)
* [UITableView的知识进阶 @ Enrica](http://www.jianshu.com/p/fcf1ca7e644f)
* [UITableViewCell 高度自适应 @Show_Perry](http://www.jianshu.com/p/99f901762f15)
* [利用长按手势移动 Table View Cells @破船之家](http://beyondvincent.com/2014/03/26/2014-03-26-cookbook-moving-table-view-cells-with-a-long-press-gesture/)
* [iOS开发系列--UITableView全面解析 @KenshinCui ](http://www.cnblogs.com/kenshincui/p/3931948.html)
### 其它设置
### 隐藏表尾视图（TableFooterView）空白部分显示的线条

```objective-c
UIView *view = [[UIView alloc] init];
view.backgroundColor = [UIColor clearColor];
//设置页脚视图
[self.tableView setTableFooterView:view];
```



```objective-c
// 设置分割线样式
self.tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
```

