# 基础数据类型

![](http://upload-images.jianshu.io/upload_images/2648731-5d23cae672e807bb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)

Tips: 合理使用限定词： `long`、`long long`、`short`、`unsigned` 及 `signed` 可以弹性地获取内存空间以节约内存。

# 数字

![](http://upload-images.jianshu.io/upload_images/2648731-f6f09adf018657a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一、整数

* 整数是没有小数点的数——一个完整的数；
* 为了保存数字，整数变量会占用一定的 **位（bit）数空间**。典型的整数尺寸有 8 位、16 位、32 位和 64 位，而 8 位的无符号数可以保存 0～255 之间的整数。（1 byte = 8 bit）
* 整数还有 **有符号** 和 **无符号** 两种类型。`char` 、`short`、`int`、`long` 和 `long long` 默认是**有符号** 的，加上 `unsigned` 前缀可以得到无符号的版本。

### 整数的声明

| 整数变量类型 | 描述           |
| ----------------- | ------------- |
| `UInt32`             | 无符号 32 位整数 |
| `SInt16`             | 有符号 16 位整数 |
| `Char`               | 8 位            |
| `Short`              | 通常是 16 位     |
| `Int`                  | 通常是 32 位     |
| `long`               | 32 位或 64 位     |
| `long long`       | 64 位           |

注：整数类型所占的位数是平台相关的。

###  用于显示整数的格式说明符号

| 进制     | 格式说明符 |
| -------- | ---------- |
| 十进制   | `%d`         |
| 八进制   | `%o`         |
| 十六进制 | `%x`         |

示例代码：

```objectivec
int x = 255;
printf("x is %d.\n",x);                // x is 255.
printf("In octal,x is %o.\n",x);       // In octal,x is 377.
printf("In hexadecimal,x is %x.\n",x); // In hexadecimal,x is ff.
```

对于位数很多的大整数，可以在 `%` 和转换字符之间加入一个 `l`（针对 `long`） 或 `ll`（针对 `long long`）。示例代码如下：

```objectivec
long x = 255;
printf("x is %ld.\n",x);                // x is 255.
printf("In octal,x is %lo.\n",x);       // In octal,x is 377.
printf("In hexadecimal,x is %lx.\n",x); // In hexadecimal,x is ff.
```

如果要输出的是**无符号的十进制数**，就应该使用 `%u`:

```objectivec
unsigned long x = 255;
printf("x is %lu.\n",x); // x is 255.

// 按八进制和十六进制输出时，会假设数字是无符号的
printf("In octal,x is %lo.\n",x);       // In octal,x is 377.
printf("In hexadecimal,x is %lx.\n",x); // In hexadecimal,x is ff.
```

### 传入特定参数，来设置最小字段宽度和最小数字位数（默认两者都是 0）

```objectivec
// 正数
int m = 42;
NSString *s = [NSString stringWithFormat:@"'%4d' '%-4d' '%+4d' '%4.3d' '%04d'", m, m, m, m, m];
// "[  42] [42  ] [ +42] [ 042] [0042]"

// 负数
int m = -42;
NSString *s = [NSString stringWithFormat:@"'%4d' '%-4d' '%+4d' '%4.3d' '%04d'", m, m, m, m, m];
// "[ -42] [-42 ] [ -42] [-042] [-042]"
```

### 整数运算符

*  算数运算符 `+`、`-` 和 `*` 的作用与数学运算符相同；
*  算数运算符遵循的优先规则：`*` 会在 `+` 或 `-` 之前运算；

#### 整数除法

```objectivec
printf("11 / 3 = %d\n",11 / 3);    //  11 / 3 = 3
printf("-11 / 3 = %d\n", -11 / 3); // -11 / 3 = -3
```

在C语言中，一个整数除以另一个整数，得到的还是整数。系统会将计算结果向 0 的方向取整;

![](http://upload-images.jianshu.io/upload_images/2648731-9d5f429c33c8add9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 ```objectivec
// 11 除以 3，得 3，余 2
printf("11 / 3 = %d remainder of %d\n",11 / 3 , 11 % 3); // 11 / 3 = 3 remainder of 2
 ```

如果要得到 3.666667 这样的结果，就要先通过**转换运算符**（cast operator）将 `int` 类型转换成 `float` 类型，让代码执行**浮点数除法**。
**转换运算符**：指需要转换的目标类型，但是要放在小括号里，并写在需要转换的变量的左侧。
转换运算符的转换规则：
* 如果分子和分母都是整数类型，那么`/` 执行的是 **整数除法**；
* 如果分子和分母有任何一个是浮点数，就会执行 **浮点数除法**；

```objectivec
printf("11 / 3.0 = %f\n",11 / (float)3); // 11 / 3.0 = 3.666667
```
这里的分母是浮点数，所以执行的是**浮点数除法**。


## 二、NSInteger 和 NSUInteger

* `NSInteger` 和 `NSUInteger` 这两种类型无论在 32 位系统还是 64 位系统上都能够通用。`NSInteger` 是有符号的，`NSUInteger` 是无符号的。
* 构建 32 位应用程序时，`NSInteger` 是 32 位整数。 而 64 位应用程序时会将 `NSInteger` 视为 64 位整数。
* 构建 32 位应用程序时，`NSUInteger` 是一个 32 位无符号整数。而 64 位应用程序会将 `NSUInteger` 视为 64 位无符号整数。
* 使用 `printf()` 输出这两种类型的变量，需要先将它们转换成对应的 `long` 类型。

`NSInteger` 和 `NSUInteger` 只是基本整数类型的 `typedef` 宏定义：
```objectivec
#if __LP64__ || NS_BUILD_32_LIKE_64
  typedef long NSInteger;
  typedef unsigned long NSUInteger;
#else
  typedef int NSInteger;
  typedef unsigned int NSUInteger;
#endif
```

`NSInteger` 和 `NSUInteger` 之间的相互转换，只需要做简单的投射：
```objectivec
NSInteger myInt = 0;
NSUInteger unsignedInt = (NSUInteger)myInt;
```


## 三、赋值运算符

C语言中的运算符

![摘自维基百科](http://upload-images.jianshu.io/upload_images/2648731-90418c36c2705e6e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/600)


* 自增运算符（++）

```objectivec
Int x = 5;
x++; // x = x + 1; 
```

* 自减运算符（--）

```objectivec
Int x = 5;
x--; // x = x - 1;
```

* +=、-=、*=、／=、%=

```objectivec
Int x = 5;
x +=5; // x = x + 5;
```

* 函数：`abs()`、`labs()`
  以上函数在标准函数库 `stdlib.h` 中被声明。

| 函数名 | 描述                     |
| ------ | ------------------------ |
| `abs()`  | 计算 `int` 类型数的绝对值  |
| `labs()` | 计算 `long` 类型数的绝对值 |


## 四、浮点数

* 浮点数可以用来代表 “带十进制小数的数”；
* 要区分浮点常量，可以看它是否包含小数点；
* 浮点常量可以用科学记数法表示（1.7e4 代表1.7*10^4）;
* 操作系统存储浮点数的方式：某个底数乘上一个以10为底的指数幂（指数为整数）；
* 与整数一样，浮点数也有不同的尺寸类型，但是浮点数都是**有符号**的。
* `double` 类型的变量可存储的范围大概是 `float` 的两倍；

| 数据类型    | 位数  |
| ----------- | ----- |
| `float`       | 32位  |
| `double`      | 64位  |
| `long double` | 128位 |


### 用于显示浮点数的格式说明符

| 计数方法                     | 格式说明符 |
| ---------------------------- | ---------- |
| 十进制计数法             | `%f`         |
| 科学计数法                 | `%e`         |
| 以 `%f`、`%e` 中较短的输出宽度输出单、双精度实数，在指数小于-4或者大于等于精度时使用`%e`格式 | `%g`        |

```objectivec
double y = 12345.6789;
printf("y is %f\n",y); // y is 12345.678900
printf("y is %e\n",y); // y is 1.234568e+04

// 小数点后保留2位
double y2 = 12345.6789;
printf("y2 is %.2f\n",y2); // y2 is 12345.68
printf("y2 is %.2e\n",y2); // y2 is 1.23e+04
```

> 浮点数的格式转符有8个：**eEfFgGaA**。但除了 **%f** 和 **%g** 外我们很少使用其它的。对于指数部分，小写的版本使用小写 e，大写的版本就使用大写 E。

> 通常 **%g** 是浮点数的全能转换符 ，它与 **%f** 的不同在下面的例子里显示得很清楚：
> （ `%g`会根据变量值自动选择格式转换符（`%f` /  `%e`）用最小的位数来显示数值）
```objectivec
double v[5] = {12345, 12, 0.12, 0.12345678901234, 0.0000012345678901234};
NSString *s = [NSString stringWithFormat:@"%g %g %g %g %g", v[0], v[1], v[2], v[3], v[4]];
// "12345 12 0.12 0.123457 1.23457e-06"
NSString *s = [NSString stringWithFormat:@"%f %f %f %f %f", v[0], v[1], v[2], v[3], v[4]];
// "12345.000000 12.000000 0.120000 0.123457 0.000001"
```

### 整数值和浮点值的相互转换
隐式转换规则：
* 浮点值➡️整型变量，自动删除数字的小数部分。
* 整型变量➡️浮点值，不变。
* 只要表达式中的两个运算数是整型，该运算就将在整数运算规则下进行。
* 对于任何处理两个值的运算，如果其中一个值是浮点变量或常量，那么这一运算将作为浮点运算来处理。

## 五、数学代码库

* 当要处理复杂的数学运算时，就要用到数学代码库：`math.h`；
* 运行 Mac 上的终端（Terminal）应用，输入 `man math`（并回车），可以查看数学代码库所提供的功能；
* 注意：所有与三角函数有关的函数，使用的单位都是 **弧度** 而不是角度。

以下是整理的常见数学函数：

```c
// 生成随机数
// 生成 [0, x-1] 之间的随机数
int value = arc4random() % x;
// 生成 [1, x] 之间的随机数
int value = (arc4random() % x) + 1;
// 生成 [0, 1] 之间的随机数
float value3 = (arc4random() % 100) / 100.0;


// 其他常用数学函数
rand()：产生随机数
abs()、labs()：整数的绝对值
fabs()、fabsf()、fabsl()：浮点数的绝对值
floor()、floorf()、floorl()：向下取整
ceil()、ceilf()、ceill()：向上取整
round()、roundf()、roundl()：四舍五入
sqrt()、sqrtf()、sqrtl()：求平方根
fmax()、fmaxf()、fmaxl()：求最大值
fmin()、fminf()、fminl()：求最小值
hypot()、hypotf()、hypotl()：求直角三角形斜边的长度
fmod()、fmodf()、fmodl()：求两数整除后的余数
modf()、modff()、modfl()：浮点数分解为整数和小数
frexp()、frexpf()、frexpl()：浮点数分解尾数和以二为底的指数
  

// 三角运算函数
sin()、sinf()、sinl()：   求正弦值
sinh()、sinhf()、sinhl()：求双曲正弦值
cos()、cosf()、cosl()：   求余弦值
cosh()、coshf()、coshl()：求双曲余弦值
tan()、tanf()、tanl()：   求正切值
tanh()、tanhf()、tanhl()：求双曲正切值


// 反三角运算　
asin()、asinf()、asinl()：   求反正弦值
asinh()、asinhf()、asinhl()：求反双曲正弦值
acos()、acosf()、acosl()：   求反余弦值
acosh()、acoshf()、acoshl()：求反双曲余弦值
atan()、atanf()、atanl()：   求反正切值
atan2()、atan2f()、atanl()： 求坐标值的反正切值
atanh()、atanhf()、atanhl()：求反双曲正切值
　 　

// 指数和对数运算
pow()、powf()、powl()：      求 n 的 m 次方的值
exp()、expf()、expl()：      求 e 的 x 次方的值
exp2()、exp2f()、exp2l()：   求 2 的 x 的次方的值
log()、logf()、logl()：      求以 e 为底的对数值
log10()、log10f()、log10l()：求以 10 为底的对数值
```
