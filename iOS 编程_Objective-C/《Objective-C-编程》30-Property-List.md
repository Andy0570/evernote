Property List 是一种既可以被计算机读取又可以被人阅读的文件格式。Property List 文件可以由以下对象组成：

* **NSArray**
* **NSDictionary**
* **NSString**
* **NSDate**
* **NSData**
* **NSNumber** （封装基础数据类型，整数、浮点数或布尔值）



### 读取或写入 Property List 文件

```objectivec
// 创建一个包含多个 NSDictionary 对象的 NSArray 对象
NSMutableArray *stocks = [[NSMutableArray alloc] init];
NSMutableDictionary *stock;

stock = [NSMutableDictionary dictionary];
[stock setObject:@"AAPL" forKey:@"symbol"];
[stock setObject:[NSNumber numberWithInt:200] forKey:@"shares"];
[stocks addObject:stock];

stock = [NSMutableDictionary dictionary];
[stock setObject:@"GOOG" forKey:@"symbol"];
[stock setObject:[NSNumber numberWithInt:160] forKey:@"shares"];
[stocks addObject:stock];

// 写入 property list 文件
[stocks writeToFile:@"/tmp/stocks.plist" atomically:YES];

// 读取之前生成的 property list 文件
NSArray *stockList = [NSArray arrayWithContentsOfFile:@"/tmp/stocks.plist"];
for (NSDictionary *d in stockList) {
    NSLog(@"I have %@ shares of %@",
          [d objectForKey:@"shares"],[d objectForKey:@"symbol"]);
}
```

使用 Xcode 内置的专用编辑器查看 Property List 文件数据：

![](http://upload-images.jianshu.io/upload_images/2648731-dae9cef3052c4ac3.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)



### 在项目中使用 Property List 文件 

其实我们在项目中看到的 `info.plist` 配置文件就是一个 Property List 文件。其次，还可以使用 Property List 文件格式化一些数据源。

例如：要在首页设置 4 个 Button，每个 Button 都有标题、图片、索引标志。直接用代码的方式将它们分散设置在代码中当然是可以的。但是如果使用 Property List 文件，我们可以把这些数据抽象提取出来格式化保存，一来代码中不同的属性被抽象提取，提高了复用性。二来，如果以后 Button 按钮标题需要修改，直接去 Property List 文件中修改就可以了。

1. 创建 Property List文件：

![](http://upload-images.jianshu.io/upload_images/2648731-9472bf1059567a74.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/800)

2. 读取 Property List 文件：

```objectivec
- (NSArray *)rootArray {
    if (!_rootArray) {
        NSString *path = [[NSBundle mainBundle] pathForResource:@"navigationButton" ofType:@"plist"];
        _rootArray = [NSArray arrayWithContentsOfFile:path];
    }
    return _rootArray;
}
```

3. 读/写 Property List  文件
```objectivec
// 读/写 property list 文件：
NSMutableDictionary *mutableDict = [NSMutableDictionary dictionary];
mutableDict[@"key"] = @"value";
[mutableDict writeToFile:@"/some/path/file" atomically:YES];
    
NSMutableDictionary *anotherDict = [[NSMutableDictionary alloc] initWithContentsOfFile:@"/some/path/file"];
```

### 读取 Property List 文件方法的演进

我需要将项目根目录下的 `mainTableViewTitleModel.plist` 文件读取到 `NSArray` 数组中，下面是关于如何迭代演进该方法的代码示例。

#### Version 1，数据模型转换
上面的示例中，我们只是简单的读取并加载了一个 NSArray 数组，数组中的每一个项都是 `NSDictionary` 实例，如果需要在数组加载完成后，把数组中的 JSON 数据转换成模型（Model），应该怎么做？

开源的数据模型转换框架有很多，YYModel、MJExtension、Mantle... 这里以 Mantle 框架为例，通过 Lazy Loading 的方式加载数据源：

```objectivec
// 从 mainTableViewTitleModel.plist 文件中读取数据源加载到 NSArray 类型的数组中
- (NSArray *)dataSourceArray {
    if (!_dataSourceArray) {
        // mainTableViewTitleModel.plist 文件路径
        NSString *path = [[NSBundle mainBundle] pathForResource:@"mainTableViewTitleModel" ofType:@"plist"];
        
        // 读取 mainTableViewTitleModel.plist 文件，并存放进 jsonArray 数组
        NSArray *jsonArray = [NSArray arrayWithContentsOfFile:path];
        
        // 将 jsonArray 数组中的 JSON 数据转换成 HQLTableViewDataSourceModel 模型
        _dataSourceArray = [MTLJSONAdapter modelsOfClass:HQLTableViewDataSourceModel.class
                                           fromJSONArray:jsonArray
                                                   error:NULL];
    }
    return _dataSourceArray;
}
```
其中，`HQLTableViewDataSourceModel` 类是一个模型类，它是 `MTLModel` 的子类对象，并且遵守 `<MTLJSONSerializing>` 协议。
现在，数组中的每一项都是一个 `HQLTableViewDataSourceModel` 对象的实例。

#### Version 2，方法兼容性适配

Version 1 的示例中，`arrayWithContentsOfFile:` 方法在 Apple 文档中已经被标记为失效（deprecated）了：
```objectivec
/* These methods are deprecated, and will be marked with API_DEPRECATED in a subsequent release. Use the variants that use errors instead. */
+ (nullable NSArray<ObjectType> *)arrayWithContentsOfFile:(NSString *)path API_DEPRECATED_WITH_REPLACEMENT("arrayWithContentsOfURL:error:", macos(10.0, API_TO_BE_DEPRECATED), ios(2.0, API_TO_BE_DEPRECATED), watchos(2.0, API_TO_BE_DEPRECATED), tvos(9.0, API_TO_BE_DEPRECATED));
```

因此，我需要通过宏 `@available(iOS 11.0, *)` 根据 iOS 的不同版本实现兼容性适配。具体来说：
* 在 iOS 11 以前的版本中，我会使用 `NSArray` 的 `arrayWithContentsOfURL:` 方法读取 plist 文件数据。
* 在 iOS 11 及其之后的版本中，我会使用 `NSArray` 的 `arrayWithContentsOfURL:error:` 方法读取 plist 文件数据。

另外，我还想查看该方法是否正常工作并正确加载我所需要的数据源，所以，我尝试在控制台打印输出内容：

```objectivec
// 从 mainTableViewTitleModel.plist 文件中读取数据源加载到 NSArray 类型的数组中
- (NSArray *)dataSourceArray {
    if (!_dataSourceArray) {

        // 1.构造 mainTableViewTitleModel.plist 文件 URL 路径
        NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
        NSURL *url = [bundleURL URLByAppendingPathComponent:@"mainTableViewTitleModel.plist"];
        
        // 2.读取 mainTableViewTitleModel.plist 文件，并存放进 jsonArray 数组
        NSArray *jsonArray;
        if (@available(iOS 11.0, *)) {
            NSError *readFileError = nil;
            jsonArray = [NSArray arrayWithContentsOfURL:url error:&readFileError];
            if (!jsonArray || jsonArray.count == 0) {
                DDLogError(@"%@, NSPropertyList File read error:\n%@", @(__PRETTY_FUNCTION__), readFileError.localizedDescription);
                return nil;
            }
        } else {
            jsonArray = [NSArray arrayWithContentsOfURL:url];
            if (!jsonArray || jsonArray.count == 0) {
                DDLogError(@"%@, NSPropertyList File read error.", @(__PRETTY_FUNCTION__));
                return nil;
            }
        }
        
         // 3.将 jsonArray 数组中的 JSON 数据转换成 HQLTableViewDataSourceModel 模型
        NSError *decodeError = nil;
        _dataSourceArray = [MTLJSONAdapter modelsOfClass:HQLTableViewDataSourceModel.class
                                           fromJSONArray:jsonArray
                                                   error:&decodeError];
        if (!_dataSourceArray) {
            DDLogError(@"%@, jsonArray decode error:\n%@", @(__PRETTY_FUNCTION__), decodeError.localizedDescription);
            return nil;
        }
    }
    return _dataSourceArray;
}
```

以上代码中，每次执行完一个操作，我都进行了空值的判断（美其名曰：编写防御性代码），如果数据为空，则打印输出当前方法名、`NSError` 错误内容以便定位发生错误的代码行，并调试解决问题。

注：`DDLogError()` 是 CocoaLumberjack 框架中打印控制台输出，记录日志的方法，你可以简单理解为类似 `NSLog()` 的宏。

#### Version 3，使用断言处理异常

每次通过控制台打印日志的方式处理错误总不是十分友好，因此，我又尝试进一步改进，使用 `NSAssert()` 断言机制来处理异常：

```objectivec
// 从 mainTableViewTitleModel.plist 文件中读取数据源加载到 NSArray 类型的数组中
- (NSArray<HQLTableViewDataSourceModel *> *)dataSourceArray {
    if (!_dataSourceArray) {
        // 1.构造 mainTableViewTitleModel.plist 文件 URL 路径
        NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
        NSURL *url = [bundleURL URLByAppendingPathComponent:@"mainTableViewTitleModel.plist"];
        
        // 2.读取 mainTableViewTitleModel.plist 文件，并存放进 jsonArray 数组
        NSArray *jsonArray;
        if (@available(iOS 11.0, *)) {
            NSError *readFileError = nil;
            jsonArray = [NSArray arrayWithContentsOfURL:url error:&readFileError];
            NSAssert1(jsonArray, @"NSPropertyList File read error:\n%@", readFileError);
        } else {
            jsonArray = [NSArray arrayWithContentsOfURL:url];
            NSAssert(jsonArray, @"NSPropertyList File read error.");
        }
        
        // 3.将 jsonArray 数组中的 JSON 数据转换成 HQLTableViewDataSourceModel 模型
        NSError *decodeError = nil;
        _dataSourceArray = [MTLJSONAdapter modelsOfClass:HQLTableViewDataSourceModel.class
                                           fromJSONArray:jsonArray
                                                   error:&decodeError];
        NSAssert1(_dataSourceArray, @"JSONArray decode error:\n%@", decodeError);
    }
    return _dataSourceArray;
}
```

通过 `NSAssert()` 断言处理异常的好处是：
1. `NSAssert()` 断言仅仅在开发环境下工作，而我的 Property List 文件本身就是静态文件，因此，我只需要在开发时确保加载的静态文件能正常工作即可。
2. `NSAssert()` 断言方法可以自动在控制台打印当前方法名和各种调试信息，不用再手动敲类似于 `NSLog(@"%s", __PRETTY_FUNCTION__);` 的代码打印当前方法名了。
3. 少写了一些不必要的判断语句和嵌套代码，代码整体更整洁。


#### Version 4，封装为 Store 类

Version 3 版本中，加载 Property List 文件的方法已经可以非常好的工作了，但当我的项目中有很多 Property List 文件需要加载时，难免不了需要编写大量重复的代码，或者称之为有冗余的样板代码。

而且从**职责分离**的角度来考虑，视图控制器也没有必要知道如何去加载文件并解析 JSON 数据。

所以我创建了一个 `HQLPropertyListStore` 对象来做这些事，通过 `HQLPropertyListStore` 对象来加载 Property List 数据。而且，通过将其封装到 `HQLPropertyListStore` 类中就可以实现职责分离，方便复用。

```objectivec
// ---------------------------------------------------------
#import <Foundation/Foundation.h>

@interface HQLPropertyListStore : NSObject

@property (nonatomic, readonly, copy) NSArray *dataSourceArray;

// 指定初始化方法
- (instancetype)initWithPlistFileName:(NSString *)fileName modelsOfClass:(Class)model;

@end

// ---------------------------------------------------------
#import "HQLPropertyListStore.h"

@interface HQLPropertyListStore ()
@property (nonatomic, readwrite, copy) NSArray *dataSourceArray;
@end

@implementation HQLPropertyListStore

- (instancetype)initWithPlistFileName:(NSString *)fileName modelsOfClass:(Class)model {
    self = [super init];
    if (self) {
        [self loadPlistDataWithFileName:fileName modelsOfClass:model];
    }
    return self;
}

- (void)loadPlistDataWithFileName:(NSString *)fileName modelsOfClass:(Class)model {
    // 1.构造 plist 文件 URL 路径
    NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
    NSURL *url = [bundleURL URLByAppendingPathComponent:fileName];
    
    // 2.读取 plist 文件，并存放进 jsonArray 数组
    NSArray *jsonArray;
    if (@available(iOS 11.0, *)) {
        NSError *readFileError = nil;
        jsonArray = [NSArray arrayWithContentsOfURL:url error:&readFileError];
        NSAssert1(jsonArray, @"NSPropertyList File read error:\n%@", readFileError);
    } else {
        jsonArray = [NSArray arrayWithContentsOfURL:url];
        NSAssert(jsonArray, @"NSPropertyList File read error.");
    }
    
    // 3.将 jsonArray 数组中的 JSON 数据转换成 model 模型
    NSError *decodeError = nil;
    self.dataSourceArray = [MTLJSONAdapter modelsOfClass:model.class
                                           fromJSONArray:jsonArray
                                                   error:&decodeError];
    NSAssert1(_dataSourceArray, @"JSONArray decode error:\n%@", decodeError);
}

@end
```

编写完成 `HQLPropertyListStore` 类之后，在任何需要加载 Property List 文件的视图控制器中，只需要编写类似如下的两行代码即可：

```objectivec
HQLPropertyListStore *store = [[HQLPropertyListStore alloc] initWithPlistFileName:@"mainTableViewTitleModel.plist" modelsOfClass:HQLTableViewDataSourceModel.class];
self.dataSourceArray = store.dataSourceArray;
```

#### Version 5，生命不息，优化不止

你要知道，不是所有的本地 JSON 文件都被保存为 `.plist` 文件，也有可能被保存为 `.json` 的原始文件了。最近，我在尝试编写城市选择器页面时，就遇到了这样的情况，如：[https://github.com/houshixian/CityList/blob/master/CityList/CityList/JsonFile/CityArray.json](https://github.com/houshixian/CityList/blob/master/CityList/CityList/JsonFile/CityArray.json)

所以，我需要加载 `.json` 文件，并将其转换为 Model 数据。如果想要单纯的实现读取本地 JSON 文件并转换为 Model 的需求，我用两个方法就可以搞定了：

```objectivec
- (void)loadProvinceData {
    // 1.读取 ProvinceCity.json 文件，并存放进 jsonArray 数组
    NSArray *jsonArray = [self readLocalFileWithName:@"ProvinceCity.json"];
    
    // 2.将 jsonArray 数组中的 JSON 数据转换成 HQLProvince 模型
    NSError *decodeError = nil;
    _provinceArray = [MTLJSONAdapter modelsOfClass:HQLProvince.class
                                     fromJSONArray:jsonArray
                                             error:&decodeError];
    NSAssert1(_provinceArray, @"JSONArray decode error:\n%@", decodeError);
    
    // 初始化并设置默认值
    self.currentProvince = _provinceArray.firstObject;
    self.currentCity = _currentProvince.children.firstObject;
}

// 读取本地 JSON 文件，如：city.json
- (NSArray *)readLocalFileWithName:(NSString *)name {
    // 1.构造文件路径
    NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
    NSURL *url = [bundleURL URLByAppendingPathComponent:name];
    // 2.将文件数据化
    NSData *data = [[NSData alloc] initWithContentsOfURL:url];
    NSError *readFileError = nil;
    // 3.对数据进行 JSON 格式化并返回字典形式
    NSArray *jsonArray = [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:&readFileError];
    NSAssert1(jsonArray, @"JSON File read error:\n%@", readFileError);
    
    return jsonArray;
}
```

但是，为了实现代码复用，我希望将这段代码也添加到 Version 4 中的 `HQLPropertyListStore` 类中：

```objectivec
// ---------------------------------------------------------
#import <Foundation/Foundation.h>

@interface HQLPropertyListStore : NSObject

@property (nonatomic, readonly, copy) NSArray *dataSourceArray;

- (instancetype)initWithPlistFileName:(NSString *)fileName modelsOfClass:(Class)modelClass;

- (instancetype)initWithJSONFileName:(NSString *)fileName modelsOfClass:(Class)modelClass;

@end

// ---------------------------------------------------------
#import "HQLPropertyListStore.h"

@interface HQLPropertyListStore ()
@property (nonatomic, readwrite, copy) NSArray *dataSourceArray;
@end

@implementation HQLPropertyListStore

#pragma mark - Initialize

- (instancetype)initWithPlistFileName:(NSString *)fileName modelsOfClass:(Class)modelClass {
    self = [super init];
    if (self) {
        [self loadPlistFile:fileName modelsOfClass:modelClass];
    }
    return self;
}

- (instancetype)initWithJSONFileName:(NSString *)fileName modelsOfClass:(Class)modelClass {
    self = [super init];
    if (self) {
        [self loadJSONFile:fileName modelsOfClass:modelClass];
    }
    return self;
}

#pragma mark - Private

- (void)loadPlistFile:(NSString *)fileName modelsOfClass:(Class)modelClass {
    NSArray *jsonArray = [self readLocalPlistFile:fileName];
    [self convertJSONArray:jsonArray toModelsOfClass:modelClass];
}

- (void)loadJSONFile:(NSString *)fileName modelsOfClass:(Class)modelClass {
    NSArray *jsonArray = [self readLocalJSONFile:fileName];
    [self convertJSONArray:jsonArray toModelsOfClass:modelClass];
}

// 读取 plist 文件
- (NSArray *)readLocalPlistFile:(NSString *)fileName {
    // 1.构造 plist 文件 URL 路径
    NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
    NSURL *url = [bundleURL URLByAppendingPathComponent:fileName];
    
    // 2.读取 plist 文件，并存放进 jsonArray 数组
    NSArray *jsonArray;
    if (@available(iOS 11.0, *)) {
        NSError *readFileError = nil;
        jsonArray = [NSArray arrayWithContentsOfURL:url error:&readFileError];
        NSAssert1(jsonArray, @"NSPropertyList File read error:\n%@", readFileError);
    } else {
        jsonArray = [NSArray arrayWithContentsOfURL:url];
        NSAssert(jsonArray, @"NSPropertyList File read error.");
    }
    
    return jsonArray;
}

// 读取本地 JSON 文件，如：city.json
- (NSArray *)readLocalJSONFile:(NSString *)fileName {
    // 1.构造 json 文件路径
    NSURL *bundleURL = [[NSBundle mainBundle] bundleURL];
    NSURL *url = [bundleURL URLByAppendingPathComponent:fileName];
    
    // 2.将文件数据化
    NSData *data = [[NSData alloc] initWithContentsOfURL:url];
    
    // 3.对数据进行 JSON 格式化并返回字典形式
    NSError *readFileError = nil;
    NSArray *jsonArray = [NSJSONSerialization JSONObjectWithData:data options:kNilOptions error:&readFileError];
    NSAssert1(jsonArray, @"JSON File read error:\n%@", readFileError);
    
    return jsonArray;
}

// 将 JSON 数据转换为 Model
- (void)convertJSONArray:(NSArray *)jsonArray toModelsOfClass:(Class)modelClass {
    if ([modelClass isSubclassOfClass:MTLModel.class]) {
        NSError *decodeError = nil;
        self.dataSourceArray = [MTLJSONAdapter modelsOfClass:modelClass fromJSONArray:jsonArray error:&decodeError];
        NSAssert1(_dataSourceArray, @"JSONArray decode error:\n%@", decodeError);
    } else {
        NSAssert(NO, @"Unsupported Class Types.");
    }
}

@end
```

更新完 `HQLPropertyListStore` 类后，你会发现，即使我在实现文件中对这两个方法都进行了拆分和优化，但之前的读取 Plist 数据的方法声明并没有变化，所以不会对之前项目中的代码造成破坏，而且我为它增加了可以读取 JSON 文件的新特性。

🎉🎉🎉








 
