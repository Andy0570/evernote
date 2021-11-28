![](http://upload-images.jianshu.io/upload_images/2648731-756487e3a357be96.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在HTML中定义脚本的方式：

1. 定义内嵌脚本；
2. 定义外部脚本；

```html
<script type="text/javascript">
    //...
</script>
```





### 定义和使用函数

定义一个函数myfunc，然后使用 myFunc() 方法调用。

💡JavaScript区分大小写。

```html
<!DOCTYPE HTML>
<html>
    <head>
        <title>Example</title>
    </head>
    <body>
        <script type="text/javascript">
            function myFunc() {
                document.writeln("This is a statment");
            }

            myFunc();
        </script>
    </body>
</html>
```

定义带参数的函数：

💡JavaScript 是一门弱类型的语言，所以定义函数的时候不必声明参数的数据类型。

不用明确声明变量的类型。可以随心所欲地用同一变量表示不同类型的值。

```html
<!DOCTYPE HTML>
<html>
    <head>
        <title>Example</title>
    </head>
    <body>
        <script type="text/javascript">
            function myFunc(name, weather) {
                document.writeln("Hello " + name + ".");
                document.writeln("It is " + weather + " today");
            }

            myFunc("Adam","Sunny");
        </script>
    </body>
</html>
```

定义会返回结果的函数：
不需要事先声明是否有返回值、或者返回值类型。

```html
<!DOCTYPE HTML>
<html>
    <head>
        <title>Example</title>
    </head>
    <body>
        <script type="text/javascript">
            function myFunc(name) {
                return ("Hello " + name + ".");
            };

            document.writeln(myFunc("Adam"));
        </script>
    </body>
</html
```



### 使用变量和类型

定义变量要使用 **var** 关键字。

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">
			// 全局变量
			var myGlobalVar = "apples";

			function myFunc(name) {
				// 局部变量，只能在该函数范围内使用。
				var myLocalVar = "sunny";
				return ("Hello " + name + ". Today is " + myLocalVar + ".");
			}
			document.writeln(myFunc("Adam"));
		</script>
		<script type="text/javascript">
			// 全局变量可以在任何地方使用，包括在其他脚本中。
			document.writeln("I like" + myGlobalVar);
		</script>
	</body>
</html>
```





### 使用基本类型

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">

			// string 类型
			var firstString = "This is a string";

			// boolean 类型
			var firstBool = true;
			var secondBool = false;

			// 数值类型
			var daysInWeek = 7;
			var pi = 3.14;
			var hexValue = oxFFFF;
            
		</script>
	</body>
</html>
```





### 创建对象

1. 使用 `new Object()` 方式创建对象：

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">
			// 创建对象
			var myData = new Object();
			myData.name = "Adam";
			myData.weather = "sunny";

			document.writeln("Hello " + myData.name + ".");
			document.writeln("Today is " + myData.weather + ".");
		</script>
	</body>
</html>
```

2. 使用对象字面量：

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">
			// 对象字面量
			var myData = {
				// 属性
				name: "Adam", // 属性之间使用逗号,分开
				weather: "sunny",
				// 方法
				printMessage: function() {
					// 方法内部使用 this 关键字
					document.writeln("Hello " + this.name + ".");
					document.writeln("Today is " + this.weather + ".");
				}
			};

			myData.printMessage();
		</script>
	</body>
</html>
```



### 使用对象

读取和修改对象属性值：

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">
			var myData = {
				name: "Adam",
				weather: "sunny",
			};

			// 修改属性值
			myData.name = "Joe";
			myData["weather"] = "raining";

			// 读取属性值
			document.writeln("Hello " + myData.name + ".");
			document.writeln("Today is " + myData["weather"]);
			
			// 枚举对象属性
			for (var prop in myData) {
				document.writeln("Name:" + prop + "value: " + myData[prop]);
			}
            
            // 为对象添加新属性
			myData.dayOfWeek = "Monday";

			// 为对象添加新方法
			myData.sayHellow = function() {
				document.writeln("Hellow");
			}

			// 删除对象的属性、方法
			delete myData.name;
			delete myData["weather"];
			delete myData.sayHellow;
            
            // in 关键字：检查对象是否具有某个属性
			var hsName = "name" in myData;
			var hasData = "data" in myData;
            
		</script>
	</body>
</html>
```



### JavaScrip 运算符

![](http://upload-images.jianshu.io/upload_images/2648731-2483bfc2c9e0a591.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




#### 相等运算符&等同运算符

* 相等运算符（==）：测试两个值是否相等，不管其类型；
* 等同运算符（===）：判断**值**和**类型**是否都相同；

基本类型的比较：

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">

			var firstValue = "5";
			var secondValue = 5;

			// 相等运算符: 会尝试将操作数转换为同一类型以便判断是否相等。
			if (firstValue == secondValue) {
				document.writeln("They are the same");
			}else {
				document.writeln("They are not the same");
			}
            // output:They are the same
            
            // 等同运算符: 不会进行类型转换
			if (firstValue === secondValue) {
				document.writeln("They are the same");
			}else {
				document.writeln("They are not the same");
			}
			// output：They are not the same
            
		</script>
	</body>
</html
```



对象类型的比较：**引用的比较**。

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">

			var myData1 = {
				name: "Adam",
				weather: "sunny",
			};

			var myData2 = {
				name: "Adam",
				weather: "sunny",
			};

			var myData3 = myData2;

			var test1 = myData1 == myData2;
			var test2 = myData2 == myData3;
			var test3 = myData1 === myData2;
			var test4 = myData2 === myData3;

			document.writeln("Test 1: " + test1 + " Test 2: " + test2
				);
     		document.writeln("Test 3: " + test3 + " Test 4: " + test4
				); 
     		// output:Test 1: false Test 2: true 
     		//        Test 3: false Test 4: true

		</script>
	</body>
</html>
```



### 显式类型转换

💡字符串连接运算符（+）比加法运算符（+）优先级更高。

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">

			var myData1 = 5 + 5;
			var myData2 = 5 + "5";

			document.writeln("Result 1: " + myData1);
     		document.writeln("Result 2: " + myData2); 
     		// output:Result 1: 10 
     		//        Result 2: 55
            
     		// 1.将数值转换为字符串
     		var myData3 = (5).toString() + String(5);
     		document.writeln("Result 3: " + myData3);
     		// output: Result 3: 55
            
            // 2.将字符串转换为数值
     		var myData4 = "5";
     		var result = Number(myData4);
            
		</script>
	</body>
</html>
```



#### 数值到字符串的常用转换方法：

| 方法             | 说明                                        | 返回   |
| ---------------- | ------------------------------------------- | ------ |
| toString()       | 以十进制形式表示数值                        | 字符串 |
| toString(2)      | 二进制                                      | 字符串 |
| toString(8)      | 八进制                                      | 字符串 |
| toString(16)     | 十六进制                                    | 字符串 |
| toFixed(n)       | 以小数点后有n位数字的形式表示实数           | 字符串 |
| toExponential(n) | 指数表示法，                                | 字符串 |
| toPrecision(n)   | 用n位有效数字表示数值，必要时使用指数表示法 | 字符串 |

#### 字符串到数值的常用转换函数：

| 函数              | 说明                                   |
| ----------------- | -------------------------------------- |
| Number(<str>)     | 通过分析指定字符串，生成一个整数或实数 |
| parseInt(<str>)   | 通过分析指定字符串，生成一个整数值     |
| parseFloat(<str>) | 通过分析指定字符串，生成一个实数值     |

### 使用数组

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">

			// 创建和填充数组
			var myArray = new Array();
			myArray[0] = 100;
			myArray[1] = "Adam";
			myArray[2] = true;

			// 使用数组字面量
			var myArray2 = [100, "Adam", true];

			// 读取数组内容
			document.writeln("Index 0 " + myArray[0]);

			// 修改数组内容
			myArray[0] = "Tuesday";
			document.writeln("Index 0 " + myArray[0]);

			// 枚举数组内容
			for (var i = myArray.length - 1; i >= 0; i--) {
				document.writeln("Index " + i + myArray[i]);
			}
			
		</script>
	</body>
</html>
```

![](http://upload-images.jianshu.io/upload_images/2648731-f82021e507d59364.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




### 处理错误

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">
			// 异常处理
			try {
				var myArray;
				for (var i = myArray.length - 1; i >= 0; i--) {
					document.writeln("Index " + i + myArray[i]);
				}
			}catch (e){
				document.writeln("Error " + e);
			}finally {
				document.writeln("总是执行的代码.");
			}
		</script>
	</body>
</html>
```

Error 对象定义的属性：

| 属性    | 说明                    | 返回   |
| ------- | ----------------------- | ------ |
| message | 对错误条件的说明        | 字符串 |
| name    | 错误的名称，默认为Error | 字符串 |
| number  | 该错误的错误代号        | 数值   |

### undefined 和 null

**undefined** :读取未赋值的变量或试图读取对象没有的属性时得到的就是undefined值

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">
			// undefined
			var myData = {
				name: "Adam",
				weather: "sunny",
			}
			document.writeln("Prop " + myData.doesntexist);
			// output:Prop undefined
		</script>
	</body>
</html>
```



**null**:已经赋了一个值，但是该值不是一个有效的object、string、number、或boolean值。

```html
<!DOCTYPE html>
<html>
	<head>
		<title>Example</title>
	</head>
	<body>
		<script type="text/javascript">
			// 区分 undefined 和 null
			var myData = {
				name: "Adam",
			}

			document.writeln("Var: " + myData.weather); 
			document.writeln("Prop " + ("weather" in myData)); 
			// Var: undefined Prop false

			myData.weather = "sunny";
			document.writeln("Var: " + myData.weather);
			document.writeln("Prop " + ("weather" in myData)); 
			// Var: sunny Prop true

			myData.weather = null;
			document.writeln("Var: " + myData.weather);
			document.writeln("Prop " + ("weather" in myData));
			// Var: null Prop true

			// 检查变量或属性是否为undefined或null
			if (!myData.name) {
				document.writeln("name is null or undefined");
			}else {
				document.writeln("name is not null or undefined");
			}

			// 区分 null 和 undefined
			var firstValue = null;
			var secondValue;

			// 想要同等对待undefined和null，使用相等运算符==
			var equality = firstValue == secondValue;
			// 想要区分undefined和null，使用等同运算符===
			var identity = firstValue === secondValue;

			document.writeln("Equality: " + equality);
			document.writeln("Identity: " + identity);
			// Equality: true Identity: false

		</script>
	</body>
</html>
```



### 使用JavaScript调试器

* Chrome
* Mozilla Firefox 的 Firebug 插件





### 使用JavaScript库

* jQuery及其配套程序库jQuery UI，[jQuery 官网](https://jquery.com)|[Github:JQuery](https://github.com/jquery/jquery)
* [Dojo](https://dojotoolkit.org)

- [Github:Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Github:JQuery](https://github.com/jquery/jquery)
- [Github:node.js](https://github.com/nodejs/node)
- [前端UI框架：layui](http://www.layui.com/doc/)
- [前端UI框架：微信WeUI](https://github.com/weui/weui)
- [前端UI框架：Bootstrap](https://getbootstrap.com)
- [前端UI框架：EasyUI](http://www.jeasyui.com/index.php)
- [前端框架list: awesomes](https://www.awesomes.cn/repos/Applications/frameworks)
- [工具：项目管理工具：phacility](https://www.phacility.com)

