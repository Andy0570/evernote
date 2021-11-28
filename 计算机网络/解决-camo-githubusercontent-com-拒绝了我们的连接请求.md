### 问题

GitHub 项目中的图片在浏览器中无法显示（图裂），点击图片链接直接打开图片，浏览器显示：
![](https://upload-images.jianshu.io/upload_images/2648731-047eff75d0db27f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

### 解决方法

Mac OS 系统下的解决方法如下。

1. 浏览器中打开 [DNS 查询工具](http://tool.chinaz.com/dns/) ，输入 `camo.githubusercontent.com`，查询域名对应的 IP 地址。

![](https://upload-images.jianshu.io/upload_images/2648731-9e746b1e22913fab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)

如图所示， TTL 值最小的 IP 地址为湖南联通的 `151.101.228.133`，（具体 IP 地址以你当前查到的为准）。

2. 打开 Terminal 终端，编辑  **hosts** 文件，将上一步查询到的 IP 地址与域名的映射添加到该文件中。控制台输入：
```bash
sudo vim /etc/hosts
```

打开 **hosts** 文件，在该文件的末尾添加如下行（键盘快捷键 `i` 即可输入）：
```bash
151.101.228.133 camo.githubusercontent.com
```
键盘输入`shift`+`:`切换到命令模式，输入 `wq` 保存退出。

3. 使 **hosts** 文件立即生效，控制台输入：
```bash
sudo killall -HUP mDNSResponder
```

至此，浏览器中即可显示 GitHub 中的图片了。









