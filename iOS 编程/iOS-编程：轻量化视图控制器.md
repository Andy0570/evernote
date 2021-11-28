> ⭐️⭐️⭐️
>
> 这是一篇轻量化视图控制器的笔记，针对原文有所更改，加了自己的一些理解。
>
> 参考的文章如下：
>
> * [更轻量的 View Controllers @ObjC 中国](https://www.objccn.io/issue-1-1/)
> * [整洁的 Table View 代码 @ObjC 中国](https://www.objccn.io/issue-1-2/)
> * [View Controller 容器 @ObjC中国](https://objccn.io/issue-1-4/)
>
> 也推荐给你，建议对着[源码](https://github.com/objcio/issue-1-lighter-view-controllers)斟酌推敲，确实大有裨益。


# 整洁的 Table View 代码

## Table View Controller 的限制

UITableViewController 对象 的 view 属性永远都是一个 tableView。如果你稍后决定在 tableView 旁边显示一些东西（比如一个地图），或者在视图底部显示一个固定位置的 Button 控件，这时候 UITableViewController  就无能为力了，要实现该需求只能把父类改成 UIViewController，然后把 tableView 设置为 subView ，再调整子视图之间的布局...总之，就是新建一个类，全部推倒重来。

### 使用 Child View Controllers

和完全抛弃 UITableViewController 对象不同，你还可以将它作为 child view controller 添加到其他 view controller 中（[关于此话题的文章](http://objccn.io/issue-1-4)）。这样，parent view controller 在管理其他的你需要的新加的界面元素的同时，table view controller 还可以继续管理它的 tableView。

```objectivec
- (void)addPhotoDetailsTableView {
  
    DetailsViewController *details = [[DetailsViewController alloc] init];
    details.photo = self.photo;
    details.delegate = self;
    [self addChildViewController:details];
  
    CGRect frame = self.view.bounds;
    frame.origin.y = 110;
    details.view.frame = frame;
    [self.view addSubview:details.view];
  
    [details didMoveToParentViewController:self];
}
```

❇️ 优化原理：把 自定义的 **UITableViewController** 子类对象设置为视图控制器的 `childViewController`。

如果你使用这个解决方案，你就必须在 child view controller 和 parent view controller 之间建立消息传递的渠道。比如，如果用户选择了一个 tableView 中的 cell，parent view controller 需要知道这个事件来推入其他 view controller。根据使用习惯，**通常最清晰的方式是为这个 table view controller 定义一个 delegate protocol，然后到 parent view controller 中去实现**。

❇️ 当选择 DetailsViewController 中的某一行时，通过 Delegate 让 parentViewController 中去实现具体的行为方法：

```objectivec
// ============================== 
// DetailsViewController.h
#import <UIKit/UIKit.h>
@class Photo;

@protocol DetailsViewControllerDelegate
- (void)didSelectPhotoAttributeWithKey:(NSString *)key;
@end

@interface DetailsViewController : UITableViewController
@property (nonatomic, strong) Photo *photo;
@property (nonatomic, weak) id <DetailsViewControllerDelegate> delegate;
@end

  
// ============================== 
// DetailsViewController.h
@implementation DetailsViewController
  
// ...
  
#pragma mark UITableViewDelegate
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
    NSString *key = self.keys[(NSUInteger) indexPath.row];
    [self.delegate didSelectPhotoAttributeWithKey:key];
}


// ============================== 
// parentViewController 的实现文件
@interface PhotoViewController () <DetailsViewControllerDelegate>
@end

@implementation PhotoViewController

// ...
  
#pragma mark DetailsViewControllerDelegate
- (void)didSelectPhotoAttributeWithKey:(NSString *)key
{
    // 实现点击 childViewController 某一行后的行为
}
```



## 分离关注点（Separating Concerns）

### 搭建 Model 对象和 Cell 之间的桥梁

在 Cell 上显示 Model 层数据，通常的做法是这样的：

```objectivec
// 通常做法：
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    PhotoCell *cell = [tableView dequeueReusableCellWithIdentifier:@"PhotoCell"
                                                      forIndexPath:indexPath];
    Photo *photo = items[indexPath.row];
    cell.photoTitleLabel.text = photo.name;
    NSString* date = [self.dateFormatter stringFromDate:photo.creationDate];
    cell.photoDateLabel.text = date;
}
```

但是这样的代码会让 data source 变得混乱，因为它向 data source 暴露了 Cell 的设计。

❇️❇️ 优化原理：为 Cell 创建一个范畴（**Category**）类来配置 Model：

```objectivec
// ============================== 
// PhotoCell+ConfigureForPhoto.h
#import <Foundation/Foundation.h>
#import "PhotoCell.h"
@class Photo;

@interface PhotoCell (ConfigureForPhoto)
// Apple 官方的推荐的做法：为避免方法冲突，在 Category 类中的方法要加前缀_
- (void)hql_configureForPhoto:(Photo *)photo;
@end

  
// ============================== 
// PhotoCell+ConfigureForPhoto.m
#import "PhotoCell+ConfigureForPhoto.h"
#import "Photo.h"

@implementation PhotoCell (ConfigureForPhoto)

- (void)hql_configureForPhoto:(Photo *)photo {
    self.photoTitleLabel.text = photo.name;
    self.photoDateLabel.text =
        [self.dateFormatter stringFromDate:photo.creationDate];
}

- (NSDateFormatter *)dateFormatter {
    static NSDateFormatter *dateFormatter;
    if (!dateFormatter) {
        dateFormatter = [[NSDateFormatter alloc] init];
        dateFormatter.timeStyle = NSDateFormatterMediumStyle;
        dateFormatter.dateStyle = NSDateFormatterMediumStyle;
    }
    return dateFormatter;
}

@end
```

有了上述代码后，视图控制器中的 data source 方法就变得简单了：

```objectivec
// 优化做法：
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    PhotoCell *cell = [tableView dequeueReusableCellWithIdentifier:@"PhotoCell"
                                                      forIndexPath:indexPath];
    Photo *photo = items[indexPath.row];
    [cell hql_configureForPhoto:photo];
    return cell;
}
```



### 让 Cell 可复用

>❇️
>
>关于如何让 cell 可以接收任何类型的 Model  一开始没理解
>
>参考了人家的笔记：[objc.io 学习笔记-ViewController瘦身记](http://devtian.me/2015/03/23/objc-io-studynotes-ViewController-slim/)

有时多种 model 对象需要用同一类型的 cell 来表示，这种情况下，我们可以进一步让 cell 可以复用。

首先，我们给 cell 定义一个 protocol，需要用这个 cell 显示的对象必须遵循这个 protocol。

```objectivec
@protocol HQLTableViewCellConfigureDelegate <NSObject>
- (NSString *)imageName;
- (NSString *)mainTitle;
- (NSString *)detailTitle;
@end
```

然后简单修改 category 中的设置方法，让它可以接受遵循这个 protocol 的任何对象。

```objectivec
- (void)setContent:(id<CellConfigDelegate>)model;
```
需要使用该 cell 的 Model 就必须遵守 cell 中的 Delegate ，返回设置 cell 所需要的模型对象：

```objectivec
// HQLModel.h
#import <Foundation/Foundation.h>
#import "HQLTableViewCell.h"

@interface HQLModel : NSObject <HQLTableViewCellConfigureDelegate>

@property (nonatomic, copy) NSString *icon;
@property (nonatomic, copy) NSString *name;
@property (nonatomic, copy) NSString *ModelDescription;

@end

// HQLModel.m
#import "HQLModel.h"

@implementation HQLModel

#pragma mark - HQLTableViewCellConfigureDelegate

- (NSString *)imageName {
    return _icon;
}

- (NSString *)mainTitle {
    return _name;
}

- (NSString *)detailTitle {
    return _ModelDescription;
}

@end
```

这些简单的步骤让 cell 和任何特殊的 model 对象之间得以解耦，让它可适应不同的数据类型。



### 在 Cell 内部控制 Cell 的状态

如果你想自定义 tableView 默认的高亮或选择行为，你可以实现两个 delegate 方法，把点击的 cell 修改成我们想要的样子。例如：

```objectivec
// 通常做法，在视图控制器中修改点击选择 cell 的高亮行为
- (void)tableView:(UITableView *)tableView
        didHighlightRowAtIndexPath:(NSIndexPath *)indexPath {
    PhotoCell *cell = [tableView cellForRowAtIndexPath:indexPath];
    cell.photoTitleLabel.shadowColor = [UIColor darkGrayColor];
    cell.photoTitleLabel.shadowOffset = CGSizeMake(3, 3);
}

- (void)tableView:(UITableView *)tableView
        didUnhighlightRowAtIndexPath:(NSIndexPath *)indexPath {
    PhotoCell *cell = [tableView cellForRowAtIndexPath:indexPath];
    cell.photoTitleLabel.shadowColor = nil;
}
```

然而，这两个 delegate 方法的实现又基于 view controller 知晓 cell 实现的具体细节。如果我们想替换或重新设计 cell，我们必须改写 delegate 代码。View 的实现细节和 delegate 的实现交织在一起了。我们应该把这些细节移到 cell 自身中去。

❇️ 优化原理：让 cell 自己的 .m 文件中去实现修改 Cell 状态的方法 ，来达到分离代码的目的。

```objectivec
@implementation PhotoCell
// ...
- (void)setHighlighted:(BOOL)highlighted animated:(BOOL)animated {
    [super setHighlighted:highlighted animated:animated];
    if (highlighted) {
        self.photoTitleLabel.shadowColor = [UIColor darkGrayColor];
        self.photoTitleLabel.shadowOffset = CGSizeMake(3, 3);
    } else {
        self.photoTitleLabel.shadowColor = nil;
    }
}
```

总的来说，我们在努力**把 view 层和 controller 层的实现细节分离开**。delegate 肯定得清楚一个 view 该显示什么状态，但是它不应该了解如何修改 view 结构或者给某些 subviews 设置某些属性以获得正确的状态。所有这些逻辑都应该封装到 view 内部，然后给外部提供一个简单的 API。



### 控制多个 Cell 类型

如果一个 tableView 里面有多种类型的 cell，data source 方法很快就难以控制了。在我们示例程序中，DetailsViewController 有两种不同类型的 cell：一种用于显示几个星，另一种用来显示一个键值对。为了划分处理不同 cell 类型的代码，data source 方法简单地**通过判断 cell 的类型，把任务派发给其他指定的方法**。

❇️ 优化原理：看源码就很容易理解，根据不同类型的 Cell ，配置方法分开写在指定的方法中：

```objectivec
- (UITableViewCell *)tableView:(UITableView *)tableView
         cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    NSString *key = self.keys[(NSUInteger) indexPath.row];
    id value = [self.photo valueForKey:key];
    UITableViewCell *cell;
    if ([key isEqual:PhotoRatingKey]) {
        cell = [self cellForRating:value indexPath:indexPath];
    } else {
        cell = [self detailCellForKey:key value:value];
    }
    return cell;
}

- (RatingCell *)cellForRating:(NSNumber *)rating
                    indexPath:(NSIndexPath *)indexPath
{
    // ...
}

- (UITableViewCell *)detailCellForKey:(NSString *)key
                                value:(id)value
{
    // ...
}
```



## 总结

Table view controllers（以及其他的 controller 对象）应该在 model 和 view 对象之间扮演[协调者和调解者的角色](http://developer.apple.com/library/mac/#documentation/General/Conceptual/DevPedia-CocoaCore/ControllerObject.html)。它不应该关心明显属于 view 层或 model 层的任务。你应该始终记住这点，这样 delegate 和 data source 方法会变得更小巧，最多包含一些简单的样板代码。

这不仅减少了 table view controllers 的大小和复杂性，而且还把业务逻辑和 view 的逻辑放到了更合适的地方。Controller 层的里里外外的实现细节都被封装成了简单的 API，最终，它变得更加容易理解，也更利于团队协作。



# 更轻量的 View Controllers

## 把 Data Source 和其他 Protocols 分离出来

> ❇️ ❇️ 
>
> 之前的理解有些偏差，我明白作者轻量化 table view controller 的目的是为了复用。
>
> 之前以为是复用 控制器 类；
>
> 后来才明白是为了复用 **UITableViewDataSource** 协议的两个实现方法：
>
> * tableView:numberOfRowsInSection:
> * tableView:cellForRowAtIndexPath:
>
> 把 **UITableViewDataSource** 协议需要实现的两个方法写在一个单独的类（创建一个 ArrayDataSource 类作为通用的数据源类）中。我们就不用在每个含有 tableView 的 ViewController 里边都实现一遍 **UITableViewDataSource** 协议了，只要创建 ArrayDataSource 类并传入指定的几个参数。

把 `UITableViewDataSource` 的代码提取出来放到一个单独的类中，是为 view controller 瘦身的强大技术之一。

示例项目中，有个 `PhotosViewController` 类，它有以下几个方法：

```objectivec
// 通常的做法：
# pragma mark - UITableViewDataSource

- (Photo*)photoAtIndexPath:(NSIndexPath*)indexPath {
    return photos[(NSUInteger)indexPath.row];
}

- (NSInteger)tableView:(UITableView*)tableView
 numberOfRowsInSection:(NSInteger)section {
    return photos.count;
}

- (UITableViewCell*)tableView:(UITableView*)tableView
        cellForRowAtIndexPath:(NSIndexPath*)indexPath {
    PhotoCell* cell =
        [tableView dequeueReusableCellWithIdentifier:PhotoCellIdentifier
                                        forIndexPath:indexPath];
    Photo* photo = [self photoAtIndexPath:indexPath];
    cell.label.text = photo.name;
    return cell;
}
```

这些代码基本都是围绕数组做一些事情，更针对地说，是围绕 view controller 所管理的 photos 数组做一些事情。我们可以尝试把数组相关的代码移到[单独的类](https://github.com/objcio/issue-1-lighter-view-controllers/blob/master/PhotoData/ArrayDataSource.h)中。我们使用一个 block 来设置 cell，也可以用 delegate 来做这件事，这取决于你的习惯。

```objectivec
// ============================== 
// ArrayDataSource.h
#import <Foundation/Foundation.h>

/**
 配置cell的Block对象

 @param cell cell 对象类
 @param item item 模型类
 */
typedef void (^TableViewCellConfigureBlock)(id cell, id item);

@interface ArrayDataSource : NSObject <UITableViewDataSource>


/**
 ArrayDataSource 的指定初始化方法

 @param anItems             参数一：数组模型
 @param aCellIdentifier     参数二：cell复用标识符
 @param aConfigureCellBlock 参数三：配置cell的Block对象
 @return 返回值：ArrayDataSource实例对象
 */
- (id)initWithItems:(NSArray *)anItems
     cellIdentifier:(NSString *)aCellIdentifier
 configureCellBlock:(TableViewCellConfigureBlock)aConfigureCellBlock;


/**
 根据 index 找到 item 模型
 
 这是一个公共方法，用到的地方主要有两处：
 1.配置 cell 时，根据 index 索引显示 model 数据；
 2.当用户点击 cell 时，根据 index 索引 找到对应的 model 作出响应
 
 @param indexPath index 索引
 @return item 模型
 */
- (id)itemAtIndexPath:(NSIndexPath *)indexPath;

@end


// ============================== 
// ArrayDataSource.m
#import "ArrayDataSource.h"

@interface ArrayDataSource ()

@property (nonatomic, strong) NSArray *items;
@property (nonatomic, copy) NSString *cellIdentifier;
@property (nonatomic, copy) TableViewCellConfigureBlock configureCellBlock;

@end


@implementation ArrayDataSource


#pragma mark - Init

- (id)init {
    return nil;
}

- (id)initWithItems:(NSArray *)anItems
     cellIdentifier:(NSString *)aCellIdentifier
 configureCellBlock:(TableViewCellConfigureBlock)aConfigureCellBlock
{
    self = [super init];
    if (self) {
        self.items = anItems;
        self.cellIdentifier = aCellIdentifier;
        self.configureCellBlock = [aConfigureCellBlock copy];
    }
    return self;
}


#pragma mark - Public

- (id)itemAtIndexPath:(NSIndexPath *)indexPath {
    return self.items[(NSUInteger) indexPath.row];
}


#pragma mark UITableViewDataSource

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return self.items.count;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell =
        [tableView dequeueReusableCellWithIdentifier:self.cellIdentifier
                                        forIndexPath:indexPath];
    id item = [self itemAtIndexPath:indexPath];
    // 调用 Block 方法配置 cell
    self.configureCellBlock(cell, item);
    return cell;
}

@end
```

现在，你可以把 view controller 中的这 3 个方法去掉了，取而代之，你可以创建一个 `ArrayDataSource` 类的实例作为 table view 的 data source。

```objectivec
- (void)setupTableView {
    // 参数一：数据源数组
    NSArray *photos = [AppDelegate sharedDelegate].store.sortedPhotos;
    // 参数三：配置 cell 的 Block 对象
    TableViewCellConfigureBlock configureCell = ^(PhotoCell *cell, Photo *photo) {
        // 内部实现的是 cell 的范畴方法：根据 model 显示 cell
        [cell configureForPhoto:photo];
    };
    self.photosArrayDataSource =
        [[ArrayDataSource alloc] initWithItems:photos
                                cellIdentifier:PhotoCellIdentifier
                            configureCellBlock:configureCell];
    self.tableView.dataSource = self.photosArrayDataSource;
    
    [self.tableView registerNib:[PhotoCell nib]
         forCellReuseIdentifier:PhotoCellIdentifier];
}
```

现在你不用担心把一个 index path 映射到数组中的位置了，每次你想把这个数组显示到一个 table view 中时，你都可以复用这些代码。你也可以实现一些额外的方法，比如 `tableView:commitEditingStyle:forRowAtIndexPath:`，在 table view controllers 之间共享。

此外，这种方法也可以扩展到其他 protocols 上面。最明显的一个就是 `UICollectionViewDataSource`。这给了你极大的灵活性；如果在开发的某个时候，你想用 `UICollectionView` 代替 `UITableView`，你几乎不需要对 view controller 作任何修改。你甚至可以让你的 data source 同时支持这两个协议。



## 将业务逻辑移到 Model 中

❇️ ❇️ 优化原理：把处理<u>业务逻辑</u>的代码移动到 Model 类的 category 中会变得更加清晰。



## 创建 Store 类

❇️ ❇️优化原理：Store 对象会关心<u>数据加载</u>、<u>缓存</u>和设置<u>数据栈</u>。它也经常被称为**服务层**或者**仓库**。通过分离，我们就可以复用这些代码，单独测试他们，并且让 view controller 保持小巧。



## 把网络请求逻辑移到 Model 层

和上面的主题相似：不要在 view controller 中做网络请求的逻辑。取而代之，你应该将它们封装到另一个类中。这样，你的 view controller 就可以在之后通过使用回调（比如一个 completion 的 block）来请求网络了。这样的好处是，缓存和错误控制也可以在这个类里面完成。

❇️ ❇️ 优化原理：就是把处理网络请求的方法封装在单独的类库中，比如猿题库的开源框架 [YTKNetwork](https://github.com/yuantiku/YTKNetwork)。



## 把 View 代码移到 View 层

不应该在 view controller 中构建复杂的 view 层次结构。你可以使用 Interface Builder 或者把 views 封装到一个 `UIView` 子类当中。例如，如果你要创建一个选择日期的控件，把它放到一个名为 `DatePickerView` 的类中会比把所有的事情都在 view controller 中做好好得多。再一次，这样增加了可复用性并保持了简单。



## 总结

我们已经看到一些用来创建更小巧的 view controllers 的技术。我们并不是想把这些技术应用到每一个可能的角落，只是我们有一个目标：**写可维护的代码。**知道这些模式后，我们就更有可能把那些笨重的 view controllers 变得更整洁。
