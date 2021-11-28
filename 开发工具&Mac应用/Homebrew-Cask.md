![Homebrew Cask](http://upload-images.jianshu.io/upload_images/2648731-e6b4ae17b52b850c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

HomeBrew 是一个简单易用的包管理器，可以让你轻松下载、管理第三方应用。

### 查看 brew cask 常用命令

```bash
man brew-cask
```

### 更新 Homebrew Cask
```bash
brew update
```

### 搜索应用

```bash
brew search 应用名（一般需英文名）
```
注：实测 `brew-cask` 工具的 ~~`brew cask search xxx`~~ 命令不管用，用 `brew` 工具搜索就可以了。

### 安装应用
```bash
brew cask install 应用名

# 示例
brew cask install google-chrome # 安装 Chrome 浏览器
brew cask install alfred        # 安装 Alred
brew cask install fliqlo        # 安装屏保程序 Fliqlo
brew cask install dropbox       # 安装 Dropbox
brew cask install java          # 安装 Java 等开发环境
```

### 更新应用
```bash
# 检查可更新应用
brew outdated
```

更新应用：
```bash
# 更新所有应用
brew upgrade

# 更新单个应用
brew upgrade 应用名
```

### 清理应用
更新完成后，移除旧版本和缓存：

```bash
# 删除 Homebrew Cask 下载的包
brew cask cleanup

# 查看可清理应用
brew cleanup -n

# 指定需要清理的应用
brew cleanup 应用名
```

### 卸载应用
```bash
brew cask uninstall 软件名
```

### 重新安装应用
```bash
brew cask uninstall 软件名
```

### 访问应用官网
```bash
brew home 应用名
```

### 查看相关软件信息
```bash
brew cask info 应用名
```

### 列出通过 Homebrew Cask 安装的包
```bash
brew cask list
```



### 参考
* [少数派：借助 Homebrew Cask，教你快速下载安装 Mac App 新姿势](https://sspai.com/post/32857)
* [少数派：9 条进阶命令，把 HomeBrew 打造成管理第三方应用的 App Store](https://sspai.com/post/43451)
* [GitHub：Awesome macOS open source applications](https://github.com/serhii-londar/open-source-mac-os-apps)
* [brew update 慢，brew install 慢如何解决？](https://www.uedbox.com/post/57246/)


