注：以下内容摘自《图解 HTTP》，仅作学习记录。若有断章取义之处，望阅原文。


## TCP/IP 协议族

通常使用的网络（包括互联网）是在 TCP/IP 协议族的基础上运作的。而 HTTP 属于它内部的一个子集。

TCP/IP 是互联网相关的各类协议族的总称。

TCP/IP 协议族：EEEE 802.3、IP、ICMP、PPPoE、DNS、TCP、UDP、FTP、FDDI、HTTP、SNMP...


## TCP/IP 的分层管理

TCP/IP 协议族按层次分为 4 层：**应用层**、**传输层**、**网络层**和**数据链路层**。

![TCP/IP 的分层管理](https://static01.imgkr.com/temp/813729d77b0e4fcb9d97e909a712b514.png)

* **应用层**：应用层决定了向用户提供应用服务时通信的活动。
* **传输层**：传输层对上层应用层，提供处于网络连接中的两台计算机之间的**数据传输**。
* **网络层**：网络层用来处理在网络上流动的**数据包**。数据包是网络传输的最小数据单位。该层规定了通过怎样的路径（所谓的传输路线）到达对方计算机，并把数据包传送给对方。
* **链路层（又名数据链路层， 网络接口层）**：用来处理连接网络的**硬件**部分。包括控制操作系统、硬件的设备驱动、NIC（Network Interface Card，网络适配器，即网卡），及光纤等物理可见部分（还包括连接器等一切传输媒介）。

![TCP/IP 通信传输流](https://upload-images.jianshu.io/upload_images/2648731-24fe1fc46465b025.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## IP 协议

按层次分，IP（Internet Protocol）网际协议位于**网络层**。

IP 协议的作用是把各种数据包传送给对方。而要保证确实传送到对方那里，则需要满足各类条件。其中两个重要的条件是 **IP 地址**和 **MAC 地址**（Media Access Control Address）。

**IP 地址指明了节点被分配到的地址**，**MAC 地址是指网卡所属的固定地址**。IP 地址可以和 MAC 地址进行配对。IP 地址可变换，但 MAC 地址基本上不会更改。

## ARP 协议

IP 间的通信依赖 MAC 地址。在网络上，通信的双方在同一局域网 （LAN）内的情况是很少的，通常是经过多台计算机和网络设备中转才能连接到对方。而在进行中转时，会利用下一站中转设备的 MAC 地址来搜索下一个中转目标。这时，会采用 **ARP 协议**（Address Resolution Protocol）。**ARP 是一种用以解析地址的协议，根据通信方 的 IP 地址就可以反查出对应的 MAC 地址**。


## 确保可靠性的 TCP 协议

按层次分，TCP 位于**传输层**，提供可靠的**字节流**服务。

所谓的**字节流服务**（Byte Stream Service）是指，为了**方便传输**，将大块数据分割成以**报文段**（segment）为单位的数据包进行管理。而**可靠的传输服务**是指，能够把数据准确可靠地传给对方。

为了准确无误地将数据送达目标处，TCP 协议采用了**三次握手** （three-way handshaking）策略。用 TCP 协议把数据包送出去后，TCP 不会对传送后的情况置之不理，它一定会**向对方确认是否成功送达**。握手过程中使用了 TCP 的标志（flag） —— **SYN**（synchronize） 和 **ACK**（acknowledgement）。

发送端首先发送一个带 SYN 标志的数据包给对方。接收端收到后，回传一个带有 SYN/ACK 标志的数据包以示传达确认信息。最后，发送端再回传一个带 ACK 标志的数据包，代表“握手”结束。

若在握手过程中某个阶段莫名中断，TCP 协议会再次以相同的顺序发送相同的数据包。

![TCP 协议中的三次握手策略](https://upload-images.jianshu.io/upload_images/2648731-2cd42eb2c3d7e0b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

除了上述三次握手策略，TCP 协议还有其他各种手段来保证通信的可靠性。

## 负责域名解析的 DNS 服务

**DNS**（Domain Name System）服务是和 HTTP 协议一样位于**应用层**的协议。它**提供域名到 IP 地址之间的解析服务**。

DNS 协议提供**通过域名查找 IP 地址**，或**逆向从 IP 地址反查域名**的服务。

## URI

URI 是 Uniform Resource Identifier 的缩写。  

**URI 是由某个协议方案表示的资源的定位标识符**。

**URI** 用字符串标识某一互联网资源，而 **URL** 表示资源的地点（互联 网上所处的位置）。可见 **URL 是 URI 的子集**。

![绝对 URI 的格式](https://upload-images.jianshu.io/upload_images/2648731-c34ba35f6172dac8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## HTTP 是不保存状态的协议

HTTP 是一种不保存状态，即**无状态（stateless）协议**。HTTP 协议自身不对请求和响应之间的通信状态进行保存。也就是说**在 HTTP 这个级别，协议对于发送过的请求或响应都不做持久化处理**。

HTTP/1.1 虽然是无状态协议，但为了实现期望的保持状态功能，于是引入了 **Cookie 技术**。有了 Cookie 再用 HTTP 协议通信，就可以管理状态了。



## HTTP 方法

* **GET** 方法用来请求访问已被 URI 识别的资源。
* **POST** 方法用来传输实体的主体。
* **PUT** 方法用来传输文件。
* **HEAD** 方法用于确认 URI 的有效性及资源更新的日期时间等。
* **DELETE** 方法用来删除文件
* **OPTIONS** 方法用来查询针对请求 URI 指定的资源支持的方法。
* **TRACE** 方法是让 Web 服务器端将之前的请求通信环回给客户端的方法。
* **CONNECT** 方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行 TCP 通信。



## 持久连接节省通信量

HTTP 协议的初始版本中，每进行一次 HTTP 通信就要断开一次 TCP 连接。

![持久连接节省通信量](https://upload-images.jianshu.io/upload_images/2648731-b6967f4ca023f717.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每次的请求都会造成无谓的 TCP 连接建立和断开，增加通信量的开销。

为解决上述 TCP 连接的问题，HTTP/1.1 和一部分的 HTTP/1.0 想出了**持久连接**（HTTP Persistent Connections，也称为 HTTP keep-alive 或 HTTP connection reuse）的方法。持久连接的特点是，**只要任意一端没有明确提出断开连接，则保持 TCP 连接状态**。

![持久连接旨在建立 1 次 TCP 连接后进行多次请求和响应的交互](https://upload-images.jianshu.io/upload_images/2648731-1f9e79edcfe47053.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

持久连接的好处在于减少了 TCP 连接的重复建立和断开所造成的额外开销，减轻了服务器端的负载。另外，减少开销的那部分时间，使 HTTP 请求和响应能够更早地结束，这样 Web 页面的显示速度也就相应提高了。

💡 **在 HTTP/1.1 中，所有的连接默认都是持久连接。**

![](https://tva1.sinaimg.cn/large/0081Kckwgy1glhlyge6rhj30j80hvaa9.jpg)

![](https://tva1.sinaimg.cn/large/0081Kckwgy1glhlym16l7j30iz0b7jrf.jpg)



## 管线化

持久连接使得多数请求以**管线化**（pipelining）方式发送成为可能。从前客户端发送请求后，需要等待并收到服务端的响应后，才能发送下一个请求。管线化技术出现后，不用等待响应亦可直接发送下一个请求。

这样就能够做到**同时并行发送多个请求**，而不需要一个接一个地等待响应了。

![不等待响应， 直接发送下一个请求](https://upload-images.jianshu.io/upload_images/2648731-e2f66904c94a98fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://tva1.sinaimg.cn/large/0081Kckwgy1glhm1elrlej30in0cyjrk.jpg)

## 使用 Cookie 的状态管理

HTTP 是**无状态协议**，它不对之前发生过的请求和响应的状态进行管理。也就是说，无法根据之前的状态进行本次的请求处理。

**Cookie 技术**通过在请求和响应报文中写入 Cookie 信息来控制客户端的状态。

Cookie 会根据从服务器端发送的响应报文内的一个叫做 `Set-Cookie` 的首部字段信息，通知客户端保存 Cookie。当下次客户端再往该服务器发送请求时，客户端会自动在请求报文中加入 `Cookie` 值后发送出 去。

服务器端发现客户端发送过来的 `Cookie` 后，会去检查究竟是从哪一 个客户端发来的连接请求，然后对比服务器上的记录，最后得到之前的状态信息。



## 编码提升传输效率

HTTP 在传输数据时可以按照数据原貌直接传输，但也可以在传输过程中**通过编码提升传输速率**。通过在传输时编码，能**有效地处理大量的访问请求**。但是，编码的操作需要计算机来完成，因此会**消耗更多的 CPU 等资源**。

HTTP 协议中有一种被称为**内容编码**的功能也能进行类似压缩邮件文件的操作。

内容编码指明应用在**实体内容**上的编码格式，并保持实体信息原样压缩。内容编码后的实体由客户端接收并负责解码。

![编码提升传输效率](https://static01.imgkr.com/temp/e324660eaac543b097e22bed5d89cc64.png)


## 发送多种数据的多部分对象集合

在 MIME 扩展中会使用一种称为多部分对象集合（Multipart）的方法，来容纳多份不同类型的数据。

HTTP 协议中也采纳了**多部分对象集合**，发送的一份报文主体内可含有多类型实体。通常是在图片或文本文件等上传时使用。

在 HTTP 报文中使用多部分对象集合时，需要在首部字段里加上 `Content-type` 字段。使用 `boundary` 字符串来划分多部分对象集合指明的各类实体。

多部分对象集合包含的对象如下：

* **multipart/form-data**
    在 Web 表单文件上传时使用。
* **multipart/byteranges**
    状态码 206（Partial Content，部分内容）响应报文包含了多个范围的内容时使用。
* **multipart/form-data**
    ```bash
    Content-Type: multipart/form-data; boundary=AaB03x 　
    --AaB03x
    Content-Disposition: form-data; name="field1" 　 Joe Blow
    --AaB03x
    Content-Disposition: form-data; name="pics"; filename="file1.txt"
    Content-Type: text/plain 　 
    ...（file1.txt的数据）... 
    --AaB03x--
    ```
* **multipart/byteranges**
    ```bash
    HTTP/1.1 206 Partial Content Date: Fri, 13 Jul 2012 02:45:26 GMT Last-Modified:         Fri, 31 Aug 2007 02:02:20 GMT Content-Type: multipart/byteranges;       boundary=THIS_STRING_SEPARATES
    
    --THIS_STRING_SEPARATES Content-Type: application/pdf Content-Range: bytes 500-999/8000
    ...（范围指定的数据）... 
    --THIS_STRING_SEPARATES Content-Type: application/pdf Content-Range: bytes 7000-7999/8000
    ...（范围指定的数据）... 
    --THIS_STRING_SEPARATES--
    ```


## 内容协商

**内容协商**机制是指客户端和服务器端就响应的资源内容进行交涉，然后提供给客户端最为适合的资源。内容协商会以响应资源的语言、字符集、编码方式等作为判断的基准。

包含在请求报文中的某些首部字段（如下）就是判断的基准：
* **Accept**
* **Accept-Charset**
* **Accept-Encoding**
* **Accept-Language**
* **Content-Language**

内容协商技术有以下 3 种类型：

* **服务器驱动协商**（Server-driven Negotiation）：由服务器端进行内容协商。
* **客户端驱动协商**（Agent-driven Negotiation）：由客户端进行内容协商的方式。
* **透明协商**（Transparent Negotiation）：是服务器驱动和客户端驱动的结合体，是由服务器端和客户端各自进 行内容协商的一种方法。
  

## HTTP 首部字段

HTTP 首部字段是构成 HTTP 报文的要素之一。在客户端与服务器之间以 HTTP 协议进行通信的过程中，无论是请求还是响应都会使用首部字段，它能起到传递额外重要信息的作用。

使用首部字段是为了给浏览器和服务器提供报文主体大小、所使用的语言、认证信息等内容。

![HTTP 首部字段](https://static01.imgkr.com/temp/11e113c374b2489eae1102d9344f68cb.png)

### 通用首部字段

首部字段名 | 说明
 :- | :-
Cache-Control | 控制缓存的行为
Connection | 逐跳首部、连接的管理
Date | 创建报文的日期时间
Pragma | 报文指令
Trailer | 报文末端的首部一览
Transfer-Encoding | 指定报文主体的传输编码方式
Upgrade | 升级为其他协议
Via | 代理服务器的相关信息
Warning | 错误通知

### 请求首部字段

首部字段名 | 说明
 :- | :-
Accept | 用户代理可处理的媒体类型
Accept-Charset | 优先的字符集
Accept-Encoding | 优先的内容编码
Accept-Language | 优先的语言（自然语言）
Authorization | Web认证信息
Expect | 期待服务器的特定行为
From | 用户的电子邮箱地址
Host | 请求资源所在服务器
If-Match | 比较实体标记（ETag）
If-Modified-Since | 比较资源的更新时间
If-None-Match | 比较实体标记（与 If-Match 相反）
If-Range | 资源未更新时发送实体 Byte 的范围请求
If-Unmodified-Since | 比较资源的更新时间（与If-Modified-Since相反）
Max-Forwards | 最大传输逐跳数
Proxy-Authorization | 代理服务器要求客户端的认证信息
Range | 实体的字节范围请求
Referer | 对请求中 URI 的原始获取方
TE | 传输编码的优先级
User-Agent | HTTP 客户端程序的信息


### 响应首部字段

首部字段名 | 说明
 :- | :-
Accept-Ranges | 是否接受字节范围请求
Age | 推算资源创建经过时间
ETag | 资源的匹配信息
Location | 令客户端重定向至指定URI
Proxy-Authenticate | 代理服务器对客户端的认证信息
Retry-After | 对再次发起请求的时机要求
Server | HTTP服务器的安装信息
Vary | 代理服务器缓存的管理信息
WWW-Authenticate | 服务器对客户端的认证信息

### 实体首部字段

首部字段名 | 说明
 :- | :-
Allow | 资源可支持的HTTP方法
Content-Encoding | 实体主体适用的编码方式
Content-Language | 实体主体的自然语言
Content-Length | 实体主体的大小（单位：字节）
Content-Location | 替代对应资源的URI
Content-MD5 | 实体主体的报文摘要
Content-Range | 实体主体的位置范围
Content-Type | 实体主体的媒体类型
Expires | 实体主体过期的日期时间
Last-Modified | 资源的最后修改日期时间


### 非 HTTP/1.1 首部字段

在 HTTP 协议通信交互中使用到的首部字段，不限于 RFC2616 中定义的 47 种首部字段。还有 `Cookie`、`Set-Cookie` 和 `Content-Disposition` 等在其他 RFC 中定义的首部字段，它们的使用频率也很高。


### End-to-end 首部和 Hop-by-hop 首部

HTTP 首部字段将定义成**缓存代理**和**非缓存代理**的行为，分成 2 种类型。
* **端到端首部**（End-to-end Header）
  分在此类别中的首部会转发给请求/响应对应的最终接收目标，且必须保存在由缓存生成的响应中，另外规定它必须被转发。
* **逐跳首部**（Hop-by-hop Header）
  分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发。HTTP/1.1 和之后版本中，如果要使用 hop-by-hop 首部，需提供 `Connection` 首部字段。
  
  
下面列举了 HTTP/1.1 中的**逐跳首部字段**。除这 8 个首部字段之外，其他所有字段都属于端到端首部。

* **Connection** 
* **Keep-Alive** 
* **Proxy-Authenticate** 
* **Proxy-Authorization** 
* **Trailer** 
* **TE** 
* **Transfer-Encoding** 
* **Upgrade**



## HTTP 使用的认证方式

HTTP/1.1 使用的认证方式如下所示：

* BASIC 认证（基本认证）
* DIGEST 认证（摘要认证）
* SSL 客户端认证
* FormBase 认证（基于表单认证）


## 参考

* [从 HTTP 到 HTTP/3 的发展简史](https://www.infoq.cn/article/uaLlZQ6Vu7emXbXdkJex)