![](http://upload-images.jianshu.io/upload_images/2648731-f28ee53fb888842a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 第3章 网络访问层

网络访问层包含了管理物理网络访问过程的服务和规范。

#### 网络访问层的特点：多样性、复杂性、透明性。

#### 协议和硬件

网络访问层管理（为物理网络准备数据所必需的）服务与功能，包括：

* 连接计算机**网络适配器**；
* 根据合适的访问方式**调整数据传输**；
* 把**数据转化**为电子流或模拟脉冲的形式，以在传输介质上进行传输；
* 对接收到的数据进行**错误检查**；
* 给发送的数据**添加错误检查信息**，从而让接收端计算机能够对数据进行错误检查。



#### 网络访问层与OSI模型

![](http://upload-images.jianshu.io/upload_images/2648731-bc9a31ff9096cd5d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



* TCP/IP模型的网络访问层 ⟺ OSI模型的物理层和数据链路层；
* **OSI的物理层**：负责把数据帧转化为适合于传输介质的比特流（即，管理和同步实际传输的电子或模拟脉冲）。
* **OSI的数据链路层**：
  1. 介质访问控制（MAC）：提供与网络适配器连接的接口。
  2. 逻辑链路控制（LLC）：对经过子网传递的帧进行错误检查，并且管理子网上通信设备之间的链路。

> NDIS & ODI
>
> **NDIS**：网络驱动程序接口规范。
>
> **ODI**：开放数据链接接口规范。
>
> **NDIS & ODI的设计目的**：让单个协议栈使用多个网络适配器，并让单个网络适配器使用多个上层协议。



#### 网络体系

网络体系（比如以太网）具有一系列的规范来管理介质访问、物理寻址、计算机与传输介质的交互。

> **以太网**只是**网络访问层**协议系统的一个例子。



##### 网络体系通信规范包含的要点：

* 访问方法；
* 数据帧格式；
* 布线类型；
* 布线规则；



##### 网络访问层体系

* **IEEE 802.3（以太网）**：在大多数办公室和家庭使用的基于线缆的网络。
* **IEEE 802.11（无线网络）**：在办公室、家庭和咖啡厅使用的无线网络技术。
* **IEEE 802.16（WiMAX）**：用于移动通信长距离无线连接的技术。
* **点到点协议（PPP）**：Modem通过电话线进行连接的技术。

![](http://upload-images.jianshu.io/upload_images/2648731-ceb7c3cdf0a9614e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 由于网络访问层封装了传输介质的细节，因此协议栈的上层可以独立于硬件进行操作。
* 协议栈的**模块化特性**使得在网络访问层里（与硬件打交道的）**软件组件**能够为（和硬件操作无关的）上层提供接口。



#### 物理寻址

网络访问层需要把**逻辑IP地址**（通过协议软件配置）与网络适配器的固定**物理地址**（MAC地址）相关联。



##### 物理地址/MAC地址：硬件通常都有预置的物理地址（由工厂固化）。

* 经过局域网传递的数据帧必须使用物理地址来标识源适配器和目的适配器。

##### 使用IP地址的原因：

* 物理地址冗长、可用性差。
* 在较高的协议层对物理地址进行编码又会破坏TCP/IP模块化带来的灵活性，因为后者要求上层协议与物理细节无关。

##### 地址解析协议

* TCP/IP使用地址解析协议（ARP）和逆向地址解析协议（RARP）把IP地址关联到网络适配器的物理地址。



#### 以太网

全部计算机共享一个传输介质。

* **载波侦听（CSMA）**：（所有计算机都监视传输介质状态，在传输之前等待线路空闲）
* **冲突检测（CD）**：（如果两台计算机尝试同时发送数据，就会发生冲突，计算机就会停止发送，等待一个随机的时间间隔再发送）





### 第4章 网际层

#### 寻址与发送

* 计算机通过网络接口设备（比如网络适配器）与网络进行通信，网络接口设备具有唯一的物理地址。
* **IP协议**：TCP/IP隐藏了物理地址，以一种逻辑化、层次化的寻址方案对网络进行组织。这种逻辑化的寻址方案由网际层的**IP协议**维护，而逻辑地址被称为**IP地址**。
* **地址解析协议（ARP）**：把IP地址映射到物理地址。



##### TCP/IP发送数据的策略：

![](http://upload-images.jianshu.io/upload_images/2648731-9bb32393e5b1775f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



1. 如果目的地址与源地址在同一个网段，源计算机就把数据包直接发送给目的计算机。IP地址被ARP解析为物理地址，数据被直接发送到目的网络适配器。

2. 如果目的地址与源地址不在一个网段上，就执行如下过程：

   a) 直接将数据报发送到网关。网关地址被ARP解析为物理地址，数据被发送到网关的网络适配器。

   b) 数据报通过网关被路由到较高级别的网段，再次重复上述过程。

   c) 数据报经过一系列网关被转发到目的网段，目的IP地址被ARP解析为物理地址，数据报被发送到目的网络适配器。



#### 网际协议（IP）

> 用于数据报的寻址、传递和路由。

IP协议提供一种分层的、与硬件无关的寻址系统，具有在复杂的路由式网络中传递数据所需的服务。TCP/IP网络上的每个网络适配器都有一个唯一的IP地址。





#### IP报头字段

每个IP数据报都以一个IP报头开始。

* 源计算机的TCP/IP软件构造这个IP报头。
* 目的计算机的TCP/IP软件利用IP报头中封装的信息处理数据。

IP报头字段：

![](http://upload-images.jianshu.io/upload_images/2648731-ba41d14779438797.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### IP寻址

* IP地址是一个32位的地址，被分为4个8位段。
* 8位二进制可以表示0～255之间的数值。
* **点分十进制形式**：例如：192.168.131.14



**IP地址：网络ID+主机ID**

##### 基于分类的寻址系统

| 地址类别 | 网络ID | 主机ID |
| ---- | ---- | ---- |
| A类地址 | 前8位  | 后24位 |
| B类地址 | 前16位 | 后16位 |
| C类地址 | 前24位 | 后8位  |



![](http://upload-images.jianshu.io/upload_images/2648731-d5c301e631f3ac47.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* A类地址：0~127

前8位最小：00000000
前8位最大：01111111

* B类地址：128～191

前16位最小：10000000.00000000.

前16位最大：10111111.11111111.

* C类地址：192~223

前24位最小：11000000.00000000.00000000.

前24位最大：11011111.11111111.11111111.



#### 32位二进制地址转换为点分十进制形式

二进制：01011001000111011100110000011000

![](http://upload-images.jianshu.io/upload_images/2648731-a4ac2a901419921b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

十进制：89.29.204.24



#### 十进制数值转化为二进制八位组

**二因式分解**：235

235÷2=117，余1

117÷2=58，  余1

58÷2=29，    余0

29÷2=14，    余1

14÷2=7，      余0

7÷2=3，        余1

3÷2=1，        余1

1，余1

得：11101011



#### 地址解析协议ARP

> 网际层的重要协议，用于获取与IP地址相对应的物理地址。ARP缓存记录着最近解析的物理地址和IP地址对。

* 地址解析协议（ARP）把IP地址映射为物理地址。
* 主机必须知道目的网络适配器的物理地址才能向它发送数据。
* TCP/IP的实现方式让ARP和关于物理地址转换的任何细节对于用户来说都是透明的。
* 网段上每台主机上都保存着一个ARP表或ARP缓存表，它会清除和更新。

![](http://upload-images.jianshu.io/upload_images/2648731-7236d8b0dc678f5f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





#### 逆向ARP（RARP）

> 根据物理地址返回IP地址，一般被远程启动的无盘工作站使用。

ARP：IP地址➡️物理地址

RARP：物理地址➡️IP地址

BOOTP：用来远程启动计算机或其它网络设备的协议。



#### Internet控制消息协议（ICMP）

> 路由器利用Internet控制消息协议（ICMP）发送消息来告知源IP关于路由的问题。

常见的ICMP消息：

* Echo Request（回显请求）和Echo Reply（回显应答）；（ping 命令）
* Source Quench（源抑制）；
* Destination Unreachable（目的不可到达）；
* Time Exceeded（超时）；（TTL = 0）
* Fragmentation Needed（需要分段）；



#### 网际层其他协议

边界网关协议（BGP）、路由信息协议（RIP）、IPSec协议（IPv6网络）





### 第5章 子网划分和CIDR

#### 子网

##### 1⃣️ IP地址分类系统的不足

* IP地址分类系统把IP地址分为「网络ID」和「主机ID」。
* 灵活性、适应性不够：现实世界中，网络具有各种规模，无法对地址空间进行任何逻辑细分。



示例：对于一个A类地址来说，它可以容纳1600万台主机。当数据报到达此网关（99.0.0.0 ）后，再考虑它在这个地址空间中是如何传递时就会变得非常复杂。



![](http://upload-images.jianshu.io/upload_images/2648731-8bc097fd062104bb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 2⃣️ 子网划分

* 子网划分可以利用IP地址系统把物理网络分解为更小的逻辑实体——子网。
* 子网划分就是在网络ID之下提供第2层逻辑组织。
* **子网掩码**：区分地址中的子网ID、主机ID。

![](http://upload-images.jianshu.io/upload_images/2648731-3a66be1594645c74.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 子网掩码中的 1 表示IP地址中属于网络ID或子网ID的位。
* 子网掩码中的 0 表示IP地址中属于主机ID的位。

##### 进行子网划分与不进行子网划分的比较：

![](http://upload-images.jianshu.io/upload_images/2648731-3f817b52e80243df.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 数据报在子网网络中的传输：

![](http://upload-images.jianshu.io/upload_images/2648731-8e1349e011363147.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 将子网掩码转换为点分十进制标记

* 子网掩码同样是32位的十进制数值。

子网掩码：11111111111111111111111100000000

点分十进制：255.255.255.0



示例：

B类网络：129.100.0.0 

子网掩码：255.255.255.0



B类网络：  10000001.01100100.00000000.00000000

子网掩码：11111111.11111111.11111111.00000000



去除全0和全1的情况：

它的子网可分配数值范围是：129.100.1.0 ~ 129.100.254.0 。即可分配254个字网。

而每个子网可分配的主机数为254台。



#### 无类别域间路由（CIDR）

子网划分是在A类、B类、C类地址的基础上进行的子网划分。

而 CIDR 技术则是直接进行子网划分，不再依赖特定的网络类型，使用CIDR前缀定义IP地址中的多少位作为网络ID的位数。

例如：205.123.196.183/25

其中 “/25” 表示地址中的前25位作为网络ID。





### 第6章 传输层

传输层为网络应用程序提供了一个接口，并且能够对网络传输提供可选的错误检测、流量控制和验证功能。

#### 传输层需要提供的功能

* 为网络应用程序提供接口；
* 多路复用／多路分解机制；
* 错误检测、流量控制和验证功能；
  * 传输控制协议（TCP）；
  * 用户数据报协议（UDP）；



#### 面向连接的协议&无连接的协议

面向连接的协议：会在通信计算机之间建立并维护一个连接，并且在通信过程中监视连接的状态。

无连接的协议：以单向方式向目的发送数据报，不承担通知目的计算机关于数据发送的职责。



#### 端口&套接字

端口：预定义的内部地址，充当从应用程序到传输层或是从传输层到应用程序之间的通路。

套接字：由IP地址和端口号组成的地址。

示例：111.121.131.141.21 表示：指向IP地址为111.121.131.141的计算机的端口21。



#### 多路复用&多路分解

多路复用：把多个来源的数据导向一个输出。

多路分解：把从一个来源接收的数据发送到多个输出。

![](http://upload-images.jianshu.io/upload_images/2648731-061d69ffc123feb1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### TCP&UDP

路由器转发但不处理传输层数据。

![](http://upload-images.jianshu.io/upload_images/2648731-40fc3edc5bd0f435.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### TCP

* 面向连接的协议；
* 提供全面的错误控制和流量控制；
* 可靠性。

TCP的重要特性：

* 面向流的处理；
* 重新排序；
* 流量控制；
* 优先级与安全；
* 适当的关闭；
* TCP的连接分主动打开／被动打开。
* 三次握手信号。

TCP数据格式：

![](http://upload-images.jianshu.io/upload_images/2648731-ee722129c9c77b1f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





##### UDP

* 无连接的协议
* 有限的的错误检验功能。
* 简单、速度快。



#### 防火墙和端口

防火墙是一个系统，保护局域网不被来自Internet的未授权用户攻击。

基本特性：阻断对特定TCP和UDP端口的访问。





### 第7章 应用层

#### 什么是应用层？

* TCP/IP的应用层是一些能够意识到网络的软件组织，向TCP和UDP端口发送和接收数据。



#### TCP/IP应用层与OSI

TCP/IP应用层对应于OSI模型的应用层、表示层和会话层。

![](http://upload-images.jianshu.io/upload_images/2648731-7c0edf2440a8190a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 应用层：OSI的应用层包含的组件为用户应用程序提供服务并支持网络访问。
* 表示层：表示层把数据转化为与平台无关的格式，并处理加密和数据压缩。
* 会话层：负责管理联网计算机上应用程序之间的通信，提供一些传输层不具备、与连接相关的功能，比如名称识别和安全。



#### 网络服务

应用层中的很多组件都是网络服务。这些服务对于软件协议的运行可能并不是必须的，更多的是为用户提供方便，或是让本地操作系统连接到网络。

![](http://upload-images.jianshu.io/upload_images/2648731-6147330eccd01ffb.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##### 文件和打印服务

* 打印服务器负责操作打印机，而文件服务器操作数据存储设备。

##### 名称解析服务

* DNS系统。

##### 远程访问服务

* Telnet、SSH、屏幕共享工具、重定向器。

##### Web服务

* 超文本传输协议（HTTP）是应用层协议，是万维网生态系统的核心。





##### TCP/IP工具

![](http://upload-images.jianshu.io/upload_images/2648731-083b3f6a1a7a6931.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/2648731-ac2e5760dcc3d734.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)







> THE END.
