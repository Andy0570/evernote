* 文档模型里任何代表某个元素的对象都至少能支持HTMLElement功能，其中一些还支持额外的功能。

> 理解：使用DOM可以读取或者设置HTML文档元素、设置CSS样式、配合JavaScript实现相关逻辑功能。

### DOM元素速览表

## 

### Document的成员

使用方式：`document.方法名`

![Document对象](http://upload-images.jianshu.io/upload_images/2648731-9fb4b6da2feb99b3..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Document对象2](http://upload-images.jianshu.io/upload_images/2648731-f1c13f89c4a0ef98..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



示例代码：

```html
<!DOCTYPE html>
<html>

<head>
    <title>Example</title>
    <meta charset="utf-8">
    <meta name="author" content="Hu QiLin">
    <meta name="description" content="使用Document对象">
    <link rel="shortcut icon" type="image/x-icon" href="favicon.icn">
    <link rel="stylesheet" href="journal.css">
</head>

<body>
    <p id="fruittext">
        There are lots of different kinds of fruit - there are over 500 varieties of <span id="banana">banana</span> alone. By the time we add the countless types of apples, oranges, and other well-known fruit, we are faced with thousands of choices.
    </p>
    <p id="apples">
        One of the most interesting aspects of fruit is the variety available in each country. I live near London, in an area which is known for its apples.
    </p>
    <script>
        /* <pre>元素表示其内容中的原有格式应被保留。 */
        /* document.URL:读取当前文档的URL*/
        document.writeln("<pre>URL: " + document.URL);

        /* getElementsByTagName：选取属于某一给定标签类型的所有元素 */
        var elems = document.getElementsByTagName("p");
        for (var i = 0; i < elems.length; i++) {
            document.writeln("Element ID: " + elems[i].id);
            
            /* 设置CSS样式：border和padding属性 */
            elems[i].style.border = "medium double black";
            elems[i].style.padding = "4px";
        }
        document.write("</pre>");

    </script>
</body>

</html>
```



使用Dccument元素获取文档的基本信息

```html
<!DOCTYPE html>
<html>

<head>
    <title>Example</title>
    <meta charset="utf-8">
    <meta name="author" content="Hu QiLin">
    <meta name="description" content="使用Document对象">
</head>

<body>

    <script>
        document.writeln("<pre>");

        document.writeln("characterSet: " + document.characterSet);
        document.writeln("charset: " + document.charset);
        document.writeln("compatMode: " + document.compatMode); /* 怪异模式：CSS1compat / BackCompat：HTML文档中含有非标准功能 */
        document.writeln("defaultCharset: " + document.defaultCharset);
        document.writeln("dir: " + document.dir);
        document.writeln("domain: " + document.domain);
        document.writeln("lastModified: " + document.lastModified);
        document.writeln("referrer: " + document.referrer);
        document.writeln("title: " + document.title);

        document.write("</pre>");
        
        /*
        characterSet: UTF-8
        charset: UTF-8
        compatMode: BackCompat
        defaultCharset: undefined
        dir: 
        domain: 127.0.0.1
        lastModified: 03/12/2018 14:09:26
        referrer: http://127.0.0.1:61374/def-guide-to-html5-master/Definitive%20Guide%20HTML5/Chapter%2026/listing-02.html
        title: 
        */
    </script>
</body>

</html>
```



### Location对象

![Location对象](http://upload-images.jianshu.io/upload_images/2648731-e4c23537a60b71db..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



使用location属性获取文档信息：

```html
<script>
    document.writeln("<pre>");

    document.writeln("protocol: " + document.location.protocol); /* http */
    document.writeln("host: " + document.location.host); /* 127.0.0.1:61374 */
    document.writeln("hostname: " + document.location.hostname); /* 127.0.0.1 */
    document.writeln("port: " + document.location.port); /* 61374 */
    document.writeln("pathname: " + document.location.pathname); /* /index.htm */
    document.writeln("search: " + document.location.search); /* 返回URL中查询字符串部分，如： ？query=apples */ 
    document.writeln("hash: " + document.location.hash); /* hash属性返回的是URL片段 */

    document.write("</pre>");

</script>
```



```html
<!DOCTYPE HTML>
<html>
    <head>
        <title>Example</title>
        <meta name="author" content="Adam Freeman"/>
        <meta name="description" content="A simple example"/>
    </head>
    <body>
        <p>
            There are lots of different kinds of fruit - there are over 500 varieties
            of banana alone. By the time we add the countless types of apples, oranges,
            and other well-known fruit, we are faced with thousands of choices. 
        </p>
        <button id="pressme">Press Me</button>
        <p>
            One of the most interesting aspects of fruit is the variety available in
            each country. I live near London, in an area which is known for
            its apples.

        </p>
        <img id="banana" src="banana-small.png" alt="small banana"/>
        <script>
            /* 用getElementById方法找到按钮，再设置按钮点击事件。 */
            document.getElementById("pressme").onclick = function() {
                /* 导航到id值匹配hash值的元素上 */
                document.location.hash = "banana";
            }
        </script>
    </body>
</html>
```







### Window 对象

![Window对象](http://upload-images.jianshu.io/upload_images/2648731-2c58431912c4ddc0..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







### History 对象

![History对象](http://upload-images.jianshu.io/upload_images/2648731-eb8470649eb37d07..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### Screen 对象

![Screen对象](http://upload-images.jianshu.io/upload_images/2648731-375fc1e064b84913..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





### HTMLElement的成员



![HTMLElement对象1](http://upload-images.jianshu.io/upload_images/2648731-1fe1f73b922c3bcc..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![HTMLElement对象2](http://upload-images.jianshu.io/upload_images/2648731-1a8ea799ba0e5fd2..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### Text 对象



![Text对象](http://upload-images.jianshu.io/upload_images/2648731-e10cac1aa5671d88..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### DOM里的CSS属性



![DOM里的CSS属性1](http://upload-images.jianshu.io/upload_images/2648731-4231748fc1353169..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![DOM里的CSS属性2](http://upload-images.jianshu.io/upload_images/2648731-497f8ab143d4664c..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![DOM里的CSS属性3](http://upload-images.jianshu.io/upload_images/2648731-3b1641a2d7b4d04d..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







### DOM 中的事件



[图片上传失败...(image-d11f11-1521387685311)]



![DOM中的事件2](http://upload-images.jianshu.io/upload_images/2648731-0f320d89c81d4d69..jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)










