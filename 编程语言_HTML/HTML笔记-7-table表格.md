### HTML 表格

| 表格元素 | 含义         |
| -------- | -------------- |
| table       | HTML文档中的表格 |
| tr       | 表格行 |
| th       | 表头。（单元格中的标题元素），对单元格数据的说明 |
| td       | 单元格 |

* **HTML中的表格基于行而不是列，每个行必须分别标记。**
* HTML5中，表格不应该，也不能用来处理页面布局。



示例代码：

```html
<!--border 属性值必须设置为1，表格边框的粗细必须用CSS设置。-->
<table border="1">
    <tr>
        <th>表头标题一</th>
        <th>表头标题二</th>
    </tr>
    <tr>
        <td>第一行，第一列</td>
        <td>第一行，第二列</td>
    </tr>
    <tr>
        <td>第二行，第一列</td>
        <td>第二行，第二列</td>
    </tr>
</table>
```

![](http://upload-images.jianshu.io/upload_images/2648731-2554b87401a1ecaa.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


#### 表格中的表头 <th>

```html
<h3>水平标题</h3>
<table border="1">
    <tr>
        <th>姓名</th>
        <th>性别</th>
        <th>备注</th>
    </tr>
    <tr>
        <td>Andy</td>
        <td>man</td>
        <td>0510-8233</td>
    </tr>
</table>

<h3>垂直标题</h3>
<table border="1">
    <tr>
        <th>姓名</th>
        <td>Andy</td>
    </tr>
    <tr>
        <th>性别</th>
        <td>man</td>
    </tr>
    <tr>
        <th>电话</th>
        <td>0510-8233</td>
    </tr>
</table>
```

![](http://upload-images.jianshu.io/upload_images/2648731-0c0f3706e3a55628.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

#### 跨行或跨列的表格单元格

| 表格元素 | 含义                 |
| -------- | -------------------- |
| colspan  | 让一个单元格横跨多列 |
| rowspan  | 让一个单元格纵跨多行 |

* 为**colspan** 和 **rowspan** 设置的值必须是整数。

```html
<h3>单元格跨两格</h3>
<table border="1">
    <tr>
        <th>Name</th>
        <th colspan="2">Telephone</th>
    </tr>
    <tr>
        <td>Bill Gates</td>
        <td>MicroSoft</td>
        <td>666</td>
    </tr>
</table>

<h3>单元格跨两列</h3>
<table border="1">
    <tr>
        <th>Name</th>
        <td>Andy</td>
    </tr>
    <tr>
        <th rowspan="2">Telephone</th>
        <td>Hello</td>
    </tr>
    <tr>
        <td>World</td>
    </tr>
</table>
```

![](http://upload-images.jianshu.io/upload_images/2648731-9b500948bebcc047.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)




#### 表格内的标签

```html
<table border="1">
    <tr>
        <td>
            <p>Stay Hungry</p>
            <p>Stay Foolish</p>
        </td>
        <td>
            这个单元格里包含一个表格
            <table border="1">
                <tr>
                    <td>A1</td>
                    <td>A2</td>
                </tr>
                <tr>
                    <td>B1</td>
                    <td>B2</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr>
        <td>
            这个单元格包含一个列表
            <ul>
                <li>Apple</li>
                <li>banana</li>
                <li>oranges</li>
            </ul>
        </td>
        <td>
            这个单元格里放一张图片😂😂😂
            <br />
            <img src="http://cdn2.jianshu.io/assets/web/logo-58fd04f6f0de908401aa561cda6a0688.png">
        </td>
    </tr>
</table>
```
![](http://upload-images.jianshu.io/upload_images/2648731-44f141ff98061a22.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

#### 单元格边距

`cellpadding`属性设置的是单元格内的留白。

```html
<h3>没有单元格边距</h3>
<table border="1">
    <tr>
        <td>A</td>
        <td>B</td>
    </tr>
    <tr>
        <td>C</td>
        <td>D</td>
    </tr>
</table>


<h3>有单元格边距</h3>
<table border="1" cellpadding="10">
    <tr>
        <td>A</td>
        <td>B</td>
    </tr>
    <tr>
        <td>C</td>
        <td>D</td>
    </tr>
</table>
```

![](http://upload-images.jianshu.io/upload_images/2648731-969d73185f48610c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



#### 单元格间距

`cellspacing` 属性设置的是单元格之间的距离。

```html
<h3>没有单元格间距</h3>
<table border="1">
    <tr>
        <td>111</td>
        <td>112</td>
    </tr>
    <tr>
        <td>1134</td>
        <td>1126</td>
    </tr>
</table>


<h3>单元格间距="0"</h3>
<table border="1" cellspacing="0">
    <tr>
        <td>111</td>
        <td>112</td>
    </tr>
    <tr>
        <td>1134</td>
        <td>1126</td>
    </tr>
</table>

<h3>单元格间距="10"</h3>
<table border="1" cellspacing="10">
    <tr>
        <td>111</td>
        <td>112</td>
    </tr>
    <tr>
        <td>1134</td>
        <td>1126</td>
    </tr>
</table>
```

![](http://upload-images.jianshu.io/upload_images/2648731-df2b11e27ea05052.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



### caption、 thead、tfoot、tbody 元素 headers 属性


示例1.1

table.css 文件：

```css
thead th,
tfoot th {
    text-align: left;
    background: grey;
    color: white;
}

tbody th {
    text-align: right;
    background: lightgrey;
    color: grey;
}
[colspan], [rowspan] {
    font-weight: bold;
    border: medium solid black;
}
thead [clospan], tfoot [colspan] {
    text-align: center;
}
caption {
    font-weight: bold;
    font-size: large;
    margin-bottom: 5px;
}

```

index.html 文件：

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Example</title>
    <meta name="author" content="Andy">
    <meta name="description" content="A simple example of aside">
    <link rel="shortcut icon" type="image/x-icon" href="favico.ico">
    <link rel="stylesheet" href="table.css">
</head>

<body>
    <table>
        <!--caption元素可以用来为表格定义一个标题并将其与表格关联起来。-->
        <caption>Result of the 2011 Fruit Survey</caption>
        <!--thead元素，用来标记表格的标题行-->
        <thead>
            <tr>
                <th id="rank">Rank</th>
                <th id="name">Name</th>
                <th id="color">Color</th>
                <th id="sizeAndVotes" colspan="2">Size & Votes</th>
            </tr>
        </thead>
        <!--tfoot元素，用来标记组成表脚的行-->
        <tfoot>
            <tr>
                <th colspan="5"> & copy; 2011 Adam Freeman Fruit Data Enterprises</th>
            </tr>
        </tfoot>
        <!--tbody元素，构成表格主体的全体行-->
        <tbody>
            <tr>
                <!--td和th元素都定义了headers属性，它可以供屏幕阅读器和其他残障辅助技术用来简化对表格的处理。
                headers属性值可以被设置为一个或多个th单元格的id属性值。
                -->
                <th id="first" headers="rank">Favourite:</th>
                <td headers="name first">Apples</td>
                <td headers="color first">Green</td>
                <td headers="sizeAndVote">Medium</td>
                <td headers="sizeAndVote">500</td>
            </tr>
            <tr>
                <th id="second" headers="rank">2nd Favourite</th>
                <td headers="name second">Oranges</td>
                <td headers="color second">Orange</td>
                <td headers="sizeAndVote second">Large</td>
                <td headers="sizeAndVote second">450</td>
            </tr>
            <tr>
                <th id="third" headers="rank">3nd Favourite</th>
                <td headers="name third">Pomegranate</td>
                <td headers="color SizeAndVote" colspan="2" rowspan="2">
                    Pomegranates and cherries can both come in a range of colors
                </td>
                <td headers="sizeAndVote third">203</td>
            </tr>
            <tr>
                <th id="forth" headers="rank" rowspan="2">Joint 4th:</th>
                <td headers="name forth">Cherries</td>
                <td rowspan="2">75</td>
            </tr>
            <tr>
                <td headers="name">Pineapple</td>
                <td headers="color">Brown</td>
                <td headers="sizeAndVote">Very Large</td>
            </tr>
        </tbody>
    </table>
</body>

</html>

```




### 处理列 —— colgroup、col

colgroup元素，该属性用来对表格中的列应用样式。

table.css 文件

```css
	thead th,
	tfoot th {
	    text-align: left;
	    background: grey;
	    color: white;
	}

	tbody th {
	    text-align: right;
	    background: lightgrey;
	    color: grey;
	}

	[colspan],
	[rowspan] {
	    font-weight: bold;
	    border: medium solid black;
	}

	thead [colspan],
	tfoot [colspan] {
	    text-align: center;
	}

	caption {
	    font-weight: bold;
	    font-size: large;
	    margin-bottom: 5px;
	}

	#colgroup1 {
	    background-color: red;
	}

	#colgroup2 {
	    background-color: green;
	    font-size: small;
	}
```

index.html 文件

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Example</title>
    <meta name="author" content="Andy">
    <meta name="description" content="A simple example of aside">
    <link rel="shortcut icon" type="image/x-icon" href="favico.ico">
    <link rel="stylesheet" href="table.css">
</head>

<body>
    <table>
        <!--表格标题-->
        <caption>Resutl of the 2011 Fruit Survey</caption>
        <!--对列进行处理,colgroup元素代表一组列，span元素表示colgroup元素负责的列数。-->
        <!--第一个colgroup负责表格中的前三列，另一个clogroup负责剩余两列。id属性值为选择器定义CSS样式。-->
        <colgroup id="colgroup1" span="3" />
        <colgroup id="colgroup2" span="2" />

        <!--表头-->
        <thead>
            <tr>
                <th>Rank</th>
                <th>Name</th>
                <th>Color</th>
                <th colspan="2">Size & Votes</th>
            </tr>
        </thead>
        <!--构成表格主体的全体行-->
        <tbody>
            <tr>
                <th>Favourite:</th>
                <td>Apples</td>
                <td>Green</td>
                <td>Medium</td>
                <td>500</td>
            </tr>
            <tr>
                <th>2th Favourite:</th>
                <td>Oranges</td>
                <td>Orange</td>
                <td>Large</td>
                <td>450</td>
            </tr>
            <tr>
                <th>3th Favourite:</th>
                <td>Pomegranates</td>
                <td colspan="2" rowspan="2">Pomegranates and cherries can both come in a range of colors and size.</td>
                <td>203</td>
            </tr>
            <tr>
                <th rowspan="2">Joint 4th</th>
                <td>cherries</td>
                <td rowspan="2">75</td>
            </tr>
            <tr>
                <td>Poneapple</td>
                <td>Brown</td>
                <td>Very Lareg</td>
            </tr>
        </tbody>
        <!--表脚-->
        <tfoot>
            <tr>
                <th colspan="5">& copy; 2011 Adam Freeman Fruit Data Enterprises</th>
            </tr>
        </tfoot>
    </table>
</body>

</html>
```


![](https://upload-images.jianshu.io/upload_images/2648731-ea4c9d6b82b4c438.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


* 应用到<colgroup>元素上的样式在具体程度上低于直接应用到tr、td和th元素上的样式。
* 不规则单元格被记入其起始列。
* <colgroup>元素的影响范围覆盖了列中所有的单元格。




#### 表示个别的列 <col>

<col> 元素既能对一组列应用样式，也能对该组中个别的列应用样式。

table.css 文件

```css
	thead th,
	tfoot th {
	    text-align: left;
	    background: grey;
	    color: white;
	}

	tbody th {
	    text-align: right;
	    background: lightgrey;
	    color: grey;
	}

	[colspan],
	[rowspan] {
	    font-weight: bold;
	    border: medium solid black;
	}

	thead [colspan],
	tfoot [colspan] {
	    text-align: center;
	}

	caption {
	    font-weight: bold;
	    font-size: large;
	    margin-bottom: 5px;
	}

	#colgroup1 {
	    background-color: red;
	}

	#col3 {
	    background-color: green;
	    font-size: small;
	}

```

index.html 文件

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>Example</title>
    <meta name="author" content="Andy">
    <meta name="description" content="A simple example of aside">
    <link rel="shortcut icon" type="image/x-icon" href="favico.ico">
    <link rel="stylesheet" href="table.css">
</head>

<body>
    <table>
        <!--表格标题-->
        <caption>Resutl of the 2011 Fruit Survey</caption>
        <!--对列进行处理,colgroup元素代表一组列，span元素表示colgroup元素负责的列数。-->
        <!--第一个colgroup负责表格中的前三列，另一个clogroup负责剩余两列。id属性值为选择器定义CSS样式。-->
        <colgroup id="colgroup1">
            <!--col中的span属性，让一个col元素代表几列，不用span属性的col元素只代表一列。-->
            <col id="col1And2" span="2" />
            <col id="col3" />
        </colgroup>
        <colgroup id="colgroup2" span="2"></colgroup>

        <!--表头-->
        <thead>
            <tr>
                <th>Rank</th>
                <th>Name</th>
                <th>Color</th>
                <th colspan="2">Size & Votes</th>
            </tr>
        </thead>
        <!--构成表格主体的全体行-->
        <tbody>
            <tr>
                <th>Favourite:</th>
                <td>Apples</td>
                <td>Green</td>
                <td>Medium</td>
                <td>500</td>
            </tr>
            <tr>
                <th>2th Favourite:</th>
                <td>Oranges</td>
                <td>Orange</td>
                <td>Large</td>
                <td>450</td>
            </tr>
            <tr>
                <th>3th Favourite:</th>
                <td>Pomegranates</td>
                <td colspan="2" rowspan="2">Pomegranates and cherries can both come in a range of colors and size.</td>
                <td>203</td>
            </tr>
            <tr>
                <th rowspan="2">Joint 4th</th>
                <td>cherries</td>
                <td rowspan="2">75</td>
            </tr>
            <tr>
                <td>Poneapple</td>
                <td>Brown</td>
                <td>Very Lareg</td>
            </tr>
        </tbody>
        <!--表脚-->
        <tfoot>
            <tr>
                <th colspan="5">& copy; 2011 Adam Freeman Fruit Data Enterprises</th>
            </tr>
        </tfoot>
    </table>
</body>

</html>

```

![1.6.png](https://upload-images.jianshu.io/upload_images/2648731-208529122f263e5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)








### 设置表格边框——border属性

border属性告诉浏览器这个表格是用来表示表格式数据而不是用来布置其他元素的。border属性的值必须设置为1或空字符串（“”）。

```html
<body>
	<table border="1">
		<!--...-->
	</table>
</body>
```



### Head First HTML 示例

源码：http://www.headfirstlabs.com/books/hfhtml/chapter13/completedjournal/journal.html

```html
<!--
    表格提供了一种在HTML中定义表格式数据的方法。
    表格由行（tr）中的数据单元（td）组成。
    表格中列的个数，取决于一行中数据单元格（td）的个数。
    表格的作用不在于外观，外观是CSS的工作。
    td：border|padding|单元格内容|padding|border|border-spacing|border|padding|单元格内容|padding...
    border-collapse:collapse; // 消除边框间距
-->
<!--summary属性，摘要，增加表格的可读性，使屏幕读取器更好的读取用户所描述的表格。-->
<table summary="This table holds data about the
       cities I visited on my travels. I've included the date
       I was in each city, the temperature when I was there,
       and altitude and population of each city. I've also
       included a rating of the diners where I had lunch, on a
       scale from 1 to 5.">
    <!-- caption，表格标题-->
    <caption>
        The cities I visited on my Segway'n USA travels
    </caption>
    <tr>
        <th>City</th>
        <th>Date</th>
        <th>Temperature</th>
        <th>Altitude</th>
        <th>Population</th>
        <th>Diner Rating</th>
    </tr>
    <tr>
        <td>Walla Walla, WA</td>
        <td class="center">June 15th</td>
        <td class="center">75</td>
        <td class="right">1,204 ft</td>
        <td class="right">29,686</td>
        <td class="center">4/5</td>
    </tr>
    <tr class="cellcolor">
        <td>Magic City, ID</td>
        <td class="center">June 25th</td>
        <td class="center">74</td>
        <td class="right">5,312 ft</td>
        <td class="right">50</td>
        <td class="center">3/5</td>
    </tr>
    <tr>
        <td>Bountiful, UT</td>
        <td class="center">July 10th</td>
        <td class="center">91</td>
        <td class="right">4,226 ft</td>
        <td class="right">41,173</td>
        <td class="center">4/5</td>
    </tr>
    <tr class="cellcolor">
        <td>Last Chance, CO</td>
        <td class="center">July 23rd</td>
        <td class="center">102</td>
        <td class="right">4,780 ft</td>
        <td class="right">265</td>
        <td class="center">3/5</td>
    </tr>
    <tr>
        <td rowspan="2">Truth or Consequences, NM</td>
        <td class="center">August 9th</td>
        <td class="center">93</td>
        <td rowspan="2" class="right">4,242 ft</td>
        <td rowspan="2" class="right">7,289</td>
        <td class="center">5/5</td>
    </tr>
    <tr>
        <td class="center">August 27th</td>
        <td class="center">98</td>
        <td class="center">
            <table>
                <tr>
                    <th>Tess</th>
                    <td>5/5</td>
                </tr>
                <tr>
                    <th>Tony</th>
                    <td>4/5</td>
                </tr>
            </table>
        </td>
    </tr>
    <tr class="cellcolor">
        <td>Why, AZ</td>
        <td class="center">August 18th</td>
        <td class="center">104</td>
        <td class="right">860 ft</td>
        <td class="right">480</td>
        <td class="center">3/5</td>
    </tr>
</table>
```

css示例：

```css
table {
  margin-left:      20px;
  margin-right:     20px;
  border:           thin solid black;
  caption-side:     bottom;   // 将标题移动到表格的底部
  border-collapse:  collapse; // 消除边框之间的距离
}

td, th {
  border:           thin dotted gray;
  padding:          5px; // 单元格补白，单元格内容与边框之间的空隙
}

th {
  background-color: #cc6600;
}

caption {
  font-style:       italic; // 字体样式为斜体
  padding-top:      8px;    // 标题顶部添加补白
}

.center {
  text-align:       center;
}

.right {
  text-align:       right;
}

.cellcolor {
  background-color: #fcba7a;
}

table table th {
  background-color: white;
}

li {
  list-style-image: url(images/backpack.gif);
  padding-top:      5px;
  margin-left:      10px;
}
```



### 其他表格相关的CSS属性

| 属性            | 说明                         | 值                |
| --------------- | ---------------------------- | ----------------- |
| border-collapse | 设置相邻单元格的边框处理样式 | collapse/separate |
| border-spacing  | 设置相邻单元格边框的间距     | 1～2个长度值      |
| caption-side    | 设置表格标题的位置           | top/bottom        |
| empty-cells     | 设置空单元格是否显示边框     | hide/show         |
| table-layout    | 指定表格的布局样式           | auto/fixed        |


