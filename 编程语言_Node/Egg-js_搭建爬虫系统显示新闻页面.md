## Demo 示例

![新闻列表页面](http://upload-images.jianshu.io/upload_images/2648731-353a0746b8bd0113.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![新闻详情页面](http://upload-images.jianshu.io/upload_images/2648731-1303752385028cc2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![程序执行流程图](http://upload-images.jianshu.io/upload_images/2648731-edaecdd24c98a58a.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 创建项目

初始化 Egg.js 项目，控制台中输入如下命令：

```bash
$ mkdir spider && cd spider
$ npm init egg --type=simple
$ npm i
```



## 配置路由控制器

```javascript
// {app_root}/app/router.js
module.exports = app => {
  const { router, controller } = app;
  router.get('/news', controller.news.index);
  router.get('/newscontent', controller.news.content);
};

// {app_root}/app/controller/news.js
const Controller = require('egg').Controller;

class NewsController extends Controller {

  // 获取数据显示到新闻页面
  async index() {
    // ToDo
  }

  // 显示新闻详情
  async content() {
    // ToDo
  }
}

module.exports = NewsController;
```



## 配置模版引擎

配置并使用 ejs 模版引擎：[egg-view-egs](<https://www.npmjs.com/package/egg-view-ejs>)

### 安装模版引擎

```shell
$ npm i egg-view-ejs --save
```

### 配置模版引擎

```javascript
// {app_root}/config/plugin.js
exports.ejs = {
  enable: true,
  package: 'egg-view-ejs',
};
 
// {app_root}/config/config.default.js
// 配置模版引擎
config.view = {
  mapping: {
    '.ejs': 'ejs',
  },
};
```



## 配置 config 公共URL地址

```javascript
// {app_root}/config/config.default.js
// 配置公共 API 地址
config.api = 'http://www.phonegap100.com';
```



## 编写代码实现新闻页面

### 控制器页面

```javascript
// {app_root}/app/controller/news.js
const Controller = require('egg').Controller;

class NewsController extends Controller {

  // 获取数据显示到新闻页面
  async index() {
    // 调用 Service 层方法
    const listInfo = await this.service.news.getNewsList();
    // 渲染模版引擎
    await this.ctx.render('news', { list: listInfo });
  }

  // 显示新闻详情
  async content() {
    // 获取get传值，即点击页面 a 标签返回的 aid 值。
    const aid = this.ctx.query.aid;
    const result = await this.service.news.getNewsContent(aid);
    // 渲染模版引擎
    await this.ctx.render('newscontent', { list: result });
  }
}

module.exports = NewsController;
```

### 服务层页面

```javascript
// {app_root}/app/service/news.js
const Service = require('egg').Service;

class NewsService extends Service {

  // 通过抓取接口返回数据
  async getNewsList() {

    // curl 的方法可以获取远程数据。
    const api = this.config.api + 'appapi.php?a=getPortalList&catid=20&page=1';
    const response = await this.ctx.curl(api);

    // response.data 返回的是<Buffer>类型，需要将其转化为对象。
    const data = JSON.parse(response.data);

    return data.result;
  }

  // 获取新闻详情
  async getNewsContent(aid) {

    // 拼接 URL 路径
    const api = this.config.api + 'appapi.php?a=getPortalArticle&aid=' + aid;
    const response = await this.ctx.curl(api);

    // response.data 返回的是<Buffer>类型，需要将其转化为对象。
    const data = JSON.parse(response.data);
    return data.result[0];
  }
}

module.exports = NewsService;
```



### 视图页面

```html
// {app_root}/app/view/news.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <h2>新闻列表数据</h2>

  <ul>
    <!-- for 循环遍历输出数组数据 -->
    <%for(var i=0;i<list.length;i++){%>
      <!-- href="/newscontent?aid=<%=list[i].aid%>" 表示点击 a 标签跳转到相对URL页面 -->
      <!-- <%=list[i].title%> 表示 a 标签显示的内容：标题文字 -->
      <li><a href="/newscontent?aid=<%=list[i].aid%>"><%=list[i].title%></a> <span>——<%=list[i].dateline%></span></li>
    <%}%>
  </ul>

</body>
</html>


// {app_root}/app/view/newscontent.html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
  <!-- 引入自定义 css 样式-->
  <link rel="stylesheet" href="/public/css/basic.css">
</head>

<body>

  <div class="content">
    <h2><%=list.title%></h2>

    <div>
      <!-- 以下渲染的是 html 数据，=号改为 -->
      <%-list.content%>
    </div>
  </div>

</body>

</html>
```



## Egg.js 框架扩展

实现需求：格式化显示日期。

需要安装第三方模块：[silly-datetime](<https://www.npmjs.com/package/silly-datetime>)

### 编写 extend 中的 helper.js 文件

```javascript
// {app_root}/app/extend/helper.js
const sd = require('silly-datetime');

module.exports = {
  formatTime(param) {
    // this 就是 app 对象，在其中可以调用 app 上的其他方法，或访问属性
    // 格式化日期
    return sd.format(new Date(param*1000), 'YYYY-MM-DD HH:mm');
  },
};
```



### 修改HTML模版文件

直接在模版文件中使用扩展方法，直接格式化日期：

```html
<li><a href="/newscontent?aid=<%=list[i].aid%>"><%=list[i].title%></a> <span>——<%=helper.formatTime(list[i].dateline)%></span></li>
```



## Egg.js 中间件

### 编写中间件

```javascript
// {app_root}/app/middleware/forbidip.js
'use strict';

/*
options: 中间件的配置项，框架会将 app.config[$(middlewareName)] 传递进来
app: 当前应用 Application 的实例
*/
module.exports = (options, app) => {

  // 返回一个异步的方法
  return async function forbidIp(ctx, next) {

    // 要屏蔽的 ID：1. 从数据库获取，2.从参数传入
    const forbidip = '192.168.0.1';
    // 获取客户端 IP：ctx.request.ip
    if (ctx.request.ip === forbidip) {
      ctx.status = 403; // 返回拒绝连接状态码
      ctx.body = '您的IP已经被屏蔽';
    } else {
      await next();
    }
  };
};
```

### 配置添加中间件

```javascript
// {app_root}/config/config.default.js
// ...
// use for cookie sign key, should change to your own and keep security
  config.keys = appInfo.name + '_1556127582848_8657';

  // 配置添加中间件
  config.middleware = [ 'printdate', 'forbidip' ];

  // 配置中间件时，可以传入参数
  config.printdate = {
    aaa: 'value',
  };

  // 配置模版引擎
  config.view = {
    mapping: {
      '.html': 'ejs',
    },
  };

  // 配置公共 API 地址
  config.api = 'http://www.phonegap100.com/';
```



## 参考

* [Egg.js 视频教程](<https://www.bilibili.com/video/av34294643/?p=4>)
