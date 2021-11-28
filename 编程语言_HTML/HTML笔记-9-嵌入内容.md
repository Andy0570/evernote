### 嵌入内容

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Hu QiLin">
	<meta name="description" content="确保输入值是电子邮箱地址或URL">
	<link rel="shortcut icon" type="image/x-icon" href="favicon.icn">
</head>
<body>
	Here is a common form for representing the three activities in a triathlon.
	<p>
		<!--嵌入图像-->
		<!--src属性：指定图像URL-->
		<!--alt属性：img元素的备用内容-->
		<!--指定 width 和 height 能让浏览器在图像尚未载入时正确摆放网页里的各个元素-->
		<img src="triathlon.png" alt="Triathlon Image" width="200" height="67"/>
	</p>
	<p>
		<!--在超链接里嵌入图像-->
		<a href="otherpage.html">
			<!--ismap, 创建了一个服务器端分区响应图：在图像上点击的位置会附加到URL上-->
			<!--服务器端分区响应图，意味着服务器会根据用户在图像上点击区域的不同作出有差别的反应。-->
			<img src="triathlon.png" ismap alt="Triathlon Image" width="200" height="67"/>
		</a>
	</p>
	<p>
		<!--客户端分区响应图-->
		<!--usemap属性：将map元素与图像关联-->
		<img src="triathlon.png" usemap="#mymap" alt="Triathlon Image">
	</p>

	<map name="mymap">
		<area shape="rect" coords="3,5,68,62" href="swimpage.html" alt="Swimimg"/>
		<area shape="rect" coords="70,5,130,62" href="cyclepage.html" alt="Running"/>
		<area shape="default" href="otherpage.html" alt="default">
	</map>

	<!--点击此处的超链接，URL会在下方的 iframe 区域中被打开-->
	<a href="fruits.html" target="frame">Fruits i Like</a>

	<!--iframe元素：在现有的HTML文档中嵌入另一张文档-->
	<iframe name="myframe" width="300" height="300">
	</iframe>

	<!--embed元素：用于添加插件支持-->
    <embed src="http://www.youtube.com/v/qzA60hHca9s?version=3"
       type="application/x-shockwave-flash" width="560" height="349"
       allowfullscreen="true"/>

    <!--使用object嵌入插件内容-->
	<object width="500" height="349" 
		dada="http://www.youtube.com/v/qzA60hHca9s?version=3" 
		type="application/x-shockwave-flash">
		<!--param 元素，定义将要传递给插件的参数-->
		<param name="allowfullscreen" value="true"/>
		<!--指定备用内容，当指定内容不可用时显示-->
		<b>Sorry!</b>We can't display this content
	</object>

	<!--使用object元素嵌入图像-->
	<object data="triathlon.png" type="iamge/png">
	</object>

	<!--使用object元素创建客户端分区响应图-->
	<map name="mymap">
		<area shape="rect" coords="3,5,68,62" href="swimpage.html" alt="Swimimg"/>
		<area shape="rect" coords="70,5,130,62" href="cyclepage.html" alt="Running"/>
		<area shape="default" href="otherpage.html" alt="default">
	</map>

	<object data="triathlon.png" type="iamge/png" usemap="#mymap">
	</object>


	<!--将object元素作为浏览器上下文环境-->
	<!--点击此处的链接，在下方object所描述的区域显示内容-->
	<a href="activities.html" target="frame">Activities I Like</a>

	<object type="text/html" name="frame" width="300" height="100">
	</object>

</body>
</html>
```



### progress 元素

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Hu QiLin">
	<meta name="description" content="确保输入值是电子邮箱地址或URL">
	<link rel="shortcut icon" type="image/x-icon" href="favicon.icn">
</head>
<body>
	<progress id="myprogress" value="10" max="100"></progress>
	<p>
		<button type="button" value="30">30%</button>
		<button type="button" value="60">60%</button>
		<button type="button" value="90">90%</button>
	</p>

	<script type="text/javascript">
		var buttons = document.getElementsByTagName('BUTTON');
		var progress = document.getElementById('myprogress');
		for (var i = buttons.length - 1; i >= 0; i--) {
			buttons[i].onclick = function(e) {
				progress.value = e.target.value;
			}
		}
	</script>
</body>
</html>
```



### meter 元素

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Hu QiLin">
	<meta name="description" content="确保输入值是电子邮箱地址或URL">
	<link rel="shortcut icon" type="image/x-icon" href="favicon.icn">
</head>
<body>

	<!--meter 元素显示了某个范围内所有可能值中的一个。-->
	<!--低于 low 属性设置的值为过低值-->
	<!--高于 high 属性设置的值为过高值-->
	<!--optimum 属性设置的值为最佳值-->
	<meter id="mymeter" value="90" min="10" max="100" low="40" high="80" optimum="60"></meter>
	<p>
		<button type="button" value="30">30%</button>
		<button type="button" value="60">60%</button>
		<button type="button" value="90">90%</button>
	</p>

	<script type="text/javascript">
		var buttons = document.getElementsByTagName('BUTTON');
		var meter = document.getElementById('mymeter');
		for (var i = buttons.length - 1; i >= 0; i--) {
			buttons[i].onclick = function(e) {
				meter.value = e.target.value;
			}
		}
	</script>
</body>
</html>
```
