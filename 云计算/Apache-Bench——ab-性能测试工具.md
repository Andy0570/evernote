### 一、相关概念

与压力测试有关的概念：

#### 1. 吞吐率（Requests per second）
* 概念：服务器并发处理能力的量化描述，单位是 reqs/s，指的是**某个并发用户数下单位时间内处理的请求数**。某个并发用户数下单位时间内能处理的最大请求数，称之为最大吞吐率。
* 计算公式：总请求数 / 处理完成这些请求数所花费的时间，即
每秒请求数 = 完成请求数 / 测试时间。

#### 2. 并发连接数（并发连接数）
* 概念：某个时刻服务器所接受的请求数目，简单的讲，就是一个会话。

#### 3. 并发用户数（并发用户数，并发级别）
* 概念：要注意区分这个概念和并发连接数之间的区别，一个用户可能同时会产生多个会话，也即连接数。

#### 4. 用户平均请求等待时间（每个请求的时间）
* 计算公式：处理完成所有请求数所花费的时间 /（总请求数 / 并发用户数），即
每个请求的时间 = 测试所花费的时间 /（完整请求 / 并发级别）。

#### 5. 服务器平均请求等待时间（每个请求的时间：跨所有并发请求）
* 计算公式：处理完成所有请求数所花费的时间 / 总请求数，即所花费的时间 / testsComplete 请求。
可以看到，它是吞吐率的倒数。
同时，它也 = 用户平均请求等待时间 / 并发用户数，即
每个请求的时间 / 并发级别。


### 二、什么是 Apache Bench
官网地址：[ab - Apache HTTP server benchmarking tool](http://httpd.apache.org/docs/2.4/programs/ab.html)

#### ab - Apache HTTP 服务器性能基准工具。

> AB 是 Apache 的超文本传输协议（HTTP）的性能测试工具。其设计意图是描绘当前所安装的的 Apache 的执行性能，主要是显示你安装的 Apache 的每秒可以处理多少个请求。

> AB 是 Apache 自带的压力测试工具。ab 非常实用，它不仅可以对 Apache 服务器进行网站访问压力测试，也可以对或其它类型的服务器进行压力测试。比如 nginx、tomcat、IIS 等。

> ab 是一种用于测试 Apache 超文本传输协议（HTTP）服务器的工具。apache 自带 ab 工具，可以测试
apache、IIs、tomcat、nginx 等服务器。但是 ab 没有 Jmeter、Loadrunner 那样有各种场景设计、各种图形报告和监控，只需一个命令即可，有输出描述，可以简单的进行一些压力测试。

> 总的来说 ab 工具 ab 小巧简单，上手学习较快，可以提供需要的基本性能指标，但是没有图形化结果，不能监控。因此 ab 工具可以用作临时紧急任务和简单测试。
同类型的压力测试工具还有：webbench、siege、http_load 等


### 三、使用

#### 查看 Apache 版本
Mac 系统下自带 Apache，可以使用 `apachectl -v` 命令查看 Apache 版本：
```shell
$ apachectl -v
Server version: Apache/2.4.34 (Unix)
Server built:   Aug 17 2018 18:35:43
```

#### 查看 ab 版本

```
$ ab -V   # 注意到，这里是大写的V
This is ApacheBench, Version 2.3 <$Revision: 1826891 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/
```


#### 查看当前系统的默认文件打开数：
```shell
ulimit -a
-t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             8192
-c: core file size (blocks)         0
-v: address space (kbytes)          unlimited
-l: locked-in-memory size (kbytes)  unlimited
-u: processes                       1418
-n: file descriptors                4864
```

设置打开文件数限制为 2048：
```
ulimit -n 2048
```

#### 测试命令


| 命令行参数 | 说明 |
| --- | --- |
| -n | 请求次数 |
| -c | 并发数 |
| -r | 当接收到错误时，不要退出套接字（socket） |


```bash
$ ab -n 100 -c 10 https://www.baidu.com/

# -------- Apache 版本信息 --------
This is ApacheBench, Version 2.3 <$Revision: 1826891 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.baidu.com (be patient).....done

# -------- Web 服务器信息 --------
# 请求返回 header 类型，可能是 nginx、apache、IIs 等
Server Software:        BWS/1.1
# 请求 ip 或者域名
Server Hostname:        www.baidu.com
# 请求端口，当前请求为 https 所以端口为 443，请求 http 端口 80
Server Port:            443
# HTTPS 端口协议
SSL/TLS Protocol:       TLSv1.2,ECDHE-RSA-AES128-GCM-SHA256,2048,128
TLS Server Name:        www.baidu.com

# -------- 请求文档相关信息 --------
# 请求路径
Document Path:          /
# 第一个成功返回的文档的字节大小（此为 http 响应的正文长度）
Document Length:        227 bytes

# -------- 压力测试指标 --------
# 并发请求数
Concurrency Level:      10
# 测试持续时间：从建立连接到最后接受完成总时间
Time taken for tests:   3.412 seconds
# 完成的请求数
Complete requests:      100
# 失败的请求数
Failed requests:        0
# 网络传输量：从服务器接收的字节总数
Total transferred:      89300 bytes
# HTML 内容传输量
HTML transferred:       22700 bytes
# ———— ⭐️ 吞吐率，每秒请求数（总请求数 / 总时间）
# ———— 相当于 LR 中的每秒事务数，后面括号中的 mean 表示这是一个平均值
Requests per second:    29.31 [#/sec] (mean)

# ———— ⭐️ 用户平均请求等待时间 =concurrency * timetaken * 1000 / done
# ———— 相当于 LR 中的平均事务响应时间，后面括号中的 mean 表示这是一个平均值
Time per request:       341.196 [ms] (mean)

# ———— ⭐️ 服务器平均请求处理时间 =timetaken * 1000 / done
Time per request:       34.120 [ms] (mean, across all concurrent requests)

# 平均每秒网络上的流量，可以帮助排除是否存在网络流量过大导致响应时间延长的问题
Transfer rate:          25.56 [Kbytes/sec] received

# -------- 网络连接情况 -------- 
# 网络上消耗的时间的分解
Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:      101  219 284.1    132    1271
Processing:    32   52  35.3     43     358
Waiting:       32   47  15.4     42     112
Total:        136  270 286.8    185    1310

# -------- 请求处理时间分布情况 -------- 
# ⭐️ 每个请求处理时间的分布情况，50%的处理时间在185ms内，66%的处理时间在202ms内...，
# 重要的是看90%的处理时间。
Percentage of the requests served within a certain time (ms)
  50%    185
  66%    202
  75%    229
  80%    235
  90%    337
  95%   1283
  98%   1308
  99%   1310
 100%   1310 (longest request)
```

### 关于登陆的问题

有时候进行压力测试需要用户登录，怎么办？
请参考以下步骤：

1. 先用账户和密码登录后，用开发者工具找到标识这个会话的 Cookie 值（Session ID）记下来

2. 如果只用到一个 Cookie，那么只需键入命令：
    `ab －n 100 －C key＝value http://test.com/`
3. 如果需要多个 Cookie，就直接设 Header：
    `ab -n 100 -H “Cookie: Key1=Value1; Key2=Value2” http://test.com/`



### 错误问题:`apr_socket_connect(): Operation already in progress (37)`

```
$ ab -n 1000000 -c 2000 -r http://119.3.86.178:80/
This is ApacheBench, Version 2.3 <$Revision: 1826891 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 119.3.86.178 (be patient)

Test aborted after 10 failures

apr_socket_connect(): Operation already in progress (37)
Total of 9725 requests completed
```

```shell
ulimit -n 2048
 ~   master ●  ulimit -a
-t: cpu time (seconds)              unlimited
-f: file size (blocks)              unlimited
-d: data seg size (kbytes)          unlimited
-s: stack size (kbytes)             8192
-c: core file size (blocks)         0
-v: address space (kbytes)          unlimited
-l: locked-in-memory size (kbytes)  unlimited
-u: processes                       1418
-n: file descriptors                2048
```

### 错误问题: `apr_socket_recv: Connection reset by peer (54)`

这个报错一般是由于使用的 MacOSX 默认自带的 ab 限制了并发数导致的。
解决办法：下载最新的 apache 并重新编译，备份原来的 ab 并将新编译的 ab 替换到原来的路径
参考：[MAC 上使用 ab](https://blog.csdn.net/u010860518/article/details/53309024)


### 重新安装 Apache




参考：[macOS 10.13 High Sierra Apache 安装程序：多个 PHP 版本](https://zhuanlan.zhihu.com/p/37885712)


### 附：ab 参数说明

```shell
Options are:
    -n requests     Number of requests to perform
                    在测试会话中所执行的请求个数。 默认时，仅执行一个请求，但通常其结果不具有代表意义
    -c concurrency  Number of multiple requests to make at a time
                    一次产生的请求个数。默认是一次一个
    -t timelimit    Seconds to max. to spend on benchmarking
                    This implies -n 50000
                    测试所进行的最大秒数。其内部隐含值是-n 50000。 它可以使对服务器的测试限制在一个固定的总时间以内。默认时，没有时间限制
    -s timeout      Seconds to max. wait for each response
                    Default is 30 seconds
                    单个请求最大延迟，默认为30s
    -b windowsize   Size of TCP send/receive buffer, in bytes
                    
    -B address      Address to bind to when making outgoing connections
    -p postfile     File containing data to POST. Remember also to set -T
                    包含了需要POST的数据的文件
    -u putfile      File containing data to PUT. Remember also to set -T
    -T content-type Content-type header to use for POST/PUT data, eg.
                    'application/x-www-form-urlencoded'
                    Default is 'text/plain'
                    POST数据所使用的Content-type头信息
    -v verbosity    How much troubleshooting info to print
                    设置显示信息的详细程度 - 4或更大值会显示头信息， 3或更大值可以显示响应代码(404, 200等), 2或更大值可以显示警告和其他信息
    -w              Print out results in HTML tables
                    以HTML表的格式输出结果。默认时，它是白色背景的两列宽度的一张表
    -i              Use HEAD instead of GET
                    执行HEAD请求，而不是GET
    -x attributes   String to insert as table attributes
                    设置<table>属性的字符串。 此属性被填入<table 这里 >
    -y attributes   String to insert as tr attributes
                    设置<tr>属性的字符串
    -z attributes   String to insert as td or th attributes
                    设置<td>属性的字符串
    -C attribute    Add cookie, eg. 'Apache=1234'. (repeatable)
                    对请求附加一个Cookie:行。 其典型形式是name=value的一个参数对。 此参数可以重复
    -H attribute    Add Arbitrary header line, eg. 'Accept-Encoding: gzip'
                    Inserted after all normal header lines. (repeatable)
                    对请求附加额外的头信息。 此参数的典型形式是一个有效的头信息行，其中包含了以冒号分隔的字段和值的对 (如, "Accept-Encoding: zip/zop;8bit")
    -A attribute    Add Basic WWW Authentication, the attributes
                    are a colon separated username and password.
                    对服务器提供BASIC认证信任。 用户名和密码由一个:隔开，并以base64编码形式发送。 无论服务器是否需要(即, 是否发送了401认证需求代码)，此字符串都会被发送
    -P attribute    Add Basic Proxy Authentication, the attributes
                    are a colon separated username and password.
                    对一个中转代理提供BASIC认证信任。 用户名和密码由一个:隔开，并以base64编码形式发送。 无论服务器是否需要(即, 是否发送了401认证需求代码)，此字符串都会被发送
    -X proxy:port   Proxyserver and port number to use
                    对请求使用代理服务器
    -V              Print version number and exit
                    显示版本号并退出
    -k              Use HTTP KeepAlive feature
                    启用HTTP KeepAlive功能，即, 在一个HTTP会话中执行多个请求。 默认时，不启用KeepAlive功能
    -d              Do not show percentiles served table.
                    不显示"percentage served within XX [ms] table"的消息(为以前的版本提供支持)
    -S              Do not show confidence estimators and warnings.
                    不显示中值和标准背离值， 而且在均值和中值为标准背离值的1到2倍时，也不显示警告或出错信息。 默认时，会显示 最小值/均值/最大值等数值。(为以前的版本提供支持)
    -q              Do not show progress when doing more than 150 requests
                    如果处理的请求数大于150， ab每处理大约10%或者100个请求时，会在stderr输出一个进度计数。 此-q标记可以抑制这些信息
    -l              Accept variable document length (use this for dynamic pages)
    -g filename     Output collected data to gnuplot format file.
                    把所有测试结果写入一个'gnuplot'或者TSV (以Tab分隔的)文件。 此文件可以方便地导入到Gnuplot, IDL, Mathematica, Igor甚至Excel中。 其中的第一行为标题
    -e filename     Output CSV file with percentages served
                    产生一个以逗号分隔的(CSV)文件， 其中包含了处理每个相应百分比的请求所需要(从1%到100%)的相应百分比的(以微妙为单位)时间。 由于这种格式已经“二进制化”，所以比'gnuplot'格式更有用
    -r              Don't exit on socket receive errors.
    -h              Display usage information (this message)
                    显示使用方法
    -Z ciphersuite  Specify SSL/TLS cipher suite (See openssl ciphers)
    -f protocol     Specify SSL/TLS protocol
                    (SSL3, TLS1, TLS1.1, TLS1.2 or ALL)
```


### 参考

* [CSDN：Apache Bench——ab 性能测试工具](https://blog.csdn.net/wx19900503/article/details/56847264)
* [简书：超实用压力测试工具－ab 工具](https://www.jianshu.com/p/43d04d8baaf7)
* [Mac OSX で apache bench が動かない時の対処。apr_socket_connect (): Operation already in progress](https://qiita.com/bohebohechan/items/57ccba3120fcdb6143d4)
