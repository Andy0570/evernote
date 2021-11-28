## Visual Studio Code 快捷键

```markdown
# 禅模式
[cmd k z]

侧栏：cmd + B
合并代码为一行：control + J
折叠代码：option + cmd + [ / ]
重命名变量：F2


# 命令面板
命令面板：cmd + P
`@` 当前文件中搜索
`>` 搜索可用命令


# 调试
* 面板：cmd + J
* 问题面板：shift + cmd + M
* 输出面板：shift + cmd + U
* 调试面板：shift + cmd + Y


# Code Runner
运行：control + option + N
停止：control + option + M


# vscode-fileheader：添加头文件
control + option + i


# Document This：JSDoc 注释
连按两遍 control + option + d


# Prettier：代码格式化
shift + cmd + p


# ToDo
TODO:
FIXME:


# Turbo Console Log
* 自动添加 log 代码：选中变量 control + option + L
* 注释所有 log 代码：    shift + option + C
* 取消注释所有 log 代码： shift + option + U
* 删除所有 log 代码：    shift + option + D
```

## 插件和扩展

### 主题和图标

* Material Theme
* Material Icon Theme



### 字体和语言包

* [Chinese (Simplified) Language Pack for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-zh-hans) - 适用于 VS Code 的中文（简体）语言包
* [Fira Code](https://github.com/tonsky/FiraCode) - 具有连字的字体
* 设置参考：[Visual Studio Code — how to enable this new sexy Fira Code font?](https://medium.com/@qjli/daily-dev-tips-96-visual-studio-code-how-to-enable-this-new-sexy-fira-code-font-89bafbfa245f)

字体的 Font Family 设置中，修改 `Menlo, Monaco, 'Courier New', monospace,` 为 `Fira Code`，然后勾选连字选项


### Git 版本控制

* [GitLens](https://github.com/eamodio/vscode-gitlens) - 增强了 Visual Studio Code 中内置的 Git 功能
* [Git Project Manager](https://github.com/felipecaputo/git-project-manager) - Git 项目管理器


### 远程协作开发

* [Live Share](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare)


### 实时运行代码

* [Quokka.js](https://marketplace.visualstudio.com/items?itemName=WallabyJs.quokka-vscode) - 实时运行代码
* [Code Runner](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner)

运行代码：`control + option + n`
停止运行：`control + option + m`


### 括号配对着色 (Bracket Pair Colorizer) 和彩虹缩进 (Indent Rainbow) 

* [Bracket Pair Colorizer](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer) - 括号配对着色
* [indent-rainbow](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow) - 对空格缩进进行彩虹着色
* [~~Indenticator~~](https://marketplace.visualstudio.com/items?itemName=SirTori.indenticator) - 视觉上突出显示当前缩进的深度

### 自动闭合标记 (Auto Close Tag) 和自动重命名标记 (Auto Rename Tag)

* [Auto Close Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)
* [Auto Rename Tag](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)

### snippets (代码片段)

* [Emmet](https://emmet.io/) - 前端代码片段输入神器，VS Code 已经内置.
* [JavaScript (ES6) code snippets](https://marketplace.visualstudio.com/items?itemName=xabikos.JavaScriptSnippets)
* [smarty](https://marketplace.visualstudio.com/items?itemName=imperez.smarty)


### 文档注释

- [Better Comments](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments) - 为注释添加样式

```JavaScript
// Better Comments
  // 是否激活多行注释高亮效果
  "better-comments.multilineComments": true,
  // 是否激活普通文本注释高亮效果
  "better-comments.highlightPlainText": true,
  // 自定义 Tag 注释效果
  "better-comments.tags": [{
      "tag": "!",
      "color": "#FF2D00",
      "strikethrough": false,
      "backgroundColor": "transparent"
    },
    {
      "tag": "?",
      "color": "#3498DB",
      "strikethrough": false,
      "backgroundColor": "transparent"
    },
    {
      "tag": "//",
      "color": "#474747",
      "strikethrough": true,
      "backgroundColor": "transparent"
    },
    {
      "tag": "todo",
      "color": "#FF8C00",
      "strikethrough": false,
      "backgroundColor": "transparent"
    },
    {
      "tag": "*",
      "color": "#98C379",
      "strikethrough": false,
      "backgroundColor": "transparent"
    }
  ]
```

- [Document This](https://marketplace.visualstudio.com/items?itemName=joelday.docthis) - 自动为 TypeScript 和 JavaScript 文件生成详细的 JSDoc 注释。

注释生成的方法：键盘连按两遍 `control + option + D`
- [vscode-fileheader](https://marketplace.visualstudio.com/items?itemName=mikey.vscode-fileheader) - 自动生成文件头注释，并自动更新修改时间
配置文件：
```
// File header Configuration
"fileheader.Author": "Your Name",
"fileheader.LastModifiedBy": "Your Name",
```


使用方法：键盘快捷键 `control + option + i`
- JSDoc
- ESDoc

### TODO 高亮

- [TODO Highlight](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight) - 高亮 TODO/FIXME 注释，并搜寻所有 TODO。

语法：
```
// TODO:
// FIXME:
```
搜寻所有 TODO 命令：`List highlighted annotations`

### 代码格式化

* [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
* [Prettier - Code formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) - 代码格式化工具

用法：
```
1. command + Shift + P -> Format Document
OR
1. Select the text you want to Prettify
2. command + Shift + P -> Format Selection
```

### Turbo Consolo Log

[Turbo Consolo log](https://marketplace.visualstudio.com/items?itemName=ChakrounAnas.turbo-console-log) - 自动添加/删除 Log 代码

* 自动添加打印代码：选中变量，`control + option + L`
* 注释所有打印代码：`option + shift + C`
* 取消注释所有打印代码：`option + shift + U`
* 删除所有打印代码：`option + shift + d`


### Polacode - 带有定制字体和主题的代码截屏

类似于 Mac 应用： Carbonize

### 代码规范
- [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) - 代码规范和错误检查工具

```bash
$ sudo npm install eslint -g
$ eslint init
```

参考： [1-VScode 格式化 ESlint - 方法（最全最好用方法！）](https://www.jianshu.com/p/23a5d6194a4b)

> ESLint、JSHint、TSLint 傻傻分不清楚 😂😂😂

### 代码压缩和打包

* minifier - 代码压缩工具
* webpack - 打包器



### cross-env 与 config 项目环境变量配置

1. 在papackage.json的scripts中声明

```json
"scripts": {
    "dev": "cross-env NODE_ENV=development PORT=80 ... index.js",
    "test": "cross-env NODE_ENV=test PORT=8081 ... index.js",
},
```

2. 可跨平台使用的环境变量

```javascript
const env = process.env.NODE_ENV;
console.log('env:', env);
// env:development
const port = process.env.PORT;
console.log('port:', port);
// port:8080
```

3. 根据环境变量自动加载不同的config文件

```JavaScript
//
// ./config
//      development.js
//      test.js
//
// 1. npm run dev
// 加载 development.js
//
// 2. npm run test
// 加载 test.js
```

### JSON 序列化

* [Paste Json As Code](https://marketplace.visualstudio.com/items?itemName=quicktype.quicktype) - 将复制黏贴来的 json 文件直接序列化成代码


### markdown 扩展
...

### Node.js 扩展
...

### Vue.js 扩展
...

### C/C++ 扩展

* [在 macOS 下使用 Visual Studio Code 进行 C/C++ 开发](https://zhuanlan.zhihu.com/p/25006796)
* [C/C++ for Visual Studio Code (Preview)](https://code.visualstudio.com/docs/languages/cpp) 
* [Visual Studio Code 的 C/C++ 扩展功能](https://blogs.msdn.microsoft.com/c/2016/04/18/visual-studio-code%E7%9A%84cc%E6%89%A9%E5%B1%95%E5%8A%9F%E8%83%BD/)

### Docker 和 Kubernetes 扩展
...


## 参考
- [[译] Visual Studio Code 文档](https://segmentfault.com/blog/louiszhangct)
- [Gitbook：史上最好用的编辑器：VSCode](https://crifan.github.io/best_editor_vscode/website/)
- [Visual Studio Code 常用快捷键](https://lzw.me/a/vscode-visual-studio-code-shortcut.html)
- [在用 VSCode? 看完这篇文章，开发效率翻倍！最后一条厉害了～](https://zhuanlan.zhihu.com/p/54164612?utm_source=com.daimajia.gold&utm_medium=social&utm_oi=41299306610688)
- [20 个帮你更快搬砖的 vscode 快捷键](https://segmentfault.com/a/1190000021055272)
- [Visual Studio Code 快捷键参考 - 中文翻译](https://www.52cik.com/vscode-keyboard-shortcuts/)
- [提高 JavaScript 开发效率的高级 VSCode 扩展！](https://segmentfault.com/a/1190000017339754)
- [提高 JavaScript 开发效率的高级 VSCode 扩展之二！](https://segmentfault.com/a/1190000017935676)
- [使用 VSCode + ESLint 实践前端编码规范](https://segmentfault.com/a/1190000009077086?from=timeline&isappinstalled=0)
- [npm - ESLint:可组装的 JavaScript 和 JSX 检查工具](https://cn.eslint.org/)
- [JSDoc](http://www.css88.com/doc/jsdoc/index.html)
- [Visual Studio Code 插件市场](https://marketplace.visualstudio.com/vscode)
- [VSCode Plugin](https://github.com/jaywcjlove/awesome-mac/blob/master/editor-plugin-zh.md#vscode-plugin)
