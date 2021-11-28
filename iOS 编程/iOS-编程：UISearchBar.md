## UISearchBar 简介

UISearchBar 继承关系：

![](https://upload-images.jianshu.io/upload_images/2648731-3eed403b9b1bf7b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### UISearchBar 样式 

```objectivec
@property (nonatomic) UISearchBarStyle searchBarStyle;
```

枚举类型，可选值如下：

```objectivec
typedef NS_ENUM(NSUInteger, UISearchBarStyle) {
    UISearchBarStyleDefault,    // currently UISearchBarStyleProminent
    UISearchBarStyleProminent,  // used my Mail, Messages and Contacts，显示背景
    UISearchBarStyleMinimal     // used by Calendar, Notes and Music，不显示背景
} API_AVAILABLE(ios(7.0));
```



### UIBarStyle，搜索框样式

```objectivec
@property(nonatomic) UIBarStyle barStyle;
```

搜索框样式是一个枚举类型，默认值为 `UIBarStyleDefault` (蓝色)

```objectivec
typedef NS_ENUM(NSInteger, UIBarStyle) {
    UIBarStyleDefault          = 0, // 白色搜索框，灰色背景
    UIBarStyleBlack            = 1, // 黑色搜索框，
    
    UIBarStyleBlackOpaque API_DEPRECATED("Use UIBarStyleBlack instead.", ios(2.0, 13.0)) = 1,
    UIBarStyleBlackTranslucent API_DEPRECATED("Use UIBarStyleBlack and set the translucent property to YES instead.", ios(2.0, 13.0)) = 2,
} API_UNAVAILABLE(tvos);
```



### UISearchBar 的初始化方法

```objectivec
- (instancetype)init;
- (instancetype)initWithFrame:(CGRect)frame NS_DESIGNATED_INITIALIZER;
- (nullable instancetype)initWithCoder:(NSCoder *)coder NS_DESIGNATED_INITIALIZER;
```



### 设置代理

```objectivec
@property(nullable,nonatomic,weak) id<UISearchBarDelegate> delegate;
```

弱引用，默认值为 `nil`。



### 设置/获取当前搜索框的文本

```objectivec
@property(nullable,nonatomic,copy)   NSString               *text;
```



### 顶部提示文本

提示信息，是在搜索框的上面可以设置一行提示文字

```objectivec
@property(nullable,nonatomic,copy) NSString *prompt;              
```

默认值为 `nil`。



### 搜索框占位符

```objectivec
@property(nullable,nonatomic,copy) NSString *placeholder;
```

默认值为 `nil`。



### 是否显示书签按钮 📖

```objectivec
@property(nonatomic) BOOL showsBookmarkButton;
```

默认值为 `NO`。

注意：书签按钮属性与搜索回车按钮属性不能同时进行设置，只能二选一，否则会出现冲突。

### 是否显示搜索回车按钮

```objectivec
@property(nonatomic) BOOL showsSearchResultsButton
```

是否显示搜索回车按钮，默认隐藏（注意：书签按钮属性与搜索回车按钮属性不能同时进行设置，只能二选一，否则会出现冲突）



### 是否显示取消按钮

```objectivec
@property(nonatomic)  BOOL showsCancelButton;

- (void)setShowsCancelButton:(BOOL)showsCancelButton animated:(BOOL)animated
```

默认值为 `NO`。注意：通常退出按钮的显示是在代理方法中设置其显示或隐藏，而不是在初始化时就设置基显示。

**该属性在 iOS 13 中的行为**：如果该 `UISearchBar` 是由 `UISearchController` 所拥有，那么会使用设置方法 (也就是 `-setShowsCancelButton:animated:`) 隐式地设置 `UISearchController` 的 `automaticShowsCancelButton` 属性为 `NO`。



### 搜索结果按钮为选中状态

```objectivec
@property(nonatomic, getter=isSearchResultsButtonSelected) BOOL searchResultsButtonSelected API_AVAILABLE(ios(3.2)) API_UNAVAILABLE(tvos); // default is NO
```

默认是 `NO` ， 设置搜索结果按钮的选中状态。



### 搜索时，在键盘上显示辅助视图

```objectivec
/// Use this method to modify the contents of the Unified Content Bar, shown on top of the keyboard when search is engaged.
/// You may modify the returned inputAssistantItem to add to or replace the existing items on the bar.
/// Modifications made to the returned UITextInputAssistantItem are reflected automatically.
@property (nonatomic, readonly, strong) UITextInputAssistantItem *inputAssistantItem;

// 输入框成为第一响应显示自定义附件输入视图
/* Allow placement of an input accessory view to the keyboard for the search bar
 */
@property (nullable, nonatomic, readwrite, strong) UIView *inputAccessoryView;
```



### 修改搜索框上所有子控件的颜色

```objectivec
/*
 The behavior of tintColor for bars has changed on iOS 7.0. It no longer affects the bar's background
 and behaves as described for the tintColor property added to UIView.
 To tint the bar's background, please use -barTintColor.
 */
@property(null_resettable, nonatomic,strong) UIColor *tintColor;
```

注：tint color 会影响搜索框中的光标的颜色



### 设置搜索框背景颜色

```objectivec
@property(nullable, nonatomic,strong) UIColor *barTintColor;  // default is nil
```



### 设置是否半透明

```objectivec
/*
 New behavior on iOS 7.
 Default is YES.
 You may force an opaque background by setting the property to NO.
 If the search bar has a custom background image, the default is inferred
 from the alpha values of the image—YES if it has any pixel with alpha < 1.0
 If you send setTranslucent:YES to a bar with an opaque custom background image
 it will apply a system opacity less than 1.0 to the image.
 If you send setTranslucent:NO to a bar with a translucent custom background image
 it will provide an opaque background for the image using the bar's barTintColor if defined, or black
 for UIBarStyleBlack or white for UIBarStyleDefault if barTintColor is nil.
 */
@property(nonatomic,assign,getter=isTranslucent) BOOL translucent API_AVAILABLE(ios(3.0)); // Default is NO on iOS 6 and earlier. Always YES if barStyle is set to UIBarStyleBlackTranslucent
```



### 搜索栏下部的选择卡，数组里面的内容是按钮的标题

在搜索栏下方显示分组菜单。

```objectivec
// 选项按钮标题(大于等于2个, 才会显示)
@property(nullable, nonatomic,copy) NSArray<NSString *> *scopeButtonTitles;

// 默认选中的选项卡
@property(nonatomic)      NSInteger  selectedScopeButtonIndex; // index into array of scope button titles. default is 0. ignored if out of range

// 是否显示选项卡(一般不需设置)
/* New behavior on iOS 13.
 If the search bar is owned by a UISearchController, then using the setter
 for this property (as well as -setShowsScopeBar:animated:) will implicitly
 set the UISearchController's automaticallyShowsScopeBar property to NO.
 */
@property(nonatomic)      BOOL       showsScopeBar; // default is NO. if YES, shows the scope bar. call sizeToFit: to update frame

- (void)setShowsScopeBar:(BOOL)show animated:(BOOL)animate;
```



### 背景图片设置

```objectivec
// 1pt wide images and resizable images will be scaled or tiled according to the resizable area, otherwise the image will be tiled
@property(nullable, nonatomic,strong) UIImage *backgroundImage; // 搜索框的背景图
@property(nullable, nonatomic,strong) UIImage *scopeBarBackgroundImage; // 搜索框附属分栏条的背景

// !!!: 搜索框背景图片的 Setter、Getter 方法
- (void)setBackgroundImage:(nullable UIImage *)backgroundImage forBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics API_AVAILABLE(ios(7.0)) UI_APPEARANCE_SELECTOR;  // Use UIBarMetricsDefaultPrompt to set a separate backgroundImage for a search bar with a prompt
- (nullable UIImage *)backgroundImageForBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics API_AVAILABLE(ios(7.0)) UI_APPEARANCE_SELECTOR;

/* In general, you should specify a value for the normal state to be used by other states which don't have a custom value set
 */

// !!!: 搜索文本框背景图片的 Setter、Getter 方法
/* The rounded-rect search text field image. Valid states are UIControlStateNormal and UIControlStateDisabled
 */
- (void)setSearchFieldBackgroundImage:(nullable UIImage *)backgroundImage forState:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
- (nullable UIImage *)searchFieldBackgroundImageForState:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;

// !!!: 搜索框 ICON 图标
- (void)setImage:(nullable UIImage *)iconImage forSearchBarIcon:(UISearchBarIcon)icon state:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
- (nullable UIImage *)imageForSearchBarIcon:(UISearchBarIcon)icon state:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;

// !!!: ScopeBarButton 背景图片
- (void)setScopeBarButtonBackgroundImage:(nullable UIImage *)backgroundImage forState:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR; 
- (nullable UIImage *)scopeBarButtonBackgroundImageForState:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;

// !!!: 设置选择按钮视图的分割线图片
- (void)setScopeBarButtonDividerImage:(nullable UIImage *)dividerImage forLeftSegmentState:(UIControlState)leftState rightSegmentState:(UIControlState)rightState API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
- (nullable UIImage *)scopeBarButtonDividerImageForLeftSegmentState:(UIControlState)leftState rightSegmentState:(UIControlState)rightState API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
```





### 设置选择按钮视图的属性字符串标题

```objectivec
- (void)setScopeBarButtonTitleTextAttributes:(nullable NSDictionary<NSAttributedStringKey,id> *)attributes forState:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
- (nullable NSDictionary<NSAttributedStringKey, id> *)scopeBarButtonTitleTextAttributesForState:(UIControlState)state API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
```



### 搜索框中文本框的背景偏移量

```objectivec
@property(nonatomic) UIOffset searchFieldBackgroundPositionAdjustment NS_AVAILABLE_IOS(5_0) UI_APPEARANCE_SELECTOR;
```

示例：

```objectivec
self.searchFieldBackgroundPositionAdjustment = UIOffsetMake(5, 3);
```



### 搜索框中文本框的文本偏移量

```objectivec
@property(nonatomic) UIOffset searchTextPositionAdjustment NS_AVAILABLE_IOS(5_0) UI_APPEARANCE_SELECTOR;
```



### 设置搜索框图标的偏移量

```objectivec
/* To nudge the position of the icon within the search text field
 */
- (void)setPositionAdjustment:(UIOffset)adjustment forSearchBarIcon:(UISearchBarIcon)icon API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
- (UIOffset)positionAdjustmentForSearchBarIcon:(UISearchBarIcon)icon API_AVAILABLE(ios(5.0)) UI_APPEARANCE_SELECTOR;
```



### UISearchBarDelegate 协议

```objectivec
@protocol UISearchBarDelegate <UIBarPositioningDelegate>

@optional

// 将要开始编辑文本时会调用该方法，返回 NO 将不会变成第一响应者
- (BOOL)searchBarShouldBeginEditing:(UISearchBar *)searchBar;                      // return NO to not become first responder

// 开始输入文本会调用该方法
- (void)searchBarTextDidBeginEditing:(UISearchBar *)searchBar;                     // called when text starts editing

// 将要结束编辑文本时会调用该方法，返回 NO 则不让搜索框释放第一响应者
- (BOOL)searchBarShouldEndEditing:(UISearchBar *)searchBar;                        // return NO to not resign first responder

// 结束编辑文本时调用该方法
- (void)searchBarTextDidEndEditing:(UISearchBar *)searchBar;                       // called when text ends editing

// 文本改变会调用该方法（包含 clear 文本）
- (void)searchBar:(UISearchBar *)searchBar textDidChange:(NSString *)searchText;   // called when text changes (including clear)

// 文字改变前会调用该方法，返回 NO 则不能加入新的编辑文字
- (BOOL)searchBar:(UISearchBar *)searchBar shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text API_AVAILABLE(ios(3.0)); // called before text changes

// 键盘上的搜索按钮点击的会调用该方法
- (void)searchBarSearchButtonClicked:(UISearchBar *)searchBar;                     // called when keyboard search button pressed

// 搜索框右侧图书按钮点击会调用该方法
- (void)searchBarBookmarkButtonClicked:(UISearchBar *)searchBar API_UNAVAILABLE(tvos); // called when bookmark button pressed

// 点击取消按钮会调用该方法
- (void)searchBarCancelButtonClicked:(UISearchBar *)searchBar API_UNAVAILABLE(tvos);   // called when cancel button pressed

// 搜索结果列表按钮被按下会调用该方法
- (void)searchBarResultsListButtonClicked:(UISearchBar *)searchBar API_AVAILABLE(ios(3.2)) API_UNAVAILABLE(tvos); // called when search results button pressed

// 搜索框的附属按钮视图中切换按钮会调用该方法
- (void)searchBar:(UISearchBar *)searchBar selectedScopeButtonIndexDidChange:(NSInteger)selectedScope API_AVAILABLE(ios(3.0));

@end
```





## 参考

* [[iOS] 关于 UISearchBar, 看这个就够了](https://www.jianshu.com/p/328330e88be6)
