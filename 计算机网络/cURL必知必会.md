
[cURL 必知必会（英文版网址）](https://ec.haxx.se/)



### cURL 项目的组成部分

cURL 项目主要由两部分组成：

* 命令行工具 **curl**;
* 提供 C API 的 **libcurl** 库；

这两个工具和库都基于网络协议，为指定的 URL 资源执行网络传输。



### curl 的默认配置文件

* 类 Unix 系统：`.curlrc` 文件。
* Windows 系统：`_curlrc` 文件。



### `-h`：列出所有的命令行选项

列出 curl 命令行工具所有的选项，并提供简要的说明：
```bash
curl -h
curl --help
```

输出 curl [整个手册](https://curl.naxx.se/docs/manpage.html)以及常见的用例教程：
```bash
curl --manual
```


### `-v`：详细（verbose）模式

让 curl 切换到详细模式：
```bash
curl -v http://example.com
curl --verbose http://example.com
```

让 curl 打开详细模式并进行 HTTP 重定向：
```bash
curl -vL http://example.com
curl --verbose --location http://example.com
```

关闭详细模式：
```bash
curl --no-verbose http://example.com
```


### 不使用 schema

URL 是 curl 的操作对象。实际上，更准确的名称是 URI（统一资源标识符）。

URL 以 “schema” 作为开头，schema 是 “http://” 这部分内容的官方名称，用以告诉 curl 传入的 URL 使用了哪个协议。

curl 允许用户省略 URL 的 schema 部分。curl 会根据主机名的第一部分猜测要使用哪种协议。可检测的协议包括 FTP、DICT、LDAP、IMAP、SMTP 和 POP3。没有提供 schema 的其他 URL 将默认使用 HTTP 协议。

**可以通过 `--proto-default` 选项将默认协议修改为 HTTP 以外的其他协议**。


### 身份验证

使用给定的用户名和密码列出 FTP 服务器目录中的内容：
```bash
curl ftp://user:password@example.com/
```

**Basic 身份验证**：要让 curl 发出带有身份验证的 HTTP 请求，可以用 `-u` 或 `--user` 选项提供用户名和密码：
```bash
curl -u admin:123456 http://example.com/
```

如果要让 curl 先确认服务器是否真的需要身份验证，可以使用 `--anyauth` 选项，它会自动使用 curl 所知道的最安全的身份验证方法。curl 将尝试无须身份验证的请求，然后在必要时使用身份验证：
```bash
curl --anyauth --user admin:123456 http://example.com/
```

**使用 `.netrc` 文件或配置文件可以避免在命令行上指定用户名和密码**。


### 主机名或地址

指定 IPV4 地址：
```bash
curl http://127.0.0.1/
```

**使用 IPV6 地址需要将其放在方括号中**：
```bash
curl http://[::1]/
curl http://[2001:0db8:3c4d:0015:0000:0000:1a2f:1a2b]/
```


### 指定 FTP 类型

告诉 curl FTP 资源的文件类型以适应不同的传输模式。

ASCII 类型：
```bash
curl "ftp://example.com/foo;type=A"
```

二进制类型（默认）：
```bash
curl "ftp://example.com/foo;type=I"
```

目录类型：
```bash
curl "ftp://example.com/foo;type=D"
```


### 多个选项和多个 URL

curl 支持数百个命令行选项和无限数量的 URL。

curl 会在处理完最后一个 URL 后返回一个退出码。

**可以通过 `--fail-early` 选项让 curl 在第一次出现错误时就退出**。


### `--next`：在一组选项和 URL 之间插入间隔

当命令行解析到 `--next` 选项时，它会**将后面的选项应用于下一组 URL**。

```bash
curl --location http://example.com/1 --next
     --data sendthis http://example.com/2 --next
     --head http://example.com/3
```


### URL 通配

curl 使用保留符号 `[]` 和 `{}` 进行通配。可以使用 `-g` 或 `--globoff` 禁用它。


### 数值范围、字母范围、列表

可以使用 `[N-M]` 语法来指定一个数值范围，其中 N 是起始索引，M 是结束索引。

请求 100 个以数字命名的图像：
```bash
curl -O http://example.com/[1-100].png
curl -O http://example.com/[001-100].png
```

支持**指定步进**，指定步进为2：
```bash
curl -O http://example.com/[1-100:2].png
```

curl 也可以处理**字母范围**：
```bash
curl -O http://example.com/section[a-z].html
```

通过花括号指定完整的列表：
```bash
curl -O http://example.com/{one,two,three,alpha,beta}.html
```



### 组合使用多个通配符

下载 Ben、Alice 和 Frank 的图像，并且需要 100x100 和 1000x1000 的分辨率：
```bash
curl -O http://example.com/{Ben,Alice,Frank}-{100x100,1000x1000}.jpg
```

获取 Web 服务器和邮件服务器一周内的日志：
```bash
curl -O http://example.com/{web,mail}-log[0-6].txt
```


### `--config`：配置文件

使用 `-K` 或者 `--config` 选项让 curl 从特定的文件中读取更多的命令行选项：
```bash
curl -K cmdline.txt http://example.com
```

cmdline.txt 文件中包含了你输入的每行命令：
```bash
# 这是注释，我们要求跟踪重定向
--location
# 要求发送 HEAD 请求
--head
# 支持使用不带破折号的长选项
location
# 命令行选项的参数必须与该选项处于同一行
user-agent "Everything-is-an-agent"
# 支持在选项与参数之间使用=或者:
user-agent = "Everything-is-an-agent"
# 如果参数中带有空格，则必须使用双引号
# 如果想在配置文件中指定 URL，则必须使用 --url 或 url
url = "http://example.com"
```


### 进度指示器

* 进度指示器显示的单位是字节（Byte, 1 Byte = 8 bit）数和每秒字节数。
* 通过 `-s` 或 `--silent` 选项可以强制关闭进度指示器。
* 启用静默模式后，可以通过 `-s` 或 `--show-error` 选项来要求它在发生错误时输出错误信息。
* 通过 `-#` 或 `--progress-bar` 选项使用一个更简单的进度指示器。



### `--trace` 和 `--trace-ascii`

`--trace [filename]` 选项可以将完整的跟踪信息（十六进制编码）保存在指定的文件中。
`--trace-ascii [filename]` 可以将跟踪信息转化为 ASCII 编码。
`--trace-time`，在输出信息前添加高精度的时间戳。
```bash
curl --trace dump http://example.com
```


### HTTP/2

在使用 HTTP/2 进行文件传输时，curl 将发送和接收**被压缩**的标头信息。但是为了以可读性和可理解的方式显示传出和传入的 HTTP/2 标头信息，curl 将**显示未经压缩的版本**。


### 保存下载的内容

可以通过 `-o [filename]` 或 `--output` 保存下载内容到指定的文件。

```bash
curl -o output.html http://example.com/
```

`-O`（大写的 O）或 `--remote-name`：用 URL 指定的文件名保存内容：

```bash
curl -O http://example.com/file.html
```


### `--compressed`：压缩

curl 可以要求 HTTP 和 HTTPS 服务器提供压缩过的数据。

`--compressed` 请求服务器使用一种受支持的压缩算法来压缩数据，curl 会在保存或发送数据到 stdout 之前对数据进行解压。

```bash
curl --compressed http://example.com/
```


### 压缩传输编码

客户端要求服务器进行压缩传输编码，如果服务器接受了，它将作出响应，并通过一个标头指明它将进行压缩编码，curl 将在接收到数据时对其进行解压。

用户可以通过 `--tr-encoding` 选项请求服务器进行压缩传输编码：
```bash
curl --tr-encoding http://example.com/
```

不过需要注意的是，并非所有的 HTTP 服务器都支持这个特性。


### shell 重定向

使用 `> filename` 将 stdout 重定向到指定文件中，使用 `2>file` 将 stderr （元数据或错误等）重定向到指定文件中。

```bash
curl http://example.com > files.html 2>errors
```


### 速率限定

在传输数据时，curl 会尝试尽快完成任务。它可以用于上传和下载。

可以使用 `--limit-rate [speed]` 让 curl 的速率不超过指定的字节/秒。

```bash
curl https://example.com/ --limit-rate 200k
```

### 最大的文件

为 curl 指定可接受的最大下载字节数：
```bash
curl --max-filesize 100000 https://example.com/
```


### 在文件系统中保存元数据

在将下载的内容保存到文件时，可以通过 `--xattr` 选项告诉 curl 将某些文件元数据也保存在“扩展文件属性”中。


### `--raw`

如果使用 `--raw` 选项，curl 会禁用所有内部的 HTTP 内容解码或传输编码，取而代之的是传输未经修改的原始数据。

如果你正在开发某种中间软件，并希望将内容传给另一个 HTTP 客户端让它来解码，那么这个选项就很有用了。


### 失败重试

通常 curl 只会尝试执行一次传输不成功则返回错误。你可以使用 `--retry` 选项让 curl 重试失败的传输。


### 恢复下载

在恢复下载时，curl 会检查本地已存在的文件的大小，然后向服务器请求剩余的内容，并追加到本地的文件中。

`-C` 或 `--continue-at` 选项可以告诉 curl 从哪里开始传输，选项的值可以是一个普通的数字字节偏移量，或者使用字符串 `-` 让 curl 根据它所知道的信息自己决定从哪里开始传输。

从字节偏移量为 100 的位置开始下载 FTP 文件：
```bash
curl --continue-at 100 ftp://example.com/bigfile
```

继续之前中断的下载：
```bash
curl --continue-at - ftp://example.com/bigfile
```

### HTTP 区间

可以用 `-r` 或者 `--range` 让 curl 发起区间请求，请求特定区间的数据。

向远程服务器请求特定字节范围的内容。请求从偏移量 100 开始的 1000 个字节：
```bash
curl --range 100-1099 ftp://example.com/bigfile
```

请求前 200 个字节的数据：
```bash
curl -r 0-199 http://example.com/
```

索引 200 之后的所有内容：
```bash
curl -r 200- http://example.com/
```

从索引 0 处获取 200 个字节，再从索引 1000 处获取 200 个字节：
```bash
curl -r 0-199,1000-199 http://example.com/
```


### 连接超时

可以用 `--connect-timeout` 设置 curl 允许尝试连接的最长时间。


### keepalive

curl 默认使用 TCP 的 keepalive 特性实现长连接。

使用 `--no-keepalive` 禁用 keepalive 特性：
```bash
curl --no-keepalive https://example.com/
```

使用 `--keepalive-time` 来指定发送探测的频率，默认为 60 秒。

将 TCP “ping” 之间的间隔改为 5 分钟：
```bash
curl --keepalive-time 300 https://example.com/
```


### 允许的最长时间

在 curl 抛出超时错误码（28）并退出前，可以用 `-m` 或 `--max-time` 选项告诉 curl 最长有多少时间（以秒为单位）可用。当指定的时间耗尽，无论当时发生什么，curl 都会退出，即使它正在传输数据。

指定的最长时间可以用小数表示，0.5 表示 50 毫秒，2.37 表示 2370 毫秒：
```bash
curl --max-time 5.5 https://example.com/
```


### 传输速率慢到一定程度就退出

你可以告诉 curl，如果传输速率低于某个特定值，并且在某个时间段内一直低于这个值，那么就放弃传输。

例如，如果 15 秒内的传输速率低于 1000 字节/秒，则停止传输：
```bash
curl --speed-time 15 --speed-limit 1000 https://example.com/
```


### 启用 TLS

使用 `--ss1` 选项意味着 cur1 会尝试将连接升级到 TLS，但如果失败，它仍将继续使用明文协议来执行传输。
```bash
curl --ssl http://example.com/file.txt
```

如果一定要使用 TLS 连接，可以使用 `-ssl-reqd` 选项，使用这个选项时如果 curl 无法成功进行 TLS 协商，传输就会失败。
```bash
curl --ssl-reqd http://example.com/file.txt
```


### HTTP 版本

从 2016 年年中开始，curl 默认用 HTTP/1.1 连接 HTTP 服务器。如果要连接到 HTTPS 服务器，并且你的 libcurl 内置了 HTTP/2 功能，那么 cur1 将尝试使用 HTTP/2，或在协商失败时降级至 HTTP/1.1。默认情况下，不支持 HTTP/2 的 curl 将使用 hTTP/1.1。


### HTTP POST

POST 是为将数据发送到 Web 应用而发明的 HTTP 方法，也是 HTML 表单中最常用的方法。它通常会向接收者发送相对少量的数据块。

在表单中填充好数据后，浏览器以“URL编码”的形式（以＆符号分隔的一系列键值对）将数据发送出去。可以用 curl 的 `-d` 或 `-data` 选项发送这样的数据，如下所示：
```bash
curl -d 'name=admin&shoesize=12' http://example.com/
```


### Content-Type

用 curl 的 `-d` 或 `-data` 选项发送 POST 请求时，请求中默认会包含一个类似 `Content-Type: application/X-www-form-urlencoded` 这样的标头。

如果想将 JSON POST 到服务器，并且准确地告诉服务器你发送的内容是什么类型，可以参见如下代码：
```bash
curl -d '{json}' -H 'Content-Type:application/json' http://example.com/
```



### POST 二进制内容

`--data-binary @filename` 选项可以让 curl 从文件中读取二进制内容。
```bash
curl --data-binary @filename http://example.com/
```



### URL 编码

curl 提供了 `--data-urlencode` 选项，可以让用户输入未经编码的数据，然后让 curl 自动编码。


### 转换为 GET

`-G` 或 `--get` 选项可以将 `-d` 选项指定的数据附加到 URL 右边，并使用 “?” 隔离，然后让 curl 用 GET 方法发送数据。

```bash
curl --get --data <data> <url>

curl --include \
     http://localhost:7001/api/v1/applications \
     --get --data 'name1=value1&name2=value2'
```


### 分块编码的 POST

通过使用分块传输编码，curl 将逐块发送 POST 数据，每个数据块中会包含块的大小。

```bash
curl -H "Transfer-Encoding: chunked" -d "payload to send" http://example.com/
```


### 使用 curl 发送表单

multipart formpost 是指 HTTP 客户端在 HTML 表单的 `enctype` 属性被设置为 `“multipart/form-data”` 时所发送的内容。


HTML 示例：
```html
<form action="submit.cgi" method="post" enctype=“multipart/form-data”>
  Name: <input type="text" name="person"><br>    
  File: <input type="file" name="secret"><br>  
  <input type="submit" value="提交">
</form>
```

使用 curl 时，可以通过`-F`（或 `--form`）选项添加每个单独的 multipart。

使用 curl 发送以上的表单数据：

```bash
curl -F person=anonymous -F secret=@file.txt http://example.com/submit.cgi
```


### HTTP 重定向

顾名思义，**“重定向”是指服务器向客户端发送指令，而不是返回客户端想要的内容**。服务器好像在说:“去这里找你想要的东西。”

但并非所有的重定向都是一样的。重定向是永久性的吗？客户端在下一个请求中应该使用哪种请求方法？

进行重定向时需要发送一个 Location: 标头其中包含新的 URI，可以是绝对路径或相对路径。


### 自定义标头

可以通过 `-H` 或 `--header` 选项自定义标头。

```bash
curl -H "Host: test.example" http://example.com/
```


### referer

当用户点击网页上的链接时，浏览器会将用户带到下一个 URL，它会在新的请求中添加 `referer` 标头，表面请求的来源。

可以在 curl 中用 `-e` 或 `--referer` 选项来设置 `referer` 标头：
```bash
curl --referer http://comes-form.example.com https://example.com/
```


### User-Agent 

客户端可以在请求中设置 `User-Agent` 标头，用于**告诉服务器它属于哪种用户代理**。有时服务器会检查这个标头，并根据标头内容决定如何作出响应。

可以用 `-A` 或 `--user-agent` 选项加上要使用的字符串来设置你喜欢的值：



### 从文件中读取 cookie

curl 使用的 cookie 文件格式叫做 **Netscape cookie** 格式。

`-b` 指示让 curl 从指定的文件读取初始 cookie：
```bash
curl -L -b cookies.txt https://example.com/
```

### 将 cookie 写入文件


让 curl 在退出之前将已知的 cookie 写入文件（也就是 cookie jar）中。

可以用 `-c` 选项指定 cookie jar，让 curl 将 cookie 写入该文件中：
```bash
curl -c cookie-jar.txt https://example.com/
```


### 新 cookie 会话

刷新 cookie 会话意味着所有会话 cookie 都将被丢弃，这相当于重启浏览器。

可以通过 `-j` 或 `--junk-session-cookies` 选项让 curl 开始新的 cookie 会话：
```bash
curl -j -b cookies.txt https://example.com/
```


### HTTP/2

默认情况下，发起 HTTP 请求时，curl 默认用 **HTTP/1.1** 连接 HTTP 服务器。而发起 HTTPS 请求时，curl 会默认使用 **HTTP/2**。

可以用以下代码让 curl 请求服务器时使用 HTTP/2：
```bash
curl --http2 http://example.com/
```

如果你已经知道服务器支持 HTTP/2（例如，在自己的受控环境中，你确切地知道服务器上运行的是什么），那么可以通过 `--http2-prior-knowledge` 选项进行快捷的 HTTP/2 “协商”。


### 多路复用

HTTP/2 协议的主要特性之一是**能够在同一物理连接上复用多个逻辑流**。在使用 curl 命令行工具时，你无法利用这个很酷的特性，因为 curl 严格按照串行的方式执行网络请求，一个接一个，后一个要在前一个结束后才能开始。

希望未来的 curl 版本可以支持这项特性。
























