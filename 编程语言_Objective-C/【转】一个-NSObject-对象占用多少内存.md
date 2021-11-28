> 原文：[一个 NSObject 对象占用多少内存](https://xiaozhuanlan.com/topic/6382510947)

一个 `NSObject` 对象占用多少内存，可以通过函数打印一下：

```objc
// 需要导入 <objc/runtime.h>
// class_getInstanceSize() 获取类的实例所占用内存的大小（单位是字节）
size_t size = class_getInstanceSize([NSObject class]);
NSLog(@"%zu", size);
// 8 byte

// 需要导入 <malloc/malloc.h>
NSObject *object = [[NSObject alloc] init];
printf("%zu", malloc_size((__bridge const void *)(object)));
// 16 byte
```

`class_getInstanceSize` 得到的值是**一个对象至少需要的内存**，也即所有的成员变量总共所需的内存大小。
`malloc_size` 得到的值是**系统实际分配的值**，因而一个 `NSObject` 对象占用 16 个字节。

---

oc 的代码底层其实是 c 和 c++ 的代码，通过如下命令可以将 oc 代码转成 c++ 代码：

```bash
clang -rewrite-objc main.m -o main.cpp
```

Mac，Windows，iOS 等不同平台，以及不同架构，生成的 c++ 代码是不一样的，可以指定平台以及架构，命令如下：

```bash
xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc main.m -o -main-arm64.cpp
```

`NSObject` 在 c++ 中的实现其实就是 struct 结构体：

```objc
@interface NSObject <NSObject> {
    Class isa; // 8 个字节，也就是 64 位，1 字节（byte）= 8 位（bit）
}
```

```objc
struct NSObject_IMPL {
    Class isa;
};
```

结构体的大小实际上就是成员变量大小的总和，可以看到名为 `NSObject_IMPL` 的对象只有一个成员变量 `isa`, 是一个指针，按说是 8 个字节，为什么说实际是 16 个字节呢？

`alloc` 内部调用的是 `allocWithZone`, 源码调用如下：

```objc
+ (id)allocWithZone:(struct _NSZone *)zone {
    return _objc_rootAllocWithZone(self, (malloc_zone_t *)zone);
}

// objc-class-old.mm
id 
_class_createInstanceFromZone(Class cls, size_t extraBytes, void *zone)
{
    void *bytes;
    size_t size;

    // Can't create something for nothing
    if (!cls) return nil;

    // Allocate and initialize
    // 分配和初始化
    size = cls->alignedInstanceSize() + extraBytes;

    // CF requires all objects be at least 16 bytes.
    // CF 要求所有对象至少为16字节。
    if (size < 16) size = 16;

    if (zone) {
        bytes = malloc_zone_calloc((malloc_zone_t *)zone, 1, size);
    } else {
        bytes = calloc(1, size);
    }

    return objc_constructInstance(cls, bytes);
}
```

系统会至少分配 16 分字节的内存空间。

---

我们尝试搞一个 `Student` 类，继承自 `NSObject`, 有两个成员变量 `age` 和 `no`, 都是 `int` 类型的，搞一个 HighSchoolStudent, 继承自 `Student`, 有一个 `int` 类型的成员变量 `grade`。

```objc
@interface Student : NSObject
{
   int _age;
   int _no;
}
@end

@implementation Student
@end

@interface HighSchoolStudent : Student
@property (nonatomic, assign) int  grade;
@end

@implementation HighSchoolStudent
@end
```

对应的 c++ 代码是这样的：

```c++
struct NSObject_IMPL {
   Class isa;//8
};
//实际占用8=8,根据内存对齐原则,为8的倍数为8
//实际分配16,可以放下

struct Student_IMPL {
   struct NSObject_IMPL NSObject_IVARS;//8
   int _age;//4
   int _no;//4
};
//实际占用8+4+4=16,根据内存对齐原则,为8的倍数为16
//实际分配16,可以放下

struct HighSchoolStudent_IMPL {
   struct Student_IMPL Student_IVARS;
   int _grade;//4
};
//实际占用8+4+4+4=20,根据内存对齐原则,则为8的倍数为24
//实际分配24?
```

```objc
NSObject *obj = [[NSObject alloc] init];
printf("obj 成员变量的实际大小 %zu\n", class_getInstanceSize([NSObject class]));
printf("obj 分配的大小 %zu\n", malloc_size((__bridge const void *)(obj)));

Student *stu = [[Student alloc] init];
printf("stu 成员变量实际大小%zu\n",class_getInstanceSize([stu class]));
printf("stu 分配的大小 %zu\n",malloc_size((__bridge const void *)(stu)));

HighSchoolStudent *highSchoolStu = [[HighSchoolStudent alloc] init];
printf("highSchoolStu 成员变量实际大小 %zu\n",class_getInstanceSize([HighSchoolStudent class]));
printf("highSchoolStu 分配的大小 %zu\n",malloc_size(CFBridgingRetain(highSchoolStu)));
```

```bash
obj 成员变量的实际大小 8
obj 分配的大小 16

stu 成员变量实际大小 16
stu 分配的大小 16

highSchoolStu 成员变量实际大小 24
highSchoolStu 分配的大小 32
```

最后一个 `highSchoolStu` 的实际配合和我们的推测不一致，为什么呢？

接着查看 `alloc` 的调用，下面调用了 `calloc`

```c
void *
calloc(size_t num_items, size_t size)
{
    void *retval;
    retval = malloc_zone_calloc(default_zone, num_items, size);
    if (retval == NULL) {
        errno = ENOMEM;
    }
    return retval;
}


void *
malloc_zone_calloc(malloc_zone_t *zone, size_t num_items, size_t size)
{
    MALLOC_TRACE(TRACE_calloc | DBG_FUNC_START, (uintptr_t)zone, num_items, size, 0);

    void *ptr;
    if (malloc_check_start && (malloc_check_counter++ >= malloc_check_start)) {
        internal_check();
    }

    ptr = zone->calloc(zone, num_items, size);

    if (malloc_logger) {
        malloc_logger(MALLOC_LOG_TYPE_ALLOCATE | MALLOC_LOG_TYPE_HAS_ZONE | MALLOC_LOG_TYPE_CLEARED, (uintptr_t)zone,
                (uintptr_t)(num_items * size), 0, (uintptr_t)ptr, 0);
    }

    MALLOC_TRACE(TRACE_calloc | DBG_FUNC_END, (uintptr_t)zone, num_items, size, (uintptr_t)ptr);
    return ptr;
}
```

找到 zone 发现是一个结构体，zone->calloc 对应如下代码：

```c
    void     *(* MALLOC_ZONE_FN_PTR(calloc))(struct _malloc_zone_t *zone, size_t num_items, size_t size); /* same as malloc, but block returned is set to zero */
```

说是有一个 bucks, 默认分配是 16 的倍数，如果是 24, 则是 32, 如果是 28, 也是 32?

未完待续

### 知识回顾

1. 结构体的地址

   `isa` 在内存中的地址就是结构体的地址，因为结构体的地址就是其中第一个成员的地址

2. 常用 LLDB 调试指令

   ```bash
   po
   p/print
   memery write 
   ```

3. 内存对齐

   结构体的大小必须是最大成员的倍数。



### 总结

* OC 对象的本质是结构体；
* 一个 obj 实例是指向一个结构体的指针；
* `getInstanceSize` 获取的是成员变量的实际大小
* `malloc` 是系统分配的内存大小
* 二者的区别相当于一个盒子有多大空间和实际用了多少空间
* 不过获取成员变量实际大小的时候已经是内存对齐之后的大小了。


