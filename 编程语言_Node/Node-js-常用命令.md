# Node.js 安装

在 Ubuntu 系统中，使用 apt 软件包管理工具安装 Node.js 环境：
```bash
# 安装 node.js v10.12.0
$ wget -qO- https://deb.nodesource.com/setup_10.x | sudo -E bash -
$ sudo apt-get install -y nodejs

# 要从 npm 编译和安装原生插件，您可能还需要安装构建工具
$ sudo apt-get install -y build-essential 

# 检查当前 node.js 版本
$ node -v 
v11.14.0

# 检查当前 npm 版本
$ npm -v
6.9.0

# 更新 node.js
$ sudo apt-get upgrade nodejs

# 卸载 node.js
$ sudo apt-get remove nodejs

# 更新 npm
$ npm i -g npm
```

参考：[官方文档：通过包管理器方式安装 Nodejs](https://nodejs.org/zh-cn/download/package-manager/)

# Node.js 版本升级

npm 上有一个开源包叫 [n](https://github.com/tj/n)，是专门用来管理 node.js 版本的。

默认情况下，Node.js 版本会被缓存在 `/usr/local/n/versions` 目录下。

1. 通过 npm 包管理器安装 n 模块。

```bash
# 命令参数-g：将一个包安装为全局可用的可执行命令
npm install -g n
```

2. 升级 node 到最新稳定版本。

```bash
# 升级 node.js 版本（v13.2.0）
sudo n stable

# 选择最新的长期支持版
$ sudo n lts

# 最新版本
$ n latest
$ n current

# 你也可以指定 node 版本，语法：n <version>
$ n v10.11.0
$ n 10.11.0

# 执行 n 命令交互式查看已安装的 Node 版本并选择。

# 移除指定版本
n rm <version>

# 移除所有缓存版本，除了当前正使用的版本
n prune
```

3. 检查当前 node 版本（最后检查时间：201912）

```bash
# 查看 node 版本
$ node -v 
v13.2.0

# 显示版本，检查 npm 是否正确安装
$ npm -v 
6.9.0

# 显示完整信息
$ npm version 
{ npm: '6.4.1',
ares: '1.14.0',
cldr: '33.1',
http_parser: '2.8.0',
icu: '62.1',
modules: '64',
napi: '3',
nghttp2: '1.33.0',
node: '10.10.0',
openssl: '1.1.0i',
tz: '2018e',
unicode: '11.0',
uv: '1.23.0',
v8: '6.8.275.30-node.24',
zlib: '1.2.11' }
```

# 卸载 Node.js

卸载通过 HomeBrew 安装的 Node.js：

```bash
brew uninstall node
```

卸载通过官网 pkg 安装的 Node.js：

```bash
sudo rm -rf /usr/local/{bin/{node,npm},lib/node_modules/npm,lib/node,share/man/*/node.*}
```

# npm 包管理器

npm 是 JavaScript 世界的包管理工具，并且是 Node.js 平台的默认包管理工具。通过 npm 可以安装、共享、分发代码，管理项目依赖关系。

参考：[npm 中文网](https://www.npmjs.cn/)

## 查看帮助

```bash
npm help
npm h
```

## 安装模块

```bash
npm install <Module Name>
npm i <Module Name> # 简写

# 配置好 package.json 文件后，批量安装所有模块
npm install

# ⭐️⭐️⭐️ 推荐安装包时，添加 --save 命令
# 同时将安装包信息记录到 package.json 的 dependencies 字段（自动更新 package.json 文件）
npm i --save <Module Name> 
npm i -S <Module Name>

# 开发阶段，记录到 package.json 的 devDependencies 字段
npm i --save-dev <Module Name> 

# 示例：
npm install express    # 安装 express 模块
npm i uuid             # 安装 uuid 模块
npm i moment           # 安装 moment 模块（时间处理模块）
npm i node-forge       # 安装 node-forge 模块（TLS）
npm i mocha            # 安装 mocha 模块（测试框架）
npm i body-parser --save # 安装消息体解析模块
npm i sqlite3 --save     # 安装 sqlite3 进程内数据库
npm i node-readability --save # 安装 HTML 页面转化模块
npm i ejs --save        # 安装渲染模版引擎
npm i http-errors --save  # Create HTTP errors for Express, Koa, Connect, etc. with ease.
npm install dotenv --save # 环境变量配置框架
```

### 指定安装版本

```bash
# 格式
npm install 包名@版本号

# 示例
npm install jqurey@1.8.2
```



### 在全局环境中安装模块（-g：启用 global 模式）

> ⚠️ 
> * 全局模式并不是将一个模块包安装为一个全局包的意思，它并不意味着可以从任何地方通过 `require()` 方法引用到它。
> * `-g` 是将一个包安装为**全局可用的可执行命令**。
> * 如果包中含有命令行工具，那么需要执行全局安装。
> * 要全局安装的包一般是工具，即你要在命令行里运行的程序，比如 gulp-cli 包。

```bash
npm install -g <Module Name>

# 全局安装 express
npm install -g express

# 全局安装 Express 生成器
sudo -s npm install -g express-generator
# 使用 Express 生成器创建应用（oauth）
express oauth
cd oauth
npm install
npm start

# 使用脚手架快速初始化 Egg.js 项目
npm i egg-init -g
egg-init egg-example --type=simple
cd egg-example
npm i
```

## 卸载模块

```bash
# 查看 uninstall 用法
npm uninstall -h  
npm uninstall [<@scope>/]<pkg>[@<version>]... [--save-prod|--save-dev|--save-optional] [--no-save]

aliases: un, unlink, remove, rm, r

# 使用示例
npm uninstall <Module Name>

npm uninstall express    # 删除指定的模块
npm uninstall -g express # 全局卸载 express
npm un md5               # 移除 md5 模块
npm un node-rsa          # 移除 node-rsa 模块
```

## 显示当前目录下安装的模块

```bash
npm list
npm ls # 分析包，分析出当前路径下能够通过模块路径找到的所有包，并生成依赖树。

npm list -g
```

## 显示模块详情

```bash
npm show <Module Name>

npm show express
```

## 升级模块

1. 在 package.json 文件所在的目录中执行 `npm update` 命令。
2. 执行 `npm outdated` 命令。不应该有任何输出。

```bash
npm update             # 升级当前目录下的项目的所有模块
npm update express     # 升级当前目录下的项目的指定模块
npm update -g express  # 升级全局安装的express模块
```


## 初始化生成包描述文件（package.json 文件）

创建一个新的 Node 项目的步骤：创建一个文件夹，然后运行 `npm init` 来创建一个 package.json 文件：

```bash
$ mkdir example-project
$ cd example-project
$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

package name: (node)
version: (1.0.0)
description:
entry point: (testCrypto.js)
test command:
git repository:
keywords: arlingbc
author:
license: (ISC)
```

你也可以使用默认值创建 package.json 文件：`npm init -y`

package.json 部分片段示例：

```json
# dependencies 配置当前程序所依赖的其他包
# dependencies 是项目运行所依赖的模块
"dependencies": {
  "async": "^2.6.2",
  "bcrypt": "^3.0.6",
  "egg": "^2.21.1",
  "egg-scripts": "^2.11.0",
  "egg-view-nunjucks": "^2.2.0",
  "moment": "^2.24.0",
  "node-forge": "^0.8.2",
  "scrypt": "^6.0.3",
  "uuid": "^3.3.2"
},
# devDependencies 配置当前程序所依赖的其他包，只会下载模块，而不下载这些模块的测试和文档框架
# devDependencies 是项目开发所需要的模块
"devDependencies": {
  "autod": "^3.1.0",
  "autod-egg": "^1.0.0",
  "egg-bin": "^4.12.3",
  "egg-ci": "^1.11.0",
  "egg-mock": "^3.22.2",
  "eslint": "^4.11.0",
  "eslint-config-egg": "^6.0.0",
  "webstorm-disable-index": "^1.2.0"
},

# 版本说明
#
# ^ 表示第一位版本号不变，后面两位取最新值。
# ～ 表示前两位版本号不变，最后一位取最新值。
# * 表示全部取最新值，即使用最新版本。
# 配置好该文件后，输入 npm install 命令系统就会按以上约定自动安装模块
```

### Semver 版本规范

按官方文档 [语义化版本 2.0](https://semver.org/lang/zh-CN/) 所描述，当你做了不兼容的 API 修改时，才需要修改主版本号。
因此推荐使用 ^ 来引入依赖，不锁版本，更积极的升级策略。



# 注册包仓库账号

```bash
$ npm adduser
```



## 上传包文件

```bash
$ npm publish <floder> # 上传包文件到官方仓库

$ npm publish .
```



## 管理包权限

通常，一个包只有一个人拥有权限进行发布。

```shell
$ npm owner ls <package name>
$ npm owner add <user> <package name>
$ npm owner rm <user> <package name>
```



## node 命令行权限问题

运行 npm 命名时，提示“permission denied...”，解决方法是设置允许不安全权限：

```shell
npm config set unsafe-perm=true

# 如果以上方法无效，不妨在命令前加 sudo 提升权限
sudo npm install -g eslint
```



## npm 脚本

npm 脚本可以说是一个**微型脚本调用工具**，你可以用它构建客户端包、执行测试、生成文档等。
启动服务器的命令（`node index.js`）可以保存为 npm 脚本，打开 package.json 文件，在 scripts 里添加一个 start 属性：
```
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js"
  },
```
启动程序：`npm start`
指定端口：`PORT=3001 npm start`


