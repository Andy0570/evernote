![shuttle Logo](http://upload-images.jianshu.io/upload_images/2648731-5a982c0920ac5069.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/100)

### [Shuttle 官网](http://fitztrev.github.io/shuttle/)

windows 下远程登录通常会使用 [Xshell](https://www.netsarang.com/products/xsh_overview.html)、[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)，而在 Mac 访问虚拟机可以使用 [shuttle](http://fitztrev.github.io/shuttle/)。

20190117 新增，推荐一个跨平台的免费开源工具：[Termius](https://www.termius.com/)

### 参考

- [mac 下的效率工具 shuttle 介绍](http://blog.qingtian16265.com/2016/04/07/introduce-shuttle/)
- [Shuttle 安装以及配置简介](https://www.bluelzy.com/articles/shuttle_brief_introduction.html)
- [Shuttle - Mac 快捷菜单神器](https://www.jianshu.com/p/1f9091641312)

### 使用 Homebrew 安装 shuttle
![安装 shuttle 的步骤](https://upload-images.jianshu.io/upload_images/2648731-f6b3923d24bef9cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



###  配置 .shuttle.json 文件

```json
{
    "_comments": [
    "Valid terminals include: 'Terminal.app' or 'iTerm'",
    "In the editor value change 'default' to 'nano', 'vi', or another terminal based editor.",
    "Hosts will also be read from your ~/.ssh/config or /etc/ssh_config file, if available",
    "For more information on how to configure, please see http://fitztrev.github.io/shuttle/"
  ],
    "editor": "default",
    "launch_at_login": true,
    "terminal": "iTerm",
    "iTerm_version": "nightly",
    "default_theme": "Homebrew",
    "open_in": "new",
    "show_ssh_config_hosts": false,
    "ssh_config_ignore_hosts": [],
    "ssh_config_ignore_keywords": [],
    "hosts": [
        {
            "cmd": "ps aux | grep defaults",
            "name": "Grep - Opens in Default-window-theme-title"
    },
        {
            "Spouses Servers": [
                {
                    "cmd": "echo '—->WARNING! Running commands<-- Are you sure? y/n'; read sure; if [ $sure == y ]; then echo running command && ps aux | grep [a]pple; else echo save to history and show... && history -s 'ps aux | grep [a]pple' && osascript -e 'tell application \"System Events\" to keystroke \"p\" using {control down}'; fi",
                    "inTerminal": "current",
                    "name": "Logs - Opens in the current active terminal window"
        },
                {
                    "Jane’s Servers": [
                        {
                            "cmd": "ssh username@blog2.example.com",
                            "inTerminal": "tab",
                            "name": "SSH blog - Opens in Tab of active window",
                            "theme": "basic",
                            "title": "title of tab"
            },
                        {
                            "cmd": "ssh username@shop1.example.com",
                            "inTerminal": "new",
                            "name": "SSH Shop - Opens in New Window",
                            "theme": "basic",
                            "title": "title of new window"
            }
          ]
        }
      ]
    },
  ]
}
```

### JSON 格式说明

```json
{
    "菜单名称": [
        {
            "cmd": "需要执行的 ssh 命令",
            "inTerminal": "命令执行的窗口模式：new/tab/current",
            "name": "子菜单名",
            "theme": "终端主题：basic 设置为默认，或者你在iTerm中偏好设置的自定义主题名称",
            "title": "新窗口/新标签页标题，缺失时使用 name 作为标题",
        }
    ]
},
```
