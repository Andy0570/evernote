原文：[slate - README](https://github.com/slatedocs/slate)

![](https://upload-images.jianshu.io/upload_images/2648731-5d253471e6acc725.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Slate 可以帮助你创建美观、智能、响应迅速的 API 文档。**

![Screenshot of Example Documentation created with Slate](https://upload-images.jianshu.io/upload_images/2648731-938213b7cf5843c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


以上 API 示例文档通过 Slate 创建。你可以点击链接 [slatedocs.github.io/slate](https://slatedocs.github.io/slate) 一探究竟。


### 特性
* **干净，直观的设计** — 使用 Slate 时，API 的描述信息位于文档的左侧, 所有代码示例都在右侧。受 Stripe 和 PayPal 的API文档启发。Slate 反应非常灵敏，因此在平板电脑，手机甚至印刷品上看起来都很棒。

* **所有内容都在同一页显示** —用户必须搜索一百万个页面才能找到所需内容的日子已经一去不复返了。Slate 将整个文档放在单个页面上。不过，我们并未牺牲可链接性。当你滚动查看文档时，浏览器的哈希值将更新为最接近的标头，因此链接到文档中的特定锚点仍然非常自然且容易。

* **Slate 只是 Markdown 而已** — 当你使用 Slate 编写 API 文档时，你只是在编写 Markdown 文档，这使得编辑和理解变得异常简单。一切都用 Markdown 编写——甚至代码示例也只是 Markdown 下的代码块。

* **用多种语言编写代码示例** — 如果你的 API 需要绑定多种编程语言，也可以轻松地在选项卡之间切换。在文档中，你将通过在每个代码块的顶部指定语言名称来区分不同的语言，就像 GitHub Flavored Markdown 一样。

* **开箱即用的语法突出显示** — 支持超过100 种语言，无需配置。

* 在页面最左侧**自动、平滑地滚动目录**（文档内容的自动跟随滚动效果）。当你滚动查看文档时，它将自动显示您在文档中的当前位置。它也很快。我们在 TripIt 上使用 Slate 为新的 API 构建文档时，其中包含180多个条目。即使对于较大的文档，我们也可以确保为其提供出色的性能。

* **让你的用户为你更新文档** — 默认情况下，通过 Slate 生成的文档托管在公共 GitHub 存储库中。这不仅意味着你可以免费使用 GitHub Pages 托管文档，而且还使其他开发人员在发现拼写错误或其他问题时，向你的文档发出拉取请求变得容易。当然，如果你不想使用 GitHub，也欢迎在其他位置托管文档。

* **RTL 支持** — 支持 RTL（right-to-left，从右向左布局）样式语言，例如阿拉伯语，波斯语（波斯语），希伯来语等。

Slate 入门非常简单！只需 fork 此存储库即可，然后按照以下说明进行操作。或者，如果您想了解 Slate 的功能，请查看  [sample docs](https://slatedocs.github.io/slate/)。

### 开始使用 Slate

### 先决条件

你需要:

 - **Linux or macOS** — Windows 系统下可能也可以运行，但并不支持。
 - **Ruby, version 2.3.1 or newer**
 - **Bundler** —如果 `bundle` 命令不起作用，只需在终端中运行 `gem install bundler` 安装即可。

```bash
# 当我执行以下命令安装 bundler 时，得到了权限不够的提示！
$ gem install bundler
Fetching bundler-2.1.4.gem
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.

# 解决方法：将 Bundler 安装到用户主目录下
# 参考：https://bundler.io/doc/troubleshooting.html
$ gem install bundler --user-install
```

### 开始设置

1. 在 GitHub 上 fork 此存储库.
2. 将你 fork 下来的存储库（不是我们原始的存储库）克隆到本地硬盘，可以执行此命令：`git clone https://github.com/YOURUSERNAME/slate.git`
3. `cd slate`
4. 初始化并启动 Slate。你可以在本地或通过 Vagrant 进行操作:

```shell
# 要么运行以下命令在本地运行
# 注：第一次运行这条命令会花很长时间来安装很多包文件。
# 建议首先执行以下命令配置路径，将 gems 安装到 ./vendor/bundle/ 目录下，否则系统会经常让你输入密码以获取操作权限，或者遇到没有拷贝权限以至于安装失败的问题。
$ bundle config set path 'vendor/bundle'
Your application has set path to "vendor/bundle". This will override the global value you are currently setting
$ bundle install
...
$ bundle exec middleman server

# 或者通过 vagrant 运行以下命令
vagrant up
```

你现在可以在 <http://localhost:4567> 看到文档了。 哇! 它真的很快!

现在已经在你的计算机上设置了 Slate，您可能想了解更多有关 [编辑 Slate markdown 文档](https://github.com/slatedocs/slate/wiki/Markdown-Syntax) 或者 [如何发布你的 API 文档](https://github.com/slatedocs/slate/wiki/Deploying-Slate)。

如果你更偏爱使用 Docker，可以阅读 [wiki](https://github.com/slatedocs/slate/wiki/Docke) 中的说明。

### 关于JavaScript运行时的注意事项

对于那些没有 JavaScript 运行时或 ExecJS 遇到 JavaScript 运行时问题的用户，建议将 [rubyracer gem](https://github.com/cowboyd/therubyracer) 添加到你的gemfile 中，然后再次运行 `bundle`。

### 使用 Slate 的公司

* [NASA](https://api.nasa.gov)
* [Sony](http://developers.cimediacloud.com)
* [Best Buy](https://bestbuyapis.github.io/api-documentation/)
* [Travis-CI](https://docs.travis-ci.com/api/)
* [Greenhouse](https://developers.greenhouse.io/harvest.html)
* [WooCommerce](http://woocommerce.github.io/woocommerce-rest-api-docs/)
* [Dwolla](https://docs.dwolla.com/)
* [Clearbit](https://clearbit.com/docs)
* [Coinbase](https://developers.coinbase.com/api)
* [Parrot Drones](http://developer.parrot.com/docs/bebop/)
* [Scale](https://docs.scaleapi.com/)

You can view more in [the list on the wiki](https://github.com/slatedocs/slate/wiki/Slate-in-the-Wild).

### 有问题? 需要帮助? 找到了一个bug?

If you've got questions about setup, deploying, special feature implementation in your fork, or just want to chat with the developer, please feel free to [start a thread in our Spectrum community](https://spectrum.chat/slate)!

Found a bug with upstream Slate? Go ahead and [submit an issue](https://github.com/slatedocs/slate/issues). And, of course, feel free to submit pull requests with bug fixes or changes to the `dev` branch.

### 贡献者

Slate was built by [Robert Lord](https://lord.io) while interning at [TripIt](https://www.tripit.com/).

Thanks to the following people who have submitted major pull requests:

- [@chrissrogers](https://github.com/chrissrogers)
- [@bootstraponline](https://github.com/bootstraponline)
- [@realityking](https://github.com/realityking)
- [@cvkef](https://github.com/cvkef)

Also, thanks to [Sauce Labs](http://saucelabs.com) for sponsoring the development of the responsive styles.

### 特别感谢

- [Middleman](https://github.com/middleman/middleman)
- [jquery.tocify.js](https://github.com/gfranko/jquery.tocify.js)
- [middleman-syntax](https://github.com/middleman/middleman-syntax)
- [middleman-gh-pages](https://github.com/edgecase/middleman-gh-pages)
- [Font Awesome](http://fortawesome.github.io/Font-Awesome/)

