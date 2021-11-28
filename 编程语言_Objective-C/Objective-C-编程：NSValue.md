* **NSArray** 等集合类型只能存储对象。——对象
* **NSNumber** 可以将基础数据类型转化为对象。——基础数据类型 ➡️ 对象
* **NSValue** 可以将结构类型转化为对象。——结构 ➡️ 对象

### 包装&展开
* **包装（wrapping）**：结构 ➡️ 对象。
* **展开（unwrapping）**:从对象中解出基本类型。

### NSValue 方法

```objectivec
+ (NSValue *)valueWithPoint:(NSPoint)point;
+ (NSValue *)valueWithSize:(NSSize)size;
+ (NSValue *)valueWithRect:(NSRect)rect;
+ (NSValue *)valueWithEdgeInsets:(NSEdgeInsets)insets NS_AVAILABLE(10_10, 8_0);
```
### 

### CGPoint

```objectivec
// NSPoint 
NSPoint somePoint = NSMakePoint(100, 100);
NSValue *point = [NSValue valueWithPoint:somePoint];

// CGPoint
NSValue *myPointValue = [NSValue valueWithPoint:CGPointMake(10, 10)];
CGPoint point = [myPointValue pointValue];
```

### CGSize

```objectivec
// 大小
NSValue *mySizeValue = [NSValue valueWithSize:CGSizeMake(375, 664)];
CGSize size = [mySizeValue sizeValue];
```

### CGRect

```objectivec
// 矩形区域
NSValue *myRectValue = [NSValue valueWithRect:CGRectMake(0, 0, 375, 664)];
CGRect rect = [myRectValue rectValue];
```

### NSRange

```objectivec
// 范围
NSValue *myRangeValue = [NSValue valueWithRange:NSMakeRange(0, 10)];
NSRange range = [myRangeValue rangeValue];
```

### 结构体

```objectivec
// 对自定义的结构体封包、解包
struct WXpoint {
    float a;
    float b;
};

struct WXpoint p = {10,50};

// 封包
NSValue *pointvalue1 = [NSValue value:&p
  withObjCType:@encode(struct WXpoint)];

// 解包
struct WXpoint p2;
[pointvalue1 getValue:&p2];
```
