# 结构

编写程序时，可能要**使用一个变量来保存多个相关的数据**。C 语言中，可以使用**结构**（structure）来实现这个功能。每份数据称为结构的**成员**（member）。

```c
#include <stdio.h>

// 声明 Person 结构，它包含两个成员变量。
struct Person {
    float heightInMeters;
    int weightInkilos;
};

int main(int argc, const char * argv[]) {
    
    // 声明类型为结构的变量时，需要使用关键字 struct
    struct Person mikey;
    // 通过点语法来访问结构的成员变量
    mikey.heightInMeters = 1.7;
    mikey.weightInkilos = 96;
    
    struct Person aaron;
    aaron.heightInMeters = 1.97;
    aaron.weightInkilos = 84;
    
    printf("Mikey 的升高是 %.2f。\n", mikey.heightInMeters);
    printf("Mikey 的体重是 %d。\n", mikey.weightInkilos);
    printf("Aaron 的升高是 %.2f。\n", aaron.heightInMeters);
    printf("Aaron 的体重是 %d。\n", aaron.weightInkilos);
    return 0;
}
```

说明：以上代码通过 `struct` 关键字声明了一个 Person 结构，它包含两个成员变量：名称为 `heightInMeters` 的 `float` 类型变量，以及名称为 `weightInkilos` 的 `int` 类型变量。

在声明类型为结构的变量时，每次都需要使用关键字 `struct`。为了简化输入，通常使用 `typedef` 关键字为某个结构声明一个新的类型。

```c
#include <stdio.h>

// 声明 Person 结构
typedef struct {
    float heightInMeters;
    int weightInkilos;
} Person;

// 计算一个人的 BMI 指数
float bodyMassIndex(Person p) {
    return p.weightInkilos / (p.heightInMeters * p.heightInMeters);
}

int main(int argc, const char * argv[]) {
    
    Person mikey;
    mikey.heightInMeters = 1.7;
    mikey.weightInkilos = 96;
    
    Person aaron;
    aaron.heightInMeters = 1.97;
    aaron.weightInkilos = 84;
    
    float bmi;
    // 将 Person 结构传给一个函数
    bmi = bodyMassIndex(mikey);
    printf("Mikey 的 BMI 指数是：%.2f。\n", bmi);
    
    bmi = bodyMassIndex(aaron);
    printf("Aaron 的 BMI 指数是：%.2f。\n", bmi);
    return 0;
}
```

# 堆

* **栈**中的内存会在调用函数时由系统自动分配，并在函数结束后**自动释放**。这也是**局部变量**常被称为**自动变量**的原因。
* **帧**：函数中的局部变量。
* **缓冲区**（buffer）：表示一块连续的内存，缓冲区来自特定的内存区域——**堆**（heap），堆和栈是分开的。
* 在堆上，缓冲区独立于任何函数的栈。因此，它可以在多个函数中使用。
* 使用 `malloc()` 函数可以得到一块内存缓冲区。当程序不再使用这块缓冲区时，可以调用 `free()` 函数以释放相应的内存，将其返还给堆。

示例：为程序分配一块至少存放 1000 个 `float` 变量的内存。
```c
#include <stdio.h>
#include <stdlib.h> // malloc 和 free 函数都来自 stdlib

int main(int argc, const char * argv[]) {
    
    // 声明指针变量
    float *startOfBuffer;
    
    // 从堆分配指定字节数的内存
    // 通过 sizeof() 函数获得 1 个缓冲区的准确大小
    startOfBuffer = malloc(1000 * sizeof(float));
    
    // ......使用缓冲区......
    
    // 释放之前分配到的内存，使之能够被重新使用
    free(startOfBuffer);
    
    // 将指针变量赋值为空
    startOfBuffer = NULL;

    return 0;
}
```
以上示例中，`startOfBuffer` 是一个指针，它指向缓冲区中的第一个浮点数，如下图所示：
![](https://upload-images.jianshu.io/upload_images/2648731-d3e8f70bb4b1f7b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们也可以**在堆中为结构分配一块缓冲区**。例如，在堆中为 Person 结构分配内存的代码如下：

```c
#include <stdio.h>
#include <stdlib.h>

// 声明 Person 结构
typedef struct {
    float heightInMeters;
    int weightInkilos;
} Person;

// 计算一个人的 BMI 指数
float bodyMassIndex(Person *p) {
    return p->weightInkilos / (p->heightInMeters * p->heightInMeters);
}

int main(int argc, const char * argv[]) {
    
    // 为一个 Person 结构分配内存，
    // 现在 mikey 是一个指针，指向一块连续的内存区域（堆中的一个缓冲区）
    Person *mikey = (Person *)malloc(sizeof(Person));
    
    // 为一个 Person 结构分配内存
    mikey->heightInMeters = 1.7;
    mikey->weightInkilos = 96;
    
    // 计算并输出 BMI 指数
    float bmi = bodyMassIndex(mikey);
    printf("Mikey 的 BMI 指数是：%.2f。\n", bmi);

    // 释放之前分配到的内存，使之能够被重新使用
    free(mikey);
    
    // 将指针变量赋值为空
    mikey = NULL;
    return 0;
}
```

注意到，`p->weightInkilos` 的作用是：先获取指针 p 指向的数据结构，然后返回该结构的成员变量 `weightInkilos`，如下图所示：

![](https://upload-images.jianshu.io/upload_images/2648731-a7b695b2a8b1196e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也可以理解为：获取对象中的一个属性的值。

**堆中的结构，是 Objective-C 对象的实现基础**。

