> 使用 `TextKit` 框架创建多列布局的文字

### 参考

- [GitHub 源码：shinobicontrols/iOS7-day-by-day](https://github.com/ShinobiControls/iOS7-day-by-day)
- [天天品尝iOS7甜点 :: Day 21 :: Multi-column TextKit text rendering](http://blog.kingiol.com/2014/01/25/ios7-day-by-day-day21-multi-column-textkit-text-rendering/)

![](http://upload-images.jianshu.io/upload_images/2648731-2881781f725298f2.gif?imageMogr2/auto-orient/strip)

### TextKit Demo

```objective-c
#import "SCViewController.h"

@interface SCViewController () {
    NSLayoutManager *_layoutManager;
    NSTextStorage *_textStorage;
}

@property (weak, nonatomic) IBOutlet UIScrollView *scrollView;

@end

@implementation SCViewController

- (void)viewDidLoad {
    [super viewDidLoad];
	
    // 1.将文本内容导入 NSTextStorage 文本存储对象
    NSURL *contentURL = [[NSBundle mainBundle] URLForResource:@"content" withExtension:@"txt"];
    _textStorage = [[NSTextStorage alloc] initWithFileURL:contentURL
                                                  options:@{NSDocumentTypeDocumentAttribute:NSPlainTextDocumentType}
                                       documentAttributes:NULL
                                                    error:NULL];
    
    // 2.创建 NSLayoutManager 布局管理对象
    _layoutManager = [[NSLayoutManager alloc] init];
    [_textStorage addLayoutManager:_layoutManager];
    
    // 布局文本内容
    [self layoutTextContainers];
}


// 循环每个列，创建新的 NSTextContainer 来指定文本的大小，然后使用屏幕上面的 UITextView 进行渲染它。
- (void)layoutTextContainers {
    
    NSUInteger lastRenderedGlyph = 0; // 循环终止变量
    CGFloat currentXOffset = 0;       // X轴偏移量
    // numberOfGlyphs 代表字形总数
    while (lastRenderedGlyph < _layoutManager.numberOfGlyphs) {
        
        // 列的大小,高 = 接近根视图的高，宽 = 根视图的一半。
        CGRect textViewFrame = CGRectMake(currentXOffset, 10,
                                          CGRectGetWidth(self.view.bounds) / 2,
                                          CGRectGetHeight(self.view.bounds) - 20);
        CGSize columnSize = CGSizeMake(CGRectGetWidth(textViewFrame) - 20,
                                       CGRectGetHeight(textViewFrame) - 10);
        
        // 3.创建 NSTextContainer 对列区域中的字形进行布局。
        NSTextContainer *textContainer = [[NSTextContainer alloc] initWithSize:columnSize];
        [_layoutManager addTextContainer:textContainer];
        
        // 4.创建并添加 UITextView 来显示文字
        UITextView *textView = [[UITextView alloc] initWithFrame:textViewFrame
                                                   textContainer:textContainer];
        textView.scrollEnabled = NO;
        [self.scrollView addSubview:textView];
        
        // 更新追踪最后的呈现字形的变量和当前列的位置
        // 增加当前的偏移量
        currentXOffset += CGRectGetWidth(textViewFrame);
        
        // 并找到我们刚刚呈现的字形的索引
        lastRenderedGlyph = NSMaxRange([_layoutManager glyphRangeForTextContainer:textContainer]);
    }
    
    // 根据 X 轴的偏移量更新 scrollView 的宽度
    CGSize contentSize = CGSizeMake(currentXOffset, CGRectGetHeight(self.scrollView.bounds));
    self.scrollView.contentSize = contentSize;
}

@end
```



#### 创建流程

![](http://upload-images.jianshu.io/upload_images/2648731-f13740a323c31f8c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
