## 简单介绍
Intel® Rapid Start Technology（IRST）：英特尔快速启动技术能够使您的系统从深度睡眠模式中更快地启动和运行，从而节省时间和功耗。

### 睡眠&休眠
* **睡眠模式**
![](http://upload-images.jianshu.io/upload_images/2648731-22301743950001b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

　**计算机睡眠（Sleep）**是计算机由**工作状态**转为**等待状态**的一种新的节能模式 ，是在Windows Vista与Windows7操作系统中新添加的系统功能。其开启方式是在电脑系统的开始菜单中点击电源按钮右侧的下拉箭头选择“睡眠”按钮。开启睡眠状态时，系统的所有搜索工作都会保存在硬盘下的一个系统文件，同时关闭除了内存外所有设备的供电。
　　这种模式结合了待机和休眠的所有优点。将系统切换到睡眠状态后，系统会将内存中的数据全部转存到硬盘上的休眠文件中（这一点类似休眠），然后关闭**除了内存外**所有设备的供电，让内存中的数据依然维持着（这一点类似待机）。这样，当我们想要恢复的时候，如果在睡眠过程中供电没有发生过异常，就可以直接从内存中的数据恢复（类似待机），速度很快；但如果睡眠过程中供电异常，内存中的数据已经丢失了，还可以从硬盘上恢复（类似休眠），只是速度会慢一点。不过无论如何，这种模式都不会导致数据丢失。

* **休眠模式**
![](http://upload-images.jianshu.io/upload_images/2648731-07c44b55447785af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

   **计算机休眠**，是指将内存中的数据暂时保存在硬盘中，然后**切断内存的电源**。需要从休眠状态唤醒机器时，一般只需按动电源开关，有的机器也可通过键盘按任意键唤醒。唤醒时间一般在30秒到40秒左右。    

* **Intel® Rapid Start Technology**
![](http://upload-images.jianshu.io/upload_images/2648731-d1542708579fca72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	使用**Intel rapid start technology**之后，计算机选择**睡眠模式**后，会在设定的时间内由**睡眠状态**转为特殊的低功耗状态（即**休眠模式**），但数据会保存到SSD固态硬盘的休眠分区中，然后下次开机时读取SSD中的数据。此时计算机的功耗相当于休眠的功耗，而唤醒的速度相当于睡眠，所以称之为快速唤醒技术。

## 实现条件
* 英特尔7系列芯片组；
* 足够容量的SSD固态硬盘；
* BIOS功能支持；
* Windows 7（32位 和 64位版本）以上版本的系统；
* BIOS开启AHCI模式

## 实现步骤

### 在固态硬盘上建立与本级内存容量一致的休眠分区。

1. **windows桌面** —>鼠标右击 **此电脑** —>**管理（G）** —>**存储—磁盘管理**
2. 选中SSD的磁盘 —> 鼠标右击 **压缩卷** —> 输入**压缩空间量（MB）**：数值为当前的电脑内存大小。（1GB=1024MB）。
	例如：我的电脑内存为：8BG，那么就输入8 * 1024 = 8192.   

     ![QQ图片20170203213625.png](http://upload-images.jianshu.io/upload_images/2648731-dd0445860fdb8d33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  设置完成后，磁盘管理工具的SSD磁盘中会有一块未分配的空间。

3. 数据恢复及磁盘分区软件DiskGenius下载
  [官方下载地址](http://www.diskgenius.cn/)
  百度网盘下载：[DiskGenius_Pro_4.3.0_XiaZaiBa.exe.zip](http://pan.baidu.com/s/1mi34Eko ) 密码: 9jrv

   安装完成后打开，选中SSD固态硬盘，鼠标右击 **空闲部分** 刚才未分配的磁盘—> 选择 **建立新分区**，（我这里已经分配好了，贴出出图来只是告诉你在水平柱状磁盘图这里鼠标右击）
![QQ截图20170203214755.png](http://upload-images.jianshu.io/upload_images/2648731-9375658641ab8e83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  在弹出的窗口中

  * 分区类型：选**主磁盘分区**；
  * 文件系统类型：选择**OS/2 hidden C:**；
  * 新分区大小：就是刚才压缩的空间，基本默认就好；
  * 点击**确定**；
  * 最后在DiskGenius主菜单中点击**保存更改**。
![参考一张贴吧的图](http://upload-images.jianshu.io/upload_images/2648731-a67c751299d9cd30.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 在主板BIOS中设置。
以上设置完成后，重启电脑，开机后按F2（其他主板按DEL键）进入BIOS设置，以下两个设置一般在**Advanced**选项卡中可以找到。

* 检查SATA模式是否设置为AHCI模式；
 > 注意：修改之后可能无法正常启动系统，所以，在修改AHCI模式之前，注册表修改一下。
修改方法：
单击“开始”，在“开始搜索”框中键入 regedit ，然后按 Enter。
如果显示“用户帐户控制”对话框，请单击“继续”。
找到并单击下面的注册表子项： HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Msahci
在右窗格中，右键单击“名称”列中的“Start”，然后单击“修改”。
在“数值数据”框中，键入 0 ，然后单击“确定”。

* Intel® Rapid Start Technology 设置为：Enable
设置好后保存退出重启电脑。

### 下载安装Intel® Rapid Start Technology 驱动程序
[Intel® Rapid Start Technology 驱动程序下载](https://downloadcenter.intel.com/zh-cn/download/21612/Intel-Rapid-Start-Technology)
驱动程序下载安装及使用比较简单，默认安装即可，之前的步骤没有设置好的话，可能弹出如下错误。
![b481279759ee3d6d0986406042166d224e4ade12.jpg](http://upload-images.jianshu.io/upload_images/2648731-72f5f0f6dc316e26.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
网传还可能是Microsoft .NET Framework 3.5安装出了问题，待验证。
安装完成后打开的设置界面如下：
![](http://upload-images.jianshu.io/upload_images/2648731-0ba3fc8a4d7e174e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
计时器可以设置从睡眠模式到激活IRST断电的时间，设置为0即开启睡眠模式马上进入IRST断电。

### 参考
* [百度百科：计算机睡眠](http://baike.baidu.com/link?url=BqEIB9v07KrLB7fVXSOK4euAONn9T6gW0yLOyDy9FEca-DU4nUk8l-cYdpQg0ls3Gon2_3mcioSC94rv64be_m3flVihkrbWCqvXZRDWiQhk86C8BImrszH82bJ1uF3CJSN8XXoLW-bIY75ApRJYRa)
* [百度百科：计算机休眠](http://baike.baidu.com/item/%E8%AE%A1%E7%AE%97%E6%9C%BA%E4%BC%91%E7%9C%A0)
* [Intel Rapid Start Technology 快速唤醒技术介绍](http://wenku.baidu.com/link?url=50VDm-HCjXyK-E3pjc0WuJryVILQIdfXWtiTZnarg6judxnDZhDxqsTdHpyWmW-zqGLqU31l2Wp5b2htHBm_Qk1D1hSmrjvItYrTNBan5vC)
* [【教程】正确安装、卸载英特尔快速启动技术 V2.0](http://tieba.baidu.com/p/2406397631?see_lz=1)
* [Intel Rapid Start Technology 实现5秒开机 | Jason's Blog](http://www.360doc.com/content/13/1117/08/14641369_329810630.shtml) 
* [研究了好久的UEFI，和intel rapid start technology，结果发现 @baidu贴吧](http://tieba.baidu.com/p/3276001568)
