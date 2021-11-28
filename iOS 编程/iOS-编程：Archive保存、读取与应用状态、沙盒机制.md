# 保存和读取数据的机制
在之前写的Homeowner基础上更新，**通过固化来保存和读取模型对象**，当用户重新运行Homeowner应用时，可以读取之前创建并保存的模型对象。

之前的应用：[《iOS编程（第四版）》Demo8：Homeowner](http://www.jianshu.com/p/08d097e17a25)

##固化机制
**固化** 是由iOS SDK提供的一种保存和读取对象的机制。当应用固化某个对象时，会将该对象的所有属性存入指定的文件夹。当应用解固某个对象时，会从指定的文件读取相应的数据，然后根据数据还原对象。   
为了固化或解固某个对象，相应对象的类必须遵守 **NSCoding** 协议，并实现两个必须方法：

```
@protocol NSCoding

- (void)encodeWithCoder:(NSCoder *)aCoder;
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder; // NS_DESIGNATED_INITIALIZER

@end
```


* 在Item.m中实现 **NSCoding** 协议：

固化：

```
- (void) encodeWithCoder:(NSCoder *)aCoder {
    [aCoder encodeObject :self.itemName       forKey      :@"itemName"];
    [aCoder encodeObject :self.serialNumber   forKey  :@"serialNumber"];
    [aCoder encodeObject :self.dateCreated    forKey   :@"dateCreated"];
    [aCoder encodeObject :self.itemKey        forKey       :@"itemKey"];
    [aCoder encodeInt    :self.valueInDollars forKey:@"valueInDollars"];
}
```

解固：

```
- (instancetype) initWithCoder:(NSCoder *)aDecoder {
    self = [super init];
    if (self) {
        _itemName       = [aDecoder decodeObjectForKey     :@"itemName"];
        _serialNumber   = [aDecoder decodeObjectForKey :@"serialNumber"];
        _dateCreated    = [aDecoder decodeObjectForKey  :@"dateCreated"];
        _itemKey        = [aDecoder decodeObjectForKey      :@"itemKey"];
        _valueInDollars = [aDecoder decodeIntForKey  :@"valueInDollars"];
    }
    return self;
}
```

## 应用沙盒机制
* [APP的沙盒文档结构](http://www.jianshu.com/p/a30ae4746aca)
每个iOS应用都有自己专属的应用沙盒（sandbox）。应用沙盒就是文件系统中的目录，但是iOS系统会将每个应用的沙盒目录与文件系统的其他部分隔离。应用只能访问各自的沙盒。

### 应用沙盒目录


* 应用程序包（application bundle）   
  包含应用可执行文件和所有资源文件，例如NIB文件和图像文件。它是只读目录。

* Doucments/   
  存放应用运行时生成的并且需要保留的数据。iTune或iCloud会在同步设备时备份该目录。当设备发生故障时，可以从iTunes或iCloud恢复该目录中的文件。例如，Homepwner应用可将用户所拥有的物品信息保存在Documents/中

* Library/Caches/   
  存放应用运行时生成的需要保留的数据。与Documents/目录不同的是，iTunes或iCloud不会在同步设备时备份该目录。不备份缓存数据的主要原因是相关数据的体积可能会很大，从而延长同步设备所需的时间。如果数据源是在别处（例如web服务器），就可以将得到的数据保存在Library/Caches/目录。当用户需要恢复设备时，相关的应用只需要从数据源（例如web服务器）再次获取数据即可。

* Library/Preferences/   
  存放所有的偏好设置，iOS的设置（Setting）应用也会在该目录中查找应用的设置信息。使用**NSUserDefaults**类，可以通过Library/Preferences/目录中的某个特定文件以键值对形式保存数据。iTunes或iCloud会在同步设备时备份该目录。

* tmp/   
存放应用运行时所需的临时数据。当某个应用没有运行时，iOS系统可能会清除该应用的 tmp/ 目录下的文件，但是为了节约用户设备空间，不能依赖这种自动清除机制，而是当应用不再需要使用 tmp/ 目录中的文件时，就及时手动删除这写文件。iTune或iCloud不会在同步设备时备份 tmp/ 目录。通过 `NSTemporaryDirectory` 函数可以得到应用沙盒中的 tmp/ 目录的全路径。

## 获取文件路径
在实现保存和读取模型对象的功能之前，需要先获取相应文件的全路径（Doucments/）

在HQLItemStore.m中编写一个实现获取路径的方法：

```
- (NSString *) itemArchivePath {

    //NSSearchPathForDirectoriesInDomains：获取沙盒中某种目录的全路径
    //注意第一个参数是NSDocumentDirectory而不是NSDocumentationDirectory
    NSArray *documentDirectiorise = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    //从documentDirectiorise数组获取第一个，也是唯一文档目录路径
    NSString *documentDirectiory = [documentDirectiorise firstObject];
    return [documentDirectiory stringByAppendingPathComponent:@"items.archive"];
}
```

* 啰嗦一下：
这里是用的 HQLItemStore 仓库类来统一管理 Item，因此这个获取路径的方法使用的是 **实例方法**，嗯，并没有任何问题。
试想，如果有一个App需要保存用户账户信息，而且一个 App 就只有一条用户信息，于是我们就把这个管理用户账户的 **Person** 类设置为单例类，但是却没有必要创建仓库类了，因为就只有一条用户信息嘛，于是获取沙盒路径的方法也这样写？写在 **Person** 类里面？也写成实例方法？可能就有点问题了。

```
// 单例类方法
+ (instancetype)sharedUser{
    static Person *sharedUser = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        // 根据路径载入固化文件
        NSString *path = [self itemArchivePath];
       // ToDo
    });
    return sharedUser;
}
```
因为当从文件中解固之前要获取对象，对象还没有创建，先要得到路径，怎么能用实例化方法呢？
* 解决方法是使用 C 函数:
头文件声明：

```
#import <Foundation/Foundation.h>

// 声明辅助函数，用于返回文件路径
NSString *docPath(void);

// ...
```
实现文件：
```
#import "Person.h"

// 辅助函数
NSString *docPath() {
    NSArray *documentDirectories = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    NSString *documentDirectory = [documentDirectories firstObject];
    return [documentDirectory stringByAppendingPathComponent:@"user.archive"];
}
```

调用：

```
Person *user = [NSKeyedUnarchiver unarchiveObjectWithFile:docPath()];
```

## NSKeyedArchiver与NSKeyedUnarchiver

### 应用退出前保存数据
Homepwner应用在退出（exit）时，通过NSKeyedArchiver类保存Item模型对象。

* 在**HQLItemStore.h**中声明一个用于保存数据的新方法：  
 ```- (BOOL) saveChanges;```

* 在HQLItemStore.m中实现该方法：

```
- (BOOL) saveChanges{
    //获取文件路径
    NSString *path = [self itemArchivePath];
    //如果固话成功就返回YES
    return [NSKeyedArchiver archiveRootObject:self.privateItems toFile:path];   
}
```

* 在**AppDelegate.m**顶部导入**HQLItemStore.h**，然后实现
```- (void)applicationDidEnterBackground:```方法,使用此方法可以释放共享资源，保存用户数据，使计时器无效，并且当应用停止运行时存储足够的应用程序状态信息以将应用程序恢复到其之前状态。

```
- (void)applicationDidEnterBackground:(UIApplication *)application {

    //保存用户数据
    BOOL success = [[HQLItemStore sharedStore] saveChanges];
    if (success) {
        NSLog(@"Saved all of the HQLItem");
    }else {
        NSLog(@"Could not save any of the HQLItem");
    }
}
```
### 应用打开前读取数据
为了能在Homepwner启动时载入之前保存的全部Item对象，需要在创建Itemstore对象时使用**NSKeyedUnarchiver**类。
在**HQLItemStore.m**中，修改初始化方法：

```
//这是真正的（私有的）初始化方法
- (instancetype)initPrivate {
    self = [super init];
    //父类的init方法是否成功创建了对象
    if (self) {
        //载入之前保存的全部HQLItem对象
        NSString *path = [self itemArchivePath];
        //根据路径载入固化文件
        _privateItems = [NSKeyedUnarchiver unarchiveObjectWithFile:path];
        //如果之前没有保存过_privateItems，就创建一个新的
        if (!_privateItems) {
            _privateItems = [[NSMutableArray alloc] init];
        }
    }

    return self;
}
```
