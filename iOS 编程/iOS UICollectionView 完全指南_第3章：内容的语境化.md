> 注：
> 本文翻译自 《iOS UICollectionView The Complete Guide 2nd Edition》
> 使用的翻译工具：<https://www.deepl.com/translator>

用基本单元格向用户展示内容时，可以让开发者展示一些简陋的内容。我们可以使用辅助视图（supplementary view）来为其增色。首先，我们看看一些新的委托方法来定制集合视图中各个单元格的布局。然后，将其应用并实践到上一章学到的技术中，来向用户呈现内容。我们会通过长达一章的案例研究来完成这个任务，在示例代码中，这个案例叫做 Survey。


## 辅助视图

辅助视图（supplementary view）是指随着集合视图的单元格滚动并显示该集合数据的某种信息的视图。这些都是 "数据驱动 "的视图，与我们在下一章探讨的装饰视图（decoration views）形成鲜明对比。现在，你只需要知道辅助视图为用户提供补充信息，它们必须显示一些数据，否则就是装饰视图。

数据源为集合视图提供了配置辅助视图所需的信息，但辅助视图是由`UICollectionViewLayout` 对象来布局的，iOS 自带的流式布局是通用布局的子类。对于大多数情况下，使用流式布局可以提供你所需要的所有功能和灵活性。

`UICollectionViewFlowLayout` 内置了两个辅助视图：页眉（headers）和页脚（footers）。虽然这是两个内置的集合视图单元的补充，但辅助视图可以用于的不仅仅是页眉和页脚，页眉和页脚只是辅助视图的特定情况。您将在接下来的两章中看到更多不是页眉或页脚的辅助视图的示例。

请记住，任何不是单元格的视图，并显示关于您的集合的数据或元数据，都应该是辅助视图。

辅助视图的行为与集合视图单元格类似；您可以在集合视图中注册一个类或 UINib，辅助视图也可以重复使用。然而，您还必须为辅助视图提供一个尺寸（Size）。

举例来说，假设我们有一个创业的想法，我们需要为我们的应用制作界面原型。我们要建立一个应用程序，向用户展示一个精选的照片。他们会选择一张，然后根据他们的选择，我们会向他们呈现我们认为他们可能会喜欢的新照片。这个推荐引擎完全是假的，但我们只需要对用户界面进行编码，就能获得那笔甜甜的风险投资基金。

我们的造假推荐引擎将使用两种类型的模型：

* 一个简单的名称和图像的包装器，我们称它为 `AFPhotoModel`。
* 一个包含 `AFPhotoModel` 数据的列表，用户可以从中选择数据。

每个数据列表都代表集合视图中的一组数据。我们把这第二个模型称为 `AFSelectionModel`。它们的接口如清单3.1所示，但它们是直接的。

```objc
//  AFPhotoModel.h
// AFPhotoModel 类用于指定某一个单元格
@interface AFPhotoModel : NSObject

@property (nonatomic, copy) NSString *name;
@property (nonatomic, strong) UIImage *image;

+(instancetype)photoModelWithName:(NSString *)name image:(UIImage *)image;

@end

//  AFSelectionModel.h
// AFSelectionModel 类用于指定一组单元格，以及哪个单元格被选中了
extern const NSUInteger AFSelectionModelNoSelectionIndex;

@interface AFSelectionModel : NSObject

@property (nonatomic, strong, readonly) NSArray *photoModels;
@property (nonatomic, assign) NSUInteger selectedPhotoModelIndex;
@property (nonatomic, readonly) BOOL hasBeenSelected;

+(instancetype)selectionModelWithPhotoModels:(NSArray *)photoModels;

@end
```

视图控制器将拥有一组模型对象的数组，这些对象是在私有的`setupModel` 方法中设置的。所有的照片都带有示例代码。视图控制器还知道我们当前提示用户的是哪组模型，称为`currentModelArrayIndex`。

比方说，我们想添加一个普通的辅助视图作为集合视图的 header。我们要怎么做呢？好吧，就像单元格一样，我们将创建一个名为`AFCollectionHeaderView` 的新类，只不过这个头将是`UICollectionReusableView` 的子类。这个父类提供了一些和`UICollectionViewCell` 一样的功能，但是更加轻量级。辅助视图，开箱即用，不支持单元格所具有的高级功能，如选择和高亮功能。清单3.2显示了我用于辅助视图的实现。

```objc
//  AFCollectionHeaderView.h
@interface AFCollectionHeaderView : UICollectionReusableView

@property (nonatomic, copy) NSString *text;

@end

//  AFCollectionHeaderView.m
#import "AFCollectionHeaderView.h"

@implementation AFCollectionHeaderView
{
    UILabel *textLabel;
}

- (id)initWithFrame:(CGRect)frame {
    if (!(self = [super initWithFrame:frame])) return nil;
    
    textLabel = [[UILabel alloc] initWithFrame:CGRectInset(CGRectMake(0, 0, CGRectGetWidth(frame), CGRectGetHeight(frame)), 30, 10)];
    textLabel.backgroundColor = [UIColor clearColor];
    textLabel.textColor = [UIColor whiteColor];
    textLabel.font = [UIFont boldSystemFontOfSize:20];
    [self addSubview:textLabel];
    
    return self;
}

-(void)prepareForReuse {
    [super prepareForReuse];
    
    [self setText:@""];
}

-(void)setText:(NSString *)text {
    _text = [text copy];
    
    [textLabel setText:text];
}

@end
```

所有的东西都和 cell 很相似，甚至是 `prepareForReuse` 方法。标签的框架有点不稳定，但我们只是把我们的框架从左边插入30点，从顶部插入10点。这给了它一个不错的余地。



## 提供辅助视图

现在，我们需要将 Class 对象注册到集合视图中，这样它就可以为自己创建辅助视图了（见清单3.3）。我们将把这段代码放在 `viewDidLoad` 中。

```objc
surveyFlowLayout.headerReferenceSize = CGSizeMake(60, 50);

[surveyCollectionView registerClass:[AFCollectionHeaderView class] forSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:HeaderIdentifier];
```

`HeaderIdentifier` 是一个 `NSString` 类型的静态变量，类似于我们之前用于单元格重用的那个。第二个参数是一个字符串，用于指定你要注册的辅助视图的种类，我们使用的是内置的 header 类型，`UICollectionElementKindSectionHeader`。还有UICollectionElementKindSectionFooter。这两种辅助视图是由流布局提供的，但你也可以指定你自己的视图（你将在后面的章节中看到）。

`headerReferenceSize` 属性告诉集合视图布局要把 header 做多大。如果你忘记设置这个，默认值是零，所以你的页眉不会被显示。这是一个常见的错误；所以如果你的页眉没有出现，请检查确保你指定了一个大小。

这个大小是如何解释并执行的，其实很有意思。当水平滚动时，系统只会用到你设置的 CGSize 的宽度值；而高度被垂直拉伸以填充当前整个 sectin 高度；当垂直滚动时，只会用到 CGSize 的高度值；而宽度会被水平拉伸以填充当前整个 section 的宽。页眉（和页脚）的布局如图3.1所示。绿色箭头表示滚动方向）。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200829-140552.png" alt="图 3.1" style="zoom:67%;" />

现在我们已经注册了Class 并指定了大小，现在是时候返回辅助视图的实例了。我们通过在 `UICollectionViewDelegate` 协议中实现一个名为`collectionView:viewForSupplementaryElementOfKind:atIndexPath:` 的新方法来实现。第二个参数是我们在注册头时使用的相同的 "kind "字符串。我们只有一种类型的辅助视图，所以在这个例子中我们将忽略这个参数。然而，如果你的集合视图有多个辅助视图类型，你就必须注意只返回正确的种类（见清单3.4）。

```objc
-(UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath {
    
    // Provides a view for the headers in the collection view
    AFCollectionHeaderView *headerView = (AFCollectionHeaderView *)[collectionView dequeueReusableSupplementaryViewOfKind:kind withReuseIdentifier:HeaderIdentifier forIndexPath:indexPath];
    
    if (indexPath.section == 0) {
        // If this is the first header, display a prompt to the user
        [headerView setText:@"Tap on a photo to start the recommendation engine."];
    } else if (indexPath.section <= currentModelArrayIndex) {
        // Otherwise, display a prompt using the selected photo from the previous section
        AFSelectionModel *selectionModel = selectionModelArray[indexPath.section - 1];
        
        AFPhotoModel *selectedPhotoModel = [self photoModelForIndexPath:[NSIndexPath indexPathForItem:selectionModel.selectedPhotoModelIndex inSection:indexPath.section - 1]];
        
        [headerView setText:[NSString stringWithFormat:@"Because you liked %@...", selectedPhotoModel.name]];
    }
    
    return headerView;
}
```

如果我们在第一组提示用户，我们会给他们一些说明，告诉他们该怎么做。否则，我们知道他们上次选择了什么照片（它存储在我们的数组或`AFSelectionModels` 中），所以我们会告诉他们，根据他们上次的选择，我们认为他们会喜欢接下来的这些照片。

让我们挂上我们的数据源方法，开始向用户展示一些 header view 视图以外的内容。

```objc
-(NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView
{
    //Return the smallest of either our curent model index plus one, or our total number of sections.
    //This will show 1 section when we only want to display section zero, etc.
    //It will prevent us from returning 11 when we only have 10 sections.
    return MIN(currentModelArrayIndex + 1, selectionModelArray.count);
}

-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
    //Return the number of photos in the section model
    return [[selectionModelArray[currentModelArrayIndex] photoModels] count];
}

-(UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    AFCollectionViewCell *cell = (AFCollectionViewCell *)[collectionView dequeueReusableCellWithReuseIdentifier:CellIdentifier forIndexPath:indexPath];
    
    //Configure the cell
    [self configureCell:cell forIndexPath:indexPath];
    
    return cell;
}
```

现在你应该很熟悉了。唯一令人烦恼的一行是`configureCell:forIndexPath:`，因为我们还没有实现它或自定义单元格子类。现在让我们来做这两件事。

我希望照片以白色哑光边框呈现。

在用户做出选择后，我们希望该部分看起来是灰色的，他们选择的照片保持选中。我们会将背景色做成白色，并将图片视图四面插入10点。单元格的代码看起来像清单3.6。

```objc
//  AFCollectionViewCell.h
@interface AFCollectionViewCell : UICollectionViewCell

@property (nonatomic, strong) UIImage *image;

-(void)setDisabled:(BOOL)disabled;

@end


//  AFCollectionViewCell.m
#import "AFCollectionViewCell.h"

@implementation AFCollectionViewCell
{
    UIImageView *imageView;
}

- (id)initWithFrame:(CGRect)frame
{
    if (!(self = [super initWithFrame:frame])) return nil;
    
    imageView = [[UIImageView alloc] initWithFrame:CGRectZero];
    imageView.backgroundColor = [UIColor blackColor];
    imageView.autoresizingMask = UIViewAutoresizingFlexibleHeight | UIViewAutoresizingFlexibleWidth;
    [self.contentView addSubview:imageView];
    
    UIView *selectedBackgroundView = [[UIView alloc] initWithFrame:CGRectZero];
    selectedBackgroundView.backgroundColor = [UIColor orangeColor];
    self.selectedBackgroundView = selectedBackgroundView;
    
    self.backgroundColor = [UIColor whiteColor];
    
    return self;
}

-(void)prepareForReuse
{
    [super prepareForReuse];
    
    [self setImage:nil];
    [self setSelected:NO];
}

-(void)layoutSubviews
{
    imageView.frame = CGRectInset(self.bounds, 10, 10);
}

-(void)setImage:(UIImage *)image
{
    _image = image;
    
    imageView.image = image;
}

-(void)setDisabled:(BOOL)disabled
{
    self.contentView.alpha = disabled ? 0.5f : 1.0f;
    self.backgroundColor = disabled ? [UIColor grayColor] : [UIColor whiteColor];
}

@end
```

实现起来相当简单。我们定义了一个 `setDisabled:` 方法，一旦用户对该部分进行了选择，我们将使用该方法来使单元格变灰。其他的东西应该在第 2 章 "使用 UICollectionView 显示内容 "中看起来很熟悉。

正如你所看到的，在实现代码中，我们使用了 `autoresizingMask` 来保持标签的宽度。这里，我们使用 `layoutSubviews` 来重新定位图片视图。这两种方法都是有效的，有时`autoresizingMask` 或 Autolayout 要么太复杂，要么太繁琐，无法使用。

现在我们来看看那个 `configureCell:forIndexPath:` 方法（见清单3.7）。

```objc
-(void)configureCell:(AFCollectionViewCell *)cell forIndexPath:(NSIndexPath *)indexPath
{
    //Set the image for the cell
    [cell setImage:[[self photoModelForIndexPath:indexPath] image]];
    
    //By default, assume the cell is not disabled and not selected
    [cell setDisabled:NO];
    [cell setSelected:NO];
    
    //If the cell is not in our current last index, disable it
    if (indexPath.section < currentModelArrayIndex)
    {
        [cell setDisabled:YES];
        
        //If the cell was selected by the user previously, select it now
        if (indexPath.row == [selectionModelArray[indexPath.section] selectedPhotoModelIndex])
        {
            [cell setSelected:YES];
        }
    }
}
```

该代码抓取特定的照片模型，并将其中的图像设置到单元格中。然后检查该单元格是否在一个已经被选择的区域中（使其变灰），然后检查该单元格是否是被选择的那一个。注意这不是一个属性，我们不希望单元格中包含模型的信息。我们只需要一个方便的方法来改变它的外观。



## 响应用户交互

现在，有趣的部分来了！我们通过代码来响应用户的选择。有一个`UICollectionViewDelegate` 方法，每当用户做出选择时就会被调用（但不是当开发人员以编程方式做出选择时）。请看一下清单3.8。

```objc
-(void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath
{
    //The user has selected a cell
    
    //No matter what, deselect that cell
    [collectionView deselectItemAtIndexPath:indexPath animated:YES];
    
    //Set the selected photo index
    [selectionModelArray[currentModelArrayIndex] setSelectedPhotoModelIndex:indexPath.item];
    
    if (currentModelArrayIndex >= selectionModelArray.count - 1)
    {
        //Let’s just present some dialogue to indicate things are done.
        
        isFinished = YES;
        
        [[[UIAlertView alloc] initWithTitle:@"Recommendation Engine" message:@"Based on your selections, we have concluded you have excellent taste in photography!" delegate:nil cancelButtonTitle:nil otherButtonTitles:@"Awesome!", nil] show];
        
        return;
    }
    
    [collectionView performBatchUpdates:^{
        currentModelArrayIndex++;
        [collectionView insertSections:[NSIndexSet indexSetWithIndex:currentModelArrayIndex]];
        [collectionView reloadSections:[NSIndexSet indexSetWithIndex:currentModelArrayIndex-1]];
    } completion:^(BOOL finished) {
        [collectionView scrollToItemAtIndexPath:[NSIndexPath indexPathForItem:0 inSection:currentModelArrayIndex] atScrollPosition:UICollectionViewScrollPositionTop animated:YES];
    }];
}
```

当一个单元格被选中后，就会调用这个方法。首先，我们要取消选择该单元格。然后，我们将检查是否已经完成调查。如果是的话，我们会将一个ivar设置为YES，并向用户抛出一个对话框。如果他们还没有完成，我们需要更新模型（设置选择的索引）并添加下一节。这是在 `performBatchUpdates:completion:` 方法中完成的。这是一种基于 Block 块的更新集合视图的方法。我们在第一个 block 中对模型和集合视图进行的更新是用同步动画来执行的。

苹果的文档中明确指出，应该先更新模型，然后对集合视图进行相应的修改。我们要重新加载用户刚才进行选择的部分，并添加下一组单元格。同样的方法也可以用来删除 item 或 section，你将在后面的章节中看到。以下方法显示了修改集合视图内容的不同方法。

```objc
// 这些方法允许动态修改集合视图中的当前项目集
- (void)insertSections:(NSIndexSet *)sections;
- (void)deleteSections:(NSIndexSet *)sections;
- (void)reloadSections:(NSIndexSet *)sections;
- (void)moveSection:(NSInteger)section toSection:(NSInteger)newSection;

- (void)insertItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths;
- (void)deleteItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths;
- (void)reloadItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths;
- (void)moveItemAtIndexPath:(NSIndexPath *)indexPath toIndexPath:(NSIndexPath *)newIndexPath;
```

现在如果我们运行这个应用，我们会得到图 3.2 的页面。这是个不错的开始，但如果我们把这个展示给投资人看，我们就不会得到应有的风险投资。到目前为止，这个应用还有一些问题。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/IMG_4709.PNG" alt="图 3.2" style="zoom: 25%;" />



首先，照片是被拉伸的（stretch）。与第二章不同的是，照片并不都是正方形的长宽比。这意味着，当我们把它们放到正方形的单元格中时，它们会被拉伸。这并不理想。最理想的是，我们可以让不同的单元格有不同的大小。现在有了一个想法 你的单元格大小通常是在流式布局对象上设置的。然而，flow 对象有自己的委托协议。我们可以通过响应它的委托方法来覆盖流式布局对象上的设置（见清单 3.9）。

```objc
-(CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath
{
    // 为每个独立的单元格设置不同的 size
    
    //Grab the photo model for the cell
    AFPhotoModel *photoModel = [self photoModelForIndexPath:indexPath];
    
    //Determine the size and aspect ratio for the model's image
    CGSize photoSize = photoModel.image.size;
    CGFloat aspectRatio = photoSize.width / photoSize.height;
    
    // start out with the detail image size of the maximum size
    // #define kMaxItemSize CGSizeMake(200, 200)
    CGSize itemSize = kMaxItemSize;
    
    if (aspectRatio < 1) {
        //The photo is taller than it is wide, so constrain the width
        itemSize = CGSizeMake(kMaxItemSize.width * aspectRatio, kMaxItemSize.height);
    } else if (aspectRatio > 1) {
        //The photo is wider than it is tall, so constrain the height
        itemSize = CGSizeMake(kMaxItemSize.width, kMaxItemSize.height / aspectRatio);
    }
    
    return itemSize;
}
```

该代码计算照片的宽高比，并返回一个相当于纵横拉伸贴合内容模式的项目尺寸。照片将被缩放到最大尺寸内。我在示例中包含了横向、纵向和方格作物照片，以向你展示它的工作原理（见图3.3）。让我们看看现在的应用是什么样子的。

这样就好多了! 如果单元格的间距总是均匀的话，看起来会更漂亮，但这需要子类化流式布局，所以我们会把它留到第4章 "用 `UICollectionViewFlowLayout` 组织内容"。

该应用程序的另一个问题是，你可以在已经进行过选择的 section 上进行选择。这是一个严重的缺陷。幸运的是，集合视图在高亮或选择单元格之前，有一对方法会对其进行询问。我们只实现第一个方法来防止高亮。因为高亮是成为选择的第一步，所以我们不需要实现禁止选中的方法（只要它不能被高亮，它就不能被选中）。

> 注
>
> 禁止 cell 的高亮和选中状态只适用于用户交互。通过代码设置的高亮和选中仍然可以正常工作。

清单3.10只有当该部分是我们当前正在提示用户的部分，并且只要用户还没有完成选择时，才允许高亮显示。

```
-(BOOL)collectionView:(UICollectionView *)collectionView shouldHighlightItemAtIndexPath:(NSIndexPath *)indexPath {
    return indexPath.section == currentModelArrayIndex && !isFinished;
}
```

表3.2提供了您可以自定义用户与您的集合视图的交互方式的完整列表。

```objc
- (BOOL)collectionView:(UICollectionView *)collectionView shouldHighlightItemAtIndexPath:(NSIndexPath *)indexPath;
- (void)collectionView:(UICollectionView *)collectionView didHighlightItemAtIndexPath:(NSIndexPath *)indexPath;
- (void)collectionView:(UICollectionView *)collectionView didUnhighlightItemAtIndexPath:(NSIndexPath *)indexPath;
- (BOOL)collectionView:(UICollectionView *)collectionView shouldSelectItemAtIndexPath:(NSIndexPath *)indexPath;
- (BOOL)collectionView:(UICollectionView *)collectionView shouldDeselectItemAtIndexPath:(NSIndexPath *)indexPath; // called when the user taps on an already-selected item in multi-select mode
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath;
- (void)collectionView:(UICollectionView *)collectionView didDeselectItemAtIndexPath:(NSIndexPath *)indexPath;
```



## 提供 Cut/Copy/Paste 支持

我们离那轮风险投资越来越近了，我能感觉到! 然而，这一章叫做 "内容的语境化"，我认为我们可以让这款应用更加具有语境意识。如果用户在使用点按手势时，能够复制照片的名称，那会怎样？我知道你在想什么。"添加交互性要等到第六章'为 `UICollectionView` 添加交互性'" 嗯，是的，这是真的。然而，复制、剪切和粘贴是内置在集合视图中的! 让我们来看看获得该功能有多简单。

首先，我们需要让集合视图知道我们支持菜单功能。有一个简单的委托方法，如清单3.11所示。

```objc
#pragma mark Tap and Hold Gesture

// 是否支持菜单功能
-(BOOL)collectionView:(UICollectionView *)collectionView shouldShowMenuForItemAtIndexPath:(NSIndexPath *)indexPath
{
    return YES;
}
```

请注意，你可以有选择地对单个单元格启用复制/剪切/粘贴菜单，而不是对整个集合视图启用。非常棒。因为我们所有的单元格都代表有名字的照片，所以我们只对所有内容返回YES。

接下来，集合视图将询问它的委托对象，了解它可以执行的不同类型的操作。它将为它所支持的每个动作反复调用清单 3.12 中的方法。目前，只有 `cut:`、`copy:`和 `paste:` 这三种操作，但是 Apple 可能会在以后添加更多的操作，所以要写代码来适应。

```objc
// 支持哪些菜单功能，可选项有 cut:、copy:、paste:
-(BOOL)collectionView:(UICollectionView *)collectionView canPerformAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(id)sender
{
    if ([NSStringFromSelector(action) isEqualToString:@"copy:"])
    {
        return YES;
    }
    
    return NO;
}
```

该方法通过一个 `SEL` 选择器，并询问委托对象是否可以在该索引路径所代表的模型上执行该操作。我们需要将选择器转化为其字符串等价物，并将其与 "`copy:`"进行比较，这是我们想要支持的唯一选择器。

接下来就是简单的部分--实际执行复制操作。

```objc
// 执行具体的菜单操作
-(void)collectionView:(UICollectionView *)collectionView performAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(id)sender
{
    if ([NSStringFromSelector(action) isEqualToString:@"copy:"])
    {
        UIPasteboard *pasteboard = [UIPasteboard generalPasteboard];
        [pasteboard setString:[[self photoModelForIndexPath:indexPath] name]];
    }
}
```

我们只需再次检查确定要执行复制操作，从系统中获取通用的粘贴板，并设置复制字符串。

现在，你已经更好地理解了集合视图以及数据源和委托协议，让我们继续阅读第4章，并仔细研究 `UICollectionViewFlowLayout`。