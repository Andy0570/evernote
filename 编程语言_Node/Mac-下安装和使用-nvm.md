[nvm](https://github.com/nvm-sh/nvm) 是一个开源的 Node.js 版本管理器，通过简单的 shell 脚本来管理和切换多个 Node.js 版本。

[官方文档](https://github.com/nvm-sh/nvm)中有详细的使用方法，以下只列出部分常用的命令。

## 安装

通过 cURL 或者 Wget 命令安装或升级 nvm：

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash
```

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.2/install.sh | bash
```

因为我的系统使用的 zsh，所以还需要把 nvm 命令的执行路径放到 `~/.zshrc` 文件下：

```bash
vim ~/.zshrc
```

然后在文件末尾追加以下内容：

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

最后，通过执行 `source` 命令使环境变量生效：

```bash
source ~/.zshrc
```

验证 nvm 是否安装成功：

```bash
nvm --version
0.35.2
```

## 使用

要下载，编译和安装最新版本的 node.js，执行以下操作：

```bash
# 以下指令中的 "node" 是最新版本的别名，表示下载最新版本的 Node.js
nvm install node
```

安装最新的 LTS 版本：

```bash
nvm install --lts
```

安装特定版本的 Node：

```bash
nvm install <node version number>

# 示例
nvm install 6.14.4 # or 10.10.0, 8.9.1, etc
```

列出当前可安装的 Node.js 版本：

```bash
nvm ls-remote
```

查看本机已安装的 Node.js 版本：

```bash
nvm ls
```

然后在任何新打开的 shell 中使用已安装的指定 Node.js 版本：

```bash
nvm use node
```

获取可执行文件的安装路径：

```bash
nvm which <version>

# 查看默认 node 的安装路径
nvm which default
```

通过 nvm 安装的 Node.js 位于用户目录下，而非系统目录下。在 npm 安装全局模块的时候，可以避免操作系统超级用户授权的问题。

```bash
which node
/Users/andy/.nvm/versions/node/v12.14.0/bin/node
```

重新安装某个全局的 npm 模块到指定的 Node  `<version>` 版本：

```bash
nvm reinstall-packages <version>
```


