
📝 使用 [ZZFLEX](https://github.com/tbl00c/ZZFLEX) 框架的模版代码。

### 命令式的列表控制器：ZZFLEXAngel

#### 初始化 Angle

```objectivec
// tableView

UITableView *tableView = self.view.addTableView(0).view;

ZZFLEXAngel *angel = [[ZZFLEXAngel alloc] initWithHostView:tableView];

// collectionView

UICollectionView *collectionView = self.view.addCollectionView(1).view;

ZZFLEXAngel *angel2 = [[ZZFLEXAngel alloc] initWithHostView:collectionView];

```

#### 页面容器层（hostView）

```objectivec
- (void)reloadListUI
{
    // 清空所有数据
    self.clear();
    
    // 添加section 1
    {
        // section 标识
        NSInteger sectionType = 1;
        /*
         添加section
         sectionInsets: section边距
         minimumLineSpacing: 行间距
         minimumInteritemSpacing: 列间距
         */
        self.angel.addSection(sectionType).sectionInsets(UIEdgeInsetsMake(15, 15, 15, 15)).minimumLineSpacing(15).minimumInteritemSpacing(15);
        
        /// 设置section header，footer亦然
        self.angel.setHeader([UICollectionReusableView class]).toSection(sectionType);
        
        /*
         往section添加cell
         withDataModel：cell数据模型
         selectedAction：cell点击事件
         eventAction: cell内部事件，业务方自行设置和调用，eventType为事件类型、model是事件数据
         viewTag: cell标识，便于调试，或用于查找定位或作为插入其他cell的锚点
         */
        self.angel.addCell([UICollectionViewCell class]).toSection(sectionType).withDataModel(@"hello")
        .selectedAction(^ (id model) {
            NSLog(@"cell 点击事件");
        })
        .eventAction(^ id(NSInteger eventType, id model) {
            NSLog(@"cell 内部事件，类型：%ld, 模型: %@", eventType, model);
            return nil;
        })
        .viewTag(10001);
        
        /*
         往section批量添加cell
         withDataModelArray：cell数据模型数组，cell个数由数组元素个数决定
         delegate：为cell设置代理
         */
        self.angel.addCells([UICollectionViewCell class]).toSection(sectionType).withDataModelArray(@[@"1", @"2", @"3"])
        .selectedAction(^ (id model) {
            NSLog(@"cell 点击事件，数据模型为：%@", model);
        }).delegate(self);
    }
    
    // 刷新数据
    [self.angel reloadView];
}
```

#### 元素层

```objectivec
/**
 * 所有要加入 ZZFLEXAngel、ZZFLEX*ViewController 的 view/cell 都要实现此协议
 *
 * 除获取大小/高度两个方法需要二选一之外，其余都可按需选择实现
 */

@protocol ZZFlexibleLayoutViewProtocol <NSObject>

@optional;
/**
 * 获取cell/view大小，与viewHeightByDataModel二选一
 * 仅 CollectionView 可选择使用
 *
 * 调用时机：添加到ZZFlexibleLayoutViewController或ZZFLEXAngel时，如实现仅调用一次后，大小会缓存在ViewModel中。
 * 其他：如需更新视图大小，需手动调用update方法
 * 小Tips：0至-1表示比例，如size.width=-0.5时，表示视图的宽度为列表宽度的50%
 */
+ (CGSize)viewSizeByDataModel:(id)dataModel;

/**
 * 获取cell/view高度，与viewSizeByDataModel二选一
 * CollectionView、TableView 均可选择使用
 *
 * 调用时机：添加到ZZFlexibleLayoutViewController或ZZFLEXAngel时，如实现仅调用一次后，高度会缓存在ViewModel中。
 * 其他：如需更新视图高度，需手动调用update方法
 * 小Tips：CollectionView也可用此方法，宽度默认为-1，即列表宽度
 */
+ (CGFloat)viewHeightByDataModel:(id)dataModel;

/**
 * 设置cell/view的数据源
 *
 * 调用时机：cellForRowAtIndexPath或者cellForItemAtIndexPath，如实现每次都会调用
 * 小Tips：如果模型未变化时不需要更新UI，建议在此方法执行时做判断直接return
 */
- (void)setViewDataModel:(id)dataModel;

/**
 * 设置cell/view的delegate对象
 *
 * 调用时机：cellForRowAtIndexPath或者cellForItemAtIndexPath，如实现每次都会调用
 */
- (void)setViewDelegate:(id)delegate;

/**
 * 设置cell/view的actionBlock
 *
 * 调用时机：cellForRowAtIndexPath或者cellForItemAtIndexPath，如实现每次都会调用
 */
- (void)setViewEventAction:(id (^)(NSInteger actionType, id data))eventAction;

/**
 * 当前视图的indexPath，所在section元素数（目前仅cell调用）
 *
 * 调用时机：cellForRowAtIndexPath或者cellForItemAtIndexPath，如实现每次都会调用
 * 小Tips：可用于UI差异化设置等，不建议cell持有indexPath，因为可能会经常变
 */
- (void)onViewPositionUpdatedWithIndexPath:(NSIndexPath *)indexPath sectionItemCount:(NSInteger)count;

@end
```


### Foundation 扩展

```objectivec
/// 创建style
NSMutableParagraphStyle *style = NSMutableParagraphStyle.zz_create.lineSpacing(5).paragraphSpacing(20).alignment(NSTextAlignmentCenter).lineBreakMode(NSLineBreakByWordWrapping).object;
/// 编辑style(整段缩进、右侧缩进或显示宽度)
style.zz_setup.headIndent(20).tailIndent(20);

/// 创建富文本
NSMutableAttributedString *attrStr = NSMutableAttributedString.zz_create(@"富文本字符串").backgroundColor([UIColor redColor]).font([UIFont systemFontOfSize:32]).foregroundColor([UIColor redColor]).object;
/// 编辑富文本(拼接图片、字符串、富文本等)
attrStr.zz_setup.paragraphStyle(style).appendString(@"拼接字符串").appendImage([UIImage imageNamed:@"要拼接的图片名称.png"], CGRectMake(0, 0, 20, 20));
/// 设置富文本属性（带range）
attrStr.zz_setup.fontWithRange([UIFont boldSystemFontOfSize:12], NSMakeRange(0, 10)).foregroundColorWithRange([UIColor blueColor], NSMakeRange(0, 10));
```

### UIView 扩展：分割线、圆角

```objectivec
/*
 添加分割线
 type：支持top、bottom、left、right、centerX、centerY，同一view可执行多个addSeparatorz操作
 beginAt：开始位置偏移，默认0
 endAt: 结束位置偏移，默认0；也可设置length属性代替
 offset: 具type类型偏移，默认0（-10为距离底端10）
 */
self.view.addSeparator(ZZSeparatorPositionTop).color([UIColor grayColor]).beginAt(15).endAt(-15).offset(-10);
self.view.removeSeparator(ZZSeparatorPositionTop);

/*
 设置圆角
 cornor: 支持topLeft、topRight、bottomLeft、bottomRight、top、bottom、left、right、all，支持逻辑或
 */
self.view.setCornor(ZZCornerPositionLeft | ZZCornerPositionTop).radius(5.0f).color([UIColor grayColor]).borderWidth(1);
self.view.removeCornor();
```

### UIView 扩展：UIButton

```objectivec
UIButton *button = self.view.addButton(1)
// 设置字体，各状态字色、背景色
.titleFont([UIFont systemFontOfSize:12])
.titleColor([UIColor blackColor]).titleColorHL([UIColor redColor]).titleColorDisabled([UIColor grayColor])
.backgroundColor([UIColor whiteColor]).backgroundColorHL([UIColor yellowColor])
// 设置圆角、阴影
.cornerRadius(5.0f).shadow(CGSizeZero, 5.0f, [UIColor grayColor], 1.0)
// 设置事件
.eventTouchDown(^ (UIButton *sender) {
    NSLog(@"touch down");
})
.eventTouchUpInside(^ (UIButton *sender) {
    NSLog(@"touch inside");
})
// 设置约束（Masonry）
.masonry(^ (UIButton *sender, MASConstraintMaker *make) {
    make.left.top.mas_equalTo(0);
    make.size.mas_equalTo(CGSizeMake(80, 30));
})
.view;


// 编辑控件的属性
button.zz_setup.title(@"hello").titleFont([UIFont boldSystemFontOfSize:32]);


// 单独创建一个控件，而不添加到视图上
UIButton *button = UIButton.zz_create(1001).title(@"hello").titleHL(@"world").view;
```

### UIView 扩展：UIImage

```objectivec
// 右侧箭头图片
self.contentView.addImageView(2)
.image([UIImage imageNamed:@"arrow"])
.masonry(^(UIView *senderView, MASConstraintMaker *make) {
    make.right.mas_equalTo(-15);
    make.centerY.mas_equalTo(0);
});
```

### UIView 扩展：UILabel

```objectivec
self.titleLabel = self.contentView.addLabel(1)
.font([UIFont boldSystemFontOfSize:16])
.textColor([UIColor blackColor])
.masonry(^(UIView *senderView, MASConstraintMaker *make) {
    make.left.mas_equalTo(15);
    make.centerY.mas_equalTo(0);
    make.right.mas_lessThanOrEqualTo(-35);
})
.view;
```




