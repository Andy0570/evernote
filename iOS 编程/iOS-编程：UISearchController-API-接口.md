## UISearchBar

```objectivec
@interface UISearchBar : UIView <UIBarPositioning, UITextInputTraits>

// 创建
- (instancetype)init;
- (instancetype)initWithFrame:(CGRect)frame;
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder;

// 设置
// 文字内容
@property(nullable,nonatomic,copy)   NSString   *text;       // current/starting search text
@property(nullable,nonatomic,copy)   NSString   *prompt;      // default is nil
@property(nullable,nonatomic,copy)   NSString   *placeholder;      // default is nil

// 样式设置
@property(nonatomic)  UIBarStyle barStyle;  // default is UIBarStyleDefault (blue)
@property (nonatomic) UISearchBarStyle searchBarStyle; //searchBarStyle样式
  typedef enum UISearchBarStyle : NSUInteger {
      UISearchBarStyleDefault,
      UISearchBarStyleProminent,
      UISearchBarStyleMinimal
  } UISearchBarStyle;

@property(null_resettable, nonatomic,strong) UIColor *tintColor;
@property(nullable, nonatomic,strong) UIColor *barTintColor;  // default is nil
@property(nonatomic,assign,getter=isTranslucent) BOOL translucent;  // 设置是否半透明

- (void)setBackgroundImage:(nullable UIImage *)backgroundImage forBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics;
- (nullable UIImage *)backgroundImageForBarPosition:(UIBarPosition)barPosition barMetrics:(UIBarMetrics)barMetrics;
@property(nullable, nonatomic,strong) UIImage *backgroundImage;

- (void)setSearchFieldBackgroundImage:(nullable UIImage *)backgroundImage forState:(UIControlState)state;
- (nullable UIImage *)searchFieldBackgroundImageForState:(UIControlState)state;
@property(nonatomic) UIOffset searchFieldBackgroundPositionAdjustment;
@property(nonatomic) UIOffset searchTextPositionAdjustment;

// UISearchBarIcon(searchBar上的几个按钮)
  typedef enum UISearchBarIcon : NSInteger {
      UISearchBarIconSearch,
      UISearchBarIconClear,
      UISearchBarIconBookmark,
      UISearchBarIconResultsList
  } UISearchBarIcon;

// 各个按钮的图标
- (void)setImage:(nullable UIImage *)iconImage forSearchBarIcon:(UISearchBarIcon)icon state:(UIControlState)state;
// 各个按钮的位置
- (nullable UIImage *)imageForSearchBarIcon:(UISearchBarIcon)icon state:(UIControlState)state;

// 按钮的位置
- (void)setPositionAdjustment:(UIOffset)adjustment forSearchBarIcon:(UISearchBarIcon)icon;
- (UIOffset)positionAdjustmentForSearchBarIcon:(UISearchBarIcon)icon;

// 自定义显示
@property (nullable, nonatomic, readwrite, strong) UIView *inputAccessoryView;
@property (nonatomic, readonly, strong) UITextInputAssistantItem *inputAssistantItem NS_AVAILABLE_IOS(9_0) ; //Shown on top of the keyboard when search is engaged.

// Button 按钮
@property(nonatomic)  BOOL showsBookmarkButton;   // default is NO
@property(nonatomic)  BOOL showsCancelButton;       // default is NO
- (void)setShowsCancelButton:(BOOL)showsCancelButton animated:(BOOL)animated;
@property(nonatomic)  BOOL showsSearchResultsButton; // default is NO
@property(nonatomic, getter=isSearchResultsButtonSelected) BOOL searchResultsButtonSelected; // default is NO

// ScopeBar
@property(nonatomic) BOOL       showsScopeBar; // default is NO. if YES, shows the scope bar. call sizeToFit: to update frame
@property(nullable, nonatomic,copy) NSArray<NSString *>   *scopeButtonTitles ; // array of NSStrings. no scope bar shown unless 2 or more items
@property(nonatomic) NSInteger  selectedScopeButtonIndex; // index into array of scope button titles. default is 0. ignored if out of range

- (void)setScopeBarButtonBackgroundImage:(nullable UIImage *)backgroundImage forState:(UIControlState)state;
- (nullable UIImage *)scopeBarButtonBackgroundImageForState:(UIControlState)state;
@property(nullable, nonatomic,strong) UIImage *scopeBarBackgroundImage;

- (void)setScopeBarButtonDividerImage:(nullable UIImage *)dividerImage forLeftSegmentState:(UIControlState)leftState rightSegmentState:(UIControlState)rightState;
- (nullable UIImage *)scopeBarButtonDividerImageForLeftSegmentState:(UIControlState)leftState rightSegmentState:(UIControlState)rightState;

- (void)setScopeBarButtonTitleTextAttributes:(nullable NSDictionary<NSString *, id> *)attributes forState:(UIControlState)state;
- (nullable NSDictionary<NSString *, id> *)scopeBarButtonTitleTextAttributesForState:(UIControlState)state;

@property(nullable,nonatomic,weak) id<UISearchBarDelegate> delegate;

@end
```

> 需要注意的是，**UISearchController** 在使用的时候，需要设置为全局的变量或者控制器属性，使其生命周期与控制器相同；如果设置为局部变量，会提前销毁，导致无法使用。



## UISearchBarDelegate

```objectivec
// return NO to not become first responder
- (BOOL)searchBarShouldBeginEditing:(UISearchBar *)searchBar{
    NSLog(@"%s",__func__);
    return YES;
}

// called when text starts editing
- (void)searchBarTextDidBeginEditing:(UISearchBar *)searchBar {
    NSLog(@"%s",__func__);
    NSLog(@"searchBar.text = %@",searchBar.text);
}

// return NO to not resign first responder
- (BOOL)searchBarShouldEndEditing:(UISearchBar *)searchBar {
    NSLog(@"%s",__func__);
    return YES;
}

// called when text ends editing
- (void)searchBarTextDidEndEditing:(UISearchBar *)searchBar {
    NSLog(@"%s",__func__);
    NSLog(@"searchBar.text = %@",searchBar.text);
}

// called before text changes
- (BOOL)searchBar:(UISearchBar *)searchBar shouldChangeTextInRange:(NSRange)range replacementText:(NSString *)text {
    NSLog(@"%s",__func__);
    NSLog(@"searchBar.text = %@",searchBar.text);
    return YES;
}

// called when text changes (including clear)
- (void)searchBar:(UISearchBar *)searchBar textDidChange:(NSString *)searchText {
    NSLog(@"%s",__func__);
    NSLog(@"searchBar.text = %@",searchBar.text);
}

// called when keyboard search button pressed 键盘搜索按钮
- (void)searchBarSearchButtonClicked:(UISearchBar *)searchBar {
    NSLog(@"%s",__func__);
    NSLog(@"searchBar.text = %@",searchBar.text);
}

// called when bookmark button pressed
- (void)searchBarBookmarkButtonClicked:(UISearchBar *)searchBar{
    NSLog(@"%s",__func__);
}

// called when cancel button pressed
- (void)searchBarCancelButtonClicked:(UISearchBar *)searchBar{
    NSLog(@"%s",__func__);
}

// called when search results button pressed
- (void)searchBarResultsListButtonClicked:(UISearchBar *)searchBar{
    NSLog(@"%s",__func__);
}

// selecte ScopeButton
- (void)searchBar:(UISearchBar *)searchBar selectedScopeButtonIndexDidChange:(NSInteger)selectedScope {
    NSLog(@"%s",__func__);
    NSLog(@"selectedScope = %ld",selectedScope);
}
```



## UISearchController

```objectivec
@interface UISearchController : UIViewController <UIViewControllerTransitioningDelegate, UIViewControllerAnimatedTransitioning>NS_CLASS_AVAILABLE_IOS(8_0)

// 初始化方法, 参数是展示搜索结果的控制器, 如果是在当前控制器展示搜索结果, 就传nil
- (instancetype)initWithSearchResultsController:(nullable UIViewController *)searchResultsController;
// 负责更新搜索结果的代理, 需要遵循 UISearchResultsUpdating 协议
@property (nullable, nonatomic, weak) id <UISearchResultsUpdating> searchResultsUpdater;

// 搜索控制器是否是活跃状态, 当在一个控制器展示搜索结果的时候, 可以此来判断返回的数据源
@property (nonatomic, assign, getter = isActive) BOOL active;

// 控制器代理  遵循 UISearchControllerDelegate协议
@property (nullable, nonatomic, weak) id <UISearchControllerDelegate> delegate;

// 当搜索框激活时, 是否添加一个半透明背景视图，如果在当前控制器展示结果, 就不需要这个透明视图
@property (nonatomic, assign) BOOL dimsBackgroundDuringPresentation __TVOS_PROHIBITED; 
@property (nonatomic, assign) BOOL obscuresBackgroundDuringPresentation NS_AVAILABLE_IOS(9_1); // default is YES

// 当搜索框激活时, 是否隐藏导航栏
@property (nonatomic, assign) BOOL hidesNavigationBarDuringPresentation; // default is YES

@property (nullable, nonatomic, strong, readonly) UIViewController *searchResultsController;
@property (nonatomic, strong, readonly) UISearchBar *searchBar;

@end
```





## UISearchResultsUpdating

```objectivec
#pragma mark UISearchResultsUpdating

// 每次更新搜索框里的文字，就会调用这个方法
// Called when the search bar's text or scope has changed or when the search bar becomes first responder.
// 根据输入的关键词及时响应：里面可以实现筛选逻辑  也显示可以联想词
- (void)updateSearchResultsForSearchController:(UISearchController *)searchController {
    NSLog(@"%s",__func__);
    // 获取搜索框里地字符串
    NSString *searchString = searchController.searchBar.text;
  
    //一般在这里实现搜索的逻辑 & 展示搜索结果
}
```

### UISearchControllerDelegate

```objectivec
#pragma mark UISearchControllerDelegate

// These methods are called when automatic presentation(展示结果) or dismissal（不展示结果） occurs.
// They will not be called if you present or dismiss the search controller yourself.
- (void)willPresentSearchController:(UISearchController *)searchController {
    NSLog(@"%s",__func__);
}
- (void)didPresentSearchController:(UISearchController *)searchController{
    NSLog(@"%s",__func__);
}
- (void)willDismissSearchController:(UISearchController *)searchController{
    NSLog(@"%s",__func__);
}
- (void)didDismissSearchController:(UISearchController *)searchController{
    NSLog(@"%s",__func__);
}

// Called after the search controller's search bar has agreed to begin editing or when 'active' is set to YES. If you choose not to present the controller yourself or do not implement this method, a default presentation is performed on your behalf.
- (void)presentSearchController:(UISearchController *)searchController {
    NSLog(@"%s",__func__);
}
```


