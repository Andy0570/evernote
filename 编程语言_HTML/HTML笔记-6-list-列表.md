### HTML 列表

| 列表元素 | 含义                            |
| -------- | ------------------------------- |
| ol       | order list，有序列表            |
| ul       | unorder list，无序列表          |
| li       | list item，列表项目             |
| dl       | definition list，自定义说明列表 |
| dt       | 说明列表中的术语                |
| dd       | 说明列表中的定义                |


示例：
```html
<h3>无序列表</h3>
<ul>
    <li>Coffee</li>
    <li>Milk</li>
    <li>bread</li>
</ul>

<h3>有序列表</h3>
<ol>
    <li>Coffee</li>
    <li>Milk</li>
    <li>break</li>
</ol>

<!--自定义列表以 <dl> 标签开始。每个自定义列表项目以 <dt> 开始。每个自定义列表项的描述以 <dd> 开始。-->
<h3>自定义列表</h3>
<dl>
    <dt>Coffee</dt>
    <dd>- black hot drink</dd>
    <dt>Milk</dt>
    <dd>- white cold drink</dd>
    <dt>break</dt>
    <dd>💡wtf?</dd>
</dl>
```
![](http://upload-images.jianshu.io/upload_images/2648731-21bc5781eb11d2cc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

#### <li>元素

li 的value属性可以用来生成不连续的有序列表。

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Example</title>
</head>

<body>
    <ol>
        <!--顺序是：145789-->
        <li>bananas</li>
        <li value="4">mangoes</li>
        <li>cherries</li>
        <li value="7">plums</li>
        <li>grapes</li>
        <li>grapes</li>
    </ol>
</body>

</html>
```

Emmet 插件快速生成有序列表代码：`ol>li*6` 再按 Tab 键。

![li 的value 属性](http://upload-images.jianshu.io/upload_images/2648731-64713695e59f6901..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





#### 不同类型的有序列表

* start属性：列表首项的编号值。
* type属性：显示在各个列表项旁边的编号类型。（1、a、A、i、I）

```html
<h4>编号列表</h4>
<ol>
    <li>Apple</li>
    <li>Bananas</li>
    <li>Lemons</li>
    <li>Oranges</li>
</ol>

<h4>大写字母列表</h4>
<ol type="A">
    <li>Apple</li>
    <li>Bananas</li>
    <li>Lemons</li>
    <li>Oranges</li>
</ol>

<h4>小写字母列表</h4>
<ol type="a">
    <li>Apple</li>
    <li>Bananas</li>
    <li>Lemons</li>
    <li>Oranges</li>
</ol>

<h4>小写罗马数字列表</h4>
<ol type="i">
    <li>Apple</li>
    <li>Bananas</li>
    <li>Lemons</li>
    <li>Oranges</li>
</ol>
```

![](http://upload-images.jianshu.io/upload_images/2648731-f9bd586d2ca5b6a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)




#### 不同类型的无序列表

```html
<b>⚠️注意：</b> 在 HTML 4 中， ul 属性已废弃， HTML 5 已不支持该属性，因此我们使用 CSS 代替来定义不同类型的无序列表

<h4>圆点列表</h4>
<ul style="list-style-type: disc;">
    <li>Apple</li>
    <li>Bananas</li>
    <li>Lemons</li>
    <li>Oranges</li>
</ul>

<h4>圆圈列表</h4>
<ul style="list-style-type: circle;">
    <li>Apple</li>
    <li>Bananas</li>
    <li>Lemons</li>
    <li>Oranges</li>
</ul>

<h4>正方形列表</h4>
<ul style="list-style-type: square;">
    <li>Apple</li>
    <li>Bananas</li>
    <li>Lemons</li>
    <li>Oranges</li>
</ul>
```

![](http://upload-images.jianshu.io/upload_images/2648731-31bd3e1e32a1b068.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)




#### 嵌套列表，示例一：

```html
<h4>嵌套列表：</h4>

<ul>
    <li>Coffee</li>
    <li>Tea</li>
    <ul>
        <li>Black tea</li>
        <li>Green tea</li>
    </ul>
    <li>Milk</li>
</ul>
```

![](http://upload-images.jianshu.io/upload_images/2648731-f15c8beade329cb6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


#### 嵌套列表，示例二：

```html
<h4>嵌套列表：</h4>

<ul>
    <li>Coffee</li>
    <li>Tea</li>
    <ul>
        <li>Black tea</li>
        <li>Green tea</li>
        <ul>
            <li>China</li>
            <li>Afrcia</li>
        </ul>
    </ul>
    <li>Milk</li>
</ul>
```

![](http://upload-images.jianshu.io/upload_images/2648731-a775df356b901419.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)
