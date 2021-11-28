[TOC]

## Linux 磁盘管理
* 硬盘分为 IDE 硬盘、SCSI 硬盘、SATA 硬盘。
  * IDE 接口的设备被称为 hd，SCSI 和 SATA 接口的设备则被称为 sd。
  * SCSI 或者 SATA 接口设备中，不同硬盘的命名：sda、sdb、sdc ...
  * 一块硬盘上只能存在4个主分区：sda1、sda2、sda3、sda4。逻辑分区则从5开始标识。
  * **操作系统主体**可以安装在**主分区**，也可以安装在**逻辑分区**，但**引导程序**必须安装在**主分区**内。
* 操作系统通过文件系统对磁盘进行操作。
* 文件系统：一种对物理空间的组织方式，通常在格式化硬盘时创建。

![image](http://upload-images.jianshu.io/upload_images/2648731-5d9226420a1c1a67.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 交换分区（swap）：当实际内存不够用时，操作系统会从内存中取出一部分暂时不用的数据，放在交换分区中。一般来说，swap 分区容量应该要大于物理内存大小，但目前不能超过 2GB。


### `df ` 查看磁盘容量

`df` 查看已挂载磁盘的总容量、使用容量、剩余容量等，可以不加任何参数，默认是按 k 为单位显示的。

该命令可以收集和整理当前已经挂载的全部文件系统的一些重要的统计数据。

| 参数 | 含义                 |
| ---- | -------------------- |
| -i   | 使用 inodes 显示结果 |
| -h   | 系统自动调节单位     |
| -k   | 以 KB 为单位输出     |
| -m   | 以 MB 为单位输出     |

```shell
$ df -h  
# 文件系统      1K-块        已用  可用      已用%  挂载点
Filesystem      Size  Used Avail Use% Mounted on
udev            3.9G     0  3.9G   0% /dev
tmpfs           799M   84M  715M  11% /run
/dev/vda1        40G  6.9G   31G  19% /
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           799M     0  799M   0% /run/user/0

$ df -t ext4    ## ⭐️⭐️⭐️ 显示所有已挂载的 ext4 文件系统
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/xvda1       8065444 1879988   6169072  24% /

$ df -ht ext4
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        40G  6.9G   31G  19% /
```

### `du`  查看目录容量

`du`：用来查看某个目录所占空间大小。

语法：`du [-abckmsh] [文件或者目录名]`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -a   | 列出所有文件与目录容量。如果不加任何选项和参数只列出目录（包含子目录）大小。 |
| -b   | 列出的值以 bytes 为单位输出，默认是以 Kbytes                 |
| -c   | 最后求和                                                     |
| -k   | 以 KB 列出容量内容                                           |
| -m   | 以 MB 列出容量内容                                           |
| -s   | 列出总量，不列出每个目录容量。                               |
| -h   | 系统自动调节单位                                             |

```shell
$ du -sh ./                 ## 查看当前目录大小
$ du -sh dictionary_name    ## 查看某一指定目录大小
$ du -a                     ## 将每一个文件大小都列出
```

### `mount` 挂载一个文件系统

管理存储设备的第一步是把设备连接到文件系统树中。这个过程叫做**挂载**，允许设备参与到操作系统中。
一个挂载点就是文件系统树中的一个目录。

* 在挂载某个分区前需要先建立一个挂载点，这个挂载点是以**目录**的形式出现的。
* 一旦把某一个分区挂载到了这个挂载点（目录）下，那么再往这个目录写数据时，则都会写到该分区中。
* 注意：在挂载该分区前，挂载点（目录）下必须是个空目录。
* Linux 把所有的设备文件都放在 **/dev** 目录下（device）.
* mount 挂载好新硬盘后，还需要编辑 **/etc/fstab** 文件让系统重启时自动挂载设备。

```shell
$ sudo mkdir /mnt/cdrom            # 新建一个目录
$ sudo mount /dev/cdrom /mnt/cdrom # 挂载光盘至这个新建的目录
...
$ cd /                             # 卸载光盘前必须退出光盘所在的目录，即 /mnt/cdrom 目录
$ sudo umount /dev/cdrom           # 卸载光盘
```

`mount` 命令被用来挂载文件系统。执行这个不带参数的命令，将会显示一系列当前挂载的文件系统：
```shell
$ mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=4067024k,nr_inodes=1016756,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=817456k,mode=755)
/dev/vda1 on / type ext4 (rw,relatime,errors=remount-ro,data=ordered)
...

# 列表格式：
# 设备 on 挂载点 type 文件系统类型（可选的）
```

挂载一个新的 CD-ROW 设备：
```shell
# 1. 创建一个新目录
$ mkdir /mnt/cdrom

# 2. 将 CD-ROW 挂载到一个新的挂载点上。-t 选项用来指定文件系统类型：
$ mount -t iso9660 /dev/hdc /mnt/cdrom

# 3. 通过新的挂载点查看 CD-ROM 内容
$ cd /mnt/cdrom
$ ls -l
```

### `umount` 卸载一个文件系统

```
$ umount /dev/hdc

# -r, 指导 umount 在无法卸载文件系统时，尝试以只读方式重新载入
$ sudo umount -r /dev/sda1    
```
> ⚠️  文件系统只有在没有被使用的情况下才可以被卸载。

### 确定设备名称


| 模式 | 设备 |
| --- | --- |
| /dev/fd | 软盘驱动器 |
| /dev/hd | 老系统中的 IDE(PATA) 磁盘。典型的主板包含两个 IDE 连接器或者是通道，每个连接器带有一根缆线，每根缆线上有两个硬盘驱动器连接点。缆线上的第一个驱动器叫做主设备，第二个叫做从设备。设备名称这样安排，/dev/hdb 是指第一通道上的主设备名；/dev/hdb 是第一通道上的从设备名；/dev/hdc 是第二通道上的主设备名，等等。末尾的数字表示硬盘驱动器上的分区。例如，/dev/hda1 是指系统中第一硬盘驱动器上的第一个分区，而 /dev/hda 则是指整个硬盘驱动器。 |
| /dev/lp | 打印机 |
| /dev/sd | CSI 磁盘。在最近的 Linux 系统中，内核把所有类似于磁盘的设备（包括 PATA/SATA 硬盘， 闪存，和 USB 存储设备，比如说可移动的音乐播放器和数码相机）看作 SCSI 磁盘。 剩下的命名系统类似于上述所描述的旧的 / dev/hd 命名方案。 |
| /dev/sr | 光盘（CD/DVD 读取器和烧写器） |

如果你工作的系统不能自动挂载可移动的设备，你可以使用下面的技巧来决定当可移动设备连接后，它是怎样被命名的。首先，启动一个实时查看文件 /var/log/messages：
```
$ sudo tail -f /var/log/messages

...
Jul 23 10:07:59 linuxbox kernel: sdb: sdb1
Jul 23 10:07:59 linuxbox kernel: sd 3:0:0:0: [sdb] Attached SCSI
removable disk
...
# 这个设备名称是 /dev/sdb 指整个设备，/dev/sdb1 是这个设备的第一分区。

# 挂载闪存驱动器
$ sudo mkdir /mnt/flash
$ sudo mount /dev/sdb1 /mnt/flash
$ df
```

### `fsck` 检查和修复一个文件系统
除了检查文件系统的完整性之外，fsck 还能修复受损的文件系统，其成功度依赖于损坏的数量。

语法：` fsck [-Aar] [分区]`

| 参数 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| -A   | 加该参数时，后不需要跟分区名作为参数。它会自动检查 /etc/fstab 文件下的所有分区（开机过程中就会执行一次该操作） |
| -a   | 自动修复检查到有问题的分区                                   |
| -r   | 当检查到有坏道的分区时会让用户决定是否修复                   |

```
# 检测文件系统
$ sudo fsck /dev/vda1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/vda1 is mounted.
e2fsck: Cannot continue, aborting.
```


### `fdisk` Linux 下的硬盘分区工具
`fdisk` 程序允许我们直接在底层与类似磁盘的设备（比如说硬盘驱动器和闪存驱动器）进行交互。使用这个工具可以在设备上编辑，删除，和创建分区。

语法：`fdisk [-l] [设备名称]`
* -l ：后边不跟设备名会直接列出系统中所有的磁盘设备以及分区表，加上设备名会列出该设备的分区表。
* 如果不加 -l 则进入另一个模式，在该模式下，可以对磁盘进行分区操作。

| 分区常用参数 | 缩写形式 | 含义                                  |
| ------------ | -------- | ------------------------------------- |
| print        | p        | 显示当前分区设置                      |
| new          | n        | 创建一个新分区                        |
| type         | t        | 设置分区类型                          |
| delete       | d        | 删除一个分区                          |
| write        | w        | 把分区信息写入硬盘                    |
| quit         | q        | 退出                                  |
| menu         | m        | 显示 fdisk 所有可用的命令及其简要介绍 |

说明：

* Linux 中最多只能创建 4 个主分区，一旦创建 4 个主分区后就不能增加任何分区了。
* swap 分区最大不能超过 2GB。swap 分区类型号：82。
* 最多只能创建一个扩展分区。扩展分区下的逻辑分区最多可以创建10个（hdb5-hdb15）。IDE 的硬盘（类似于 hda, hdb, hdc 等）。
* 只有在使用 **write** 命令之后，硬盘上的信息才会真正被改变。

```
# 卸载设备
$ sudo umount /dev/sdb1
$ sudo fdisk /dev/sdb
...
【修改分区号。。。】
```


参考：
* [AWS: EC2 Linux 添加硬盘 2014](http://blog.51cto.com/beanxyz/1529931)
* [AWS: EC2 Linux 扩展硬盘 2014](http://blog.51cto.com/beanxyz/1532125)



### `mkfs` 创建文件系统
`mkfs`（"make file system" 的简写）能创建各种格式的文件系统。
```shell
# 创建一个 ext3 文件系统，/dev/sdb1 是要格式化的分区设备名。
$ sudo mkfs -t ext3 /dev/sdb1

# 把设备格式化为 FAT32 文件系统
$ sudo mkfs -t vfat /dev/sdb1
```

### `mkfs.ext2`、`mkfs.ext3`、`mke2fs` 格式化 Linux 硬盘分区



| 常用参数 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| -b       | 分区时设定每个数据区块占用空间大小，目前支持 1024, 2048 以及 4096 bytes 每个块 |
| -i       | 设定 inode 大小                                              |
| -N       | 设定 inode 数量，有时使用默认的 inode 数不够用，所以要自定设定 inode 数量 |
| -c       | 在格式化前先检测一下磁盘是否有问题，加上这个选项后会非常慢   |
| -L       | 预设该分区的标签 label                                       |
| -j       | 建立 ext3 格式的分区，如果使用 mkfs.ext3 就不用加这个选项了  |

示例：

```shell
mkfs.ext3 -b 4096 -i 4096 /dev/hdb2
mkfs.ext3 -L label2 /dev/hdb2    # 自定义分区label标签
```

### `e2label`  查看或者修改分区的标签（label）

```shell
e2label /dev/hdb2    # 查看分区标签
e2lable /dev/hdb2 label1    # 修改分区标签
```

### `blkid` 定位/打印块设备的属性

### `fdformat` 格式化一张软盘

```
# 1. 对这张软盘执行低级格式化
$ sudo fdformat /dev/fd0

# 2. 创建一个 FAT 文件系统
$ sudo mkfs -t msdos /dev/fd0

# 3. 挂载设备
...
```


### `dd` 把面向块的数据直接写入设备
把数据块从一个地方复制到另一个地方:
```
dd if=input_file of=output_file [bs=block_size [count=blocks]]

# 把第一个驱动器中的所有数据复制到第二个驱动器中
dd if=/dev/sdb of=/dev/sdc

# 把某个驱动器上的内容复制到一个普通文件中供以后恢复或复制数据
dd if=/dev/sdb of=flash_drive.img

# 制作一张现有 CD-ROM 的 iso 映像，
# 使用 dd 命令来读取 CD-ROW 中的所有数据块，并把它们复制到本地文件中
dd if=/dev/cdrom of=ubuntu.iso
```


### `genisoimage (mkisofs)` 创建一个 ISO 9660 的映像文件
`genisoimag` 命令：创建一个包含目录内容的 iso 映像文件。

如果我们已经创建一个叫做~/cd-rom-files 的目录，然后用文件填充此目录，再通过下面的命令来创建一个叫做 cd-rom.iso 映像文件：
```
genisoimage -o cd-rom.iso -R -J ~/cd-rom-files
```
"-R" 选项添加元数据为 Rock Ridge 扩展，这允许使用长文件名和 POSIX 风格的文件权限。 同样地，这个 "-J" 选项使 Joliet 扩展生效，这样 Windows 中就支持长文件名了。

### `wodim (cdrecord)` 把数据写入光存储媒介

#### 清除一张可重写入的 CD-ROM
可重写入的 CD-RW 媒介在被重使用之前需要擦除或清空。为此，我们可以用 wodim 命令，指定设备名称和清空的类型。此 wodim 程序提供了几种清空类型。最小（且最快）的是 "fast" 类型：
```shell
wodim dev=/dev/cdrw blank=fast
```

#### 写入镜像
写入一个映像文件，我们再次使用 wodim 命令，指定光盘设备名称和映像文件名：
```shell
wodim dev=/dev/cdrw image.iso
```

### `quota` 管理磁盘配额


quota 这个模块主要分为：
`quota`、`quotacheck`、`quotaoff`、`quotaon`、`quotastats`、`edquota`、`setquota`、`warnquota`、`repquota` 这几个命令


### `md5sum` 计算 MD5 检验码

通常验证一下我们已经下载的 iso 映像文件的完整性很有用处。

当你下载完映像文件之后，你应该对映像文件执行 md5sum 命令，然后把运行结果与发行商提供的 md5sum 数值作比较。
```
md5sum image.iso
34e354760f9bb7fbf85c96f6a3f94ece    image.iso
```

除了检查下载文件的完整性之外，我们也可以使用 md5sum 程序验证新写入的光学存储介质。 
```
md5sum /dev/cdrom
34e354760f9bb7fbf85c96f6a3f94ece    /dev/cdrom
```
