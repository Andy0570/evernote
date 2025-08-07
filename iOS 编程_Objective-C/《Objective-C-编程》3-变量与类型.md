### 变量命名规范

名称必须以**字母**或**下划线**（_）开头，之后可以是任何（大写或小写）字母、下划线或者 0～9 之间的数字组合。

合法名称：
* `price`、`isEqual`、`_number`、`myBook`、`abc_AE86`、

非法名称：
* `sum$value`——$是一个非法字符。
* `piece flag`——名称中间不允许有空格。
* `3Spencer`——名称不能以数字开头。
* `int`——不可使用保留字。

规范的变量名命名方式：
* 小写字母开头。
* 名称中的新单词用大写字母开头。
例：`currentDate`、`businessType`、`name`、`idNumber`、

### 变量

执行中的程序需要空间保存数据，以供将来使用。能够保存数据的空间称为**变量**，每个变量都有名称和类型。通过**声明**类型和名称，可以为程序创建新的变量。

```c
float weight
```
以上代码声明了一个变量，变量的类型是 `float`，名称是 `weight`，但是这个变量还没有被赋值。

为什么要给变量声明类型？
1. 有了变量的类型信息，编译器才能替你检查代码、报告潜在的错误或问题。
2. 有了变量的类型信息，编译器才知道要为相应的变量保留多少存储空间。


```c
#include <stdio.h>

int main(int argc, const char * argv[]) {
    
    // 声明变量，变量名为weight,类型是float
    float weight;
    
    // 为weight变量赋值
    weight = 14.2;
    
    // 向控制台输出weight变量，以便查看
    // 打印：The turkey weights 14.200000.
    printf("The turkey weights %f.\n",weight);
    
    // 再声明一个类型为float的变量
    float cookingTime;
    
    // 计算烤制时间，将结果赋给cookingTime变量
    cookingTime = 15.0 + 15.0 * weight;
    
    // 向控制台数输出cookingTime变量，以便查看
    // 打印：Cook it for 228.000000 minutes.
    printf("Cook it for %f minutes.\n",cookingTime);
    
    // -----------------练习：TwoFloats---------------
    
    float a = 3.14;
    float b = 42.0;
    double sum = a + b ;
    printf("sum = %f \n",sum); // 打印：sum = 45.139999
    printf("sum = %.f \n",sum); // 打印：sum = 45
    printf("sum = %.2f \n",sum); // 打印：sum = 45.14
    
    // 函数结束，返回 0 表示成功
    return 0;
}
```

### 附：C语言保留关键字
——摘自[维基百科](https://zh.wikipedia.org/wiki/C语言#.E5.9C.A8C.E8.AA.9E.E8.A8.80.E4.B8.AD.E7.9A.84.E8.BF.90.E7.AE.97.E7.AC.A6.E5.8F.B7)

![](http://upload-images.jianshu.io/upload_images/2648731-f8e8a1cc4da34942.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/500) 
