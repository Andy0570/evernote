> 注：
> 本文翻译自 《iOS UICollectionView The Complete Guide 2nd Edition》
> 使用的翻译工具：<https://www.deepl.com/translator>



你现在已经掌握了使用 `UICollectionView` 向用户显示自定义内容的技巧，可以显示单元格以及辅助视图。到目前为止，我们一直关注实际的内容，而不是如何在屏幕上组织内容。本章将探讨 `UICollectionView` 是如何被设计成使用 `UICollectionViewLayout` 来组织其内容的。我们仔细研究了 `UICollectionViewFlowLayout`，以及如何通过子类化它来获得大量的可定制性，而不需要大量的额外工作。当我们以简短的历史课结束，我们会探索 `UITableView` 以及它与 `UICollectionView` 的关系。


## 什么是布局

`UICollectionViewLayout` 是一个抽象类，它不应该被直接创建，它存在的唯一目的是被子类化。每个集合视图都有一个与之相关联的布局对象，它的工作是将内容布局出来。布局对象并不关心其布局的视图中所包含的数据，它只关心向用户展示的布局。

`UICollectionViewFlow` 是一个直接的子类，它以基于行的、分块的方式来布局内容。我们已经看到了`UICollectionViewFlowLayout` 最基本的形式，一个网格。我们用本章的其余部分来探索一个简单的流式布局子类给你作为一个开发者带来的力量。你可以用很少的代码来创建令人震惊的布局，如果你知道把它放在哪里的话。

子类布局对象有一些职责。集合视图依靠这个子类布局对象来告诉它如何显示其单元格。这是一个关键的概念。布局内容并不是通过创建一个 `UICollectionView` 的子类来实现的。虽然这是在子类`UIScrollView` 时布局子视图的常见模式，但除非绝对必要，我们要避免创建 `UICollectionView` 子类。

所以，集合视图会向其布局对象询问如何布局其内容的线索。当一个集合视图向用户显示内容时，实际发生的事件顺序是什么？

首先，集合视图询问其数据源，以获得关于要向用户显示的内容的信息。这包括要显示多少组数据、每组数据要显示多少单元格、辅助视图的数量。

接下来，集合视图从其布局对象中收集有关如何显示单元格、辅助视图和装饰视图的信息。这些信息存储在一个名为 `UICollectionViewLayoutAttributes` 的类的实例中。

最后，集合视图将有关布局的信息转发给单元格、辅助视图和装饰视图。这些类中的每一个类都负责使用它所得到的信息将这些布局属性应用到自己身上。推迟到父类的实现，或者完全省略一个实现，将确保已经由集合视图处理的布局属性（如框架）得到应用。你的实现应该集中在你添加的任何自定义属性上（但后面会有更多的介绍）。

每当当前布局失效时，就会发生这些步骤，你可以通过在布局对象上调用 `invalidateLayout` 来强制执行布局更新。

现在你已经知道了布局内容时使用的不同类。`UICollectionView`，它是向用户展示内容的视图；`UICollectionViewCell`，它负责向用户展示一个单元格的内容；`UICollectionViewLayout`，它确定项目的属性，并将这些信息返回给集合视图；`UICollectionViewLayoutAttributes`，它是一个布局存储信息的类，要将这些信息调配给单元格、辅助视图和装饰视图。

如果回过头来看这些类，就会发现有一个明显的划分，哪些是参与数据和自身布局的，哪些是只负责布局的。图4.1显示了这种划分。`UICollectionView` 从橙色框中的类中收集数据信息，并将其与蓝色框中的类的布局信息相结合。

![图 4.1](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200829-154111.png)

请注意，布局对象对集合视图的委托对象有一个间接的引用。这个连接可以被布局对象用来询问委托对象关于特定项目布局的信息。例如，`UICollectionViewDelegateFlowLayout` 协议扩展了`UICollectionViewDelegate`，并被 `UICollectionViewFlowLayout` 用来询问委托对象关于特定 item 的布局信息。这个话题很复杂，但你已经在上一章看到了一个例子，当委托对象为不同的 item 指定单独的 size 时。你将在后面看到一个进一步扩展这个功能的例子。

我们已经介绍了基础知识：什么是布局，它有什么作用，以及它如何与集合视图架构的其他部分进行交互。到目前为止，这已经是非常学术性的内容了。让我们来看看一些代码。



## 子类化 `UICollectionViewFlowLayout`

我们已经看到很多复杂的行为和布局是使用内置的 `UICollectionViewFlowLayout` 生成的，那么为什么会选择将其子类化呢？原因有很多。

* 要修改你的子类的布局的属性，这超出了委托方法所能实现的范围。
* 在你的布局中加入装饰视图 
* 增加新的辅助视图 
* 要扩展 `UICollectionViewLayoutAttributes`，为你的布局类添加新的项目属性来管理。
* 要添加手势支持
* 要自定义插入、更新和删除更新到集合视图的动画。

除了在第6章 "为 `UICollectionView` 添加交互性 "中涵盖的手势支持外，我们将针对每个原因的子类流式布局看代码示例。

让我们回顾 Survey 示例——它的代码在 Better Survey 中。有几种方法可以让它变得更好，第一种方法如图 4.2 所示。因为不是所有的单元格都有相同的大小，所以单元格不会再垂直对齐。开箱即用，`UICollectionViewFlowLayout` 并没有提供对那种 "均匀间隔 "感觉的支持，我认为这种感觉在这里会更好。幸运的是，我们想要的东西属于 "基于行的，打破布局 " 的流式布局，所以我想我们可以通过创建一个 `UICollectionViewFlowLayout` 子类的方式来实现我们想要的视觉效果。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2085%20%E9%A1%B5.png" alt="图 4.2" style="zoom: 60%;" />

我将在 Xcode 中创建一个新文件，并将其称为`AFCollectionViewFlowLayout`；它将是 `UICollectionViewFlowLayout` 的子类（见清单4.1）。接下来，我们可以把视图控制器中的很多布局逻辑代码移动到该布局类中。

```objc
#import <UIKit/UIKit.h>

#define kMaxItemDimension   100.0f
#define kMaxItemSize        CGSizeMake(kMaxItemDimension, kMaxItemDimension)

extern NSString * const AFCollectionViewFlowLayoutBackgroundDecoration;

@interface AFCollectionViewFlowLayout : UICollectionViewFlowLayout

@end
```

你可以看到，我们已经将最大单元格大小移动到布局的头文件中。这是个（比把它放在视图控制器实现文件中）更合适的地方。

接下来，我们将实现我们自己的 `init` 方法，这样我们就可以在里面设置我们的属性（见清单4.2）。

```objc
-(id)init {
    if (!(self = [super init])) return nil;
    
    // 在初始化方法中设置默认布局参数
    self.sectionInset = UIEdgeInsetsMake(15.0f, 5.0f, 15.0f, 5.0f);
    self.minimumInteritemSpacing = 5.0f;
    self.minimumLineSpacing = 5.0f;
    self.itemSize = kMaxItemSize;
    self.headerReferenceSize = CGSizeMake(60, 70);
        
    return self;
}
```

最后，我们需要更新并创建视图控制器中的布局对象。使用 `#import` 导入`AFCollectionViewFlowLayout` 头文件，将布局和集合视图的创建方式改为清单4.3所示的代码。

```objc
    //Create our view
    //Create a basic flow layout that will accomodate three columns in portrait
    AFCollectionViewFlowLayout *surveyFlowLayout = [[AFCollectionViewFlowLayout alloc] init];
    
    //Create a new collection view with our flow layout and set ourself as delegate and data source
    UICollectionView *surveyCollectionView = [[UICollectionView alloc] initWithFrame:CGRectZero collectionViewLayout:surveyFlowLayout];
```

通过将布局设置移到它的初始化方法中，我们在视图控制器中写的代码就少了很多。此外，如果我们重用该布局，我们就不会在两个地方重复代码。重用这个布局的视图控制器总是可以进一步自定义布局属性，但他们不必这样做。这是你在编写自己的自定义布局时应该遵守的好模式。

接下来，需要在我们的 `UICollectionViewFlowLayout` 子类中重写两个方法，当集合视图在布局其单元格、辅助视图和装饰视图时，这些方法将被调用。这两个方法是 `layoutAttributesForElementsInRect:` 和 `layoutAttributesForItemAtIndexPath:`。我们还要创建第三个私有方法，叫做 `applyLayoutAttributes:`，我们在后面讨论。这两个被覆盖的方法都会调用这个自定义的方法（见清单4.4）。

```objc
/**
 该方法返回一个包含所有布局信息 UICollectionViewLayoutAttributes 的数组。
 
 我们通过父类方法 [super layoutAttributesForElementsInRect:rect] 先创建了一个正常情况下的所有属性的数组。
 这个父类方法默认情况下，只会创建在 rect 范围内的视图的布局属性。
 所以如果你想把原来不会被现实的视图也显示出来的话，你就不得不自己把所有布局属性都创建出来，放入数组中。
 */
-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect {
    
    NSArray *attributesArray = [super layoutAttributesForElementsInRect:rect];
    
    NSMutableArray *newAttributesArray = [NSMutableArray array];
    for (UICollectionViewLayoutAttributes *attributes in attributesArray) {
        [self applyLayoutAttributes:attributes];
        
        // MARK: 添加装饰视图
        if (attributes.representedElementCategory == UICollectionElementCategorySupplementaryView) {
            UICollectionViewLayoutAttributes *newAttributes = [self layoutAttributesForDecorationViewOfKind:AFCollectionViewFlowLayoutBackgroundDecoration atIndexPath:attributes.indexPath];
            [newAttributesArray addObject:newAttributes];
        }
    }
    attributesArray = [attributesArray arrayByAddingObjectsFromArray:newAttributesArray];
    
    return attributesArray;
}

// 布局 item
-(UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath {
    UICollectionViewLayoutAttributes *attributes = [super layoutAttributesForItemAtIndexPath:indexPath];
    [self applyLayoutAttributes:attributes];
    return attributes;
}
```

这两个方法所做的第一件事就是调用它们父类的实现。通过这样做，我们免费获得了所有的 `UICollectionViewFlowLayout` 默认行为。在我们检索到默认属性后，再调整各个 item 的布局。

现在我们来看看 `applyLayoutAttributes:` 方法。我们首先检查布局属性的 `representedElementKind` 属性。对于普通的 `UICollectionViewCells`，这将是 nil。否则，它将是集合视图注册的辅助视图类型；在我们的例子中，它将是`UICollectionElementKindSectionHeader`。还有一点值得记住，center 和 size 分别定义了一个 item 的 position 和 size。当计算这些时，你可能最终会在半像素上渲染视图，使它们变得模糊不清。frame 属性是一种方便的方法，用于访问布局属性的大小和中心。通过将框架设置为自身的 `CGRectIntegral`（见清单4.5），我们可以确保视图不会呈现在像素边界上。

```objc
/**
 修改并更新每一个 item 的位置
 */
-(void)applyLayoutAttributes:(UICollectionViewLayoutAttributes *)attributes
{
    // 检查 representedElementKind 是否为 nil，表明这是一个单元格，而不是一个 header view 或装饰视图。
    if (attributes.representedElementKind == nil) {
        CGFloat width = [self collectionViewContentSize].width;
        CGFloat leftMargin = [self sectionInset].left;
        CGFloat rightMargin = [self sectionInset].right;
        
        NSUInteger itemsInSection = [[self collectionView] numberOfItemsInSection:attributes.indexPath.section];
        // xPosition 指单元格的 centerX
        CGFloat firstXPosition = (width - (leftMargin + rightMargin)) / (2 * itemsInSection);
        CGFloat xPosition = firstXPosition + (2*firstXPosition*attributes.indexPath.item);
        
        attributes.center = CGPointMake(leftMargin + xPosition, attributes.center.y);
        attributes.frame = CGRectIntegral(attributes.frame);
    }
}
```

清单4.5 只是图4.3 中所列公式的编辑版本。它已被概括为允许每行有任意数量的项目，而不是只有三个。

![图 4.3](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200829-160844.png)



啊，你知道这本电子书最终会有一些数学的内容! 但是，其实并没有那么复杂。

如果我们再运行这个应用程序，我们会看到单元格是均匀分布的，如图4.4所示。

![图4.4](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2090%20%E9%A1%B5.png)



现在我们已经把我们的单元格排列成一个漂亮的网格模式，让我们添加一个装饰视图。装饰视图是对 `UICollectionView` 的数据驱动内容的视觉补充。它们并不显示单元格的信息；相反，它们伴随着单元格的视觉效果：设计师最好的朋友。

我不是设计师，但我已经成功地想出了一个文件夹的想法。我们的应用要炫耀这股 "扁平化设计 "的热潮，将我们的照片摆放在一个三环的文件夹上面。我拍了一张文件夹的照片，然后把它拉长。我们要让这个装饰视图铺在每一排照片的后面。

因为装饰视图不是数据驱动的，所以不会向视图控制器添加任何代码。相反，装饰视图的所有代码都将存在于我们的 `AFCollectionViewFlowLayout` 和 `UICollectionReusableView` 的一个子类中。

这个类 `UICollectionReusableView` 是 `AFCollectionHeaderView` 甚至`UICollectionViewCell` 的父类。它提供了重用集合视图中任何特定视图的通用逻辑，其中包括单元格、补充视图和装饰视图。因为这些类可以重用，我们可以把已经学到的关于重用的知识应用到装饰视图中。现在就让我们这样做。

创建一个新的类，父类是 `UICollectionReusableView`。我把我的类叫做 `AFDecorationView`。它没有任何属性，而且它的实现看起来相当无聊（见清单4.6）。

```objc
#import "AFDecorationView.h"

@implementation AFDecorationView
{
    UIImageView *binderImageView;
}

- (id)initWithFrame:(CGRect)frame
{
    if (!(self = [super initWithFrame:frame])) return nil;
    
    binderImageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"binder"]];
    binderImageView.frame = CGRectMake(10, 0, CGRectGetWidth(frame), CGRectGetHeight(frame));
    binderImageView.contentMode = UIViewContentModeLeft;
    binderImageView.autoresizingMask = UIViewAutoresizingFlexibleHeight | UIViewAutoresizingFlexibleWidth;
    [self addSubview:binderImageView];
    
    return self;
}

@end
```

这个类所做的就是，当初始化时，在它的视图层次结构中添加一个`UIImageView`，里面有我们的 "binder "图像。没有必要覆盖 `prepareForReuse` 方法，因为在我们的装饰视图中没有特定的数据内容。

现在我们已经创建了装饰视图子类，让我们把它添加到集合视图中。这比 header 视图要棘手一些，因为 `UICollectionView` 并没有为我们内置任何内容，我们需要自己构建一切。

将装饰视图的头文件导入到布局子类中。修改 `layoutAttributesForElementsInRect:` 方法的实现，使其看起来像清单4.7。

```objc
-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect {
    
    NSArray *attributesArray = [super layoutAttributesForElementsInRect:rect];
    
    NSMutableArray *newAttributesArray = [NSMutableArray array];
    
    for (UICollectionViewLayoutAttributes *attributes in attributesArray) {
        
        [self applyLayoutAttributes:attributes];
        
        // MARK: 添加装饰视图
        if (attributes.representedElementCategory == UICollectionElementCategorySupplementaryView) {
            
            UICollectionViewLayoutAttributes *newAttributes = [self layoutAttributesForDecorationViewOfKind:AFCollectionViewFlowLayoutBackgroundDecoration atIndexPath:attributes.indexPath];
            
            [newAttributesArray addObject:newAttributes];
        }
    }
    
    attributesArray = [attributesArray arrayByAddingObjectsFromArray:newAttributesArray];
    
    return attributesArray;
}
```

增加了检查布局属性的元素类型的 if 语句。我们想在每个部分添加一个装饰视图，而每个部分只有一个 header，所以我们将搭载这个逻辑来添加我们的辅助视图。

代码本身可能看起来有点奇怪。请记住，`layoutAttributesForElementsInRect:` 是为所有类型的元素调用的，而不仅仅是单元格。因此，当它被调用到我们的 header 视图时，我们的 if 语句评估为 YES，我们就会创建一个新的布局属性。我们返回的数组将包含这个新属性。

接下来，我们需要为 `layoutAttributesForDecorationViewOfKind:atIndexPath:` 实现一个方法，因为默认的实现会返回 nil，当我们试图将它添加到我们的可变字典中时，我们的应用程序会崩溃。

我们需要实现一个方法，该方法将创建一个新的 `UICollectionViewLayoutAttributes` 对象，并自定义它的属性，以便装饰视图将适合我们的单元格内容后面（见清单4.8）。

```objc
// 装饰视图布局
-(UICollectionViewLayoutAttributes *)layoutAttributesForDecorationViewOfKind:(NSString *)decorationViewKind atIndexPath:(NSIndexPath *)indexPath
{
    UICollectionViewLayoutAttributes *layoutAttributes = [UICollectionViewLayoutAttributes layoutAttributesForDecorationViewOfKind:decorationViewKind withIndexPath:indexPath];
    
    if ([decorationViewKind isEqualToString:AFCollectionViewFlowLayoutBackgroundDecoration])
    {
        UICollectionViewLayoutAttributes *tallestCellAttributes;
        NSInteger numberOfCellsInSection = [self.collectionView numberOfItemsInSection:indexPath.section];
        
        for (NSInteger i = 0; i < numberOfCellsInSection; i++)
        {
            NSIndexPath *cellIndexPath = [NSIndexPath indexPathForItem:i inSection:indexPath.section];
            
            UICollectionViewLayoutAttributes *cellAttribtes = [self layoutAttributesForItemAtIndexPath:cellIndexPath];
            
            if (CGRectGetHeight(cellAttribtes.frame) > CGRectGetHeight(tallestCellAttributes.frame))
            {
                tallestCellAttributes = cellAttribtes;
            }
        }
        
        CGFloat decorationViewHeight = CGRectGetHeight(tallestCellAttributes.frame) + self.headerReferenceSize.height;
        
        layoutAttributes.size = CGSizeMake([self collectionViewContentSize].width, decorationViewHeight);
        layoutAttributes.center = CGPointMake([self collectionViewContentSize].width / 2.0f, tallestCellAttributes.center.y);
        layoutAttributes.frame = CGRectIntegral(layoutAttributes.frame);
        // Place the decoration view behind all the cells
        layoutAttributes.zIndex = -1;
    }
    
    return layoutAttributes;
}
```

本实施例使用类方法 `layoutAttributesForDecorationViewOfKind:withIndexPath:` 创建一个新的 `UICollectionViewLayoutAttributes` 对象。然后。

它根据我们要找的东西来定制属性中的属性。我们希望我们的装饰视图以其部分中最高的项目为垂直中心，所以我们需要循环处理每一个项目。幸运的是，检索这些属性的逻辑已经在 `layoutAttributesForItemAtIndexPath:` 中实现了。当我们向我们的超级类询问给定单元格的属性时，它会查询集合视图委托的大小（代码我们已经写好了）。

我们可以利用这个现有的功能来处理繁重的工作。我们并没有计算装饰视图的中心，实际上，我们只是依靠最高物品的垂直中心，它已经为我们计算好了。万岁!

所以，在我们定义了装饰视图的大小和高度之后，我们需要设置它的 z-index。这将告诉集合视图以何种顺序呈现其项目。重叠但具有相同 z-index 的项目有一个未定义的渲染顺序。我们希望装饰视图渲染在所有单元格后面，而这些单元格的默认 z-index 为0，所以我们将装饰视图的 z-index设 置为 -1。

我们需要做的唯一一件事就是将我们的装饰视图类注册到布局类中（见清单 4.9）。我们将在`AFCollectionViewFlowLayout`的`init`方法中添加下面高亮显示的一行。

```objc
-(id)init {
    if (!(self = [super init])) return nil;
    
    // 在初始化方法中设置默认布局参数
    self.sectionInset = UIEdgeInsetsMake(30.0f, 80.0f, 30.0f, 20.0f);
    self.minimumInteritemSpacing = 20.0f;
    self.minimumLineSpacing = 20.0f;
    self.itemSize = kMaxItemSize;
    self.headerReferenceSize = CGSizeMake(60, 70);
    
    // !!!: 注册装饰视图
    [self registerClass:[AFDecorationView class] forDecorationViewOfKind:AFCollectionViewFlowLayoutBackgroundDecoration];
    
    insertedSectionSet = [NSMutableSet set];
    
    return self;
}
```

惊人！图4.5显示，我们几乎达到了目的。最后，我认为这个演示程序可以使用一些漂亮的动画。`UICollectionViewLayout` 中已经内置了对动画的支持，我们只需要实现一些方法。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/%E5%9B%BE%E7%89%87%E6%9D%A5%E8%87%AA%20iOS%20UICollectionView%20The%20Complete%20Guide%202nd%20Edition.2014.%E8%8B%B1%E6%96%87%E7%89%88%EF%BC%8C%E7%AC%AC%2095%20%E9%A1%B5.png" style="zoom:80%;" />

 当一个新的 item 被添加或更新到集合视图中时，`initialLayoutAttributesForAppearingItemAtIndexPath:` 方法就会被调用。我们可以通过它在动画开始时为 item 提供初始布局属性，集合视图将把可动画的属性，如 `frame` 和 `alpha`，插值到它们的正常位置。还有一个对应的方法叫做`finalLayoutAttributesForDisappearingItemAtIndexPath:` 用于通过动画方式从集合视图中移除 item。

不过我们可以动画的不仅仅是 item 元素。辅助视图和装饰视图都有相应的出现/消失方法。`UICollectionViewLayout` 的默认实现返回 `nil`，表示简单的交叉淡化（crossfade）动画。我们也可以返回 `nil` 来使用交叉淡化动画。

剩下的问题是，当我们插入一个新的 section 时，其他 section 也会被重新加载。这会导致不仅仅是出现的 secion 有动画。我们还需要限制哪些 section 会执行动画。

在对集合视图进行任何更新之前，`prepareForCollectionViewUpdates:` 被调用，其参数是一个 `UICcollectionViewUpdateItem` 对象数组。这些是即将发生的更新。在它们完成后，调用 `finalizeCollectionViewUpdates`。这些都是成对的。我们将创建一个实例变量`NSMutableSet` 来获取正在插入的 section。我们使用 set 是因为它具有恒时查找功能（见清单4.10）。

```objc
@implementation AFCollectionViewFlowLayout
{
    NSMutableSet *insertedSectionSet;
}

-(id)init {
    if (!(self = [super init])) return nil;
    
    // 在初始化方法中设置默认布局参数
    self.sectionInset = UIEdgeInsetsMake(30.0f, 80.0f, 30.0f, 20.0f);
    self.minimumInteritemSpacing = 20.0f;
    self.minimumLineSpacing = 20.0f;
    self.itemSize = kMaxItemSize;
    self.headerReferenceSize = CGSizeMake(60, 70);
    
    // !!!: 注册装饰视图
    [self registerClass:[AFDecorationView class] forDecorationViewOfKind:AFCollectionViewFlowLayoutBackgroundDecoration];
    
    insertedSectionSet = [NSMutableSet set];
    
    return self;
}
```

现在我们只需要实现 `prepareForCollectionViewUpdates:` 和 `finalizeCollectionViewUpdates` 方法来更新集合视图。对于这些方法，始终调用你的 `super` 实现是非常重要的（见清单4.11）。

```objc
#pragma mark Animation Support

-(void)prepareForCollectionViewUpdates:(NSArray *)updateItems {
    [super prepareForCollectionViewUpdates:updateItems];
    
    [updateItems enumerateObjectsUsingBlock:^(UICollectionViewUpdateItem *updateItem, NSUInteger idx, BOOL *stop) {
        // 如果当前的 item 动作为 Insert，则记录到 NSMutableSet 集合中
        if (updateItem.updateAction == UICollectionUpdateActionInsert) {
            [insertedSectionSet addObject:@(updateItem.indexPathAfterUpdate.section)];
        }
    }];
}

-(void)finalizeCollectionViewUpdates {
    [super finalizeCollectionViewUpdates];
    
    /**
     当更新完成后，从可变集中删除所有项目，将其重置为空状态，以便进行下一批更新。
     */
    [insertedSectionSet removeAllObjects];
}
```

你可以看到，当我们准备更新时，我们的布局会检查更新动作，看看是否是一个正在插入的 item。如果是，它就会向集合中添加一个NSNumber 实例，代表 item 在 section 中的索引。在集合（NSSet）中，重复的 item 会被忽略，所以我们不必检查它是否已经存在。

当更新完成后，我们从可变集中删除所有项目，将其重置为空状态，以便进行下一批更新。

现在，我们已经完成了这些工作，让我们来看看在 item 和装饰视图中的动画代码，如清单4.12所示。

```objc
// 自定义动画，添加装饰视图
- (UICollectionViewLayoutAttributes *)initialLayoutAttributesForAppearingDecorationElementOfKind:(NSString *)elementKind atIndexPath:(NSIndexPath *)decorationIndexPath {
    // 返回 nil 则执行默认的 crossfade 动画
    
    UICollectionViewLayoutAttributes *layoutAttributes;
    if ([elementKind isEqualToString:AFCollectionViewFlowLayoutBackgroundDecoration]) {
        if ([insertedSectionSet containsObject:@(decorationIndexPath.section)]) {
            layoutAttributes = [self layoutAttributesForDecorationViewOfKind:elementKind atIndexPath:decorationIndexPath];
            layoutAttributes.alpha = 0.0f;
            layoutAttributes.transform3D = CATransform3DMakeTranslation(-CGRectGetWidth(layoutAttributes.frame), 0, 0);
        }
    }
    
    return layoutAttributes;
}

// 自定义动画，添加 item
- (UICollectionViewLayoutAttributes *)initialLayoutAttributesForAppearingItemAtIndexPath:(NSIndexPath *)itemIndexPath {
    // 返回 nil 则执行默认的 crossfade 动画
    
    UICollectionViewLayoutAttributes *layoutAttributes;
    if ([insertedSectionSet containsObject:@(itemIndexPath.section)]) {
        layoutAttributes = [self layoutAttributesForItemAtIndexPath:itemIndexPath];
        layoutAttributes.transform3D = CATransform3DMakeTranslation([self collectionViewContentSize].width, 0, 0);
    }
    
    return layoutAttributes;
}
```

因为默认的实现返回 `nil`，所以我们不必担心调用 `super` 关键字以调用父类实现。

这两个实现是相似的，因为它们构造的动画非常相似。对于装饰视图，我们检查确保装饰视图是我们设置的那个；虽然没有其他装饰视图，但这是很好的实践，以防我们以后添加更多的装饰视图。

无论在哪种情况下，我们都要检查确保索引路径的 item 部分是否包含在我们插入的部分集合中。如果是，我们从我们之前实现的 `layoutAttributesForItemAtIndexPath:` 或`layoutAttributesForDecorationViewOfKind:atIndexPath:` 中抓取一个`UICollectionViewLayoutAttributes` 的实例--我们在利用我们已经写好的代码。

然后，我们设置一个变换，将装饰视图向左移动，将单元格向右移动，使它们在动画开始时完全脱离可见的集合视图。我们还将装饰视图的` alpha` 设置为零，这样它就会渐渐消失。

现在，每当插入一个新的部分，用户就会看到文件夹从左边移入，而照片从右边移入。这是一个非常好的触动。

从这一节中，你应该有一个关键的架构启示，那就是编写 `UICollectionViewFlowLayout`子类就是尽可能地依赖现有的代码。如果你发现自己要做复杂的数学计算一些已经布局好的东西，请检查是否有一些方法可以访问这些信息。

## 使用自定义属性布局 item

`UICollectionViewLayoutAttributes`是一个类，这意味着我们可以对它进行子类化。为什么我们要这么做呢？当然是为了增加对更多属性的支持! 让我们来看看我的意思。

该类包含以下属性，它们在运行时应用于项目：

* Frame (convenience property for center and size) 
* Center
* Size 
* 3D Transform
* Alpha (opacity) ▪ Z-index
* Hidden ▪ Element category (cell, supplementary view, or decoration view)
* Element kind (nil for cells)

以上这些属性非常棒，你可以用它们来完成很多事情。但是有时候，你可能想添加自己的属性。

这就是我们现在要做的。

这个项目在示例代码中叫做 Dimensions。它已经完成了一些图像和模型的设置，我在这里不做介绍。它要解决的问题是，照片有时在拉伸到纵横向填充时看起来是最好的，裁剪图像中多余的部分以适合它的容器。其他时候，你想使用纵横适配，它将缩小图像，使整个图像在一个容器中可见。我们将编写一个布局，作为布局属性来处理这个问题。

我用 Single View application 模板创建了一个新的 Xcode 项目。在删除了.xib之后，我将应用程序委托中的主窗口设置改为清单4.13的样子。

```objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    UINavigationController *navigationController = [[UINavigationController alloc] initWithRootViewController:[[AFViewController alloc] init]];
    navigationController.navigationBar.barStyle = UIBarStyleBlack;
    
    self.viewController = navigationController;
    self.window.rootViewController = self.viewController;
    [self.window makeKeyAndVisible];
    
    return YES;
}
```

我们所做的就是用 Xcode 为我们创建的根自定义视图控制器设置一个导航控制器，我们稍后将实现这个控制器。注意，我必须将 `viewController` 属性的类型改为通用的 `UIViewController`。

现在我们已经在屏幕上有了我们的视图控制器，我们可以设置集合视图和布局了（见清单4.14）。

```objc
@implementation AFViewController
{
    // 数组模型对象
    NSArray *photoModelArray;
    
    UISegmentedControl *aspectChangeSegmentedControl;
    
    AFCollectionViewFlowLayout *photoCollectionViewLayout;
}

//Static identifier for cells
static NSString *CellIdentifier = @"CellIdentifier";

-(void)loadView
{
    // Create our view
    
    // 创建自定义布局对象实例
    photoCollectionViewLayout = [[AFCollectionViewFlowLayout alloc] init];
    
    // 创建自定义集合视图
    // Create a new collection view with our flow layout and set ourself as delegate and data source.
    UICollectionView *photoCollectionView = [[UICollectionView alloc] initWithFrame:CGRectZero collectionViewLayout:photoCollectionViewLayout];
    photoCollectionView.dataSource = self;
    photoCollectionView.delegate = self;
    
    // Register our classes so we can use our custom subclassed cell and header
    [photoCollectionView registerClass:[AFCollectionViewCell class] forCellWithReuseIdentifier:CellIdentifier];
    
    // Set up the collection view geometry to cover the whole screen in any orientation and other view properties.
    photoCollectionView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    photoCollectionView.allowsSelection = NO;
    photoCollectionView.indicatorStyle = UIScrollViewIndicatorStyleWhite;
    
    // Finally, set our collectionView (since we are a collection view controller, this also sets self.view)
    self.collectionView = photoCollectionView;
    
    // 初始化模型
    [self setupModel];
}
```

这应该是你熟悉的代码了。注意，我们已经禁用了集合视图中所有单元格的选择。我们还有一个分段控件作为实例变量。这个控件将放在导航栏中，这样用户就可以在纵横交错和纵横填充之间进行选择。

我们稍后将实现 `loadView` 中引用的 `AFCollectionViewFlowLayout` 类，但我们先看看视图控制器的其他代码。它在我们的导航栏中设置了分段控件（见清单4.15）。

```objc
-(void)viewDidLoad
{
    [super viewDidLoad];
    
    // 在导航栏上添加自定义 UISegmentedControl 对象
    aspectChangeSegmentedControl = [[UISegmentedControl alloc] initWithItems:@[@"Aspect Fit", @"Aspect Fill"]];
    aspectChangeSegmentedControl.selectedSegmentIndex = 0;
    aspectChangeSegmentedControl.segmentedControlStyle = UISegmentedControlStyleBar;
    [aspectChangeSegmentedControl addTarget:self action:@selector(aspectChangeSegmentedControlDidChangeValue:) forControlEvents:UIControlEventValueChanged];
    self.navigationItem.titleView = aspectChangeSegmentedControl;
}
```

视图控制器的其余实现是非常标准的（见清单4.16）。

```objc
//A handy method to implement — returns the photo model at any index path
-(AFPhotoModel *)photoModelForIndexPath:(NSIndexPath *)indexPath
{
    if (indexPath.item >= [photoModelArray count]) return nil;
    
    return photoModelArray[indexPath.item];
}

//Configures a cell for a given index path
-(void)configureCell:(AFCollectionViewCell *)cell forIndexPath:(NSIndexPath *)indexPath
{
    // Set the image for the cell
    [cell setImage:[[self photoModelForIndexPath:indexPath] image]];
}

#pragma mark - UICollectionViewDataSource

-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
    //Return the number of photos in our model array
    return [photoModelArray count];
}

-(UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
    AFCollectionViewCell *cell = (AFCollectionViewCell *)[collectionView dequeueReusableCellWithReuseIdentifier:CellIdentifier forIndexPath:indexPath];
    
    //Configure the cell
    [self configureCell:cell forIndexPath:indexPath];
    
    return cell;
}
```

在我们的视图控制器中剩下的最后一个方法将是响应用户与分段控件交互的方法（见清单4.17）。

```objc
-(void)aspectChangeSegmentedControlDidChangeValue:(id)sender
{
    // We need to explicitly tell the collection view layout that we want the change animated.
    [UIView animateWithDuration:0.5f animations:^{
        // This just swaps the two values
        
        if (photoCollectionViewLayout.layoutMode == AFCollectionViewFlowLayoutModeAspectFill) {
            photoCollectionViewLayout.layoutMode = AFCollectionViewFlowLayoutModeAspectFit;
        } else {
            photoCollectionViewLayout.layoutMode = AFCollectionViewFlowLayoutModeAspectFill;
        }
    }];
}
```

我们还没有定义 `layoutMode` 属性，所以我们现在就去做。这就是自定义布局属性子类的作用。我们要添加一个新的布局属性来指定照片的缩放模式。创建一个新的类，它是 `UICollectionViewLayoutAttributes`的子类（见清单4.18）。

```objc
typedef enum : NSUInteger{
    AFCollectionViewFlowLayoutModeAspectFit,    //Default
    AFCollectionViewFlowLayoutModeAspectFill
}AFCollectionViewFlowLayoutMode;

@interface AFCollectionViewLayoutAttributes : UICollectionViewLayoutAttributes

@property (nonatomic, assign) AFCollectionViewFlowLayoutMode layoutMode;

@end
```

这就是我们真正需要的所有东西--布局模式的定义和一个持有它们的属性。然而，看看 `UICollectionViewLayoutAttributes` 的定义；注意它遵守 `NSCopying` 协议。非常重要的是，我们也要遵守这个协议并实现`copyWithZone` 方法。(见清单4.19)。否则，我们的属性将始终为零（编译器保证的）。在iOS 7中的新功能。你现在必须在子类化布局属性时覆盖 `isEqual:` 方法。

```objc
#import "AFCollectionViewLayoutAttributes.h"

@implementation AFCollectionViewLayoutAttributes

-(id)copyWithZone:(NSZone *)zone {
    AFCollectionViewLayoutAttributes *attributes = [super copyWithZone:zone];
    attributes.layoutMode = self.layoutMode;
    return attributes;
}

@end
```

现在我们可以实现我们的流式布局子类了。我创建了一个名为`AFCollectionViewFlowLayout` 的新类，它是`UICollectionViewFlowLayout` 的子类。它如清单4.20所示，从本章前面展示的改进的Survey应用中应该看起来很熟悉。

```objc
#import <UIKit/UIKit.h>

#import "AFCollectionViewLayoutAttributes.h"

#define kMaxItemDimension   100
#define kMaxItemSize        CGSizeMake(kMaxItemDimension, kMaxItemDimension)

@protocol AFCollectionViewDelegateFlowLayout <UICollectionViewDelegateFlowLayout>
@optional
-(AFCollectionViewFlowLayoutMode)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout layoutModeForItemAtIndexPath:(NSIndexPath *)indexPath;
@end

@interface AFCollectionViewFlowLayout : UICollectionViewFlowLayout

@property (nonatomic, assign) AFCollectionViewFlowLayoutMode layoutMode;

@end
```

我们所做的是扩展 `UICollectionViewDelegateFlowLayout` 协议来创建我们自己的布局。就像我们为 `Survey` 应用定制单个单元格的大小一样，我们希望提供一个接口，让使用我们布局的开发人员可以为他们单元格中的照片指定单独的纵横比。

现在我们已经有了我们的自定义布局属性类，让我们简单地看看我们的自定义布局的部分，你应该已经熟悉了（见清单4.21）。

```objc
-(id)init
{
    if (!(self = [super init])) return nil;
    
    // Some basic setup. 140x140 + 3*13 ~= 320, so we can get a two-column grid in portrait orientation.
    self.itemSize = kMaxItemSize;
    self.sectionInset = UIEdgeInsetsMake(13.0f, 13.0f, 13.0f, 13.0f);
    self.minimumInteritemSpacing = 13.0f;
    self.minimumLineSpacing = 13.0f;
    
    return self;
}

-(void)applyLayoutAttributes:(AFCollectionViewLayoutAttributes *)attributes
{
    // Check for representedElementKind being nil, indicating this is a cell and not a header or decoration view
    if (attributes.representedElementKind == nil)
    {
        // Pass our layout mode onto the layout attributes
        attributes.layoutMode = self.layoutMode;
        
        if ([self.collectionView.delegate respondsToSelector:@selector(collectionView:layout:layoutModeForItemAtIndexPath:)])
        {
            attributes.layoutMode = [(id<AFCollectionViewDelegateFlowLayout>)self.collectionView.delegate collectionView:self.collectionView layout:self layoutModeForItemAtIndexPath:attributes.indexPath];
        }
    }
}

-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
{
    NSArray *attributesArray = [super layoutAttributesForElementsInRect:rect];
    
    for (AFCollectionViewLayoutAttributes *attributes in attributesArray)
    {
        [self applyLayoutAttributes:attributes];
    }
    
    return attributesArray;
}

-(UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath
{
    AFCollectionViewLayoutAttributes *attributes = (AFCollectionViewLayoutAttributes *)[super layoutAttributesForItemAtIndexPath:indexPath];
    
    [self applyLayoutAttributes:attributes];
    
    return attributes;
}
```

这与我们在本章前面的第一个流式布局子类中看到的代码是一样的，不同的是我们使用的是 `AFCollectionViewLayoutAttributes` 而不是 `UICollectionViewLayoutAttributes`，而且我们还传递了我们的`layoutMode`。

在 `applyLayoutAttributes:` 中，我们检查集合视图的委托，看它是否响应我们在`AFCollectionViewDelegateFlowLayout`协议中定义的选择器。如果它响应了，我们就把它投给一个符合协议的 id，这样我们就可以从它那里抓取布局模式。

观察敏锐的读者可能会问自己，集合视图是如何知道使用我们自定义的`UICollectionViewLayoutAttributes`子类的。答案很简单。我们的布局需要实现一个类方法来告诉集合视图使用哪个自定义类（见清单4.22）。显然，默认的实现会返回`UICollectionViewLayoutAttributes`。

```objc
+(Class)layoutAttributesClass
{
    // Important for letting UICollectionView know what kind of attributes to use.
    return [AFCollectionViewLayoutAttributes class];
}
```

唯一缺少的另一个组件是我们的布局可能最终处于无效状态。如果我们改变布局模式而不更新已经在屏幕上布局的单元格，已经显示的单元格将仍然应用旧的布局，而由于滚动或插入而变得可见的单元格将拥有新的布局。我们需要的是，每当我们的布局模式发生变化时，就调用 `invalidateLayout` 方法。

```objc
-(void)setLayoutMode:(AFCollectionViewFlowLayoutMode)layoutMode {
    // Update our backing ivar...
    _layoutMode = layoutMode;
    
    // 然后使我们旧的布局无效。
    [self invalidateLayout];
}
```

我知道我们已经写了很多代码，但没有任何回报，但请再忍耐一下。即使我们有了我们的自定义布局，并且正在设置自定义属性，我们仍然没有任何代码将该属性应用到单元格中。我创建了一个 `UICollectionViewCell` 的子类 `AFCollectionViewCell`。它显示由其`setImage:` 方法设置的图像。清单4.24所示的实现，与第3章的 Survey 应用程序中使用的实现几乎相同。然而，存在两个关键的区别。

首先，我们为布局模式声明了一个实例变量，其次，我们在一个新的方法中使用该实例变量来设置图像视图的 `frame`。这个问题与iOS 7中引擎的变化有关；现在方法的调用顺序不同，所以每当设置一个新的图像时，设置图像的 `frame` 是很重要的（这很有意义，因为图像的 `frame` 取决于图像的纵横比，而我们在设置 `UIImage` 实例之前是不知道的）。

```objc
@implementation AFCollectionViewCell {

UIImageView *imageView;

AFCollectionViewFlowLayoutMode layoutMode; }

- (id)initWithFrame:(CGRect)frame { if (!(self = [super initWithFrame:frame])) return nil;

// Set up our image view imageView = [[UIImageView alloc] initWithFrame:CGRectMake(0, 0,

CGRectGetWidth(frame), CGRectGetHeight(frame))]; imageView.contentMode = UIViewContentModeScaleAspectFill; imageView.autoresizingMask = UIViewAutoresizingFlexibleWidth |

UIViewAutoresizingFlexibleHeight; imageView.clipsToBounds = YES; [self.contentView addSubview:imageView];

// This will make the rest of our cell, outside the image view, appear transparent against a black background.

self.backgroundColor = [UIColor blackColor]; return self;

}

-(void)prepareForReuse { [super prepareForReuse];

[self setImage:nil];

}

#pragma mark - Public Methods

-(void)setImage:(UIImage *)image { [imageView setImage:image]; [self setImageViewFrame]; }

-(void)setImageViewFrame { //start out with the detail image size of the maximum size CGSize imageViewSize = self.bounds.size;

if (layoutMode == AFCollectionViewFlowLayoutModeAspectFit) {

//Determine the size and aspect ratio for the model's image CGSize photoSize = imageView.image.size; CGFloat aspectRatio = photoSize.width / photoSize.height;

if (aspectRatio < 1) {

//The photo is taller than it is wide, so constrain the width

imageViewSize = CGSizeMake(CGRectGetWidth(self.bounds) *

aspectRatio, CGRectGetHeight(self.bounds)); } else if (aspectRatio > 1) {

//The photo is wider than it is tall, so constrain the height

imageViewSize = CGSizeMake(CGRectGetWidth(self.bounds),

CGRectGetHeight(self.bounds) / aspectRatio); }

}

// Set the size of the imageView ... imageView.bounds = CGRectMake(0, 0, imageViewSize.width, imageViewSize.height); // And the center, too.

imageView.center = CGPointMake(CGRectGetMidX(self.bounds), CGRectGetMidY(self.bounds));

}

@end
```

重要的是，图像视图的 `clipsToBounds` 属性被设置为 `YES`。这就确保了当照片被缩放以适合于图像视图，并裁剪自身的一部分时，被裁剪的区域将不可见。

接下来，我们有代码来实际应用布局模式到单元格中（见清单4.25）。

```objc
-(void)applyLayoutAttributes:(UICollectionViewLayoutAttributes *)layoutAttributes {
    [super applyLayoutAttributes:layoutAttributes];
    
    // Important! Check to make sure we're actually this special subclass.
    // Failing to do so could cause the app to crash!
    if (![layoutAttributes isKindOfClass:[AFCollectionViewLayoutAttributes class]]) {
        return;
    }
    
    AFCollectionViewLayoutAttributes *castedLayoutAttributes = (AFCollectionViewLayoutAttributes *)layoutAttributes;
    
    //start out with the detail image size of the maximum size
    CGSize imageViewSize = self.bounds.size;
    
    if (castedLayoutAttributes.layoutMode == AFCollectionViewFlowLayoutModeAspectFit) {
        
        //Determine the size and aspect ratio for the model's image
        CGSize photoSize = imageView.image.size;
        CGFloat aspectRatio = photoSize.width / photoSize.height;
        
        if (aspectRatio < 1) {
            //The photo is taller than it is wide, so constrain the width
            imageViewSize = CGSizeMake(CGRectGetWidth(self.bounds) * aspectRatio, CGRectGetHeight(self.bounds));
        } else if (aspectRatio > 1) {
            //The photo is wider than it is tall, so constrain the height
            imageViewSize = CGSizeMake(CGRectGetWidth(self.bounds), CGRectGetHeight(self.bounds) / aspectRatio);
        }
    }
    
    // Set the size of the imageView ...
    imageView.bounds = CGRectMake(0, 0, imageViewSize.width, imageViewSize.height);
    // And the center, too.
    imageView.center = CGPointMake(CGRectGetMidX(self.bounds), CGRectGetMidY(self.bounds));
}
```

这个方法属于 `UICollectionReusableView`，因为布局属性适用于单元格 item、辅助视图和装饰视图。首先，你必须调用 `super` 覆盖父类的实现。接下来，它检查确保布局属性是我们自定义子类的一个实例，然后再投递指针。

我们使用布局模式来决定是否应该将图像视图的大小设置为我们的 `bounds` 大小，或者是否应该调整它。如果模式是纵横适配，我们使用类似于第3章 "内容上下文化 "中的调查视图控制器的逻辑来调整它。最后，我们设置图像视图的边界和中心。我们使用大小和位置而不是 `contentMode`，这样我们就可以很容易地从一种模式过渡到另一种模式的动画。`bounds` 和 `center` 是隐式的可动画属性）。

最后，在所有这些代码之后，你可以运行应用程序，并在纵横向适合和纵横向填充照片之间进行过渡（见图4.6）。它将对过渡进行动画处理，即使是滚动或旋转的动画。

...

## 网格视图之外

到目前为止，我们看到的流式布局所做的都是网格视图的一些变化。虽然网格布局的确是一种基于线条的、打破常规的布局方式，但它只是这种布局的一种特殊情况。让我们更进一步，做一些真正有趣的事情。

我们要实现一个封面流布局。在此之前，我要特别感谢 Mark Pospesel 在 GitHub 上建立了他的 [Introducing Collection Views](https://github.com/mpospese/IntroducingCollectionViews) 项目。我书中这一节的代码大量借鉴了他的例子，经他许可使用。本节的示例代码可以以 Cover Flow 的名义获得。

创建一个标准的 “single-view Xcode 项目并删除.xib文件" 之后，我们要做的第一件事是在项目导航器窗格中打开项目设置。在 "Build Phases"中，展开 "Link Binary with Libraries "并点击加号。选择并添加 QuartzCore 框架，打开 Supporting Files 组下的 Prefix 文件。我的叫 `CoverFlowPrefix.pch`；它是一个头文件，会被导入到所有的头文件中。添加 `#import <QuartzCore/QuartzCore.h>` 到 PCH 中。现在我们可以在整个项目中访问所有的 QuartzCore 框架。我们以后会需要这个来使用 `CALayer`。这一步对我来说是创建 Xcode 项目中很常见的一步，苹果公司默认不包含它真是个奇迹。

视图控制器将与 Dimensions 非常相似，只是这次我们将有两个布局。我们将像上次一样，在导航栏中使用一个分段控件来切换这两种布局（见清单4.26）。

```objc
@implementation AFViewController
{
    // Array of selection objects
    NSArray *photoModelArray;
    
    UISegmentedControl *layoutChangeSegmentedControl;
    
    AFCoverFlowFlowLayout *coverFlowCollectionViewLayout;
    UICollectionViewFlowLayout *boringCollectionViewLayout;
}

// Static identifiers for cells and supplementary views
static NSString *CellIdentifier = @"CellIdentifier";

-(void)loadView
{
    // Create our view
     
     /**
     MARK:这里创建了两个布局对象，一个是自定义的 AFCoverFlowFlowLayout，另一个是 UICollectionViewFlowLayout。
     通过 UISegmentedControl 进行布局方式的切换
     */
    // 初始化自定义集合视图布局，封面流布局
    coverFlowCollectionViewLayout = [[AFCoverFlowFlowLayout alloc] init];
    
    // 创建一个基本的流程布局，将在纵向容纳三列
    boringCollectionViewLayout = [[UICollectionViewFlowLayout alloc] init];
    boringCollectionViewLayout.itemSize = CGSizeMake(140, 140);
    boringCollectionViewLayout.minimumLineSpacing = 10.0f;
    boringCollectionViewLayout.minimumInteritemSpacing = 10.0f;
    
    // Create a new collection view with our flow layout and set ourself as delegate and data source
    UICollectionView *photoCollectionView = [[UICollectionView alloc] initWithFrame:CGRectZero collectionViewLayout:boringCollectionViewLayout];
    photoCollectionView.dataSource = self;
    photoCollectionView.delegate = self;
    
    // Register our classes so we can use our custom subclassed cell and header
    [photoCollectionView registerClass:[AFCollectionViewCell class] forCellWithReuseIdentifier:CellIdentifier];
    
    // Set up the collection view geometry to cover the whole screen in any orientation and other view properties
    photoCollectionView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    photoCollectionView.allowsSelection = NO;
    photoCollectionView.indicatorStyle = UIScrollViewIndicatorStyleWhite;
    
    // Finally, set our collectionView (since we are a collection view controller, this also sets self.view)
    self.collectionView = photoCollectionView;
    
    // Set up our model
    [self setupModel];
}

-(void)viewDidLoad {
    [super viewDidLoad];
    
    // Crate a segmented control to sit in our navigation bar
    layoutChangeSegmentedControl = [[UISegmentedControl alloc] initWithItems:@[@"Boring", @"Cover Flow"]];
    layoutChangeSegmentedControl.selectedSegmentIndex = 0;
    [layoutChangeSegmentedControl addTarget:self action:@selector(layoutChangeSegmentedControlDidChangeValue:) forControlEvents:UIControlEventValueChanged];
    self.navigationItem.titleView = layoutChangeSegmentedControl;
}
```

配置集合视图的数据源方法与上一节中使用的方法完全相同，因此这里没有显示它们。然而，我们将实现一个新的`UICollectionViewDelegateFlowLayout` 方法，它将负责返回我们布局的边缘插入量（见清单4.27）。我们使用这种方法是因为 Cover Flow 布局需要不同的 section 边缘插入量，这取决于接口的方向和它运行的具体设备。如果可能的话，我喜欢把这种逻辑保留在`UICollectionViewLayout` 子类之外。

```objc
// 自定义 section 边缘插入量
-(UIEdgeInsets)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout *)collectionViewLayout insetForSectionAtIndex:(NSInteger)section
{
    if (collectionViewLayout == boringCollectionViewLayout) {
        // A basic flow layout that will accommodate three columns in portrait
        return UIEdgeInsetsMake(10, 20, 10, 20);
    } else {
        if (UIInterfaceOrientationIsPortrait(self.interfaceOrientation)) {
            // Portrait is the same in either orientation
            return UIEdgeInsetsMake(0, 70, 0, 70);
        } else {
            // We need to get the height of the main screen to see if we're running
            // on a 4" screen. If so, we need extra side padding.
            if (CGRectGetHeight([[UIScreen mainScreen] bounds]) > 480) {
                return UIEdgeInsetsMake(0, 190, 0, 190);
            } else {
                return UIEdgeInsetsMake(0, 150, 0, 150);
            }
        }
    }
}
```

这些值主要是通过实验来确定的，看看什么看起来是正确的。我鼓励你采取这种方法，而不是用数学方法来预测它们，原因很简单，如果你的用户看起来不正确，那么某件事在数学上是否正确并不重要。

最后，我们需要实现我们的用户交互代码。如清单4.28所示，你会发现它与上一个例子类似。

```objc
// !!!: 动态更新集合视图布局
- (void)layoutChangeSegmentedControlDidChangeValue:(id)sender {
    // Change to the alternate layout
    if (layoutChangeSegmentedControl.selectedSegmentIndex == 0) {
        [self.collectionView setCollectionViewLayout:boringCollectionViewLayout animated:NO];
    } else {
        [self.collectionView setCollectionViewLayout:coverFlowCollectionViewLayout animated:NO];
    }
    
    // Invalidate the new layout
    [self.collectionView.collectionViewLayout invalidateLayout];
}
```

我们明确地不对布局的变化进行动画处理，因为它们之间的差别太大，它们之间的动画对用户来说显得很刺眼。正如你在下一章中看到的那样，用动画在布局之间进行改变其实是很容易做到的。

在改变布局之后，我们需要将新布局无效化。虽然这一点没有包含在文档中，但我注意到，如果你省略了这一点，一些布局会出现一些奇怪的行为。实验一下，看看什么对你的自定义布局有效。

我们将创建一个新的自定义 `UICollectionViewLayoutAttributes` 子类，以保存两个值：一个用于指示我们是否应该栅格化图层，另一个用于指示单元格应该如何 "遮挡"。我们不能使用 `alpha`，因为半透明的后面的单元格会 "渗入"。新的子类如清单4.29所示。对于我们的封面视图布局，单元格将始终是栅格化的，因为否则它们会因为3D变换而得到一些锯齿状的边缘。

至于遮罩层，我们希望不在集合视图中心的项目不要那么突出，所以我们会在每个单元格的顶部放置一个半透明的遮罩视图。

```objc
//  AFCollectionViewLayoutAttributes.h
@interface AFCollectionViewLayoutAttributes : UICollectionViewLayoutAttributes

@property (nonatomic, assign) BOOL shouldRasterize;
@property (nonatomic, assign) CGFloat maskingValue;

@end

//  AFCollectionViewLayoutAttributes.m
#import "AFCollectionViewLayoutAttributes.h"

@implementation AFCollectionViewLayoutAttributes

-(id)copyWithZone:(NSZone *)zone
{
    AFCollectionViewLayoutAttributes *attributes = [super copyWithZone:zone];
    
    attributes.shouldRasterize = self.shouldRasterize;
    attributes.maskingValue = self.maskingValue;
    
    return attributes;
}

@end
```

> 开启 `shouldRasterize` 后，`CALayer` 会被光栅化为 bitmap，`layer` 的阴影等效果也会被保存到 bitmap 中。

接下来，让我们看看自定义的 `UICollectionViewFlowLayout` 子类本身（见清单4.30）。我省略了文件顶部的#定义，这些定义在后面会用到。我将把它们包含在那里。

```objc
@implementation AFCoverFlowFlowLayout

#pragma mark - Overridden Methods

-(id)init
{
    if (!(self = [super init])) return nil;
    
    // Set up our basic properties
    self.scrollDirection = UICollectionViewScrollDirectionHorizontal;
    self.itemSize = CGSizeMake(180, 180);
    self.minimumLineSpacing = -60;      // Gets items up close to one another
    self.minimumInteritemSpacing = 200; // Makes sure we only have 1 row of items in portrait mode
    
    return self;
}

+(Class)layoutAttributesClass
{
    return [AFCollectionViewLayoutAttributes class];
}

-(BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)oldBounds
{
    // Very important — needed to re-layout the cells when scrolling.
    return YES;
}

-(NSArray*)layoutAttributesForElementsInRect:(CGRect)rect
{
    NSArray* layoutAttributesArray = [super layoutAttributesForElementsInRect:rect];
    
    // We're going to calculate the rect of the collection view visible to the user.
    CGRect visibleRect = CGRectMake(self.collectionView.contentOffset.x, self.collectionView.contentOffset.y, CGRectGetWidth(self.collectionView.bounds), CGRectGetHeight(self.collectionView.bounds));
    
    for (UICollectionViewLayoutAttributes* attributes in layoutAttributesArray)
    {
        // We're going to calculate the rect of the collection view visible to the user.
        // That way, we can avoid laying out cells that are not visible.
        if (CGRectIntersectsRect(attributes.frame, rect))
        {
            [self applyLayoutAttributes:attributes forVisibleRect:visibleRect];
        }
    }
    
    return layoutAttributesArray;
}

- (UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath
{
    UICollectionViewLayoutAttributes *attributes = [super layoutAttributesForItemAtIndexPath:indexPath];
    
    // We're going to calculate the rect of the collection view visible to the user.
    CGRect visibleRect = CGRectMake(self.collectionView.contentOffset.x, self.collectionView.contentOffset.y, CGRectGetWidth(self.collectionView.bounds), CGRectGetHeight(self.collectionView.bounds));
    
    [self applyLayoutAttributes:attributes forVisibleRect:visibleRect];
    
    return attributes;
}
```

其中大部分是标准的流程布局代码。然而，请注意，我们正在计算集合视图中的可见矩形。这个矩形将被用来决定对每个单元格应用多少3D变换和转换。我们将通过获取集合视图的内容偏移和边界大小来轻松计算。

我们还在 `shouldInvalidateLayoutForBoundsChange` 中返回YES，这样当用户滚动集合视图时，单元格的变换会被重新计算（在每一帧刷新时）。

最小线间距（`minimumLineSpacing`）为负值，因为我们希望我们的单元格 "捆绑 "在一起，而在水平滚动的集合视图中，线间距是每个垂直列单元格之间的距离。如图 4.7 所示，行间距是按行间的空间计算的，而项目间的间距是沿行的单元格之间的空间。

![图 4.17](https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/Jietu20200831-163719.png)



这可能是一个棘手的问题，所以请记住，在垂直滚动的集合视图中，行间距和项目间的间距分别类似于写作中的行高和内核。在水平滚动的集合视图中，它们是翻转的。

接下来是用于对我们的单元格应用透视三维变换的密集数学计算（见清单4.31）。再次，我需要感谢 Mark Pospesel 的帮助）。

```objc
#define ACTIVE_DISTANCE         100
#define TRANSLATE_DISTANCE      100
#define ZOOM_FACTOR             0.2f
#define FLOW_OFFSET             40
#define INACTIVE_GREY_VALUE     0.6f

#pragma mark - Private Custom Methods

/**
 !!!: 自定义布局，通过 item 与中心点的距离执行 3D 变换
 */
-(void)applyLayoutAttributes:(UICollectionViewLayoutAttributes *)attributes forVisibleRect:(CGRect)visibleRect
{
    // Applies the cover flow effect to the given layout attributes.
    
    // We want to skip supplementary views.
    if (attributes.representedElementKind) return;
    
    // Calculate the distance from the center of the visible rect to the center of the attributes.
    // Then normalize it so we can compare them all. This way, all items further away than the
    // active get the same transform.
    CGFloat distanceFromVisibleRectToItem = CGRectGetMidX(visibleRect) - attributes.center.x;
    CGFloat normalizedDistance = distanceFromVisibleRectToItem / ACTIVE_DISTANCE;
    BOOL isLeft = distanceFromVisibleRectToItem > 0;
    CATransform3D transform = CATransform3DIdentity;
    
    CGFloat maskAlpha = 0.0f;
    
    if (fabs(distanceFromVisibleRectToItem) < ACTIVE_DISTANCE) {
        // We're close enough to apply the transform in relation to
        // how far away from the center we are.
        
        transform = CATransform3DTranslate(CATransform3DIdentity, (isLeft? - FLOW_OFFSET : FLOW_OFFSET)*ABS(distanceFromVisibleRectToItem/TRANSLATE_DISTANCE), 0, (1 - fabs(normalizedDistance)) * 40000 + (isLeft? 200 : 0));
        
        // Set the perspective of the transform.
        transform.m34 = -1/(4.6777 * self.itemSize.width);
        
        // Set the zoom factor.
        CGFloat zoom = 1 + ZOOM_FACTOR*(1 - ABS(normalizedDistance));
        transform = CATransform3DRotate(transform, (isLeft? 1 : -1) * fabs(normalizedDistance) * 45 * M_PI / 180, 0, 1, 0);
        transform = CATransform3DScale(transform, zoom, zoom, 1);
        attributes.zIndex = 1;
        
        CGFloat ratioToCenter = (ACTIVE_DISTANCE - fabs(distanceFromVisibleRectToItem)) / ACTIVE_DISTANCE;
        // Interpolate between 0.0f and INACTIVE_GREY_VALUE
        maskAlpha = INACTIVE_GREY_VALUE + ratioToCenter * (-INACTIVE_GREY_VALUE);
    } else {
        // We're too far away - just apply a standard perspective transform.
        
        transform.m34 = -1/(4.6777 * self.itemSize.width);
        transform = CATransform3DTranslate(transform, isLeft? -FLOW_OFFSET : FLOW_OFFSET, 0, 0);
        transform = CATransform3DRotate(transform, (isLeft? 1 : -1) * 45 * M_PI / 180, 0, 1, 0);
        attributes.zIndex = 0;
        
        maskAlpha = INACTIVE_GREY_VALUE;
    }
    
    attributes.transform3D = transform;
    
    // Rasterize the cells for smoother edges.
    [(AFCollectionViewLayoutAttributes *)attributes setShouldRasterize:YES];
    [(AFCollectionViewLayoutAttributes *)attributes setMaskingValue:maskAlpha];
}
```

唷！不要担心，如果它看起来像很多。我将介绍更多细节，你可以稍后再做具体实验，毕竟这不是一本关于 CATransform3D 的书。最重要的是，你可以通过集合视图在三维空间应用变换。酷毙了!

如果属性的 itme 足够接近可见区域的中心，第一个if分支就会执行。它将根据它与中心的接近程度，给它进行缩放、平移和三维透视变换。如果一个 item 正好在中心，那么变换就不会有任何作用。

如果 item 离中心足够远，则 else 分支会执行，以确保 item 不会变得过于变换。想象一下，在 Cover Flow 中，延伸到边缘的 item 不断地被应用了越来越多的变换，它们最终会变得如此变换，以至于它们会翻转到它们的另一边！我们还想设置一个默认的变换分支。

我们还要设置默认的蒙版值为0，并始终将光栅化设置为YES。现在让我们运行应用程序，看看发生了什么。请注意，你可以非常容易地在普通流布局和封面流布局之间切换（见图4.8）。

<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/screenshot_01.PNG" style="zoom:50%;" />



<img src="https://blog-andy0570-1256077835.cos.ap-shanghai.myqcloud.com/site_Images/screenshot_02.PNG" style="zoom:50%;" />

它看起来很棒。然而，这有几个问题。首先，注意到集合视图在单元格之间停了一半；在真正的 Cover Flow 中，滚动视图在一个项目完全居中的情况下停止。其次，你可以清楚地看到，我们的蒙版和栅格化的布局属性没有被应用。嗯，那是因为我们还没有应用遮罩和光栅化的布局属性。哦，那是因为我们还没有写出这样的代码。我们先来处理第一个问题。

`targetContentOffsetForProposedContentOffset:withScrollingVelocity:` 是一个定义在 `UICollectionViewLayout`  中的方法，并且可以被重写。

子类，包括我们的子类。它为子类提供了一个定义集合视图将 "扣 "到哪里的机会。我们要实现它，并使用我们在  `layoutAttributesForElementsInRect:`  中的现有代码来获取拟议矩形中元素的属性（见清单4.32）。然后，我们将找到其项目将最接近拟议的可见矩形中心的属性。然后，我们将找出该项目将有多远，并返回一个调整后的内容偏移，使该视图居中。

```objc
- (CGPoint)targetContentOffsetForProposedContentOffset:(CGPoint)proposedContentOffset withScrollingVelocity:(CGPoint)velocity
{
    // 返回一个我们想要让集合视图停止滚动的坐标点
    
    // First, calculate the proposed center of the collection view once the collection view has stopped
    CGFloat offsetAdjustment = MAXFLOAT;
    CGFloat horizontalCenter = proposedContentOffset.x + (CGRectGetWidth(self.collectionView.bounds) / 2.0);
    // Use the center to find the proposed visible rect.
    CGRect proposedRect = CGRectMake(proposedContentOffset.x, 0.0, self.collectionView.bounds.size.width, self.collectionView.bounds.size.height);
    
    // Get the attributes for the cells in that rect.
    NSArray* array = [self layoutAttributesForElementsInRect:proposedRect];
    
    // This loop will find the closest cell to proposed center of the collection view
    for (UICollectionViewLayoutAttributes* layoutAttributes in array)
    {
        // We want to skip supplementary views
        if (layoutAttributes.representedElementCategory != UICollectionElementCategoryCell)
            continue;
        
        // Determine if this layout attribute's cell is closer than the closest we have so far
        CGFloat itemHorizontalCenter = layoutAttributes.center.x;
        if (fabs(itemHorizontalCenter - horizontalCenter) < fabs(offsetAdjustment)) {
            offsetAdjustment = itemHorizontalCenter - horizontalCenter;
        }
    }
    
    return CGPointMake(proposedContentOffset.x + offsetAdjustment, proposedContentOffset.y);
}
```

现在，我们的应用程序将快照到最近的项目。接下来让我们实现我们的UICollectionViewCell子类。清单4.33中有完整的实现，但重要的方法是 `applyLayoutAttributes:`。

```objc
@implementation AFCollectionViewCell
{
    UIImageView *imageView;
    UIView *maskView;
}

- (id)initWithFrame:(CGRect)frame {
    if (!(self = [super initWithFrame:frame])) return nil;
    
    // Set up our image view
    imageView = [[UIImageView alloc] initWithFrame:CGRectInset(CGRectMake(0, 0, CGRectGetWidth(frame), CGRectGetHeight(frame)), 10, 10)];
    imageView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    imageView.clipsToBounds = YES;
    [self.contentView addSubview:imageView];
    
    // 遮罩视图
    maskView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, CGRectGetWidth(frame), CGRectGetHeight(frame))];
    maskView.backgroundColor = [UIColor blackColor];
    maskView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    maskView.alpha = 0.0f;
    [self.contentView insertSubview:maskView aboveSubview:imageView];
    
    // This will make the rest of our cell, outside the image view, appear transparent against a black background.
    self.backgroundColor = [UIColor whiteColor];
    
    return self;
}

#pragma mark - Overridden Methods

-(void)prepareForReuse {
    [super prepareForReuse];
    
    [self setImage:nil];
}

-(void)applyLayoutAttributes:(UICollectionViewLayoutAttributes *)layoutAttributes {
    [super applyLayoutAttributes:layoutAttributes];
    
    maskView.alpha = 0.0f;
    self.layer.shouldRasterize = NO;
    
    // Important! Check to make sure we're actually this special subclass.
    // Failing to do so could cause the app to crash!
    if (![layoutAttributes isKindOfClass:[AFCollectionViewLayoutAttributes class]])
    {
        return;
    }
    
    AFCollectionViewLayoutAttributes *castedLayoutAttributes = (AFCollectionViewLayoutAttributes *)layoutAttributes;
    
    self.layer.shouldRasterize = castedLayoutAttributes.shouldRasterize;
    maskView.alpha = castedLayoutAttributes.maskingValue;
}

#pragma mark - Public Methods

-(void)setImage:(UIImage *)image {
    [imageView setImage:image];
}

@end
```

现在我们可以运行应用程序，看看其他单元格的 "淡入淡出 "效果和快照效果。

很好！玩一玩吧。实验旋转和改变布局，同时集合视图正在减速。找到它的能力和局限性。

现在，我们已经实现了想要达到的效果，我想谈谈我发现的集合视图的几个问题。

首先，封面流视图的旋转动画并不完美。我似乎无法让它实现无缝连接；我想这可能与旋转过程中改变 `contentSize` 有关。

我最初尝试在旋转期间将布局改为 Cover Flow，这样在纵向时使用普通流布局，在横向时使用 Cover Flow 布局。在旋转过程中改变布局非常麻烦，因为在旋转过程中布局子类中的`contentSize` 不可靠，在改变布局时更不可靠。

我研究了这些问题，找到了布局使用时的精确事件顺序。

1. `prepareLayout` 是在布局上调用的，这样它就有机会进行任何前期的计算。
2. `collectionViewContentSize` 在布局上被调用，以确定集合视图的内容大小。
3. `layoutAttributesForElementsInRect:` 被调用。

然后，布局激活，并继续调用 `layoutAttributesForElementsInRect:` 和`layoutAttributesForItemAtIndexPath:`，直到布局变得无效。然后，再次重复这个过程。

在布局中使用 content size 可能不是一个好主意；`UICollectionView` 仍然是非常新的，社区仍在确定使用它的最佳实践。

根据你对布局的想法，可能最好转向 `UICollectionViewLayout`，就像我们在下一章做的那样。然而，始终要先考虑 `UICollectionViewFlowLayout` 是否能完成你的目标。它为你做了很多繁重的工作。

我们现在已经涵盖了装饰视图、集合视图布局、布局属性和自定义动画。你已经巩固了前三章的知识，并为即将到来的一章浸入了水中。我们即将做一些非常有趣的事情，但首先，让我们回顾一下 `UITableView`。



## UITableView：UICollectionView 它爹

UICollectionView 是在 iOS 6 中才引入的，但 UITableView 从 2008 年最初的 iPhone SDK发布时就已经存在了。UITableView 所使用的许多相同的原则也适用于 UICollectionView，但有些已经被修改。

UITableView 最近才开始使用类注册方法来创建其单元格。这是集合视图的唯一方法。

列表视图的 "批量更新（batch updates）"是通过调用一个方法来开始更新，执行更新，然后调用另一个方法来表示更新结束。然而，集合视图只提供了基于 Block 块的 `performBatchUpdates:` 方法（在我看来更好）。

这些都是开发人员通过类来实现其目标的一些微小的差异。这两个类之间更大的哲学差异是，列表视图单元格处理了很多内部布局。这与集合视图单元格形成了鲜明的对比，集合视图单元格完全不处理。这迫使开发者每次都要从头开始实现自己的 UITableViewCell 子类。同时，UITableViewCell有 四种不同的 "样式"，定义了它的两个文本标签、图像视图、"附件 "视图和编辑样式的布局方式。相当大的区别!

我相信，如果苹果今天要引入 `UITableView`，知道他们在过去6年里学到的框架设计知识，`UITableViewCell` 根本不会有样式。相反，他们会有一些直接的子类，开发者可以使用，也可以实现自己的子类。

尽管以现代 Objective-C 框架的标准来看，`UITableView` 显得很臃肿，但`UICollectionView` 的圆滑在很大程度上要归功于苹果公司从最初制作 `UITableView` 时学到的经验。