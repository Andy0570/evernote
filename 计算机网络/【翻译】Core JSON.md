> 原文：[Core JSON](https://dzone.com/refcardz/core-json?chapter=1)



无脂肪的 XML 替代品

涵盖 JSON 语法、验证、建模、JSON 模式。包括用各种工具和编程语言使用 JSON 的技巧和窍门。

## JSON 概述

JSON（JavaScript Object Notation）是一种标准的基于文本的数据交换格式，使应用程序能够在计算机网络上交换数据。用 Ruby、Java/EE、JavaScript、C#/.Net、PHP 等编写的程序可以很容易地消费和产生 JSON 数据，因为它与语言和计算平台无关。丰富的 JSON 相关的 API 和工具使你很容易从你喜欢的编程语言、IDE 和运行时环境中使用 JSON。此外，流行的 NoSQL 数据库，如 MongoDB 和 CouchBase 都是基于 JSON 的。

JSON 是由 Douglas Crockford 在 2001 年创建的，并在 RFC 4627 中规定了 IETF（互联网工程任务组）标准；见 <http://tools.ietf.org/html/rfc4627>。根据该规范，JSON 的 IANA（Internet Assigned Numbers Authority）媒体类型为application/json，文件类型为 .json。



### 什么是 JSON？

JSON 是一种简单的数据格式，有 3 种基本数据结构：

* Name/Value（或 Key/Value）对。
* 对象。
* 数组。



一个有效的 JSON 文档（JSON document）总是用大括号包围，像这样：

```json
{ JSON-Data }
```

请注意，JSON 社区的一些成员使用 "string" 而不是 "document" 这个术语。



### 为什么使用 JSON？

JSON 正逐渐取代 XML 成为互联网上首选的数据交换格式，因为 JSON 易于阅读，其结构可以映射到常见的编程概念，如对象和数组。JSON 比 XML 更有效（即解析和网络传输更快），而且 JSON 更紧凑——没有开始和结束标签。



### 键值对

一个键值对看起来像这样：

```json
{
  "firstName": "John"
}
```

属性名称（即 `firstName`）是一个字符串，用双引号包围。一个值可以是一个字符串（如上面的例子），但这只是几种有效的数据类型之一（详情请见数据类型部分）。一些著名的技术声称他们使用 JSON 数据格式，但他们没有用引号包围他们的字符串。然而，这不是有效的 JSON；请看 JSON 验证部分。



### 对象

对象是无序的键值对的集合。下面的例子描述了一个地址对象：

```json
{
  "address" : {
    "line1" : "555 Main Street",
    "city" : "Denver",
    "stateOrProvince" : "CO",
    "zipOrPostalCode" : "80202",
    "country" : "USA"
  }
}
```

一个对象（本例中为地址）由逗号分隔的键值对组成，周围有大括号。



### 数组

数组是一个有序值的集合，看起来像这样：

```json
{
  "people" : [
    { "firstName": "John", "lastName": "Smith", "age": 35 },
    { "firstName": "Jane", "lastName": "Smith", "age": 32 }
  ]
}
```



### 值类型

值（即键值对的右侧部分）可以是以下类型之一：

* 对象（Object）
* 数组（Array）
* 字符串（String）
* 数字（Number）
* 布尔（Boolean）
* null



### Number

一个数字可以是一个整数或双精度浮点数。下面是一些例子：

```json
"age": 29
"cost": 299.99
"temperature": -10.5
"speed_of_light": 1.23e11
"speed_of_light": 1.23e+11
"speed_of_light": 1.23E11
"speed_of_light": 1.23E+11
```

属性名称（即 `age` 等）是一个由双引号包围的字符串，但值没有引号。一个数字的前缀可以是一个减号。指数部分（用 `e` 或 `E` 表示）在数字值的后面，可以有一个可选的加号或减号。前导零、八进制和十六进制的数值都不允许使用。



### Boolean

JSON 中的布尔值可以是 true 或 false，如下所示：

```json
{
  "emailValidated" : true
}
```

属性名（`emailValidated`）是一个由双引号包围的字符串，但值（`true`）没有引号。



### null

虽然在技术上不是一个数据类型，但 `null` 是一个特殊的值，表示一个数据元素没有价值。在下面的例子中，年龄字段没有值（可能是因为用户选择不输入这个信息）。

```json
{
  "age" : null
}
```



### 注释

JSON 不允许有注释。注释最初是 JSON 的一部分，但开发者滥用了它们，把解析指令放在注释中。当 Douglas Crockford 看到这种做法时，他从 JSON 中删除了注释，以保持计算平台之间的互操作性。



### 风格

你可能已经注意到，属性名称（即冒号左侧的名称）使用驼峰式的命名法。这不是一个规则或标准，而是谷歌的 JSON 风格指南中规定的惯例，网址是：<https://google.github.io/styleguide/jsoncstyleguide.xml>，[社区中文版](https://github.com/darcyliu/google-styleguide/blob/master/JSONStyleGuide.md)。



### 官方语法

Douglas Crockford 的 JSON 网站（<http://www.json.org>）提供了 JSON 语法的完整描述。
此外，[JSON Pro 快速指南](https://apps.apple.com/us/app/json-pro-quick-guide/id454037729)（可在 iPhone 应用商店免费下载）提供了 JSON 语法的例子和概述。



## JSON 验证

一个文本文档必须遵循 JSON 语法规则才能被认为是一个有效的 JSON 文档。有效的 JSON 很重要，因为它可以确保应用程序和 JSON 相关工具之间的互操作性。尽管 JSON.org 网站显示了 JSON 语法，但有时看到 JSON 验证的实际效果会更容易。[JSONLint](http://www.jsonlint.com) 提供了一个互动的、基于网络的 JSON 验证器。要使用它，在主文本区输入或粘贴一些文本，然后按下验证按钮。如果文本不是有效的 JSON，你会看到一个错误信息，如下：

![](https://dzone.com/storage/rc-covers/13096-thumb.png)

在这种情况下，地址对象的属性名称缺少一个结束的双引号。在你解决了这个问题并按下验证按钮后，JSONLint 会对JSON 文档进行漂亮的打印，如下所示：

![](https://dzone.com/storage/rc-covers/13097-thumb.png)

JSONLint 也可以作为 Chrome 网络商店中的一个 Chrome 扩展。



## JSON 建模

为实际应用开发有效的 JSON 文档可能很繁琐且容易出错。为了避免排版错误，你可以使用 JSONPad、JSON Editor Online 和 JSON Designer 来创建一个逻辑模型（类似于UML）并生成有效的 JSON 文档。

### JSONPad

JSONPad（来自<http://www.jsonpad.com/en/Home.html>）是一个 GUI 工具，通过提供一个界面，使你能够创建对象、键（即键值对）和数组，从而消除了输入 JSON 文本的麻烦。JSONPad 可作为 Windows 或 Mac GUI 使用，也可在JSONPad 网站上在线使用。要创建一个模型，使用文本区下面的绿色加号键。支持以下数据类型。键（即，名称/值对）、对象和数组。模型完成后，按下蓝色的向上箭头按钮（在树形标签下），就可以根据模型生成一个有效的、印刷精美的JSON文档。

![](https://dzone.com/storage/rc-covers/13098-thumb.png)

最终的结果是一个有效的 JSON 文档，可以在你的应用程序中使用。你也可以通过将 JSON 文本粘贴到文本区并按下树形标签中的绿色向下箭头来生成一个模型。在格式选项卡下，您可以压缩或漂亮地打印一个 JSON 文档。当您按下工具选项卡中的 JSON 按钮时，JSONPad 将验证文本中的 JSON 文档。

### JSON Editor Online

JSON Editor Online (<http://jsoneditoronline.org/>)是一个在线 JSON 建模器，也可作为 Chrome 浏览器的扩展使用。



## 浏览器中的 JSON

Firefox 和 Chrome 提供了优秀的扩展程序（即附加组件和插件），使其更容易处理 JSON。



### REST Client

Rest Client 是一个 Firefox 扩展，它提供了从浏览器调试和测试 RESTful Web 服务的能力。从浏览器进行测试的能力并不新鲜，但输出的格式更容易阅读。

![](https://dzone.com/storage/rc-covers/13099-thumb.png)

 上面的例子使用了来自开放图书馆 API 的书籍服务。在输入服务 URI 后，响应体（高亮）标签显示了 JSON输出。

### Pretty Printing with JSONView

JSON 在浏览器中原生显示时可读性不强。JSONView 是一个 Firefox 和 Chrome 的扩展，可以漂亮地打印出 JSON。

### JSONView in Firefox

安装这个扩展并重新启动火狐浏览器后，来自开放图书馆图书服务 URI 的 JSON 响应是可读的，而且你可以在页面上展开/折叠对象。

![](https://dzone.com/storage/rc-covers/13100-thumb.png)

### JSONView in Chrome

JSONView 也可以作为 Chrome 网络商店的一个 Chrome 扩展。

![](https://dzone.com/storage/rc-covers/13101-thumb.png)

[JSONQuery](https://www.sitepen.com/blog/2008/07/16/jsonquery-data-querying-beyond-jsonpath) 是几种可以搜索 JSON 文档并返回所需元素的技术之一。



### 使用 JSON SH 美化 JSON

JSON SH 是一个 Chrome 扩展（可在谷歌 Chrome 网络商店中找到），作为一个漂亮的打印机和一个验证器。将一个有效的（但不漂亮的）JSON 文档粘贴到页面顶部的文本区域，JSON SH 会将文本美化成人类可读的格式。



## JSON 和 AJAX

AJAX（Asynchronous JavaScript and XML）是 JSON 的原始用例之一，下面的 jQuery 例子显示了一个 JavaScript 客户端如何向一个 RESTful Web 服务发出 HTTP Get 请求并处理 JSON 响应：

```javascript
$.getJSON('http://example/service/addresses/home/1',   
  function(data) {
    var address = JSON.parse(data);

    console.log("Address Line 1 = " + address.line1);
  }
);
```

在上面的代码中，`$.getJSON()`（jQuery `$.ajax()` 调用的简写版本）发出一个 HTTP GET 请求。匿名的）成功回调函数接收 JSON 响应，并使用 `JSON.parse()` 将其解析为一个 JavaScript 对象，这是 ECMAScript-262 标准的一部分（从第五版开始） - 请参阅http://www.ecmascript.org/，了解更多信息）。`console.log()` 方法会将地址的第1行记录到浏览器的控制台。相反，`JSON.stringify()` 方法将一个 JavaScript 值转换为 JSON 字符串（可选择漂亮打印）。



## JSON 和 Java

[Jackson](https://github.com/FasterXML/jackson) 库是一个流行的基于 Java 的 JSON API。这里有一个例子，说明如何将一个地址对象从 JSON 中提取/释放出来：

```java
import java.io.Writer;
import java.io.StringWriter;
import org.codehaus.jackson.map.ObjectMapper;

public class Address {
    private String line1;
    private String city;
    private String stateOrProvince;
    private String zipOrPostalCode;
    private String country;

    public Address() {}

    public String getLine1() {
        return line1;
    }

    public void setLine1(line1) {
       this.line1 = line1;
    }

    // Remaining getters and setters ...
}

Address addrOut = new Address();
// Call setters to populate addrOut …

ObjectMapper mapper = new ObjectMapper(); // Reuse this.

// Marshal Address object to JSON String.
Writer writer = new StringWriter();
mapper.writeValue(writer, addrOut);
System.out.println(writer.toString());

// Unmarshal Address object from JSON String.
String addrJsonStr = 
"{" +
    "\"address\" : {" +
    "\"line1\" : \"555 Main Street\"," +
    "\"city\" : \"Denver\","
    "\"stateOrProvince\" : \"CO\","
    "\"zipOrPostalCode\" : \"80202\"," +
    "\"country\" : \"USA\"" +
    "}" +
"}";

Address addrIn = mapper.readValue(addrJsonStr, Address.class);
```

除了Jackson 之外，其他著名的基于 Java 的 JSON API 包括：

...



## JSON 和 Ruby

有许多 Ruby 的 JSON 相关库。下面是一个使用 Ruby 标准配置的 JSON gem 的例子：

```ruby
require 'json'

class Address

  attr_accessor :line1, :city, :state_or_province,
                :zip_or_postal_code, :country

  def initialize(line1='', city='', state_or_province='', 
                 zip_or_postal_code='', country='')
    @line1 = line1
    @city = city
    @state_or_province = state_or_province
    @zip_or_postal_code = zip_or_postal_code
    @country = country
  end

  def to_json
    to_hash.to_json
  end

  def from_json!(str)
    JSON.parse(str).each { |var, val| send("#{var}=", val) }
  end

  private

  def to_hash
    Hash[instance_variables.map { |var| [var[1..-1].to_sym, 
         send(var[1..-1])] }]
  end
end 
```

JSON gem 的 `to_json` 方法将一个字符串或哈希值转换为 JSON。地址对象的 `to_json` 方法通过将其数据成员转换为 Hash，然后在 Hash 上调用`to_json`，将地址转换为 JSON 格式。要将地址转换为 JSON 格式，请执行以下步骤：

```ruby
addr1 = Address.new('555 Main Street', 'Denver', 'CO', '80231', 'US')
puts addr1.to_json 

# Outputs the following …
{"line1":"555 Main Street","city":"Denver","state_or_province":"CO","zip_or_postal_code":"80231","country":"US"}
```

JSON gem 的 `JSON.parse` 方法将一个 JSON 字符串转换为哈希值。地址对象的 `from_json!` 方法接收一个 JSON 字符串，调用 `JSON.parse` 转换为 Hash，并从 Hash 中设置每个相应的数据成员，如下所示：

```ruby
json_addr = <<END
{
  "line1" : "999 Broadway", "city" : "Anytown",
  "state_or_province" : "CA", "zip_or_postal_code" : "90210", 
  "country" : "USA"
}
END

addr2 = Address.new
addr2.from_json!(json_addr)
```

除了 JSON gem 外，其他与 JSON 相关的 gem 包括：

...



## JSON 和 Ruby on Rails

| API                             | SOURCE                                                     |
| :------------------------------ | :--------------------------------------------------------- |
| Google GSON                     | http://code.google.com/p/google-json/                      |
| SOJO                            | http://sojo.sourceforge.net/                               |
| org.json (by Douglas Crockford) | http://www.json.org/java                                   |
| json-lib                        | http://sourceforge.net/projects/json-lib/                  |
| json-io                         | https://code.google.com/archive/p/json-io/                 |
| jsontools                       | http://jsontools.berlios.de/                               |
| jsonbeans                       | https://github.com/EsotericSoftware/jsonbeans              |
| ActiveSupport JSON              | http://api.rubyonrails.org/classes/ActiveSupport/JSON.html |
| Yajl                            | https://github.com/brianmario/yajl-ruby                    |
| Oj                              | https://github.com/ohler55/oj                              |

Ruby on Rails 提供了额外的功能，使 Ruby 对象转换为 JSON 更加容易。下面的控制器使用 ActionController 的 `render` 方法，将地址对象输出为 JSON：

```ruby
class Person
  attr_accessor :first_name, :last_name

  def initialize(first_name=nil, last_name=nil)
    @first_name = first_name
    @last_name = last_name
  end
end

class MyController < ApplicationController
  def index
    person = Person.new('John', 'Doe')
    respond_to do |format|
      format.html # index.html.erb
      format.json { render :json => person}
    end
  end
end
```

Rails 的 ApplicationController 负责将对象编入/编出 JSON，所以没有必要在这里写一个 `to_json` 方法。



## JSON Schema

JSON Schema 规定了 JSON 文档的结构。JSON Schema 可用于验证发送到/从 RESTful 网络服务接收的 JSON 内容。JSON Schema 是用 JSON 写的。

主要的 JSON Schema 网站可以在以下网址找到：http://json-schema.org。JSON Schema 是一项正在进行的工作--JSON Schema团队刚刚发布了0.4版本，可以在以下网站找到：http://tools.ietf.org/html/draft-zyp-json-schema-04。

一些重要的 JSON 模式结构包括：

| **CONSTRUCT** | **DESCRIPTION**                                              |
| :------------ | :----------------------------------------------------------- |
| type          | The data type – object, array, string, number, etc.          |
| $schema       | The URI that provides the schema version.                    |
| required      | true/false                                                   |
| id            | Data element id                                              |
| properties    | Validation properties for a data element include type (see above), minimum – minimum value, maximum – maximum value, enum, etc. |

下面是一个在线礼品登记处的部分 JSON Schema 的样本：

```json
{
    "type": "object",
    "$schema": "http://json-schema.org/draft-03/schema",
    "id": "#",
    "required": true,
    "properties": {
        "registrants": {
            "type": "array",
            "id": "registrants",
            "required": true,
            "items": {
                "type": "object",
                "required": false,
                "properties": {
                    "address": {
                        "type": "object",
                        "id": "address",
                        "required": true,
                        "properties": {
                            "city": {
                                "type": "string",
                                "id": "city",
                                "required": true
                            },
                            "country": {
                                "type": "string",
                                "id": "country",
                                "required": false
                            },
                            "line1": {
                                "type": "string",
                                "id": "line1",
                                "required": true
                            },
                            "line2": {
                                "type": "string",
                                "id": "line2",
                                "required": false
                            },
                            "postalCode": {
                                "type": "string",
                                "id": "postalCode",
                                "required": true
                            },
                            "premise": {
                                "type": "string",
                                "id": "premise",
                                "required": true,
                                "enum": [
                                    "work",
                                    "home",
                                    "other"
                                ]
                            },
                            "stateOrProvince": {
                                "type": "string",
                                "id": "stateOrProvince",
                                "required": true
                            }
                        }
                    },
                    "firstName": {
                        "type": "string",
                        "id": "firstName",
                        "required": true
                    },
                    "lastName": {
                        "type": "string",
                        "id": "lastName",
                        "required": true
                    },
                    "phoneNumber": {
                        "type": "object",
                        "id": "phoneNumber",
                        "required": true,
                        "properties": {
                            "channel": {
                                "type": "string",
                                "id": "channel",
                                "required": true,
                                "enum": [
                                    "cell",
                                    "work",
                                    "home"
                                ]
                            },
                            "number": {
                                "type": "string",
                                "id": "number",
                                "required": true
                            }
                        }
                    }
                }
            }
        }
    }
}
```

上述模式。

* 需要一个注册者对象的数组。
* 将 `phoneNumber.channel` 字段限制为以下值：手机、工作、传真或家庭。
* 将地址.前提字段限制为这些值：家庭、工作、或其他。

一个 Web 服务消费者可以使用这个模式来验证以下 JSON 文档：

```json
{
    "registrants": [
        {
            "firstName": "Fred",
            "lastName": "Smith",
            "phoneNumber": {
                "channel": "cell",
                "number": "303-555-1212"
            },
            "address": {
                "premise": "home",
                "line1": "555 Broadway NW",
                "line2": "# 000",
                "city": "Denver",
                "stateOrProvince": "CO",
                "postalCode": "88888",
                "country": "USA"
            }
        }
    ]
}
```



### JSON Schema 生成器

创建一个 JSON Schema 是繁琐和容易出错的。使用 JSON Schema 生成器可以从任何有效的 JSON 文档中生成一个模式。访问在线 JSON Schema 生成器（<www.jsonschema.net/>），通过以下方式生成一个模式。

* 将 JSON 文档粘贴到右边的文本区。
* 选择 JSON 输入选项。
* 按下 "Generate Schema" 按钮。



### JSON Schema 验证器

一个应用程序使用 JSON Schema 验证器来确保 JSON 文档符合模式所指定的结构。JSON Schema 验证器可用于大多数现代编程语言。

| **JSON SCHEMA VALIDATOR**  | **LANGUAGE** | **SOURCE**                                         |
| :------------------------- | :----------- | :------------------------------------------------- |
| JSV                        | JavaScript   | https://github.com/garycourt/JSV                   |
| Ruby JSON Schema Validator | Ruby         | https://github.com/hoxworth/json-schema            |
| json-schema-validator      | Java         | https://github.com/fge/json-schema-validator       |
| php-json-schema (by MIT)   | PHP          | https://github.com/hasbridge/php-json-schema       |
| JSON.Net                   | .NET         | http://james.newtonking.com/projects/json-net.aspx |

除了特定语言的工具，还有一个优秀的在线 JSON Schema 验证器，网址是：http://json-schema-validator.herokuapp.com。要使用这个网站，在相应的文本框中输入JSON文档和模式，然后按下验证按钮。



## 总结

我们已经介绍了 JSON 的基本知识，但我们只是触及了表面。尽管 JSON 是一种简单的数据格式，但有许多工具可以简化设计和开发过程。JSON 是一种标准，它已经取代 XML 成为互联网上首选的数据交换格式，并使开发者能够创建高效、可互操作的企业级应用程序。
