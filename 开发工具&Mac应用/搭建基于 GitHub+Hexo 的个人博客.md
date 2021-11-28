## 参考

### 博客搭建教程

* [Hexo.io：快速、简洁且高效的博客框架](<https://hexo.io/zh-cn/index.html>)
* [Hexo主题：Next 官方文档 ](https://github.com/next-theme/hexo-theme-next/) 
* [Hexo+GithubPages&CodingPages 搭建自己的个人博客](http://jmyblog.top/Hexo-GithubPages-CodingPages搭建自己的个人博客/)
* [知乎：GitHub+Hexo 搭建个人网站详细教程@吴润](<https://zhuanlan.zhihu.com/p/26625249>)
* [打造个性超赞博客 Hexo+NexT+GitHubPages 的超深度优化](https://reuixiy.github.io/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html)
* [Hexo 搭建技术博客使用与常见问题详细讲解](<https://code.skyheng.com/post/50982.html>)
* [知乎：hexo 的 next 主题个性化教程：打造炫酷网站](<https://zhuanlan.zhihu.com/p/28128674>)
* [使用 Travis CI 自动更新 GitHub Pages](<https://notes.iissnan.com/2016/publishing-github-pages-with-travis-ci/>)
* [GitHub Pages 搭建教程](https://sspai.com/post/54608)
* [设置 hexo 下 next 主题的 404 页面](https://blog.cmyr.ltd/archives/e85d425e.html)



### 站点参考

* [知乎：你见过的最棒的个人博客界面是什么样的？](<https://www.zhihu.com/question/29755481>)
* <https://dandyxu.me/>
* <https://raincal.com/>



### 新建 GitHub 仓库

注意点：新建 GitHub 仓库时，必须设置成 Public 仓库。（一开始我设置成了 Private，然后打开自己的站点就是 404！）



## Hexo 命令

### 安装 Hexo

```bash
npm install -g hexo-cli
```

### 升级 Hexo

```bash
npm update -g hexo-cli
```

### 新建一个网站

初始化博客站点：

```bash
$ hexo init [folder]
```

### 新建一篇文章

``` bash
$ hexo new "文章标题"

# 简写
$ hexo n "文章标题"
```

### 生成静态文件

在发布写好的 markdown 文章之前，需要先使用以下命令将他们生成静态文件。

``` bash
$ hexo generate
$ hexo generate --deploy

# 简写
$ hexo g
$ hexo g -d # 生成静态文件并自动部署到服务器
```

参数：

| 选项             | 描述                   |
| ---------------- | ---------------------- |
| `-d`, `--deploy` | 文件生成后立即部署网站 |
| `-w`, `--watch`  | 监视文件变动           |

### 新建草稿

草稿类似于"私密文章"，默认是不发布的。

```
$ hexo new draft "filename"
```

### 发表草稿

默认情况下，草稿文件会被保存到 `source/_drafts` 文件夹，您可通过 `publish` 命令将草稿移动到 `source/_posts` 文件夹。

```bash
$ hexo publish [layout] <filename>
```

### 启动服务器

通常用于本地环境测试。

``` bash
$ hexo server

# 简写
$ hexo s

hexo server #Hexo 会监视文件变动并自动更新，无须重启服务器
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP
```

启动服务器，默认情况下，访问网址为：<http://localhost:4000/>

| 选项             | 描述                           |
| ---------------- | ------------------------------ |
| `-p`,` --post`   | 重设端口                       |
| `-s`,` --static` | 只使用静态文件                 |
| `-l`, `--log`    | 启动日记记录，使用覆盖记录格式 |

### 部署网站

将网站部署到服务器上。

``` bash
$ hexo deploy
$ hexo deplay --generate # 同 hexo generate --deplay

# 简写
$ hexo d
$ hexo d -g
```





## NexT 主题配置

很多旧的教程会让你使用这条 Git 命令下载 NexT 主题：

```bash
$ cd hexo
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

⚠️ 这个旧的 GitHub 仓库 [**iissnan/hexo-theme-next**](<https://github.com/iissnan/hexo-theme-next>) 已经不再维护了，因此我们需要使用社区维护版的仓库：[**theme-next/hexo-theme-next**](<https://github.com/theme-next/hexo-theme-next>)：

```bash
$ cd hexo
$ git clone https://github.com/theme-next/hexo-theme-next themes/next
```



### 配置 RSS 订阅

1. 首先需要安装 [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed) 插件。

   ```bash
   $ npm install hexo-generator-feed --save
   ```

2. 然后需要编辑项目配置文件（根目录下的 _config.yml 文件）:

   ```yml
   # Extensions 扩展
   ## Plugins: https://hexo.io/plugins/
   ## Themes: https://hexo.io/themes/
   plugins:
       hexo-generator-feed  ## ⚠️ 注意这一行的格式，注意空格，不要顶格写！！！
   theme: next
   
   ## 另外，上面的是只添加一个插件的格式，如果有多个插件，格式如下：
   #plugins:
   #  - hexo-generator-feed
   #  - hexo-generator-sitemap
   
   # Feed 插件，自动生成订阅 RSS 的 XML 文件。
   feed:
     type: atom
     path: atom.xml
     limit: 20
     hub:
     content:
     content_limit: 140
     content_limit_delim: ' '
     order_by: -date
     icon: icon.png
   ```

3. 最后在主题目录下的 _config.yml 目录下，添加如下配置：

   ```yaml
   # Set rss to false to disable feed link.
   # Leave rss as blank to use site's feed link, and install hexo-generator-feed: `npm install hexo-generator-feed --save`.
   # Set rss to specific value if you have burned your feed already.
   rss: /atom.xml
   ```



### GitHub Banner 设置

参考网址：

* [GitHub Corners](<http://tholman.com/github-corners/>)

* [hexo 的 next 主题个性化教程：打造炫酷网站](<https://zhuanlan.zhihu.com/p/28128674>)



### 配置 [canvas_nest](https://github.com/theme-next/theme-next-canvas-nest) 动态背景主题

参考：[next 主题如何添加动态背景@Moorez](<http://shenzekun.cn/hexo%E5%A6%82%E4%BD%95%E6%B7%BB%E5%8A%A0%E5%8A%A8%E6%80%81%E8%83%8C%E6%99%AF.html>)

```yml
canvas_nest:
  enable: true
  onmobile: true # display on mobile or not
  color: '0,0,255' # 线条颜色，RGB 值, 使用 ',' 分开
  opacity: 0.5 # 线条不透明度: 0~1
  zIndex: -1 # 背景的 z-index 属性，css 属性用于控制所在层的位置，默认: -1
  count: 99 # 线条的总数量
```



### 配置 [fancybox3](<https://github.com/theme-next/theme-next-fancybox3>)





### 为脚本设置 CDN 加速





### 转义标签特殊符号

编辑文章的 [Front-matter](https://hexo.io/zh-cn/docs/front-matter) 时，如果 Tag 标签中含有特殊符号，比如说我要写一篇关于 iOS 编程中 **@property** 属性的文章，设置 Tag 为 **@property**，前面的 @ 符号需要转义，不然会渲染异常。

```
Tag: '@property'
```

参考：[Hexo 特殊符号的转义问题](<https://wxnacy.com/2018/01/12/hexo-specific-symbol/>)



### 博文摘要

```
<!--more-->
```

参考：[[http://saili.science/2017/04/02/github-for-win/](http://saili.science/2017/04/02/github-for-win/)]([http://saili.science/2017/04/02/github-for-win/](http://saili.science/2017/04/02/github-for-win/))



### 对文章进行分类

默认情况下，每次新建文章 `hexo new 'filename'` 都会把所有的 markdown 文件都在 _post 目录下，越往后文件越来越多，不方便更新和管理。

本来我是想能不能在 _post 目录下新建子文件夹，把所有文件按 categorys 分类存放，但是还没有找到方法。

目前只找到了支持按年月日存放的方法，修改 hexo 的配置文件：

```
new_post_name: :year/:month/:day/:title.md
```





## 配置第三方服务



### 配置 Leancloud

参考：[注册 Leancloud 并创建应用](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/LEANCLOUD-COUNTER-SECURITY.md) ⭐️⭐️⭐️

[Leancloud 访客统计插件重大安全漏洞修复指南](https://leaferx.online/2018/02/11/lc-security/)

[Leancloud 文档](https://leancloud.cn/docs/error_code.html?)

[hexo-leancloud-counter-security 插件 Too many requests 错误](https://yunhao.space/2018/06/29/hexo-leancloud-counter-security-too-many-requests-error/)





### 配置 Algolia 搜索

参考：

* [hexo-algolia](https://github.com/oncletom/hexo-algolia)
* [hexo：（三）hexo Next 主题配置 --- 补充 ---Algolia 搜索]([https://chentging.github.io/2018/05/16/hexo%EF%BC%9A%EF%BC%88%E4%B8%89%EF%BC%89hexo-Next-%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE-%E8%A1%A5%E5%85%85-Algolia-%E6%90%9C%E7%B4%A2/](https://chentging.github.io/2018/05/16/hexo：（三）hexo-Next-主题配置-补充-Algolia-搜索/))
* [Hexo-Next 搭建个人博客（集成 Algolia 站内搜索引擎）]([https://yfzhou.coding.me/2018/08/08/Hexo-Next%E6%90%AD%E5%BB%BA%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%EF%BC%88%E9%9B%86%E6%88%90Algolia%E7%AB%99%E5%86%85%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%EF%BC%89/](https://yfzhou.coding.me/2018/08/08/Hexo-Next搭建个人博客（集成Algolia站内搜索引擎）/))

```bash
## Search-only API Key 的密钥设置不正确
$ export HEXO_ALGOLIA_INDEXING_KEY=982fe6ebfad21e13b4b46263eb0bb90a
$ hexo clean
INFO  Deleted database.
$ hexo algolia
INFO  [hexo-algolia] Testing HEXO_ALGOLIA_INDEXING_KEY permissions.
ERROR [hexo-algolia] Invalid Application-ID or API key
ERROR >> You might have used an Admin Key or an invalid Key.
ERROR >> Read https://npmjs.com/hexo-algolia#security-concerns for more informations.

## 错误原因，需要添加 Search-only API Key 的 ACL 权限
$ export HEXO_ALGOLIA_INDEXING_KEY=22fbefd84c8343239f5c04365b9728de
$ hexo clean
INFO  Deleted database.
$ hexo algolia
INFO  [hexo-algolia] Testing HEXO_ALGOLIA_INDEXING_KEY permissions.
INFO  Start processing
INFO  [hexo-algolia] 320 records to index (post, page).
INFO  [hexo-algolia] Indexing chunk 1 of 7 (50 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:611
INFO  [hexo-algolia] Indexing chunk 2 of 7 (50 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:578
INFO  [hexo-algolia] Indexing chunk 3 of 7 (50 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:979
INFO  [hexo-algolia] Indexing chunk 4 of 7 (50 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:599
INFO  [hexo-algolia] Indexing chunk 5 of 7 (50 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:673
INFO  [hexo-algolia] Indexing chunk 6 of 7 (50 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:626
INFO  [hexo-algolia] Indexing chunk 7 of 7 (20 records)
ERROR [hexo-algolia] Not enough rights to update an object near line:1 column:645
INFO  [hexo-algolia] Indexing done.

## 正确示例
$ hexo clean
INFO  Deleted database.
$ hexo algolia
INFO  [hexo-algolia] Testing HEXO_ALGOLIA_INDEXING_KEY permissions.
INFO  Start processing
INFO  [hexo-algolia] 320 records to index (post, page).
INFO  [hexo-algolia] Indexing chunk 1 of 7 (50 records)
INFO  [hexo-algolia] Indexing chunk 2 of 7 (50 records)
INFO  [hexo-algolia] Indexing chunk 3 of 7 (50 records)
INFO  [hexo-algolia] Indexing chunk 4 of 7 (50 records)
INFO  [hexo-algolia] Indexing chunk 5 of 7 (50 records)
INFO  [hexo-algolia] Indexing chunk 6 of 7 (50 records)
INFO  [hexo-algolia] Indexing chunk 7 of 7 (20 records)
INFO  [hexo-algolia] Indexing done.
```





### 图床服务

[在 iPic 中添加七牛云](https://toolinbox.net/iPic/AddQiniuImageHost.html)



## 标签插件

* [NexT - Tag Plugins](https://theme-next.org/docs/tag-plugins/)



### Note

* [Fixes and enhancements. [12] #1697](https://github.com/iissnan/hexo-theme-next/pull/1697)
* [内置标签]([http://theme-next.iissnan.com/tag-plugins.html](http://theme-next.iissnan.com/tag-plugins.html))
* [好玩的写作样式](https://io-oi.me/technology/computer/computer-aided-art/2017/06/09/hexo-next-optimization.html)



### Tabs





