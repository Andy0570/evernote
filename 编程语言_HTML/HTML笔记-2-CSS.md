![hype_enterprise_login.png](http://upload-images.jianshu.io/upload_images/2648731-eaf20ade2f63c635.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

CSS（层叠样式表）作用：规定HTML文档的呈现形式（外观和格式编排）。

CSS 是在HTML 4开始使用的,是为了更好的渲染HTML元素而引入的。

## 添加CSS的三种方式

CSS 可以通过以下方式添加到HTML中:

1. 内联样式- 在HTML元素中使用"style"**属性**
2. 内部样式表 -在HTML文档头部 <head> 区域使用<style>元素 来包含CSS
3. 外部引用 - 使用外部 CSS**文件**



### 一、内联样式

```html
<!--内联样式，嵌入在起始标签中-->
<p style="color: blue;margin-left: 20px;">This is a paragraph.</p>

<!--背景颜色-->
<body style="background-color: yellow">
	<h2 style="background-color: red">这是一个很长的标题</h2>
	<p style="background-color: green">这是一个段落</p>
</body>

<!--字体、字体颜色、字体大小-->
<h1 style="font-family: verdana">这是一个标题</h1>
<p style="font-family: arial;color: red;font-size: 20px;">这是一个段落</p>

<!--文本对齐方式-->
<h1 style="text-align: center;">居中对齐的标题</h1>
```



### 二、内部样式表

当单个文件需要特别样式时，就可以使用内部样式表。你可以在<head> 部分通过 <style>标签定义内部样式表:

```html
<head>
	<style type="text/css">
		body {background-color: :yellow;}
		p {color:red; font-family:cursive; font-size:20pt; }
	</style>
</head>
```



#### 三、外部样式表

当样式需要被应用到很多页面的时候，外部样式表将是理想的选择。使用外部样式表，你就可以通过更改一个文件来改变整个站点的外观。

```html
<!--当前文件夹路径-->
<link rel="stylesheet" type="text/css" href="mystyle.css">

<!--链接到“父”文件夹 ../../-->
<link rel="stylesheet" type="text/css" href="../mystyle.css">

<!--链接到子目录文件夹-->
<link rel="stylesheet" type="text/css" href="Resource/mystyle.css">
```



使用外部样式表的步骤：

1⃣️第一步：编写CSS文件

span.css 文件：

样式表，设计好选择器，{…}内写样式声明。

```css
a {
	background-color:grey;
	color: white;
}
span {
	border:thin black solid;
	padding: 10px;
}
```

combined.css 文件：

1. 从其他样式表中导入样式：在样式表顶端使用`@import "styles.css";`语句导入其他样式表。
   1. 声明样式表的字符编码；`@charset "UTF-8";`

```css
@charset "UTF-8";
@import "styles.css";
span {
	border:medium black dashed;
	padding: 5px;
}
```

HTML5_Test.html 文件：



2⃣️第二步：使用`link`元素导入外部样式表。

```html
<!DOCTYPE html>
<html>
<head>
	<title>用全局属性style定义样式</title>
    <!--rel: 说明文档与所关联资源的关系类型-->
	<link rel="stylesheet" type="text/css" href="combined.css">
</head>
<body>
	<a href="http://apress.com">Visti the Apress site </a>
	<p>I Like <span>apples</span> and oranges.</p>
	<a href="http://w3c.org">Visit the w3c website</a>
</body>
</html>
```

rel 属性值最全面的介绍参考：https://www.iana.org/assignments/link-relations/link-relations.xml




## 样式的层叠和继承


![样式定义方式](http://upload-images.jianshu.io/upload_images/2648731-b67b4672ab2b66cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



### 浏览器样式（用户代理样式）

元素尚未设置样式时浏览器应用在它身上的默认样式：

类似浏览器默认的样式：

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		a {
			color: blue;
			text-decoration: underline;
		}
	</style>
</head>
<body>
	<a href="http://apress.com">Visti the Apress site </a>
	<p>I Like <span>apples</span> and oranges.</p>
	<a href="http://w3c.org">Visit the w3c website</a>
</body>
</html>
```

### 用户样式

浏览器允许用户自定义自己的样式表。

Chrome 浏览器自定义样式表位置：Default\User StyleSheets\Custom.css



### 样式层叠顺序

浏览器要显示元素时求索一个CSS属性值的次序：

1. 元素内嵌样式（用元素的全局属性style定义的样式）；
2. 文档内嵌样式（定义在style元素中的样式）；
3. 外部样式（用link元素导入的样式）；
4. 用户样式（用户定义的样式）；
5. 浏览器样式（浏览器应用的默认样式）；



### 用重要样式调整层叠次序

在样式声明后附上`!important`即可将对应的属性值标记为重要。

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		a {
			color: black !important;
			text-decoration: underline;
		}
	</style>
</head>
<body>
	<a href="http://apress.com">Visti the Apress site </a>
	<p>I Like <span>apples</span> and oranges.</p>
	<a href="http://w3c.org">Visit the w3c website</a>
</body>
</html>
```



### 根据具体程度和定义次序解决同级样式冲突

当样式的层次相等时，浏览器需要评估样式的具体程度来判断到底使用哪一个样式，样式的具体程度通过统计三类特征得出：

1. 样式的选择器中id值的数目；
2. 选择器中类和伪类的数目；
3. 选择器中元素和伪元素的数目；

如果样式的具体程度完全相等，那么浏览器还会根据样式的先后位置次序选择使用样式。规则是**后来者居上**。

示例：

| 选择器      | 注释               | 具体程度结果 |
| ----------- | ------------------ | ------------ |
| h1          | 只有一个元素       | 001          |
| h1.bule     | 有一个元素，一个类 | 011          |
| #elixirs h1 | 一个id，一个元素   | 101          |
| p img       | 两个元素           | 002          |



### 继承

如果浏览器在直接相关的样式中找不到某个属性的值，就会使用继承机制。使用父元素的样式属性值。

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		p {
			color:white;
			background:grey;
			border:medium solid black;
		}
	</style>
</head>
<body>
	<a href="http://apress.com">Visti the Apress site </a>
	<p>I Like <span>apples</span> and oranges.</p>
	<a href="http://w3c.org">Visit the w3c website</a>
</body>
</html>
```

注意到：以上文档并没有设置 span 元素的`color`属性值，但是它从父元素p继承了`color `值。

![](http://upload-images.jianshu.io/upload_images/2648731-a74b6dac274907b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)




💡并非所有的CSS属性均可继承：

* 与元素外观（文字颜色、字体等）相关的样式会被继承；

* 与元素在页面上的布局相关的样式不会被继承；

* 在样式中使用 `inherit`可以强行实施继承。

  ```html
  <!DOCTYPE html>
  <html>
  <head>
  	<title>Example</title>
  	<style type="text/css">
  		p {
  			color: white;
  			background: grey;
  			border: medium solid black;
  		}
  		span {
  			border: inherit;
  		}
  	</style>
  </head>
  <body>
  	<a href="http://apress.com">Visti the Apress site </a>
  	<p>I Like <span>apples</span> and oranges.</p>
  	<a href="http://w3c.org">Visit the w3c website</a>
  </body>
  </html>
  ```

  

### 浏览器特定厂商前缀

| 浏览器            | 厂商前缀 |
| ----------------- | -------- |
| Chrome、Safari    | -webkit- |
| Opera             | -o-      |
| Firefox           | -moz-    |
| Internet Explorer | -ms-     |



## CSS属性



### 选择器属性

![CSS选择器](https://upload-images.jianshu.io/upload_images/2648731-cb49f43e658230b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![CSS选择器2.png](https://upload-images.jianshu.io/upload_images/2648731-782cd3b2e48e9ff9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### 基本选择器和组合选择器

```css
/* 元素选择器 */
p {

}

/* 类选择器 */
.MyClass {
    
}

div.MyClass {
    
}

/* ID 选择器*/
#idName {
    
}

/* 属性选择器:[属性=值] */
[href] {
    
}

/* 复合选择器,并集:<选择器>, <选择器>, <选择器> */
h1, h2 {
    
}

/* 后代选择器：<选择器> <选择器> */
p span {
    /* 匹配p元素的后代span元素，未必是直接子元素 */
}

/* 子元素选择器：A > B */
body > * span, tr > th {
    /*  *号代表任意元素，body的任意子元素中的span元素 */
}

/* 相邻兄弟选择器：A + B */
p + a {
    /* 选择紧跟在某元素之后的元素 */
}

/*普通兄弟选择器：A ~ B */
p ~ a {
    /* 选择某元素之后的元素，不仅限于紧跟的兄弟元素*/
}
```



### 伪选择器


![伪选择器](https://upload-images.jianshu.io/upload_images/2648731-cc344b7de3857971.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





#### 伪元素

参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/Reference


![](https://upload-images.jianshu.io/upload_images/2648731-37775e64cd2e4926.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




#### 伪类

参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/Reference



![伪类](https://upload-images.jianshu.io/upload_images/2648731-9cbc343d7ed546b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)









#### 边框和背景属性

![边框和背景属性1.png](https://upload-images.jianshu.io/upload_images/2648731-86df4d8f75992558.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![边框和背景属性2.png](https://upload-images.jianshu.io/upload_images/2648731-793da8b5dcdbbecf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/2648731-d3144946158ba8ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### border 属性

设置边框的简写属性。border可以用于设置一个或多个以下属性的值： [`border-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-width), [`border-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-style), [`border-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-color)。

参考 https://developer.mozilla.org/zh-CN/docs/Web/CSS/border

```css
border-width: 1px; /* <长度值>|<百分数>|thin|medium|thick */
border-style: dashed;
border-color: black;

/*简写属性*/
/*border: [border-width ||border-style ||border-color |inherit] ;*/
border: 1px dashed black;
```



### border-style 属性

`border-style` 属性描述了边框的样式。

参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-style

```css
/* border-style 示例 */
.b1 {border-style:none;}
.b2 {border-style:hidden;}
.b3 {border-style:dotted;} /*圆点*/
.b4 {border-style:dashed;} /*短的方形虚线*/
.b5 {border-style:solid;} /*实线*/
.b6 {border-style:double;} /*双实线*/
.b7 {border-style:groove;} /*雕刻效果边框*/
.b8 {border-style:ridge;} /*浮雕效果边框*/
.b9 {border-style:inset;} /*陷入效果边框*/
.b10 {border-style:outset;} /*突出效果边框*/
```




### outline 属性

`outline` 属性用来设置轮廓。

参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/outline

* 轮廓不占据空间，它们被描绘于内容之上。因此应用轮廓不需要调整页面布局。



```css
outline-width: 5px;
outline-style: solid;
outline-color: darkturquoise; /* 深蓝色 */

/* 简写方式：宽度 | 样式 | 颜色 */
outline: 5px solid darkturquoise;
```


![outline元素.png](https://upload-images.jianshu.io/upload_images/2648731-b7e629a1671b7bdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 盒模型属性




![盒模型属性1.png](https://upload-images.jianshu.io/upload_images/2648731-36d8f9d4c2d27a82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![盒模型属性2.png](https://upload-images.jianshu.io/upload_images/2648731-6f0aa23fc34bbd76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### CSS盒模型


![CSS盒模型.png](https://upload-images.jianshu.io/upload_images/2648731-38ef6e83515c3068.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* margin 是元素之间的空间。
* padding 是内容和边框之间的空间。
* border 边框包围着元素和内边距，能把元素和周围的内容隔离开来。


### padding 属性

`padding` 属性可以设置元素的内边距。

`padding` 值的书写顺序是：**上右下左**，即时钟的顺时针方向。

```css
/*分别设置各个内边距*/
padding-top: 20px;
padding-right: 25px;
padding-bottom: 20px;
padding-left: 20px;

/* 应用于四个边 */
padding: 1em;

/* 垂直方向| 水平方向*/
padding: 5% 10%;

/* 顶部| 水平方向| 底部*/
padding: 1em 2em 2em; 

/* 顶部| 右边| 底部| 左边*/
padding: 2px 1em 0 1em;

padding: inherit;
```



### margin 属性

参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin

margin属性为给定元素设置所有四个（上下左右）方向的外边距属性。这是四个外边距属性设置的简写。四个外边距属性设置分别是： [`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top)， [`margin-right`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-right)， [`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom) 和 [`margin-left`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-left) 。指定的外边距允许为负数。

```css
/* 分别设置各个方向的外边距 */
margin-top: 20px;
margin-right: 20px;
margin-bottom: 10px;
margin-left: 20px;

margin: 5%;                /* 所有的边都是 5% 的边距 */

margin: 10px;              /* 所有的边都是 10像素 的边距 */

margin: 1.6em 20px;        /* 上和下边是 1.6字距, 左和右是 20像素 边距 */

margin: 10px 3% 1em;       /* 上边 10像素, 左和右 3%, 下边 1字距 边距 */

margin: 10px 3px 30px 5px; /* 上边 10像素, 右边 3像素, bottom 30px, left 5px margin */

margin: 1em auto;          /* 上和下边 1字距 边距, 该盒子是水平居中的 */

margin: auto;              /* 该盒子是水平居中的, 上下边距为0 */
```



### overflow 属性

**overflow **属性指定当内容溢出其块级容器时,是否剪辑内容，显示滚动条或显示溢出的内容。

参考：https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow

```css
/* 单独设置水平方向和垂直方向上的溢出值 */
overflow-x: auto;
overflow-y: auto;

/* 默认值。内容不会被修剪，会呈现在元素框之外 */
overflow: visible;

/* 内容会被修剪，并且其余内容不可见 */
overflow: hidden;

/* 内容会被修剪，浏览器会显示滚动条以便查看其余内容 */
overflow: scroll;

/* 由浏览器定夺，如果内容被修剪，就会显示滚动条 */
overflow: auto;

/* 规定从父元素继承overflow属性的值 */
overflow: inherit;
```



### float 属性

参考：https://developer.mozilla.org/zh-CN/docs/CSS/float

float CSS属性指定一个元素应沿其容器的左侧或右侧放置，允许文本和内联元素环绕它。该元素从网页的正常流动中移除，尽管仍然保持部分的流动性（与[绝对定位](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position#Absolute_positioning)相反）。

```css
/* Keyword values */
float: left;
float: right;
float: none;
float: inline-start;
float: inline-end;

/* Global values */
float: inherit;
float: initial;
float: unset;
```



### clear 属性

 **clear** [CSS](https://developer.mozilla.org/en-US/docs/CSS) 属性指定一个元素是否可以在它之前的浮动元素旁边，或者必须向下移动(清除浮动) 在它的下面。clear 属性适用于浮动和非浮动元素。

```css
clear: none; /* 元素不会向下移动清除之前的浮动。 */
clear: left; /* 元素被向下移动用于清除之前的左浮动。 */
clear: right; /* 元素被向下移动用于清除之前的右浮动。 */
clear: both; /* 元素被向下移动用于清除之前的左右浮动。 */
clear: inline-start; /* inline-start是一个关键字，表示该元素向下移动以清除其包含块的起始侧上的浮动。即在某个区域的左侧浮动或右侧浮动。 */
clear: inline-end; /* inline-end是一个关键字，表示该元素向下移动以清除其包含块的末端的浮点，即在某个区域的右侧浮动或左侧浮动。 */

clear: inherit; /*  */
```





### 布局属性


![布局属性.png](https://upload-images.jianshu.io/upload_images/2648731-791e12109de23d5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### 弹性盒布局

使用弹性盒布局可以创建对浏览器窗口调整响应良好的流动界面。

```css
<!DOCTYPE HTML>
<html>

<head>
    <title>Example</title>
    <meta name="author" content="Adam Freeman" />
    <meta name="description" content="A simple example" />
    <link rel="shortcut icon" href="favicon.ico" type="image/x-icon" />
    <style>
        p {
            width: 150px;
            max-width: 250px;
            border: thin solid black;
            background-color: lightgrey;
            margin: 2px;
        }

        #container {
            /* 通过 display 属性创建弹性盒。将该属性应用到弹性盒容器 */
            display: -webkit-box;
            
            -webkit-box-direction: reverse;
            
            /* 指定浏览器如何处理多余的垂直空间：start|end|center|strech */
            -webkit-box-align: start;
            
            /* box-pack属性表示在所有的可伸缩元素均达到最大尺寸时，多余的空间如何处理 */
            /* start|end|center|justify */
            -webkit-box-pack: justify; /* 均匀分配 */
        }
        
        #first {
            /* 指定元素的可伸缩性，应用于弹性盒容器内的元素。告诉浏览器当容器大小改变时哪些元素的尺寸是弹性的 */
            /* 值为3：浏览器为其分配的多余空间是为 id=second 元素的三倍 */
            -webkit-box-flex: 3;
        }
        #second {
            
            -webkit-box-flex: 1;
        }

    </style>
</head>

<body>
    <div id="container">
        <p id="first">
            There are lots of different kinds of fruit - there are over 500 varieties of banana alone. By the time we add the countless types of apples, oranges, and other well-known fruit, we are faced with thousands of choices.
        </p>
        <p id="second">
            One of the most interesting aspects of fruit is the variety available in each country. I live near London, in an area which is known for its apples.
        </p>
        <p id="third">
            When travelling in Asia, I was struck by how many different kinds of banana were available - many of which had unique flavours and which were only avaiable within a small region.
        </p>
    </div>
</body>

</html>

```



![弹性盒模型.png](https://upload-images.jianshu.io/upload_images/2648731-33337a6cc39b40ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 文本属性

![文本属性.png](https://upload-images.jianshu.io/upload_images/2648731-e0cbfbb19fa0b6fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





#### font-family 属性

font-family 字体系列参考：https://developer.mozilla.org/en-US/docs/Web/CSS/font-family

![font-family字体系列.png](https://upload-images.jianshu.io/upload_images/2648731-b4f06ff04df1d58b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


用CSS定义字体系列：

```css
body {
    font-family: Verdana, Geneva, Arial, "Goudy Bookletter 1911", sans-serif;
}
/*
 * font-family定义工作的原理：
 * 浏览器会按顺序查找计算机种可用的具体字体：Verdana, Geneva, Arial, "Goudy Bookletter 1911",
 * 如果以上具体的字体都没有，就使用浏览器默认的某个 sans-serif 系列字体。
*/
```



### 使用 @font-face 设置Web字体

谷歌字体库：[Google Fonts](https://fonts.google.com)

```css
<link href="https://fonts.googleapis.com/css?family=Open+Sans+Condensed:300" rel="stylesheet">
<style>
    @font-face {
        font-family: 'Open Sans Condensed', sans-serif;
        font-style: normal;
        font-weight: normal;
/* src: url('https://fonts.googleapis.com/css?family=Open+Sans+Condensed:300');*/
    }

    p {
        font-family: 'Open Sans Condensed', sans-serif;
    }
</style>
```





### font-style 属性

| font-style值 | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| normal       | 选择 font-family 的常规字体。                                |
| italic       | 选择斜体，如果当前字体没有可用的斜体版本，会选用倾斜体（oblique ）替代。 |
| oblique      | 选择倾斜体，如果当前字体没有可用的倾斜体版本，会选用斜体（ italic ）替代。 |

* `italic`文本向右倾斜而且衬线有弯曲。
* `oblique`，只是把普通字母倾斜显示。


![font-style.png](https://upload-images.jianshu.io/upload_images/2648731-b627d6be2a354010.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### font-size 属性

`font-size` 属性指定字体的大小。

```css
/* <absolute-size>，绝对大小值 */
/* 每种尺寸比前一种大20%*/
font-size: xx-small;
font-size: x-small;
font-size: small;
font-size: medium; /*初始值*/
font-size: large;
font-size: x-large;
font-size: xx-large;

/* <relative-size>，相对大小值 */
font-size: larger;
font-size: smaller;

/* <length>，长度值 */
font-size: 12px;
font-size: 0.8em; /*em = 希望得到的像素大小 / 父元素字体像素大小*/

/* <percentage>，百分比值 */
font-size: 80%;

font-size: inherit;
```

使用技巧：

* 选择一种关键字（推荐用small或medium）定义body字体大小，也就是网页的默认字体大小。
* 用em或百分数把别的元素的字体大小指定为相对于body字体大小的字体尺寸。

一个流行的技巧是设置body元素的字体大小为62.5% (即默认大小16px的62.5%)，等于10px。现在你可以通过计算基准大小10px的倍数，在任何元素上方便的使用em单位。这样有6px = 0.6em, 8px = 0.8em, 12px = 1.2em, 14px = 1.4em, 16px = 1.6em。





#### font-weight 属性

设置字体的粗细


![font-weight属性.png](https://upload-images.jianshu.io/upload_images/2648731-42cc19f99d0c7652.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### font 属性

[font参考](https://developer.mozilla.org/zh-CN/docs/Web/CSS/font)

```css
/* style | variant | weight | size/line-height | family */
font: italic small-caps bolder 16px/3 cursive;
```

![font.png](https://upload-images.jianshu.io/upload_images/2648731-4857351ef5d71113.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### text-decoration属性

参考文档：https://developer.mozilla.org/en-US/docs/Web/CSS/text-decoration

添加文本排版样式（下划线、顶划线、删除线或者闪烁）。

text-decoration属性是一种简写属性，并且可以使用普通属性三个值中的任何一个。普通属性如下：[`text-decoration-line`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-line)，[`text-decoration-color`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-color)和[`text-decoration-style`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-decoration-style)

语法形式：`<'text-decoration-line'> || <'text-decoration-style'> || <'text-decoration-color'>`



![text-decoration.png](https://upload-images.jianshu.io/upload_images/2648731-4d063fd716c12e58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* Safari 使用普通属性或者简写语法需要添加 `-webkit-`前缀。
* Firefox 浏览器需要添加 `moz`前缀。
* IE浏览器不支持该属性。









### 过度、动画和变换类型



![](https://upload-images.jianshu.io/upload_images/2648731-62609f111904d36b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![过度、动画和变换类型2.png](https://upload-images.jianshu.io/upload_images/2648731-ddfa374e3fcd7562.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





### 其他属性

![其他属性.png](https://upload-images.jianshu.io/upload_images/2648731-a21d6e44e778db46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)








### color 属性

完整列表：www.w3.org/TR/css3-color

1. 使用颜色名；
2. 十进制、十六进制[Color Hex Color Codes](http://www.color-hex.com)；

CSS颜色函数：

![](http://upload-images.jianshu.io/upload_images/2648731-969ecc8eee77d2bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



### opacity 属性

`opacity` 属性表示让整个元素和文本内容透明。取值范围是0～1。








## CSS 中的长度



### 绝对长度

CSS支持五种绝对长度：

| 单位标识符 | 说明                  |
| ---------- | --------------------- |
| in         | 英寸                  |
| cm         | 厘米                  |
| mm         | 毫米                  |
| pt         | 磅（1磅等于1/72英寸） |
| pc         | pica（1pica等于12磅） |



```html
<style type="text/css">
		p {
			color: white;
			background: grey;
			width: 5cm;
			font-size: 20pt;
		}
</style>
```



### 相对长度

主流浏览器支持的一些CSS相对单位：

| 单位标识符 | 说明                                                |
| ---------- | --------------------------------------------------- |
| em         | 与元素字号挂钩                                      |
| ex         | 与元素字体的“x高度”（字体基线到中线之间的距离）挂钩 |
| rem        | 与根元素的字号挂钩                                  |
| px         | CSS像素（假定显示设备的分辨率为96dpi）              |
| %          | 另一属性的值的百分比                                |

px在CSS中原本是一个相对单位，但在实际使用中却变成了绝对单位。



使用相对单位：


```html
<!DOCTYPE HTML>
<html>
    <head>
        <title>Example</title>
        <style type="text/css">
            p {
                background: grey;
                color:white;
                font-size: 15pt;
                height: 2em;
            }
        </style>
    </head>
    <body>
        <a href="http://apress.com">Visit the Apress website</a>
        <p>I like <span>apples</span> and oranges.</p>
        <p style="font-size:12pt">I also like mangos and cherries.</p>
        <a class="myclass1 myclass2" href="http://w3c.org">Visit the W3C website</a>
    </body>
</html>

```

height值为 2em，表示高度是字号的两倍。



使用从另一个相对单位推算出来的相对单位：

```html
<!DOCTYPE HTML>
<html>
    <head>
        <title>Example</title>
        <style type="text/css">
            html {
                font-size: 0.2in;
            }
            p {
                background: grey;
                color:white;
                font-size: 2rem;
                height: 2em;
            }
        </style>
    </head>
    <body>
        <a href="http://apress.com">Visit the Apress website</a>
        <p>I like <span>apples</span> and oranges.</p>
        <p style="font-size:12pt">I also like mangos and cherries.</p>
        <a class="myclass1 myclass2" href="http://w3c.org">Visit the W3C website</a>
    </body>
</html>
```

font-size: 2rem 表示使用该值的所有元素的字号是根元素的两倍。



### 百分比单位

```html
<!DOCTYPE HTML>
<html>
    <head>
        <title>Example</title>
        <style type="text/css">  
            p {
                background: grey;
                color:white;
                font-size: 200%;
                width: 50%;
            }
        </style>
    </head>
    <body>
        <a href="http://apress.com">Visit the Apress website</a>
        <p>I like <span>apples</span> and oranges.</p>
        <a class="myclass1 myclass2" href="http://w3c.org">Visit the W3C website</a>
    </body>
</html>
```



### 使用CSS角度

| 单位标识符 | 说明                              |
| ---------- | --------------------------------- |
| deg        | 度（取值范围：0deg~360deg）       |
| grad       | 百分度（取值范围：0grad~400grad） |
| rad        | 弧度（取值范围：0rad~6.28rad）    |
| turn       | 圆周（1turn等于360deg）           |

### 使用CSS时间

| 单位标识符 | 说明                |
| ---------- | ------------------- |
| s          | 秒                  |
| ms         | 毫秒（1s = 1000ms） |







### 测试CSS特性的支持情况

* http://caniuse.com, 可以查询各款浏览器的各种版本对HTML和CSS3的支持情况。

* www.modernizr.com, [GitHub](https://github.com/modernizr/modernizr) 

  > Modernizr is a JavaScript library that detects HTML5 and CSS3 features in the user’s browser.





### CSS工具

* CSS格式校验器：[W3C CSS Validation Service](https://jigsaw.w3.org/css-validator/#validate_by_uri)
* 用 SelectorGadget 生成选择器：http://selectorgadget.com
* 用LESS改进CSS：http://lesscss.org
* 使用CSS框架：[Blueprint](http://www.blueprintcss.org) | [GitHub 源码](https://github.com/joshuaclayton/blueprint-css)
* CSS模糊效果：[Blurred Background CSS](https://codepen.io/ariona/pen/geFIK)

