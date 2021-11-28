* 1字节（byte）=  8 位（bit）
* 内存是有数字编号的，通常用 **地址**（address）来指代某个特定字节的数据。
* 32位CPU或64位CPU，其实是指地址的大小。而64位CPU的寻址能力比32位CPU更强。

### 获取地址

* **变量的地址**：指内存中的某个位置，该位置的内存中保存着变量的值。通过**`&` 运算符**，可以得到变量的地址。
* `%p` 是针对内存地址的格式说明符。
* 一般情况下，内存地址都会以**十六进制**格式输出。

```c
int i = 17;
printf("i stores its value at %p\n", &i);
// i stores its value at 0x7fff5fbff79c
```

* 当程序在计算机上运行时，相关的一切数据都会保存在内存中，并有各自的地址。
* **函数**也有自己的地址，通过函数的函数名，就能得到相应函数的地址。

```c
printf("this function starts at %p\n", main);
// this function starts at 0x100000d10
```

### 指针

* **指针**，就是用特定类型的变量来保存某个内存地址。
* 使用指针的意义：程序不一定能够通过拷贝来传递数据，但是一定能够**直接传递**或**通过拷贝**来传递**数据的起始地址**。一旦有数据的起始地址，就能够很容易地存取相应数据。
* 指针的声明方式：例 `NSString  *a`; 
* 使用**`*` 运算符**，可以访问保存在某个地址中的数据。

`*` 运算符的作用：
   1. 声明指针；
   2. **去引用**：通过指针访问某个地址中的数据；
   3. 向指定的地址保存数据；

```c
int i = 17;
// *声明指针，将变量addressOfI声明为int *，即指向保存int值的内存地址
int *addressOfI = &i;
printf("i stores its value at %p\n",addressOfI);

// *访问保存在addressOfI地址中的int值
printf("the int stored at addressOfI is %d\n",*addressOfI);

//在赋值表达式的左侧使用*运算符，将数据保存在指定的地址中
*addressOfI = 89;
printf("Now i is %d\n",i);
```

输出结果

> **i stores its value at 0x7fff5fbff79c**
>
> **the int stored at addressOfI is 17**
>
> **Now i is 89**



### 不同类型的数据所占用的字节大小

使用 `sizeof()` 函数可以返回某个数据类型的大小，`sizeof()` 函数会返回一个类型为 **size_t** 的数，与之对应的格式说明符为：`%zu` ;

```c
// 使用 sizeof() 可以得到某个数据类型的大小
printf("An char is %zu bytes\n",sizeof(char));
printf("An short is %zu bytes\n",sizeof(short));
printf("An int is %zu bytes\n",sizeof(int));
printf("An long is %zu bytes\n",sizeof(long));
printf("An float is %zu bytes\n",sizeof(float));
printf("An double is %zu bytes\n",sizeof(double));

printf("A pointer is %zu bytes\n",sizeof(int *));

// 调用 sizeof() 时，传入的实参也可以是变量
printf("An int is %zu bytes\n",sizeof(i));
printf("A pointer is %zu bytes\n",sizeof(addressOfI));
```

 输出结果：

> **An char is 1 bytes**
>
> **An short is 2 bytes**
>
> **An int is 4 bytes**
>
> **An long is 8 bytes**
>
> **An float is 4 bytes**
>
> **An double is 8 bytes**
>
> **A pointer is 8 bytes**
>
> **An int is 4 bytes**
>
> **A pointer is 8 bytes**

> 可以通过查看**指针的所占用的字节大小**反向判断运行该程序的处理器（CPU）位数。
如果指针的长度是4个字节，那么运行该程序的处理器就是32位的；
如果指针的长度是8个字节，那么处理器就是64位的。

* 范围，能够保存的最小数和最大数

  
  | 数据类型           | 位数   | 最小数    | 最大数   |
  | -------------- | ---- | ------ | ----- |
  | char           | 8位   | -128   | 127   |
  | Unsigned char  | 8位   | 0      | 255   |
  | short          | 16位  | -32768 | 32767 |
  | Unsigned short | 16位  | 0      | 65535 |

  

### NULL

* **“空”指针**：某些情况下，要有一个能够保存地址的指针变量，但是要赋上某个值，用于明确表示该指针没有指向任何地址。C语言中的 `NULL` 就是这里的“某个值”；
* `NULL` 就是0；
* 有时 `NULL` 表示此处为空值；


在 **if** 的条件表达式中，判断指针是否为空：

  ```c
float *myPointer;
if (myPointer) {
    // myPointer 为 真，即 myPointer != NULL
    printf("myPointer 不是 NULL.\n");
}else {
    printf("myPointer 是 NULL.\n");
}

//  同上
if (myPointer != NULL) {
    printf("myPointer 不是 NULL.\n");
}else {
    printf("myPointer 是 NULL.\n");
}
  ```

  输出结果：

  > **myPointer 是NULL.**
  >
  > **myPointer 是NULL.**

💡 `nil` 和 `NULL` 是相等的，但是Objective-C的程序员通常会使用 `nil` 来代表该地址上没有对象。
