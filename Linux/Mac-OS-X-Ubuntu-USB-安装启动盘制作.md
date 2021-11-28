参考：[Mac OS X Ubuntu usb 安装启动盘制作](https://www.jianshu.com/p/e2f41e217109)

```
☁  Desktop [master] ⚡ hdiutil convert -format UDRW -o ./ubuntu-18.04-desktop-amd64 ./ubuntu-18.04-desktop-amd64.iso    # 将ISO转为DMG
正在读取Driver Descriptor Map（DDM：0）…
正在读取Ubuntu 18.04 LTS amd64          （Apple_ISO：1）…
正在读取Apple（Apple_partition_map：2）…
正在读取Ubuntu 18.04 LTS amd64          （Apple_ISO：3）…
.............................................................................
正在读取EFI（Apple_HFS：4）…
.............................................................................
正在读取Ubuntu 18.04 LTS amd64          （Apple_ISO：5）…
..............................................................................
已耗时： 2.082s
速度：880.0M 字节/秒
节省：0.0%
created: /Users/huqilin/Desktop/ubuntu-18.04-desktop-amd64.dmg

☁  Desktop [master] ⚡ diskutil list    # 得到 U 盘设备号
/dev/disk0 (internal):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                         251.0 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         250.7 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +250.7 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD            112.5 GB   disk1s1
   2:                APFS Volume Preboot                 21.1 MB    disk1s2
   3:                APFS Volume Recovery                517.8 MB   disk1s3
   4:                APFS Volume VM                      3.2 GB     disk1s4

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *7.8 GB     disk2
   1:                  Apple_HFS USB8G                   7.8 GB     disk2s1

☁  Desktop [master] ⚡ diskutil unmountDisk /dev/disk2    # 卸载 U 盘设备
Unmount of all volumes on disk2 was successful
☁  Desktop [master] ⚡ sudo dd if=ubuntu-18.04-desktop-amd64.dmg of=/dev/disk2 bs=1m    # 创建可启动的 USB 驱动盘
Password:
1832+1 records in
1832+1 records out
1921843200 bytes transferred in 207.200284 secs (9275292 bytes/sec)

☁  Desktop [master] ⚡ diskutil eject /dev/disk2    # 弹出磁盘
Disk /dev/disk2 ejected
```
