### hgroup 元素 - 隐藏子标题

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		h1, h2, h3 {background: grey; color: white;}

		hgroup > h1 {margin-bottom: 0px;}

		hgroup > h2 {background: grey; color: white; font-size: 1em; margin-top: 0px}
	</style>
</head>
<body>
	<!--hgroup元素可以用来将几个标题元素作为一个整体处理-->
	<hgroup>
		<h1>我是标题一</h1>
		<h2>我是标题二</h2>
	</hgroup>
	我喜欢苹果和橘子。
	<h2>外部的标题二</h2>
	我也喜欢苹果和橘子。
	<h3>更多信息</h3>
	待到山花烂漫时，它在丛中笑。

</body>
</html>
```
作者的意图：通过缩小hgroup元素中两个标题元素的间距将二者拉拢在一起，并且为二者设置同样的背景色，可以在外观上明确揭示二者的关系。
![](http://upload-images.jianshu.io/upload_images/2648731-5189cde6d1cf3549.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


### section 元素 - 表示文档中的一节
<section>元素通常包含一个或多个段落及一个标题，不过标题并不是必须的。

使用 section 元素后，浏览器就会负责理顺标题元素的层次关系，让作者从确定和管理各个标题元素的正确次序的差事中解脱出来。

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		h1, h2, h3 {background: grey; color: white;}

		hgroup > h1 {margin-bottom: 0px;}

		hgroup > h2 {background: grey; color: white; font-size: 1em; margin-top: 0px}
	</style>
</head>
<body>


	<!--section 元素表示文档中的一节，section元素用来包含的是那种应该列入文档大纲或目录中的内容-->
	<section>
		<hgroup>
			<h1>我是标题一</h1>
			<h2>我是标题二</h2>
		</hgroup>
		我喜欢苹果和橘子。
		<h2>外部的标题二</h2>
		我也喜欢苹果和橘子。
		<h3>更多信息</h3>
		待到山花烂漫时，它在丛中笑。
		<section>
					<hgroup>
			<h1>我是标题一</h1>
			<h2>我是标题二</h2>
		</hgroup>
		我喜欢苹果和橘子。
		<h2>外部的标题二</h2>
		我也喜欢苹果和橘子。
		<h3>更多信息</h3>
		待到山花烂漫时，它在丛中笑。
		<section>
					<hgroup>
			<h1>我是标题一</h1>
			<h2>我是标题二</h2>
		</hgroup>
		我喜欢苹果和橘子。
		<h2>外部的标题二</h2>
		我也喜欢苹果和橘子。
		<h3>更多信息</h3>
		待到山花烂漫时，它在丛中笑。
		</section>
		</section>
	</section>
</body>
</html>
```

### hearder 和 footer  - 添加首部和尾部

* <header>元素表示一节的首部，通常包含刊头或徽标，一个标题元素或一个<hgroup>元素，还可以包含该节的导航<nav>元素。
* <footer>元素表示一节的尾部，通常包含该节的总结信息、作者介绍、版权信息、相关链接、免责声明等内容。

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		h1, h2, h3 {background: grey; color: white;}

		hgroup > h1 {margin-bottom: 0px;}

		hgroup > h2 {background: grey; color: white; font-size: 1em; margin-top: 0px}

		body > header *, footer > * {background: transparent; color: black;}
		body > section, body > section > section,
		body > section > section > section {margin-left: 10px;}

		body > header, body > footer {
			border: medium solid black; padding-left: 5px; margin: 10px 0 10px 0;
		}
	</style>
</head>
<body>

	<!--header 元素表示一节的首部，包括刊头、徽标等-->

	<!--整个文档的首部-->
	<header>
		<hgroup>
			<h1>我是标题一</h1>
			<h2>我是标题二</h2>
		</hgroup>
	</header>

	<!--section 元素表示文档中的一节，section元素用来包含的是那种应该列入文档大纲或目录中的内容-->
	<section>
		<!--某一节的首部-->
		<header>
			<hgroup>
				<h1>我是标题一</h1>
				<h2>我是标题二</h2>
		    </hgroup>
		</header>
		我喜欢苹果和橘子。
		<h2>外部的标题二</h2>
		我也喜欢苹果和橘子。
		<h3>更多信息</h3>
		待到山花烂漫时，它在丛中笑。
	</section>

	<!--footer表示一节的尾部，通常包含该节的总结信息，作者介绍、版权信息、相关链接、徽标、免责声明等-->
	<footer id="mainFooter">
		&#169;2011, Adam Freeman. <a href="http://apress.com">Visit Apress</a>
	</footer>
</body>
</html>
```
![](http://upload-images.jianshu.io/upload_images/2648731-a384653c78c6ed1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### nav - 添加导航区域
```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<style type="text/css">
		h1, h2, h3 {background: grey; color: white;}

		hgroup > h1 {margin-bottom: 0px;}

		hgroup > h2 {background: grey; color: white; font-size: 1em; margin-top: 0px}

		body > header *, footer > * {background: transparent; color: black;}
		body > section, body > section > section,
		body > section > section > section {margin-left: 10px;}

		body > header, body > footer {
			border: medium solid black; padding-left: 5px; margin: 10px 0 10px 0;
		}

		body > nav {text-align: center; padding: 2px; border: dashed thin black;}
		body > nav > a {padding: 2px; color: black}
	</style>
</head>
<body>
	<header>
		<hgroup>
			<h1>我是标题一</h1>
			<h2>我是标题二</h2>
		</hgroup>

		<!--nav表示文档中的一个区域，它包含着到其他页面或同一页面的其他部分的链接-->
		<!--nav元素的目的是规划出文档的主要导航区域-->
		<nav>
			<h1>Contents</h1>
			<ul>
				<li><a href="#fruitsilike">Fruits I Like</a></li>
			</ul>
			<li><a href="#activitiesilike">Activities I Like</a></li>
			<ul>
				<li><a href="#tritypes">Kinds of Triathlon</a></li>
				<li><a href="#mytri">The kind of triathlon I am aiming</a></li>
			</ul>
		</nav>
	</header>

	<section>
		<!--某一节的首部-->
		<header>
			<hgroup>
				<h1 id="fruitsilike">Fruits I Like</h1>
				<h2>How I learned to love Citrus</h2>
		    </hgroup>
		</header>
		我喜欢苹果和橘子。
		<h2>外部的标题二</h2>
		我也喜欢苹果和橘子。
		<h3>更多信息</h3>
		待到山花烂漫时，它在丛中笑。
	</section>

	<footer id="mainFooter">
		&#169;2011, Adam Freeman. <a href="http://apress.com">Visit Apress</a>
	</footer>
</body>
</html>
```

![](http://upload-images.jianshu.io/upload_images/2648731-9daa38f3f14a85e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


### article - 文章内容
article 元素代表HTML文档中一段独立成篇的内容。从理论上讲，可以独立于页面其余内容发布或使用。


### aside - 生成附注栏
类似书籍或杂志中的侧栏，包含背景内容、相关链接等。
```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Andy">
	<meta name="description" content="A simple example of aside">
	<link rel="shortcut icon" type="image/x-icon" href="favico.ico">

	<!--文档内嵌CSS样式-->
	<style type="text/css">
		h1, h2, h3, article > footer {background: grey; color: white;}
		hgroup > h1 {margin-bottom: 0; margin-top: 0;}
		hgroup > h2 {background: grey; color: white; font-size: 1em; margin-top: 0px; margin-bottom: 2px;}

		body > header *, body > footer * {background: transparent; color: black;}

		article {border: thin black solid; padding: 10px; margin-bottom: 5px;}
		article > footer {padding: 5px; margin: 5px; text-align: center;}
		article > footer > nav > a {color: white}

		body > article > section, 
		body > article > section > section {margin-left: 10px;}
		body > header,
		body > footer {
			border: medium solid black; padding-left: 5px; margin: 10px 0 10px 0;
		}
		body > nav {text-align: center; padding: 2px; border: dashed thin black;}
		body > nav > a {padding: 2px; color: black;}

		aside { width: 40%; background: white; float: right; border: thick solid black; margin-left: 5px;
		}
		aside > section {padding: 5px;}
		aside > h1 {background: white; color: black; text-align: center;}
	</style>
</head>
<body>

	<!--文档首部-->
	<header>
		<hgroup>
			<h1>Things I like</h1>
			<h2>by Adam Freeman</h2>
		</hgroup>

		<!--文档导航区域-->
		<nav>
			<h1>Contents</h1>
			<ul>
				<li><a href="#fruitsilike">Fruits I Like</a></li>
				<li><a href="#activitiesilike">Activities I Like</a></li>
			</ul>
		</nav>
	</header>

	<!--文档中一段独立成篇的内容-->
	<article>
		<!--文章内的首部-->
		<header>
			<hgroup>
				<h1 id="fruitsilike">Fruits I Like</h1>
				<h2>How I Learned to love Citrus</h2>
			</hgroup>
		</header>
		<!--附注栏-->
		<aside>
			<h1>Why Fruit is Healthy</h1>
			<section>
				Here are three reasons why everyone should eat more fruit:
				<ol>
					<li>Fruit contains lots of vitamins</li>
					<li>Fruit is a source of fibre</li>
					<li>Fruit contains few calories</li>
				</ol>
			</section>
		</aside>
		I like apples Android oran
		<!--文章章节-->
		<section>
			<h1 id="morefruit">Additional fruits</h1>
			I also like bananas, mangoes, cherries, apricots, plums,peaches Android gra
			<section>
				<h1>More information</h1>
				You can see other fruits I like <a href="fruitlist.html">here</a>>
			</section>
		</section>
			<!--文档的尾部信息-->
			<footer>
				<nav>
					More Information:<a href="http://fruit.org">Learn More About Fruit</a>
				</nav>
			</footer>
	</article>

	<!--文档中一段独立成篇的内容-->
	<article>
		<!--章节首部信息-->
		<header>
			<hgroup>
				<h1 id="activitiesilike">Activities I Like</h1>
				<h2>It hurts, but I keep doing it</h2>
			</hgroup>
		</header>
		<!--章节-->
		<section>
				<p>I like to swim, cycle and run. I am in training for my first taiathlon, but it is hard work.</p>
				<h1 id="triptypes">Kinds of Triathlon</h1>
				There are different kinds of triathlon - spring, Olympic and so on.
				<section>
					<h1 id="mytri">The kind of taiathlon I am aiming for</h1>
					I am aiming for Olympic, which consists of the following:
					<ol>
						<li>1.5Km swim</li>
						<li>40km cycle</li>
						<li>10km run</li>
					</ol>
				</section>
			</section>

			<!--章节尾部信息-->
			<footer>
				<nav>
				<a href="http://triathlon.org">Learn More About Triathlons</a>
				</nav>
			</footer>
	</article>

	<!--文档尾部信息-->
    <footer id="mainFooter">
    &#169;2011, Adam Freeman. <a href="http://apress.com">Visit Apress</a>
	</footer>

</body>
</html>
```
![](http://upload-images.jianshu.io/upload_images/2648731-22af236205dde896.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### adress - 提供联系信息
```html
<!--此例演示如何在 HTML 文件中写地址。-->
<address>
Written by <a href="mailto:webmaser@example.com">Andy</a>.<br />
Visite us at:<br />
Example.com<br />
Box 567, Disneyland<br />
USA
</address>
```
![地址](http://upload-images.jianshu.io/upload_images/2648731-ebca6aab1c0800ad.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### details - 生成详情区域
```html
<!--详情区域-->
<details>
	<summary>Kinds of Triathlon</summary>
	Therr are different kinds of triathlon - spring, Olympic and so on.
	I am aming of Olympic, which consists of the following:
	<ol>
		<li>1.5Km swim</li>
		<li>40km cycle</li>
		<li>10km run</li>
	</ol>
</details>
```

![](http://upload-images.jianshu.io/upload_images/2648731-a902abd2b2fe58af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)
