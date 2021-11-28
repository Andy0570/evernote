AWS 的**弹性计算云**（Elastic Compute Cloud，EC2）服务用来提供虚拟服务器。

虚拟服务器是一台物理服务器的一部分。物理服务器通过软件来隔离其上的各个虚拟服务器。一台虚拟服务器由CPU、内存、网络接口和存储组成。物理服务器也称为**宿主服务器**（host serverguest），其上运行的虚拟服务器称为**客户机**（guest）。**虚拟化管理器**（hypervisor）负责孤立各个客户机并调度它们对硬件的请求。

* AWS 使用 Xen，一个开源的虚拟机管理程序，作为 EC2 服务的底层技术。
* HVM 是最新也是最快的虚拟化类型，HVM 技术是使用硬件辅助的虚拟化技术。

参考：[AWS 文档：什么是 Amazon EC2](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/concepts.html)

## 1⃣️ 启动虚拟服务器

步骤：
1. 选择操作系统（根据需求，选择合适的实例类型和家族）；
2. 选择虚拟服务器的尺寸；
   参考：[AWS 文档：AWS EC2 实例类型](https://amazonaws-china.com/cn/ec2/instance-types/)
3. 设置实例详细信息、存储、防火墙和标签；
   * 标签：清晰的组织分类可以营造整洁的环境。在 AWS 平台上，使用标签可以帮助用户很好地组织资源。
4. 检查输入并为 SSH 选择一个密钥对；
   * 在 AWS 上运行 Linux 的虚拟服务器强制使用 SSH 密钥访问方式。

## 2⃣️ 连接到虚拟服务器

创建一个免费实例，使用 SSH 方式远程连接到实例：

![连接到实例](http://upload-images.jianshu.io/upload_images/2648731-465ed463d40923f1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3⃣️ 安装和运行软件

使用程序包管理软件 apt 下载和安装软件。
略。。。

## 4⃣️ 监控和调试虚拟服务器
### 4.1 显示虚拟服务器的日志

在 Web 管理控制台上查看服务器的日志：【操作】- 【实例设置】- 【获取系统日志】：

![查看服务器日志](http://upload-images.jianshu.io/upload_images/2648731-62221f982b41eeb9.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4.2 监控虚拟服务器的负载

2.1 在 Web 管理控制台上查看服务器的负载情况：选择【监控】标签页，查看 CloudWatch 监控指标。

2.2 在控制台查看虚拟服务器的 CPU 和内存信息

```shell
$ cat /proc/cpuinfo # 查看 CPU
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 63
model name	: Intel(R) Xeon(R) CPU E5-2676 v3 @ 2.40GHz
stepping	: 2
microcode	: 0x3c
cpu MHz		: 2400.062
cache size	: 30720 KB
physical id	: 0
siblings	: 1
core id		: 0
cpu cores	: 1
apicid		: 0
initial apicid	: 0
fpu		: yes
fpu_exception	: yes
cpuid level	: 13
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx rdtscp lm constant_tsc rep_good nopl xtopology eagerfpu pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm invpcid_single kaiser fsgsbase bmi1 avx2 smep bmi2 erms invpcid xsaveopt
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf
bogomips	: 4800.12
clflush size	: 64
cache_alignment	: 64
address sizes	: 46 bits physical, 48 bits virtual
power management:

$ free -m # 查看内存信息
              total        used        free      shared  buff/cache   available
Mem:            990         664          85          11         240         130
Swap:             0           0           0
```

## 5⃣️ 更改虚拟服务器的容量

要调整的虚拟服务器的容量，首先需要先**停止**（⚠️ 「停止」不是「终止」）正在运行的实例，然后再**更改实例类型**。


## 6⃣️ 在另一个数据中心开启虚拟服务器

* AWS 为全球提供数据中心，要使互联网上的请求获得低延迟，为主要用户选择一个最近的数据中心是很重要的。
* AWS 的各个区域间是完全独立的。典型情况下，一个区域由两个或更多位于同一地区的数据中心组成。

## 7⃣️ 分配一个公有 IP 地址

每个虚拟服务器都会自动连接到一个公有IP地址。但是，每次启动或停止一台虚拟服务器，公有IP地址就改变了。如果想要用一个固定IP地址运行一个应用程序，这样做就不可行了。AWS提供一项服务叫作**弹性IP地址（Elastic IP address）**来**分配固定的公有 IP 地址**。

在默认情况下，所有 AWS 账户在每个区域最多可拥有五 (5) 个弹性 IP 地址，因为公有 (IPv4) Internet 地址是稀缺的公共资源。我们大大鼓励您主要使用弹性 IP 地址，以便在实例发生故障的情况下能够将该地址映射到另一实例，并能够将 DNS 主机名用于所有其他节点间通信。

> ⚠️
>
> IPv4 地址是稀缺资源。为了防止浪费弹性 IP 地址资源，AWS 将对没有关联到任何服务器的弹性 IP 地址收费。😱😱😱

参考：[AWS 文档：弹性 IP 地址](https://docs.aws.amazon.com/zh_cn/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html)



## 8⃣️ 向虚拟服务器添加额外的网络接口

用户可以向一台虚拟服务器添加多个网络接口，并且控制关联到这些网络接口的私有 IP 地址和公有 IP 地址。用户可以使用额外的网络接口关联第二个公有 IP 地址到自己的虚拟服务器上。

可以让一台虚拟服务器通过两个不同的公有 IP 地址来访问，这样就可以根据公有 IP 地址提供两个不同的网站服务：

![多个网络接口](http://upload-images.jianshu.io/upload_images/2648731-62ce9c1c0e39997d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

命令行查看附加的网络接口：

```bash
ifconfig
```

## 9⃣️ 优化虚拟服务器的开销

| 服务器计费方式 | 优点                                     | 缺点                 | 备注 |
| -------------- | ---------------------------------------- | -------------------- | ---- |
| 按需实例       | 灵活性最大                               | 价格最高、可靠性中等 |      |
| 竞价型实例     | 价格随供求波动，价格最低                 | 灵活性中等、可靠性低 |      |
| 预留实例       | 包年包月，有折扣优惠，价格中等、可靠性高 | 灵活性最低           |      |

> 💡
>
> 用户如果使用预留实例（全部前期费用，3年试用期），相比按需实例，最多可节省 60% 的费用。
>
> 预留实例以灵活性换取成本减少。
>
> 推荐：开始时使用按需服务器，然后切换到按需与预留服务器混合的模式！
>
> ---
>
> 竞价型实例适合运行异步任务，如数据分析或者对媒体资源进行编码。

