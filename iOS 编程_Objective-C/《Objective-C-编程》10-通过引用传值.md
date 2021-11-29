通过引用传值：调用函数时传入某个地址（也称为引用），然后由函数将数据存入该地址指向的内存。

* `modf()` 函数：传入一个 `double` 类型的数据，返回该浮点数的整数部分和小数部分。调用该函数时，需要传入一个地址供 `modf()` 保存整数部分的计算结果。准确的说，`modf()` 会返回小数部分，然后将整数部分拷贝至传入的地址。

```
#include <stdio.h>
#include <math.h>

int main(int argc, const char * argv[]) {
    
    double pi = 3.14;
    double integerPart;
    double fractionPart;
    
    // 将 integerPart 的地址作为实参传入
    fractionPart = modf(pi, &integerPart);
    
    // 获取integerPart地址上的值
    printf("integerPart = %.0f,factionPart = %.2f\n",integerPart,fractionPart);

    return 0;
}
```

输出结果

> **integerPart = 3,factionPart = 0.14**



### 编写通过引用传递参数的函数

写一个函数，将公制单位米转化为非公制单位的英尺和英寸。

 ```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

/**
 将公制单位米转换为非公制单位英寸和英尺

 @param meters 输入参数：米
 @param ftPtr 英尺的地址
 @param inPtr 英寸的地址
 */
void metersToFeetAndInches(double meters,unsigned int *ftPtr,double *inPtr) {
    //这个函数假定 meters 的值是非负数
    
    //将meters变量的值转化为feet的值，类型为浮点数。
    double rawFeet = meters * 3.281;
    
    //计算类型为无符号的整形feet变量的值
    //floor():向下取整
    unsigned int feet = (unsigned int)floor(rawFeet);
        
    //将feet变量的值存储在提供的地址里
    if (ftPtr) {  //ftPtr != NULL
        printf("Storing %u to the address %p\n",feet,ftPtr);
        *ftPtr = feet;
    }

    //计算英寸
    double fractionalFoot = rawFeet - feet;
    double inches = fractionalFoot * 12.0;
    
    //将inches变量的值保存到传入的地址
    if (inPtr) {
        printf("Stroing %.2f to the address %p\n",inches,inPtr);
        *inPtr = inches;
    }
}

int main(int argc, const char * argv[]) {
    
    double meters = 3.0;
    unsigned int feet;
    double inches;
    
    metersToFeetAndInches(meters, &feet, &inches);
    printf("%.1f meters is equal to %d feet and %.1f inches.\n",meters,feet,inches);
    
    /*  有时我们只需要获取返回值的一部分时，比如我只需要获得英寸inch的数据，而不需要获得英尺feet的数据；
        通常的做法是将 NULL 作为地址传入函数，表示“无须传回相应的值”。
        所以，metersToFeetAndInches函数中会有 if 条件表达式判断是否为空指针。
    */
    metersToFeetAndInches(meters, NULL, &inches);
    printf("%.1f meters is equal to %.1f inches.\n",meters,inches);
    
    return 0;
}
 ```
