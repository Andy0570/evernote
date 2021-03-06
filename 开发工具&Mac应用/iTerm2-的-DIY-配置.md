![](https://upload-images.jianshu.io/upload_images/2648731-4ff2c98a5eb1da16.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### iTerm2
* [iTerm 主题](https://iterm2colorschemes.com/) - iTerm/iTerm2 的 200 多种终端配色主题集合

### Zsh

> Bash 作为大多数系统默认安装的 Shell，大家都多少有所接触，Zsh 和 Bash 类似都是一个 Shell，但是 Zsh 更注重用户体验和与人的交互，OS X 默认也安装好了 Zsh，然而你想自己从头开始配置一个顺手的 Zsh 是比较浪费时间的，有人已经帮我们配置好了，这个流行的 Zsh 配置叫 --- Oh My ZSH
* [oh-my-zsh](https://ohmyz.sh/) - 一个用于管理 Zsh 配置的开源框架。
* [GitHub：oh-my-zsh themes](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)
* [Meslo LG M Regular for Powerline.ttf](https://github.com/powerline/fonts/blob/master/Meslo%20Slashed/Meslo%20LG%20M%20Regular%20for%20Powerline.ttf)


查看安装的 zsh 版本：
```shell
zsh --version
```
切换默认 Shell 为 Zsh:
```
chsh -s /bin/zsh
```
修改回默认 bash
```
chsh -s /bin/bash
```
修改 Zsh 主题：
```shell
# 1.打开配置文件：
vim ~/.zshrc

# 2.修改配置文件项
ZSH_THEME="agnoster"
```




### Go2Shell
* [简书：关于Go2Shell](https://www.jianshu.com/p/bae3a64ea762)



### 声明高亮
```bash
brew install zsh-syntax-highlighting
```

打开 ～/.zshrc 文件：`vim ~/.zshrc`，编辑如下：
```bash
# zsh-syntax-highlighting support
source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

### 自动补全

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions

# Which plugins would you like to load? (plugins can be found in ~/.oh-my-zsh/plugins/*)
# Custom plugins may be added to ~/.oh-my-zsh/custom/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
        git
        zsh-autosuggestions
        zsh-syntax-highlighting
        )

```

### iTerm2 隐藏用户名和主机名

打开 ～/.zshrc 文件：`vim ~/.zshrc`，编辑如下：
```bash
# Set name of the theme to load. Optionally, if you set this to "random"
# it'll load a random theme each time that oh-my-zsh is loaded.
# See https://github.com/robbyrussell/oh-my-zsh/wiki/Themes
ZSH_THEME="agnoster"
DEFAULT_USER="your_name" # 此处修改你的用户名
```
* 注意等号（=）左右没有空格。

### 参考链接
* [Mac OS 终端利器 iTerm2](https://www.cnblogs.com/xishuai/p/mac-iterm2.html)
* [少数派：10 个 Terminal 主题，让你的 macOS 终端更好看](https://sspai.com/post/53008)
* [知乎：高效 MacBook 工作环境配置](https://zhuanlan.zhihu.com/p/24868436)
* [让你的 Mac 提前用上 macOS Catalina 的 Shell —— Oh My Zsh 配置指南](https://blog.hly0928.com/post/set-up-oh-my-zsh-on-macos/)
* [这 22 款命令行神器必须安排上](https://mp.weixin.qq.com/s/Mn5Vlftwm_mSt3RY-rI8dw)
