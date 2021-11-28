### 网络拓扑图

![](https://upload-images.jianshu.io/upload_images/2648731-e28ef8605a42ae58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




1. 首先打开 Windows 设置 > 控制面板 > 网络和 Internet > 网络和共享中心 > 更改适配器设置 ：

   ![image](http://upload-images.jianshu.io/upload_images/2648731-ec85dc3d4df38ab5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   这边有两点需要注意的地方：

   一：主机连到外网的网卡（这里是 VMware Network Adapter VMnet1）需要鼠标右击属性 > 设置 Internet 连接共享，添加 VMware Network Adapter VMnet8：

   ![image](http://upload-images.jianshu.io/upload_images/2648731-0339c560e6ef6508.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   添加共享之后，可以去 Windows 的 cmd 控制台查看网卡为 VMnet8 分配的 IP 地址：

   使用命令 `ipconfig` 即可查看：

   ![image](http://upload-images.jianshu.io/upload_images/2648731-8d99d8ca7674b76c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



   二：VMware Network Adapter VMnet8 网卡属性设置 Internet 协议版本（TCP/IPV4）属性如下：

   * IP 地址就是上一步查询到的 IPv4 地址：192.168.137.1

   ![image](http://upload-images.jianshu.io/upload_images/2648731-0969ade9a4af4b89.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 设置 VMware 菜单栏的编辑 > 虚拟网络编辑器 :

   ![image](http://upload-images.jianshu.io/upload_images/2648731-989fe0881a62d872.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   ![image](http://upload-images.jianshu.io/upload_images/2648731-2e975abdfdf31ab0.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   * 因为分配给 VMnet8 的IP地址是 192.168.137.1，所以子网IP要设置为192.168.137.0

   * 取消勾选 DHCP 之前，可以先点击右边的 **DHCP设置**，查看 Ubuntu 虚拟机可用的IP地址范围：

     ![image](http://upload-images.jianshu.io/upload_images/2648731-acdb04f28f6bbb46.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

     可以看到可用的IP地址范围是：192.168.137.128 ～ 192.168.137.254。所以说 **IP 地址的值并不是完全任意设置的**。

   * 点击 NAT 设置，主要是查看并设置网关IP：192.168.137.2。然后这里的子网掩码、网关IP都需要记住，因为接下来设置静态IP会用到。下面的端口转发设置可以先不用管，下面会说到。

     ![image](http://upload-images.jianshu.io/upload_images/2648731-abdf0f7bb31ff698.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 编辑虚拟机设置，网络适配器设置为自定义 VMnet8（NAT模式）：

   💡 这一步需要在虚拟机关机状态下才能设置。

   ![image](http://upload-images.jianshu.io/upload_images/2648731-d753e3e1961d8d59.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4. 打开 Ubuntu虚拟机，设置该虚拟机的静态IP地址，编辑 `/etc/network/interfaces` 配置文件。

   打开控制台，命令行中输入：`sudo vim /etc/network/interfaces` ，编辑添加如下所示：

   ![image](http://upload-images.jianshu.io/upload_images/2648731-1c05743eb63e8300.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   * ens33 是虚拟器的网卡名称，可以通过 `ifconfig` 查看你的这个网卡名称是啥，因为不一定是 eth0。
   * address 是IP地址，因为IP地址的设置范围是192.168.137.128 ～ 192.168.137.254，所以可以设置为 192.168.137.130.
   * netmask 是子网掩码。
   * gateway 是网关地址。
   * broadcast 是广播地址，不知道的话可以在控制台输入`ifconfig` 查看。
   * dns-nameservers 是 DNS 地址。

5. 编辑 `/etc/resolv.conf` DNS 配置文件。命令行输入`sudo vim /etc/resolv.conf`，配置 DNS 地址为阿里云的地址：

   ![image](http://upload-images.jianshu.io/upload_images/2648731-94141a0908f3c32a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   > 注意：在相对较早的版本中，你需要设置 / etc 下的 resolv.conf 文件，并加入 nameserver，这样才可以连接互联网。但在 Ubuntu 12.04 之后，已经不推荐这种方式了，因为无论你想该配置文件中设置什么值，重新启动之后都会被还原为初始状态。推荐的做法是直接在 interfaces 配置文件中加入 dns-nameserver <网关 IP> 这一行。

   编辑完成后重启网卡：  `sudo /etc/init.d/networking restart`，或者重启虚拟器：`sudo reboot`。

   最后使用 ping 命令验证。



### 其它问题

#### 更改 Ubuntu 键盘配置

```shell
# 更改为标准 104 键盘
sudo dpkg-reconfigure keyboard-configuration
```



#### 使用 vi 命令编辑配置文件时，键盘输入i底部不显示 -- INSERT -- 来显示编辑器模式

```shell
# 安装并使用 vim
sudo apt-get install vim
```



#### su

1. 编辑 /etc/NetworkManger/NetworkManger.conf 文件
   `sudo vim /etc/NetworkManger/NetworkManger.conf`
   将 `managed=false` 修改为 `managed=true`。
2. 重启 network-manager service:
   `sudo service network-manager restart`



#NAT模式实现局域网物理机与虚拟机的互通访问

这里就是上面略过的端口转发设置，不难理解，参考如下：

* [ NAT 模式实现局域网物理机与虚拟机的互通访问](https://blog.csdn.net/u011031430/article/details/77131841)
* [局域网访问电脑中 VMware 虚拟机](https://blog.csdn.net/u012268339/article/details/61204801)

#### 虚拟机所属电脑的防火墙设置

端口转发设置好之后，同一个局域网内的主机应该可以访问 Ubuntu 上运行的 Server，或者使用 SSH 登录，如果不行或者 ping 不通的话，你可能需要稍微关注一下 Ubuntu 所在的 windows 主机防火墙设置，需要添加入站规则以打开端口：

![image](http://upload-images.jianshu.io/upload_images/2648731-a1378986d9037c67.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 桥接模式

NAT模式中，虚拟机和宿主机不是在同一个网段的，而使用桥接模式则可以让虚拟机和宿主机在同一个网段中。

注意点：

1. 宿主机需要勾选 VMware Bridge Protocol（更改适配器设置-VMware Network Adapter VMnet1 属性 - 网络 - 勾选 VMware Bridge Protocol）。
2. VMware 设置：虚拟网络编辑器添加桥接模式网络。
3. 虚拟机网络适配器设置：网络连接勾选「桥接模式」。
4. 打开虚拟机，设置IP地址需要和宿主机在同一个网段内，网关IP地址需要和宿主机相同。
5. 重启虚拟机网络适配器或重启电脑即可。



### 参考

* [~~Vmware 虚拟机设置静态 IP 地址~~](https://www.cnblogs.com/chengssblog/p/6531964.html) | 文章里的修改网络配置文件目录指的应该是CentOS系统，而Ubuntu下应该是 /etc/network/interfaces 文件。
* [Ubuntu 的网络配置文件](http://blog.163.com/yi_yixinyiyi/blog/static/136286889201122221528757/)
* [Linux公社: 为 VMware 虚拟机内安装的 Ubuntu 16.04 设置静态 IP 地址 | 2017-04-24](https://www.linuxidc.com/Linux/2017-04/143102.htm)
* [简书: 为 VMware 虚拟机内安装的 Ubuntu 16.04 设置静态 IP 地址](https://www.jianshu.com/p/625de9911bfb)
* [How to Configure Ubuntu Server 16.04 to Use a Static IP Address](https://graspingtech.com/ubuntu-server-16-04-static-ip/)

