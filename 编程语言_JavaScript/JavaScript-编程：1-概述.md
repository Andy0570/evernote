# JavaScript 简介

> 所有可以用 JavaScript 编写的程序，最终都会出现 JavaScript 的版本。
> (Any application that can be written in JavaScript will eventually be written in JavaScript.) 
> —— Jeff Atwood

* 1995 年，Netscape 公司的布兰登·艾奇（Brendan Eich）在 Netscape Navigator 浏览器上首次设计实现了 JavaScript。
* JavaScript 诞生的目的是在客户端处理由服务端负责的**输入验证**操作。
* JavaScript 与 Java 半毛钱关系也没有。起初 JavaScript 项目的名字叫做 LiveScript，Netscape 公司为了蹭 Java 的热点，临时把 LiveScript 改名为了 JavaScript。
* 早期，JavaScript 是一种专为与网页交互而设计的脚本语言。现在，JavaScript 已经发展成为一门功能全面的编程语言。

一个完整的 JavaScript 实现由三个不同的部分组成：
![JavaScript 组成](https://upload-images.jianshu.io/upload_images/2648731-5d42197954ec47e1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


## ECMAScript

**ECMAScript 是脚本语言标准，而 JavaScript 实现了 ECMAScript。**

* ECMAScript（发音为“ek-ma-script”） 是欧洲计算机制造商协会（ECMA）制定的**脚本语言标准**，它用来标准化 JavaScript 语言的基本语法结构，它是 JavaScript 实现的基础。
* 由 ECMA-262 定义的 ECMAScript 与 Web 浏览器没有依赖关系，Web 浏览器只是 ECMAScript 实现可能的**宿主环境**之一。
* 不同 Web 浏览器对 ECMAScript 的兼容性不同。
* ES6（即 ECMAScript 2015 ）于 2015 年 6 月 17 日发布。


## DOM

* 文档对象模型（DOM）是针对 XML 但经过扩展用于 HTML 的应用程序编程接口（API）。
* DOM 把 HTML 页面映射为一个多层节点结构。
* W3C 制定 DOM 标准。
* 不同 Web 浏览器对 DOM 标准（DOM1、DOM2、DOM3）的支持情况。

DOM 主要包括两个部分：
* DOM 核心规定的是如何映射基于 XML 的文档结构，以便简化对文档中任意部分的访问和操作。
* DOM HTML 模块则在 DOM 核心的基础上加以扩展，添加了针对 HTML 的对象和方法。

## BOM

浏览器对象模型（BOM），支持访问和操作浏览器窗口和框架。BOM 的相关标准在 HTLML 5 中有描述。


# 在 HTML 中使用 JavaScript

把 JavaScript 插入到 HTML 页面中需要使用 &#60;script&#62; 元素。

## &#60;script&#62; 元素

使用 &#60;script&#62; 元素的两种方式：
1. 直接在页面中嵌入 JavaScript 代码；
2. 包含外部 JavaScript 文件。

### &#60;script&#62; 标签的位置
#### 传统方式

与解析嵌入式 JavaScript 代码一样， 在解析外部 JavaScript 文件（包括下载该文件）时，页面的处理也会暂时停止（HTML 页面的 UI 会被阻塞）。
```html
<!-- 第一行是 HTML 5 的文档模式声明 -->
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <!-- 必须等到全部 JavaScript 代码都被下载、解析和执行完成以后，才能开始呈现页面的内容 -->
  <script type="text/javascript" src="example1.js"></script>
  <script type="text/javascript" src="example2.js"></script>
</head>

<body>
  <!-- 页面内容 -->
</body>

</html>
```

> 注：`type` 属性是必选属性，用于设置脚本语言的内容类型，默认值为：`text/javascript`。

#### 推荐方式

将 `<script>` 标签放在 `<body>` 标签体末尾：
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>

<body>
  <!-- 这里放内容 -->
  <!-- 在解析包含的 JavaScript 代码之前，页面的内容将首先被完全呈现在浏览器中。 -->
  <script type="text/javascript" src="example1.js"></script>
  <script type="text/javascript" src="example2.js"></script>
</body>

</html>
```

> 💡
> 所有 &#60;script&#62; 元素会按照它们在页面中出现的**先后顺序依次被解析**。所以应该把 &#60;script&#62; 元素放在页面最后，&#60;/body&#62; 标签之前。

### 延迟脚本

```html
<script defer type="text/javascript" src="指向外部 JavaScript 文件的链接">
  /* 如果包含了 src 属性，此处就不应该再包含额外的 JavaScript 代码 */
  /* 即使包含了嵌入的代码，浏览器也会忽略嵌入代码 */
</script>
```

* `defer` 属性（HTML 5 引入）：延迟到文档完全被解析和显示之后再执行脚本。
* `defer` 属性只适用于外部脚本文件。
* 在现实当中，存在多个脚本时，延迟脚本并不一定会按照顺序执行，也不一定会在 `DOMContentLoaded` 事件触发前执行，因此最好只包含一个延迟脚本。（也就是说，当脚本之间互相存在依赖关系时，可能会产生问题）
* 在 XHTML 文档中，要把 `defer` 属性设置为 `defer="defer"`。

> 💡 把延迟执行的脚本放在页面底部仍然是最佳选择。 


### 异步脚本

```html
<script async type="text/javascript" src="指向外部 JavaScript 文件的链接">
</script>
```

* `async` 属性（HTML 5 引入）：异步执行脚本。
* `async` 属性只适用于外部脚本文件。
* 标记为 `async` 的脚本并不保证按照指定它们的先后顺序执行。
* 在 XHTML 文档中，要把 `async` 属性设置为 `async="async"`。


## CData 片段（该部分内容可忽略）

* 可扩展超文本标记语言，即 **XHTML**（Extensible HyperText Markup Language），是将 HTML 作为 XML 的应用而重新定义的一个标准。编写 XHTML 代码的规则要比编写 HTML 严格得多，而且直接影响能否在嵌入 JavaScript 代码时使用 &#60;script/&#62; 标签。
* CData 片段解决的是在 HTML 代码中能正确解析的内容无法被 XHTML 解析的问题。

```javascript
// CDATA 片段解决方法中出现特殊字符导致解析语法错误
// 很多浏览器不兼容 XHTML，可以把 CDATA 片段加注释解决。
<script type="text/javascript">
//<![CDATA[
    function compare(a, b) {
        if (a < b) {
            alert("A is less than B");
        }else if {
            alert("A is greater than B");
        }else {
            alert("A is equal to B");
        }
    }
//]]>
</script>
```

> 💡
> 将页面的 MIME 类型指定为 **“application/xhtml+xml”** 可以触发 XHTML 模式，但并不是所有的浏览器都支持以这种方式提供 XHTML 文档。



## &#60;noscript&#62; 元素

下列情况下会显示 &#60;noscript&#62; 中的内容：

1. 浏览器不支持 JavaScript 脚本；
2. 浏览器支持 JavaScript 脚本，但 JavaScript 脚本被禁用；

```html
<!DOCTYPE html>
<head>
  <title>Document</title>
</head>
<body>
  <noscript>
    <p>本页面需要浏览器支持（启用）JavaScript</p>
</noscript>
</body>
</html>
```

