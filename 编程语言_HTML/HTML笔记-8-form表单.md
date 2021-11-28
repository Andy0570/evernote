### 表单

* form：表示HTML页面上的表单；

* input：输入文本框，用于收集用户输入的数据；

* button：表单中的按钮；

* method：get/pust

* action：发送表单数据到指定的服务器位置；

* enctype：表单数据的编码方式

  | enctype值                         | 说明         |
  | --------------------------------- | ------------ |
  | application/x-www-form-urlencoded | 默认值       |
  | multipart/form-data               | 用于上传文件 |
  | text/plain                        |              |

  

### form 表单

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>文档标题</title>
</head>

<body>

    <!--当用户单击确认按钮时，表单的内容会被传送到另一个文件。-->
    <!--表单的 action 属性定义了目的文件的文件名。由 action 属性定义的这个文件通常会对接收到的输入数据进行相关处理。-->
    <form method="post" action="demo-form.php">
        姓名： <input type="text" name="name"><br /> 用户名：
        <input type="text" name="user"><br /> 密码： <input type="password" name="password"><br /> 单选框：

        <br />
        <input type="radio" name="sex" value="male">男<br />
        <input type="radio" name="sex" value="female">女<br /> 复选框：

        <br />
        <input type="checkbox" name="vehicle" value="Bike"> I have a Bike<br />
        <input type="checkbox" name="vehicle" value="Car"> I have a Car<br /> 下拉按钮
        <select name="cars">
            <option value="volvo">Volvo</option>
            <option value="saab">Saab</option>
            <option value="fiat" selected>Fiat</option>
            <option value="audi">Audi</option>
        </select><br /> 文本输入域

        <br />
        <textarea rows="10" cols="30">
            文本输入域占位符内容...
        </textarea><br /> 提交按钮

        <br />
        <input type="submit" value="submit">
        <input type="reset" value="reset">
        <p>点击 “submit” 按钮，表单数据将被提交到服务器上的“demo-form.php”文件中</p>

        按钮
        <input type="button" name="我是按钮" value="按钮">
    </form>


    <form>
        <fieldset>
            <legend>个人信息</legend>
            Name:<input type="text" size="30"><br /> Email:
            <input type="text" size="30"><br /> Date of Birth:<input type="text" size="10">
        </fieldset>
    </form>
</body>

</html>
```

![HTML表单](http://upload-images.jianshu.io/upload_images/2648731-872dd72e020e4065.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)



### form 表单的其他属性

```html
<!DOCTYPE html>
<html>

<head>
    <title>Example</title>
    <meta charset="utf-8">
    <meta name="author" content="Andy">
    <meta name="description" content="A simple example of form">
    <link rel="shortcut icon" type="image/x-icon" href="favico.ico">
</head>

<body>
    <!--form 元素的 autocomplete 属性默认为 off-->
    <!--target 属性用于处理浏览器反馈信息-->

    <!-- name & id 属性-->
    <!--name 属性用来为表单设置一个独一无二的标识符，以便使用 DOM 时区分各个表单-->
    <!--id 属性多用于 CSS 选择器-->

    <form autocomplete="off" target="_blank" name="fruitvote" id="fruitvote" method="post" action="http://titan:8080/form">
        <!--fieldset,表示对表单元素进行编组-->
        <fieldset>
            <!--legend ，向用户提供 fieldset 分组内容的相关说明-->
            <!--legend 元素必须是 fieldset 元素的第一个子元素-->
            <legend>Enter Your Details</legend>
            <!--该 input 元素上启用 autocomplete 属性-->
            <!--autofocus ,让表单显示出来的时候即聚焦于某个 input 元素-->
            <!--disabled ,禁用input输入-->
            <!--将 label 的 for 属性值设置与 input 元素的 id 值相同，可关联两者，有助于屏幕阅读器和其他残障辅助技术对表单的处理-->
            <p><label for="fave">Fruit:<input autofocus autocomplete="on" name="fave" id="fave"></label></p>
            <p><label for="name">Name:<input name="name" id="name"></label></p>
        </fieldset>
        <!--fieldset 的 disabled 属性可以禁用多个input元素。-->
        <fieldset disabled>
            <legend>Vote For Your Three Favourite Fruits</legend>
            <p><label for="fave1">#1：<input id="fave1" name="fave1"></label></p>
            <p><label for="fave2">#2：<input id="fave2" name="fave2"></label></p>
            <p><label for="fave3">#3：<input id="fave3" name="fave3"></label></p>
        </fieldset>
        <!--提交按钮-->
        <button type="submit" formaction="http://titan:8080/form" formmethod="post">Submit Vote</button>
        <!--重置按钮-->
        <button type="reset">Reset</button>
        <!--普通按钮-->
        <button type="button">Do <strong>NOT</strong> press this button</button>
    </form>
</body>

</html>
```

![](https://upload-images.jianshu.io/upload_images/2648731-bb6cb4f506670fa7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### 使用表单外的元素

把表单内的元素放在表单外面，然后使用 input 元素的 `form` 属性关联到相应的表单。

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Andy">
	<meta name="description" content="A simple example of form">
	<link rel="shortcut icon" type="image/x-icon" href="favico.ico">
</head>
<body>
	<form id="voteform" method="post" action="http://titan:8080/form">
		<p>
			<label for="fave">Fruit: <input autofocus id="fave" name="fave"></label>
		</p>
	</form>
	<p>
		<!--将表单外的元素使用 form 属性关联到指定的表单 id-->
		<label for="name">Name: <input form="voteform" id="name" name="name"></label>
	</p>
</body>
</html>
```



### input 元素的 maxlength 和 size 属性

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Andy">
	<meta name="description" content="A simple example of form">
	<link rel="shortcut icon" type="image/x-icon" href="favico.ico">
</head>
<body>
	<form method="post" action="http://titan:8080/form">
		<p>
			<label for="name">
				<!--maxlength: 用户能够输入字符的最大数目。浏览器可以自行确定文本框宽度。-->
				Name: <input maxlength="10" id="name" name="name">
			</label>
		</p>
		<p>
			<!--size： 文本框能够显示的字符数目。不会限制用户输入字符数-->
			<label for="city">
				City: <input size="10" id="city" name="city">
			</label>
		</p>
		<p>
			<label for="fave">
				Ff=ruit: <input maxlength="10" size="10" name="fave">
			</label>
		</p>
	</form>
</body>
</html>
```



### input 输入文本框占位符 placeholder 属性

```html
<label for="name">
    <!--设置占位符-->
    Name: <input placeholder="Enter Your Name" id="name" name="name">
</label>
```



### input 输入文本框初始值

```html
<label for="city">
    <!--设置初始值-->
    City: <input value="Shanghai" id="city" name="city">
</label>
```



### input 输入文本框的 list 属性

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Andy">
	<meta name="description" content="A simple example of form">
	<link rel="shortcut icon" type="image/x-icon" href="favico.ico">
</head>
<body>
	<form method="post" action="http://titan:8080/form">
		<p>
			<label for="fave">
				<!--使用数据列表-->
				Fruit: <input list="fruitlist" id="fave" name="fave">
			</label>
		</p>
		<button type="submit">Subimt</button>
	</form>

	<datalist id="fruitlist">
		<option value="Apples" label="Lovely Apples"></option>
		<option value="Oranges">Refreshing Oranges</option>
		<option value="Cherries"></option>
	</datalist>
</body>
</html>
```



### input 输入文本框输入密码

```html
<label for="password">
    <!--输入密码，设置type类型-->
    Password: <input type="password" id="password" name="password">
</label>
```



### input 元素生成按钮

```html
<input type="submit" value="Submit Vote">
<input type="reset" value="Reset Form">
<input type="button" value="My Button">
```



### 各种类型的 input 元素

```html
<!DOCTYPE html>
<html>

<head>
    <title>Example</title>
    <meta charset="utf-8">
    <meta name="author" content="Andy">
    <meta name="description" content="A simple example of form">
    <link rel="shortcut icon" type="image/x-icon" href="favico.ico">
</head>

<body>
    <form method="post" action="http://titan:8080/form">
        <p>
            <!--密码-->
            <label for="password">
				Password: <input type="password" id="password" name="password">
			</label>
        </p>
        <p>
            <!--步进器-->
            <label for="number">
				Age: <input type="number" step="1" min="0" max="100" value="1" id="price" name="price">
			</label>
        </p>
        <p>
            <!--进度条-->
            <label for="range">
				Range: <input type="range" step="1" min="0" max="100" value="1" id="price" name="price">
			</label>
        </p>
        <p>
            <!--复选框-->
            <label for="checkbox">
                <!--
                    checked:默认勾选。
                    required:提交表单时必须勾选，否则无法通过验证。
                    value：勾选时提交给服务器的值。默认为on。
                    只有处于勾选状态的复选框的数据值会发送给服务器。如果没有勾选，该条数据（key=value）不会发送给服务器。
                -->
				<input type="checkbox" id="veggie" name="veggie" checked required value="on">
				我同意用户使用协议与***条款。
			</label>
        </p>
        <p>
            <!--单选按钮-->
            <fieldset>
                <legend>Vote for your favourite fruit</legend>
                <label for="apples">
					<input type="radio" checked value="Apples" id="apples" name="fave">
					Apples
				</label>
                <label for="oranges">
					<input type="radio" value="Oranges" id="oranges" name="fave">
					Oranges
				</label>
                <label for="cherries">
					<input type="radio" value="Cherries" id="cherries" name="fave">
					Cherries
				</label>
            </fieldset>
        </p>
        <p>
            <!--使用emaill型-->
            <label for="email">
				Email: <input type="email" placeholder="user@domain.com" id="email" name="email">
			</label>
        </p>
        <p>
            <!--telphone-->
            <label for="tel">
				Tel: <input type="tel" placeholder="(xxx)-xxx-xxxx" id="tel" name="tel">
			</label>
        </p>
        <p>
            <!--url-->
            <label for="url">
				Your homepage:<input type="url" id="url" name="url">
			</label>
        </p>
        <p>
            <!--日期-->
            <label for="lastbuy">
				When did you las buy:
				<input type="date" id="lastbuy" name="lastbuy">
			</label>
        </p>
        <p>
            <!--颜色值-->
            <label for="color">
				Color: <input type="color" id="color" name="color">
			</label>
        </p>
        <p>
            <!--search 搜索用词-->
            <label for="search">
				Search: <input type="search" id="search" name="search">
			</label>
        </p>
        <p>
            <!--hidden 隐藏的数据项,不让用户看到，但是表单发送时又能作为主键上传到服务器-->
            <input type="hedden" name="recordID" value="1234">
        </p>
        <p>
            <!--图像按钮-->
            <input type="image" src="accept.png" name="submit">
        </p>
        <!--上传文件-->
        <input type="file" name="filedata">
        <button type="submit">Subimt</button>
    </form>
</body>

</html>

```



### select 选择器元素

```html
<!--使用select和option元素-->
<!--size 属性可以让select元素显示多个选项-->
<!--multiple 属性可以让用户一次选择多个选项-->
<label for="fave">
    Favourite Fruit:
    <select id="fave" name="fave" size="5" multiple>
        <!--optgroup 元素可以对option元素进行编组-->
        <optgroup label="Top Choices">
            <option value="apples" selected label="Apples">Apples</option>
            <option value="oranges" label="Oranges">Oranges</option>
        </optgroup>
        <optgroup label="Others">
            <option value="Cherries" label="cherries">Cherries</option>
            <option value="pears" label="Pears">Pears</option>
        </optgroup>
    </select>
</label>
```



### 输入多行文字

```html
<!--输入多行文字-->
<textarea cols="20" rows="5" wrap="hard" id="story" name="story">
    Tell us why this is your favourite fruit
</textarea>
```



### required 属性

代表用户必须提供一个值才能提交表单。

```html
<label for="password">
    <!--密码-->
    Password: <input type="password" required id="password" name="password">
</label>
```



### 使用正则表达式

```html
<!--使用正则表达式匹配-->
<label for="name">
    Name: <input type="text" id="name" name="name" pattern="^.* .*$*">
</label>
```



### 其他输入验证特性

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
	<form method="post" action="http://titan:8080/form">
		<input type="hidden" name="recordID" value="1234">
		<p>
			<label for="name">
				Name:
				<!--required 属性用来确保用户提供了一个值，否则无法提交表单--> 
				<!--pattern 正则表达式输入验证，确保输入值与指定模式匹配（以空格分隔的姓氏和名字）-->
				<input type="text" required id="name" name="name" pattern="^.* .*&"/>
			</label>
		</p>
		<p>
			<label for="password">
				Password：
				<input type="password" placeholder="Min 6 characters" id="password" name="password"/>
			</label>
		</p>
		<p>
			<label for="price">
				$ per unit in your area:
				<!--min 和 max 属性：确保输入值位于某个范围之内-->
				<input type="number" min="0" max="100" value="1" id="price" name="price"/>
			</label>
		</p>
		<p>
			<label for="email">
				Email:
				<!--将 pattern 属性与 email 型 input 元素结合使用可以进一步限制用户输入的值-->
				<!--requried 属性确保用户提供一个输入值-->
				<!--pattern 属性确保输入的电子邮箱输入指定的域-->
				<input type="email" placeholder="user@mydomain.com" required pattern=".*@mydomain.com$" id="email" name="email"/>
			</label>
		</p>
		<input type="submit" value="submit"/>
		<!--formnovalidate:禁用输入验证，不经验证就能提交表单-->
		<input type="submit" value="save" formnovalidate>
	</form>
</body>
</html>
```



### accesskey 属性
accesskey 作用：设定一个或几个用来选择页面上元素的快捷键。

```html
<!DOCTYPE html>
<html>
<head>
    <title>使用 accesskey 属性设定页面元素的快捷键</title>
</head>
<body>
    <form>
        Name:<input type="text" name="name" accesskey="n"/>
        <p/>
        Password:<input type="Password" name="password" accesskey="p"/>
        <p/>
        <input type="submit" value="="Log In" accesskey="s"/>
    </form>
</body>
</html>
```

效果就是，在 Mac 的 Safari 浏览器上同时按：**control+option+accesskey** 就可以将焦点跳转到指定的 inpput 输入框。




### tabindex 属性
作用：HTML页面上的键盘焦点可以通过按Tab键在各个元素之间进行切换。用`tabindex` 属性可以改变默认的转移顺序。

示例：
 ```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
</head>
<body>
	<form>
		<label>Name:<input type="text" name="name" tabindex="1"></label>
		<p></p>
		<label>City:<input type="text" name="city" tabindex="-1"></label>
		<p></p>Country:<input type="text" name="country" tabindex="2">
		<label></label>
		<p></p>
		<input type="submit" tabindex="3">
	</form>
</body>
</html>
 ```
注意按下Tab的切换顺序：1-2-3，忽视-1。
![tabindex](http://upload-images.jianshu.io/upload_images/2648731-0f05ed8cea0e39a7.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





### spellcheck 属性

作用：表明浏览器是否应该对元素的内容进行拼写检查。只有用在用户可以编辑的元素上时才有意义。

示例：
```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
</head>
<body>
	<textarea spellcheck="true">This is some misspelled text</textarea>
</body>
</html>
```
⚠️目前大多数浏览器中的拼写检查都会忽略 `lang` 属性。它们的拼写检查实际上基于用户所用操作系统中的语言设置或浏览器的语言设置。
![spellcheck](http://upload-images.jianshu.io/upload_images/2648731-c5dfe7e492b627a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### form表单的自动完成功能——autocomplete 属性

自动填充功能

```html
<!DOCTYPE html>
<html>
<head>
	<title>Example</title>
	<meta charset="utf-8">
	<meta name="author" content="Andy">
	<meta name="description" content="A simple example of form">
	<link rel="shortcut icon" type="image/x-icon" href="favico.ico">
</head>
<body>
	<!--form 元素的 autocomplete 属性默认为 off-->
	<form autocomplete="off" method="post" action="http://titan:8080/form">
		<!--该 input 元素上启用 autocomplete 属性-->
		<input autocomplete="on" name="fave">
		<input name="name">
		<button>Submit Vote</button>
	</form>
</body>
</html>
```
