![HTML5备忘录](http://upload-images.jianshu.io/upload_images/2648731-40954e0c49587d54.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

超文本标记语言（英语：HyperText Markup Language，简称：HTML）是一种用于创建网页的标准标记语言。

### HTML、CSS、 JavaScript 的区别 

* HTML 元素负责文档内容的**结构**和**含义**。
* CSS (Cascading Style Sheets) 层叠样式表，控制内容呈现的**样式**。 
* JavaScript 用来操纵HTML文档的内容以及响应用户的操作。

### 父元素、子元素、后代元素和兄弟元素

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <code>apples</code>
</body>
</html>
```

* 包含另一个元素的元素是被包含元素的**父元素**。body元素是code元素的**父元素**。相反，code元素是body元素的**子元素**。
* 子元素是关系最近的后代元素。html元素包含着body元素，而后者包含着code元素。body元素和code元素都是html元素的**后代元素**，但是二者中只有body元素是html元素的**子元素**。
* 具有同一个父元素的几个元素互为**兄弟元素**。head元素和body元素是兄弟元素。




### 元数据元素、流元素和短语元素。

* 元数据元素（metadata element）：用来构建HTML文档的基本结构，以及就如何处理文档向浏览器提供信息和指示。
* 短语元素(phrasing element)是HTML的基本成分。
* 流元素是短语元素的超集。
* 所有短语元素都是流元素，但并非所有流元素都是短语元素。




## HTML 文档结构

![image](http://upload-images.jianshu.io/upload_images/2648731-f56d09c861e05f9a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
只有 <body> 区域 (白色部分) 才会在浏览器中显示。

实例：

```html
<!DOCTYPE html>
<html>
    <head>
	    <meta charset="UTF-8">
	    <title>文档的标题</title>
    </head>
    <body>
	    <h1>我是一个标题</h1>
	    <p>我是一个段落</p>
    </body>
</html>
```

实例解析：


* `<!DOCTYPE html>` 告诉浏览器两件事：1.它处理的是HTML文档；2.标记文档内容的HTML所属的版本。
* `<html>` 元素是 HTML 页面的根元素，它表示文档中HTML部分的开始。
* `<head>` 元素包含文档的元数据（meta）和文档信息。
* `<meta charset="utf-8">` 对于中文网页需要使用 **<meta charset="utf-8">** 声明编码，否则会出现乱码。有些浏览器会设置 GBK 为默认编码，则你需要设置为 **<meta charset="gbk">。**

  > 【只有在MySQL中可以使用"utf-8"的别名"utf8"，但是在其他地方一律使用大写"UTF-8"。】
* `<title>` 元素描述了文档的标题。
* `<body>` 元素包含了可见的页面内容。
* `<h1>` 元素定义一个一级标题。
* `<p>` 元素定义一个段落。




### 文档和元数据元素

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <!-- 设置文档编码类型 -->
    <meta charset="UTF-8">

    <!--使用 <title> 标签定义HTML文档的标题或名称-->
    <title>Document</title>

    <!--    
        * base 标签用于设置一个基准URL，让HTML文档中的相对链接在此基础上进行解析。
        * href（Hypertext Reference），超链接目标URL。
        * target 属性告诉浏览器链接的打开方式，target="_black" 表示在新窗口打开。
        * 如果HTML文档中不设置base元素，或不用其href属性设置一个基准URL，
            那么浏览器会将当前文档的URL认定为所有相对URL的解析基准。
   -->
    <base href="http://reeoo.qiniudn.com/" target="_black">

</head>

<body>
    <!--绝对链接地址-->
    <a href="http://apress.com">Visit Apress.com</a>

    <!--相对链接地址-->
    <a href="page2.html">Page 2</a>

    <!--图片相对地址-->
    <img src="Coowl-icon.png!icon512" alt="Icon Image">

</body>

</html>
```



#### <meta>元素

meta标签描述了一些基本的元数据。
<meta> 标签提供了元数据.元数据也不显示在页面上，但会被浏览器解析。
META 元素通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据。
元数据可以使用于浏览器（如何显示内容或重新加载页面），搜索引擎（关键词），或其他Web服务。
<meta> 一般放置于 <head> 区域

[MetaExtensions 元数据扩展](https://wiki.whatwg.org/wiki/MetaExtensions)

```html
<!--为网页定义字符集-->
<meta charset="UTF-8">

<!--定义网页作者-->
<meta name="author" content="作者">

<!--为网页定义描述内容-->
<meta name="description" content="描述信息">

<!--为搜索引擎定义关键词-->
<meta name="keywords" content="关键字，逗号分开，用来描述页面内容">

<!--每30秒刷新当前页面-->
<meta http-equiv="refresh" content="30">

<!--模拟HTTP标头字段-->
<!--1.1 让浏览器每隔5秒刷新一次-->
<meta http-equiv="refresh" content="5">
<!--1.2 让浏览器5秒之后载入另一个URL页面-->
<meta http-equiv="refresh" content="5; http://www.apress.com">
<!--2 指定页面优先使用的样式表-->
<meta http-equiv="default-style" content="xxx.css">
<!--3 另一种声明HTML页面所用字符编码的方法-->
<meta http-equiv="content-type" content="text/html charset=UTF-8">

<!--
noindex是谷歌发明的一个HTML标签，其作用是向搜索引擎声明该网页禁止被索引收录。
noindex标签的作用有两方面，简单的说，一是不给链接投票，增加此链接的权重，二是使添加noindex的部分内容不参与网站排名，便于集中网站权重。

nofollow标签是由谷歌领头创新的一个“反垃圾链接”的标签，并被百度、yahoo等各大搜索引擎广泛支持，引用nofollow标签的目的是：用于指示搜索引擎不要追踪（即抓取）网页上的带有nofollow属性的任何出站链接，以减少垃圾链接的分散网站权重！

noarchive 防止缓存版本
-->
<meta name="robots" content="noindex">
```



#### <head>元素

<head> 元素包含了所有的头部标签元素。在 <head>元素中你可以插入脚本（scripts）, 样式文件（CSS），及各种 meta 信息。

可以添加在头部区域的元素标签为: <title>, <style>, <meta>, <link>, <script>, <noscript>, and <base>.



#### <title>元素

<title> 标签定义了不同文档的标题。

<title> 在 HTML/XHTML 文档中是必须的。

<title> 元素:

- 定义了浏览器工具栏的标题
- 当网页添加到收藏夹时，显示在收藏夹中的标题
- 显示在搜索引擎结果页面的标题



#### <base>元素

<base> 标签描述了基本的链接地址/链接目标，该标签作为HTML文档中所有的链接标签的默认链接:



#### <link>元素

<link> 标签定义了文档与外部资源之间的关系。

1. <link> 标签通常用于链接到**CSS层叠样式表**:

```html
<link rel="stylesheet" type="text/css" href="mystyle.css">
```
2. 为页面定义网站标志：
```html
<!--定义与页面联系在一起的32x32像素的图标-->
<link rel="shortcut icon" type="image/x-icon" href="favicon.ico">
```
3. 要求浏览器预先获取预计很快将会使用到的资源：
```html
<!--预先获取资源-->
<link rel="prefetch" href="/page2.html">
```

![image](http://upload-images.jianshu.io/upload_images/2648731-1eef8f61150b8acf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`rel`属性最全面的介绍参考：http://iana.org/assignments/link-relations/link-relations.xml
![image](http://upload-images.jianshu.io/upload_images/2648731-3f78e8583831f72b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### <style>元素

<style> 标签定义了HTML文档的样式文件引用地址。在<style> 元素中你也可以直接添加CSS样式来渲染 HTML 文档:

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>我的第一个HTML页面</title>
    <!--1. 渲染整个HTML文档-->
	<style type="text/css">
		body {background-color:gray}
		p {color:red}
	</style>
</head>
<body>
	<!-- 2. 直接在元素身上定义CSS样式-->
	<a href="http://apress.com" style="background: grey;color: white;padding: 10px;">Visit the Apress site</a>
</body>
</html>
```
##### 指定样式适用的媒体
media 属性可以用来表明文档在什么情况下应该使用该元素中定义的样式。
```html
    <style media="screen" type="text/css">
    	// 浏览器在屏幕上显示文档时使用该 <style> 中的样式。
    </style>
    <style media="screen AND (max-width:500px)" type="text/css">
    	// 浏览器窗口小于500像素时使用
    </style>
        <style media="screen AND (min-width:500px)" type="text/css">
    	// 浏览器窗口大于500像素时使用
    </style>
```
![image](http://upload-images.jianshu.io/upload_images/2648731-50dc5379a322ca04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
以下特性还可以配合`min-`或者`max-`修饰，构成阈值而不是精确值。
![image](http://upload-images.jianshu.io/upload_images/2648731-99720d015e48ee0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### <script>元素 和<noscript> 元素

<script>标签用于加载脚本文件，如： JavaScript

* `script`，用于定义脚本并控制其执行过程。
* `noscript`，用于规定在浏览器不支持或禁用了脚本的情况下的处理办法。

##### script
> script元素可以放在HTML文档的任意位置，不过一般建议都集中放到文档的head部分，这有助于查看脚本。

```html
<!DOCTYPE html>
<html>
<head>
	<!--定义script脚本-->
	<script type="text/javascript">
		document.writeln("This is from the script");
	</script>

	<!--用src属性载入外部脚本,设置src属性的script元素不能含有任何内容！即，不能既内嵌脚本又引用外部脚本。-->
	<script src="simple1.js"></script>
    
    <!--推迟脚本的执行-->
    <!--defer：告诉浏览器要等页面载入和解析完毕之后才能执行脚本。-->
    <!--defer 属性只能用于外部脚本文件-->
    <script defer src="simple2.js"></script>

    <!--异步执行脚本-->
    <script async src="simple3.js"></script>
</head>
<body>
</body>
</html>
```

![image](http://upload-images.jianshu.io/upload_images/2648731-cc530fdf31176bb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### noscript
```html
<!DOCTYPE html>
<html>
<head>
	<!--定义noscript脚本-->
	<!--1.noscript元素可以用来向禁用了JavaScript或浏览器不支持JavaScript的用户显示一些内容。-->
	<noscript>
		<h1>JavaScript is required!</h1>
		<p>You cannot use this page without JavaScript</p>
	</noscript>

	<!--2.用noscript元素重定向浏览器-->
	<noscript>
		<meta http-equiv="refresh" content="0; http://www.apress.com">
	</noscript>
</head>
<body>
</body>
</html>
```



## 文本元素

### 标记文字

#### h1~h6 标题
![image](http://upload-images.jianshu.io/upload_images/2648731-7b52881f25ad7814.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```html
<!--请确保将 HTML 标题标签只用于标题。不要仅仅是为了生成粗体或大号的文本而使用标题。-->
<!--搜索引擎使用标题为您的网页的结构和内容编制索引。-->
<!--因为用户可以通过标题来快速浏览您的网页，所以用标题来呈现文档结构是很重要的。-->
<!--应该将 h1 用作主标题（最重要的），其后是 h2（次重要的），再其次是 h3，以此类推。-->
<!--提示：浏览器会自动在标题前后添加空行-->

<h1>一级标题</h1>
<font size="6">这是6号字体文本</font>

<h2>二级标题</h2>
<font size="5">这是5号字体文本</font>

<h3>三级标题</h3>
<font size="4">这是4号字体文本</font>

<h4>四级标题</h4>
<font size="3">这是3号字体文本</font>

<h5>五级标题</h5>
<font size="2">这是2号字体文本</font>

<h6>六级标题</h6>
<font size="1">这是1号字体文本</font>
```

![image](http://upload-images.jianshu.io/upload_images/2648731-2bfa95d1b3623442.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




#### <p> 元素

```html
<p>有时候阳光很好，有时候阳光很暗</p>
```

当显示页面时，浏览器会移除源代码中多余的空格和空行。所有连续的空格或空行都会被算作一个空格。需要注意的是，HTML 代码中的所有连续的空行（换行）也被显示为一个空格。



#### HTML链接 a

a = attributes，表示属性标签。
默认情况下，链接将以以下形式出现在浏览器中：

- 一个未访问过的链接显示为蓝色字体并带有下划线。
- 访问过的链接显示为紫色并带有下划线。
- 点击链接时，链接显示为红色并带有下划线。

> 注意：如果为这些超链接设置了 CSS 样式，展示样式会根据 CSS 的设定而显示。

![image](http://upload-images.jianshu.io/upload_images/2648731-f492334a1ac5e893.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<base href="http://titan/listings">
</head>
<body>
	<!--生成指向外部的超链接-->
	<a href="http://www.jianshu.com/u/84c5fadc7c18">我的主页：独木舟的木</a>

	<!--使用相对URL-->
	<a href="fruitlist.html">here</a>

	<!--配合使用id，生成内部超链接-->
	<a href="#banana">Banana</a>

	<p id="banana">
	I also like bananas, mangoes, cherries, apricots, plums.
	</p>
</body>
</html>
```


#### HTML 链接 - target元素

使用 target 属性，你可以定义被链接的文档在何处显示。
![image](http://upload-images.jianshu.io/upload_images/2648731-8e53127481afe092.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```html
<!--html链接：在新窗口打开-->
<a href="http://www.baidu.com/" target="_blank">打开 Baidu</a>

<!--跳出框架-->
<a href="http://www.baidu.com/" target="_top">打开 Baidu</a>

<!--电子邮件-->
<!--注意：单词之间空格使用 %20 代替，以确保浏览器可以正常显示文本。-->
<p>
这是一个电子邮件
<a href=@"andywhm@qq.com?Subject=Hello%20World" target="_top">发送邮件</a>
</p>
```

####  id 属性
作用：给元素分配一个唯一的标识符。这种标识符通常用来将CSS样式应用到元素上或在JavaScript程序中用来选择元素。

**提示:** 书签是不以任何特殊的方式显示，在HTML文档中是不显示的，所以对于读者来说是隐藏的。

将CSS样式应用到元素上：
```html
<!DOCTYPE html>
<html>
<head>
    <title>使用 id 属性</title>
    <style type="text/css">
    	#w3clink {
    		background: grey;
    		color: white;
    		padding: 5px;
    		border: thin solid black;
    	}	
    </style>
</head>
<body>
	<a href="http://apress.com">Apress Web Site</a>
	<p></p>
	<a id="w3clink" href="http://w3c.org">w3c web site</a>
</body>
</html>
```
![image](http://upload-images.jianshu.io/upload_images/2648731-254855ed9410c216.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



导航到文档中的特定位置：
```html
<!--在HTML文档中插入ID-->
<a id="tips">有用的提示部分</a>

<!--在HTML文档中创建一个链接到“有用的提示部分（id=“tips”）”-->
<!--类似于导航功能-->
<a href="#tips">访问有用的提示部分</a>

<!--或者。从另一个页面创建一个链接到“有用的提示部分（id="tips"）”-->
<a href="example.html#tips">访问有用的提示部分</a>
```

**注释：** 请始终将正斜杠添加到子文件夹。假如这样书写链接：href="http://www.runoob.com/html"，就会向服务器产生两次 HTTP 请求。这是因为服务器会添加正斜杠到这个地址，然后创建一个新的请求，就像这样：href="http://www.runoob.com/html/"。


#### 自定义属性
规定：自定义属性的前缀为 `data-`。 
```html
Enter your name: <input type="text" name="name" disabled="true" data-purpose="collection">
```


#### HTML 链接 - 图片链接

将图片作为链接使用。

```html
<!--图片链接-->
<p>创建图片链接
    <a href="http://icon.reeoo.com/coowl/">
        <img border="10" src="http://reeoo.qiniudn.com/Coowl-icon.png!icon180" alt="coowl" width="32" height="32">
    </a>
</p>

<p>无边框的图片链接
    <a href="http://icon.reeoo.com/coowl/">
        <img border="0" src="http://reeoo.qiniudn.com/Coowl-icon.png!icon180" alt="coowl" width="32" height="32">
    </a>
</p>
```

![image](http://upload-images.jianshu.io/upload_images/2648731-a576a3c829418119.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### JPEG & GIF 图片的区别

| 照片和复杂图像使用JPEG格式                                   | 纯色图像、logo和几何图形使用GIF格式                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 可在连续调次（复制品中有中间层次，如照片）图像中获得最好效果 | 对于几种纯色组成的图像、线条组成的图像（如logo、剪贴画）和含有小段文字的图像，使用GIF比较合适 |
| 可以用1600万种不同的颜色显示图像                             | 用256种颜色显示图像                                          |
| 是一种“有损”格式，因为文件缩小时会丢失部分图像信息           | GIF同样会压缩文件来减小尺寸，但是不会丢失任何东西，是一种“无损”格式 |
| 不支持透明                                                   | 允许8⃣️背景颜色设置为透明的，图像背景就可以穿透显示            |



### title 属性

作用：提供元素的额外信息。浏览器通常用这些东西显示工具提示。

示例：

```html
<a title="Apress Publishing" href="http://apress.com">Visti the Apress site</a>
```

鼠标停留后会显示浮窗：
![title 属性](http://upload-images.jianshu.io/upload_images/2648731-b9591d9ea77d9210.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### HTML 图像 img

```html
<img src="https://i.pinimg.com/564x/6c/77/9b/6c779b9f24ba0ec56dc62c7bbfaf004b.jpg" width="542" height="548">
```



#### 使用实例

```html
<!--<img> 是空标签，它只包含属性，并且没有闭合标签。-->
<!--src:图像的存储位置-->
<!--alt:为图像定义一串预备的可替换的文本,图像无法载入时，浏览器会显示这个替换文本-->
<!--height、width：图像的高和宽-->
<!--border:图像的边框-->
<img border="0" src="https://ws2.sinaimg.cn/large/006tNc79ly1fm9bzhpv4aj30f20f8jrr.jpg" alt="linyuner" width="200" height="200">
<img src="https://ws2.sinaimg.cn/large/006tNc79ly1fm9bzhpv4aj30f20f8jrr.jpg" alt="让图片浮动到文本的最左边" style="float: left;">
<img src="https://ws2.sinaimg.cn/large/006tNc79ly1fm9bzhpv4aj30f20f8jrr.jpg" alt="让图像浮动到文本的最右边" style="float: right;">
```

#### 图像映射

```html
<!--创建图像映射：显示如何创建带有可供点击区域的图像地图。其中的每个区域都是一个超级链接。-->
<p>点击同一张图片的不同区域，打开的是不同的链接地址</p>

<img src="planets.gif" alt="planets" width="145" height="145" usemap="#planetmap">
<!--map:定义图像地图-->
<map name="planetmap">
  	<!--area:定义图像地图中的可点击区域-->
    <area shape="rect" coords="0.0.82,126" href="sun.html" alt="Sun">
    <area shape="circle" coords="90,58,3" href="mercur.html" alt="Mercury">
    <area shape="circle" coords="124,58,8" href="venus.html" alt="Venus">
  
    <!--矩形：左上角顶点坐标（x1,y1）,右下角顶点坐标（x2,y2）-->
    <area shape="rect" coords="x1,y1,x2,y2" href="url">
    <!--圆形:圆心坐标为（x1,y1）,半径为r-->
    <area shape="circle" coords="x1,y1,r" href="url">
    <!--多边形：各个顶点坐标依次为：（x1,y1）、（x2,y2）...-->
    <area shape="poly" coords="x1,y1,x2,y2..." href="url">
</map>
```

### 使用插图 figure

<figcaption>元素必须是<figure>元素的第一个或最后一个子元素。

```html
<figure>
    <figcaption>插图标题</figcaption>
    <code>var fruits = ["apples", "oranges", "managoes", "cherries"];<br/>
    </code>
</figure>
```

![image](http://upload-images.jianshu.io/upload_images/2648731-35a97461a48b67df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### HTML 换行

两种换行方式：

* br：强制换行。
* wbr：建议换行。

```html
<!--
	* 如果你希望在不产生新行的情况下进行换行操作！就使用<br />元素
    * 在开始标签中添加斜杠，比如 <br />，是关闭空元素的正确方法，HTML、XHTML 和 XML 都接受这种方式。即使 	<br> 在所有浏览器中都是有效的，但使用 <br /> 其实是更长远的保障。
	* 注意到br 和 / 之间有空隔，这是为了保证HTML5之前的浏览器向上的兼容性
-->

<p>
    我是我命运的主宰<br />
    我是我灵魂的统帅<br />
</p>
```



### `<hr />` 主题分离
hr:表示主题的改变，呈现效果是在HTML文档中显示一条**水平线**。
<hr /> ,这种只能使用一个标签表示，在其中放置任何内容都不符合HTML规范的元素称之为**虚元素**。

```html
<hr />
```

### 空元素、自闭合元素、虚元素

**元素=开始标签 + 内容 + 结束标签**：`<code>apples</code>`



```html
<!--空元素：元素的开始标签和结束标签之间没有任何内容。-->
<p></p>

<!--自闭合元素，将开始标签和结束标签合二为一，效果同上，写法更简洁-->
<p/>

<!--虚元素：本身只能用一个标签表示-->
<hr />
```

### HTML 注释

```html
<!--注释内容-->
```

在 .css 文件中的注释格式：`/*注释内容*/`



### HTML 格式化文本

#### 文本格式化

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
</head>
<body>
	正常文本字体。<br/>

	<!--不附带任何重要性含义地表示一段文本-->
	<b>b 元素：用来表示文章提要中的关键词和产品评论中的产品名称</b><br/>

	<!--加以强调-->
	<em>em 元素：表示对一段文字的强调</em><br/>

	<!--表示外文词语或科技术语-->
	<i>i 元素：表示一段文字与周围内容有本质区别，使用场景：外文词语、科技术语、某人的想法</i><br/>

	<!--表示不准确或校正-->
	<s>s 元素：表示一段文字不再正确或准确</s><br/>

	<!--表示重要的文字-->
	<strong>string 元素：表示一段重要文字</strong><br/>

	<!--为文字添加下划线-->
	<u>u 元素：让一段文字从周围内容中凸现出来，但并不表示强调或重要性有所增加</u><br/>

	<!--添加小号字体内容-->
	<small>small 元素：表示小号字体内容，常用于免责声明和澄清声明</small><br/>

    <big>放大字体</big><br/>

	<!--添加上标和下标-->
	上标：2<sup>100</sup>、x<sup>2</sup>、10<sup>th</sup>point<br/>

	下标：A<sub>1</sub>、A<sub>2</sub><br/>

	<!--换行-->
	br元素会引起一次换行。其习惯样式是将后续内容转移到新行上。<br/>
	br元素只宜用在换行也是内容的一部分的情况，切勿用它创建段落或别的内容组。<br/>

	<!--指明可以安全换行的建议位置-->
	💡wbr元素是HTML5新增的，用来表示长度超过当前浏览器窗口的内容适合在此换行。<wbr>究竟换不换行由浏览器决定。<wbr> wbr元素只不过是对恰当的换行位置的建议而已。
	wbr元素可以让浏览器将一个长单词分为几小截，
</body>
</html>
```
![image](http://upload-images.jianshu.io/upload_images/2648731-68822d247cff4e0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 计算机的输入和输出

```html
    code：表示计算机代码片段:<br/>
    <code>for (var i = Things.length - 1; i >= 0; i--) {
            Things[i]
        }</code><br/>
    var：在编程语境中表示变量。也可表示一个供读者在想象中插入一个指定值的占位符。<br/>
    <p>The variable in this examples is :<var>fruits</var></p>

    samp：表示程序或计算机系统的输出<br/>
    <p>The out put from the code is:<samp>I like banana.</samp></p>

    kbd：表示用户键盘输入<br/>
    <p>When prompted for my favourite fruit,I type: <kbd>vherries</kbd></p>

    <tt>tt：打字机文本</tt>

    <!--使用预先编排好格式的内容-->
    <!--pre元素可以改变浏览器处理内容的方式，阻止合并空白字符，让源文档中的格式得以保留）-->
    <!--（理解：默认情况下浏览器会合并文本中的空格和换行，使用此元素可以保留文本中的编排格式。-->
    <pre>
    此例演示如何使用 pre 标签
    对空行和 空格 
    进行控制
    </pre>
```
![image](http://upload-images.jianshu.io/upload_images/2648731-39e19cc132b4ee1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 使用标题引用、引文、定义和缩写
```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Example</title>
</head>

<body>
    使用标题引用、引文、定义和缩写<br/>

    <!--abbr元素用来表示缩写-->
    The <abbr title="Florida Department of citrus">FDDC</abbr> regulates the Florida citrus industry.

    <!--dfn元素表示定义中的术语，title属性的值=定义的术语-->
    <p>
        The <dfn title="apple">apple</dfn> is the. pomaceous fruit of the apple tree...
    </p>

    <!--
        * <q>元素表示引用来自他处的内容，
        
        * <q>元素是简短地“引用”现有文字中的一部分。
        * <q>元素是内联（inline）元素，内联元素总是在网页中随着文字流出现在“行内”。
        * <blockquote>元素则是引用一大段文字并独立显示。
        * <blockquote>是块（block）元素，块元素前后都有换行符。
        * 记住：块元素特立独行，内联元素随波逐流。

    -->
    <q cite="http://en.wikoipedia.org">  All problems in computer science can be solved by another level of indirection.——Butler Lampson
	<br/>计算机科学领域的任何问题都可以通过增加一个间接的中间层来解决</q> <br> 《春》是现代散文家朱自清的作品。《春》是朱自清的散文名篇，最初发表于1933年7月，此后长期被中国中学语文教材选用。[1] 在该篇“贮满诗意”的“春的赞歌”中，事实上饱含了作家特定时期的思想情绪、对人生及至人格的追求，表现了作家骨子里的传统文化积淀和他对自由境界的向往。
    <blockquote>
        盼望着，盼望着，东风来了，春天的脚步近了。一切都像刚睡醒的样子，欣欣然张开了眼。山朗润起来了，水涨起来了，太阳的脸红起来了。
    </blockquote>
    小草偷偷地从土里钻出来，嫩嫩的，绿绿的。园子里，田野里，瞧去，一大片一大片满是的。坐着，躺着，打两个滚，踢几脚球，赛几趟跑，捉几回迷藏。风轻悄悄的，草软绵绵的。

    <!--cite元素，表示引用其他作品（如图书、文章、电影和诗歌）的标题-->
    <p>
        My favourite movie on fruit is <cite>这个杀手不太冷</cite>.
    </p>
</body>

</html>
```



#### 块引用

```html
<!--块引用,演示如何实现长短不一的引用语-->
<!--q:定义短的引用语-->
<p>WWF's goal is to:
    <q>Build a future where people live in a harmony with natrue.</q>
    We hope they succeed.
</p>

<!--blockquote:定义长的引用，通常用在需要引用的内容更多的情况。-->
<p>不可屈服
    <blockquote>透过覆盖着我的层层夜幕，黑暗层层无底，感谢万能的上苍，赐给我不可征服的灵魂，就算被地狱牢牢抓住，我不会畏缩也不会哭叫，任凭命运百般捉弄，我头破血流但不低头，在这充满愤怒和眼泪的土地之上，恐怖阴影阴森的逼近，不过，即使岁月不停恐吓，亦将发现我毫不畏缩，无论大门何等狭窄，无论承受多么深重的责罚，我是我命运的主宰，我是我灵魂的统帅。</blockquote>
    ——By William henley
</p>
```

![块引用](http://upload-images.jianshu.io/upload_images/2648731-02d4be43447506d9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





#### 使用语言元素
##### ruby、rt和rp

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
</head>
<body>
	使用语言元素<br/>

	<!--ruby元素表示一段包含注音符号的文字-->
	<!--ruby元素需要与rt元素（标记注音符号）和rp元素（供不支持注音符号的浏览器显示在注音符号符号前后的括号）搭配使用-->
	<ruby>魑<rp>(</rp><rt>chī</rt><rp>)</rp></ruby>
	<ruby>魅<rp>(</rp><rt>mèi</rt><rp>)</rp></ruby>
	<ruby>魍<rp>(</rp><rt>wǎng</rt><rp>)</rp></ruby>
	<ruby>魉<rp>(</rp><rt>liǎng</rt><rp>)</rp></ruby>
</body>
</html>
```
![image](http://upload-images.jianshu.io/upload_images/2648731-c32220c87630d669.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### bdo元素
bdo元素可以用来撇开默认的文字方向设置，明确地指定其内容中文字的方向。

`dir`属性值设置：
* ltr：文字方向从左到右；
* rtl：文字方向从右到左；

```html
<!--文字方向-->
<p>该段落文字从左到右显示</p>
<p><bdo dir="rtl">该段落文字从左到右显示</bdo></p>
```
![文字方向](http://upload-images.jianshu.io/upload_images/2648731-5e13cb8518b304c9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### bdi元素
bdi元素表示一段出于文字方向考虑而与其他内容隔离开来的文字。
这个元素适用于欲显示内容的文字方向未知的情况（参考HTML5权威指南8.6.3）。

#### 表示一般性的内容
```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		.fruit {
			border: thin solid black;
			padding: 1px;
		}
	</style>
</head>
<body>
	<!--1.表示一段一般性的内容-->
	<!--span元素本身没有任何意义，它可以用来把一些全局属性应用到一段内容上。-->
	I like <span class="fruit">oranges</span> and <span class="fruit">apples</span>.<br/>

	<!--2.突出显示文本-->
	<!--mark元素用来表示因为与某段上下文相关而被突出显示的一段文字-->
	今天你<mark>吃鸡</mark>了吗？<br/>

	<!--3.表示添加和删除的内容-->
	<!--ins元素和del元素可以分别用来表示文档中添加和删除的文字-->
	<p>
		<del>I can <mark>sea</mark> the <mark>see</mark></del>
		<ins>I can <mark>see</mark> the <mark>sea</mark></ins>
	</p>

	<!--4.表示时间和日期-->
	我在<time datetime="15:00">3 o'clock</time>购买了面包和牛奶
	日期是<time datetime="2018-02-02">2月2号</time>

</body>
</html>
```
![image](http://upload-images.jianshu.io/upload_images/2648731-08058f96db3afa7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





#### 缩写和首字母缩写

```html
<!--文本和缩略-->
<abbr title="etcetera">etc.</abbr>
<br />
<acronym title=@"World Wide Web">WWW</acronym>
<br />

<p>在某些浏览器中，当您把鼠标移至缩略词语上时，title 可用于展示表达的完整版本</p>
<p>仅对于 IE 5 中的 acronym 元素有效</p>
<p>对于 Netscape 6.2 中的 abbr 和 acronym 元素都有效</p>
```



#### 文本显示为单行,超过部分隐藏并使用省略号

```css
div {
  overflow:hidden;
  text-overflow:ellipsis;
  white-space:nowrap;
  width:120px;
}
```




### HTML 区块 <div>、<span>

* <div> —— **块（block）元素**，定义了文档的区域，会自动换行。
* <span> —— **内联（inline）元素**，用来组合文档中的行内元素，不会换行。



#### HTML 布局

**Tip:** 使用 CSS 最大的好处是，如果把 CSS 代码存放到外部样式表中，那么站点会更易于维护。通过编辑单一的文件，就可以改变所有页面的布局。

**Tip:** 由于创建高级的布局非常耗时，使用模板是一个快速的选项。通过搜索引擎可以找到很多免费的网站模板（您可以使用这些预先构建好的网站布局，并优化它们）。

#### 使用 <div> 元素的网页布局

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>文档标题</title>
</head>
<body>
	<div id="container" style="width: 500px">

		<div id="header" style="background-color: #27AEE8">
			<h1 style="margin-bottom: 0;"> 主要的网页标题</h1>
		</div>

		<div id="menu" style="background-color: #313842h; height: 200px; width: 100px; float: left;">
			<b>导航栏</b><br />
			HTML<br />
			CSS<br />
			JavaScript<br />		
		</div>

		<div id="content" style="background-color: #C72F2E; height: 200px; width: 400px; float: left;">
			内容在这里
		</div>

		<div id="footer" style="background-color: #bcbcbc; clear: both; text-align: center;">
			版权所有©️️ abc.com
		</div>
		
	</div>
</body>
</html>
```

![image](http://upload-images.jianshu.io/upload_images/2648731-b22368478e2a52b4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 使用 <table> 元素的网页布局，不推荐的方式！

> 💡虽然我们可以使用HTML table标签来设计出漂亮的布局，但是table标签是不建议作为布局工具使用的 - 表格不是布局工具。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>文档标题</title>
</head>
<body>

	<table width="500" border="0">
		<tr>
			<td colspan="2" style="background-color: #27AEE8">
				<h1>主要的网页标题</h1>
			</td>
		</tr>

		<tr>
			<td style="background-color: #313842h; width: 100px; vertical-align: top;">
				<b>导航栏</b><br>
				HTML<br>
				CSS<br>
				JavaScript<br>
			</td>
			<td style="background-color: #C72F2E; height: 200px; width: 400px; vertical-align: top;">
				内容在这里
			</td>
		</tr>
		
		<tr>
			<td colspan="2" style="background-color: #bcbcbc; text-align: center;">
				版权所有©️️ abc.com
			</td>
		</tr>
	</table>
	
</body>
</html>
```




![image](http://upload-images.jianshu.io/upload_images/2648731-a683864cb2cab77b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







### HTML 框架

HTML 框架语法：`<iframe scr="URL"></iframe>`

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>文档标题</title>
</head>
<body>
	<iframe src="URL" width="200" height="200"></iframe>

	<p>该 URL 指向不同的网页</p>
	<p>一些旧的浏览器不支持 iframe。</p>
	<p>如果浏览器不支持 iframe 则不会显示</p>

</body>
</html>
```

#### 移除边框

```html
<iframe src="URL" frameborder="0"></iframe>
```

#### 使用 iframe 来显示目标链接页面

```html
<iframe src="URL" name="iframe_a"></iframe>
<p><a href="http://www.example.com/" target="iframe_a">www.example.com</a></p>
```



### HTML 颜色

HTML 颜色由红色、绿色、蓝色混合而成。

#### 颜色值

HTML 颜色由一个十六进制符号来定义，这个符号由红色、绿色和蓝色的值组成（RGB）。

```html
<p style="background-color: #0e5f71">
    通过十六进制设置背景颜色
</p>

<p style="background-color: rgb(255,255,0);">
    通过 rbg 值设置背景颜色
</p>

<p style="background-color: green">
    通过颜色名设置背景颜色	
</p>
```
![HTML 颜色](http://upload-images.jianshu.io/upload_images/2648731-e4751b513daa66cf.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


三种颜色 红，绿，蓝的组合从0到255，一共有1600万种不同颜色(256 x 256 x 256)。



### HTML 颜色名

141个颜色名称是在HTML和CSS颜色规范定义的（17标准颜色，再加124）。

> ![Remark](http://upload-images.jianshu.io/upload_images/2648731-545663c1708c3ced.gif?imageMogr2/auto-orient/strip)**提示:** 17标准颜色：黑色，蓝色，水，紫红色，灰色，绿色，石灰，栗色，海军，橄榄，橙，紫，红，白，银，蓝绿色，黄色。点击其中一个颜色名称（或一个十六进制值）就可以查看与不同文字颜色搭配的背景颜色。



### HTML 脚本

JavaScript 使 HTML 页面具有更强的动态和交互性。

#### HTML <script> 标签

<script> 标签用于定义客户端脚本，比如 JavaScript。

<script> 元素既可包含脚本语句，也可通过 src 属性指向外部脚本文件。

JavaScript 最常用于图片操作、表单验证以及内容动态更新。

下面的脚本会向浏览器输出"Hello World!"：

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>文档标题</title>
</head>
<body>

	<p>JavaScript 能够直接写入 HTML 输出流中</p>
	<script type="text/javascript">
		document.write("<h1>这是一个标题</h1>");
		document.write("<p>这是一个段落。</p>");
	</script>

	<p>
		你只能在 HTML 输出流中使用 <strong>document.write</strong><br />
		如果你在文档已加载后使用它（比如在函数中），会覆盖整个文档。
	</p>

	<p id="demo">
		JavaScript 可以触发事件，就像按钮点击。
	</p>
	<script type="text/javascript">
		function myFunction()
		{
			// 事件响应
			document.getElementById("demo").innerHTML="Hello JavaScript"; 

			// 处理 HTML 样式
			x=document.getElementById("demo") // 找到元素
			x.style.color = "#ff0000"; // 改变样式
		}
	</script>

	<button type="button" onclick="myFunction()">点我</button>

</body>
</html>
```

![HTML脚本](http://upload-images.jianshu.io/upload_images/2648731-eb7c9b58678e0483.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### HTML 字符实体

如需显示小于号，我们必须这样写： `&lt；` 或`&#60；` 或 `&#060;`

**提示：** 使用实体名而不是数字的好处是，名称易于记忆。不过坏处是，浏览器也许并不支持所有实体名称（对实体数字的支持却很好）。

![image](http://upload-images.jianshu.io/upload_images/2648731-a4b33e7f2fcdca23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




#### 不间断空格

HTML 中的常用字符实体是不间断空格`&nbsp;`。

 HTML 实体参考手册：http://www.runoob.com/tags/ref-entities.html



### HTTP 统一资源定位符 URL

```
scheme://host.domain:port/path/filename
```

- scheme - 定义因特网服务的类型。最常见的类型是 http
- host - 定义域主机（http 的默认主机是 www）
- domain - 定义因特网域名，比如 runoob.com
- :port - 定义主机上的端口号（http 的默认端口号是 80）
- path - 定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）。
- filename - 定义文档/资源的名称




### class 属性
作用：将元素归类；
* 找出文档中的某一类元素；
* 为某一类元素应用CSS样式，可以减少重复设置；

应用CSS样式示例代码：
```html
<!DOCTYPE html>
<html>
<head>
    <title>使用 class 属性将元素归类</title>
    <style type="text/css">
    	.class2 {
    		background-color: grey;
    		color: white;
    		padding: 5px;
    		margin: 2px;
    	}
    	.class1 {
    		font-size: x-large;
    	}
    </style>
</head>
<body>
    <a class="class1 class2" href="http://appress.com">Appress web site</a>
	<p/>
	<a class="class2 otherclass" href="http://w3c.org">W3C web site</a>
</body>
</html>
```

![应用CSS样式](http://upload-images.jianshu.io/upload_images/2648731-e1288c97ed7bd60b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在JS脚本中使用class属性示例代码：
```html
<!DOCTYPE html>
<html>
<head>
    <title>使用 class 属性将元素归类</title>
</head>
<body>
    <a class="class1 class2" href="http://appress.com">Appress web site</a>
	<p/>
	<a class="class2 otherclass" href="http://w3c.org">W3C web site</a>
	<script type="text/javascript">
		var elems = document.getElementsByClassName("otherclass");
		for (i = 0; i <= elems.length; i++) {
			var x = elems[i];
			x.style.border = "thin solid black";
			x.style.backgroundColor = "white";
			x.style.color = "black";
		}
	</script>
</body>
</html>
```
JS脚本的作用：找出所有属于 otherclass 类的元素并对其设置一些样式。
![在脚本中使用class属性](http://upload-images.jianshu.io/upload_images/2648731-7b093a389d809c0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### contenteditable 属性
作用：让用户能够修改页面上的内容

```html
<!DOCTYPE html>
<html>
<head>
    <title>使用 contenteditable 属性</title>
</head>
<body>
    <p contenteditable="true">It is raining right now</p>
</body>
</html>
```
单击改段文字后即可开始编辑：
![contenteditable 属性](http://upload-images.jianshu.io/upload_images/2648731-ae9cfd07674ef42c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### contentmenu 属性
作用：为元素设定快捷菜单。

### draggable 属性
作用：HTML5支持拖放操作的方式之一，用来表示元素是否可被拖放。

### dropzone 属性
作用：HTML5支持拖放操作的方式之一


### hidden 属性
作用：表示相关元素当前毋需关注，浏览器会隐藏相关元素。

示例代码：
```html
<!DOCTYPE html>
<html>
<head>
	<!--hidden:表示当前元素无需关注-->
    <title>使用 hidden 属性</title>
    <!--JS函数的作用：如果当前元素存在hidden属性，则移除该属性（显示元素），否则添加该属性（隐藏元素）。-->
    <script type="text/javascript">
    	var toggleHidden = function() {
    		var elem = document.getElementById("toggle")
    		if (elem.hasAttribute("hidden")) {
    			elem.removeAttribute("hidden");
    		}else {
    			elem.setAttribute("hidden","hidden");
    		}
    	}
    </script>
</head>
<body>
    <button onclick="toggleHidden()">Toggle</button>
    <table>
    	<tr><th>Name</th><th>city</th></tr>
    	<tr><td>Adam Freeman</td><td>London</td></tr>
    	<tr id="toggle" hidden><td>Joe Simth</td><td>New Work</td></tr>
    	<tr><td>Anne Jones</td><td>Paris</td></tr>
    </table>
</body>
</html>
```
![hidden](http://upload-images.jianshu.io/upload_images/2648731-8cd7fe594f27719a.gif?imageMogr2/auto-orient/strip)


### lang 属性
作用：用于说明元素内容使用的语言。使用 lang 属性的目的是让浏览器调整其表达元素内容的方式。比如说，改变使用的引号，在使用了文字朗读器的情况下正确发音。
语言声明规范说明参考：https://tools.ietf.org/html/bcp47

示例：
```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
</head>
<body>
	<p lang="en">Hello - how are yor?</p>
	<p lang="fr">Bonjour - comment ça va-vous</p>
	<p lang="es">Hola - cómo estás</p>
</body>
</html>
```

### 参考资料
- [HTML 教程](http://www.runoob.com/html/html-tutorial.html)
- [HTML 速查列表](http://www.runoob.com/html/html-quicklist.html)
- [HTML 手册](http://www.runoob.com/tags/html-reference.html)
- [万维网联盟w3c](http://www.chinaw3c.org)
- 《HTML5 权威指南》

