Egg.js 在 POST 提交表单数据时会自动进行 CSRF 安全验证，需要进行配置。
参考：[Egg.js 文档：安全威胁 CSRF 的防范](https://eggjs.org/zh-cn/core/security.html#%E5%AE%89%E5%85%A8%E5%A8%81%E8%83%81-csrf-%E7%9A%84%E9%98%B2%E8%8C%83)

![表单页面](http://upload-images.jianshu.io/upload_images/2648731-376435857c67411e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 方法一：渲染表单时传入 csrf 值

### 路由配置

配置 /app/router.js 文件：
```JavaScript
'use strict';

module.exports = app => {
  const { router, controller } = app;

  // 显示表单页面的路由
  router.get('/', controller.home.index);
  // 提交表单数据的路由
  router.post('/add', controller.home.add);
};
```

### 控制器配置

配置 /app/controller/home.js 文件：
```JavaScript
'use strict';

const Controller = require('egg').Controller;

class HomeController extends Controller {

  // 显示表单页面
  async index() {

    // 显示表单页面，加载并渲染 HTML 模版文件
    // this.ctx.csrf 用户访问页面时生成的密钥
    await this.ctx.render('home', { csrf: this.ctx.csrf });
  }

  // 接收 post 提交的数据
  async add() {

    // 获取提交数据
    console.log(this.ctx.request.body);
  }
}

module.exports = HomeController;
```

### 模版文件

配置模版文件，因为在提交表单时，需要验证返回动态的 CSRF 值：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>
  <!-- _csrf=<%=csrf%> 表示回传 csrf 值 -->
  <form action="/add?_csrf=<%=csrf%>" method="post">

    用户名：<input type="text" name="username" /> <br><br>
    密码：<input type="password" name="password" /> <br><br>
    <button type="submit">提交</button>

  </form>
  
</body>
</html>
```

## 方法二：通过中间件方式进行全局配置


### 路由配置

/app/router.js 文件同上，不需要更改：
```JavaScript
'use strict';

module.exports = app => {
  const { router, controller } = app;

  // 显示表单页面的路由
  router.get('/', controller.home.index);
  // 提交表单数据的路由
  router.post('/add', controller.home.add);
};
```

### 中间件配置
配置 /app/middleware/auth.js 中间件，配置 csrf 全局变量：

```JavaScript
'use strict';

module.exports = (option, app) => {
  return async function auth(ctx, next) {
    // 设置模版的全局变量
    ctx.state.csrf = ctx.csrf;
    await next();
  };
};
```

中间件配置好之后，还需要在 config.default.js 文件中引入：

```JavaScript
'use strict';

/**
 * @param {Egg.EggAppInfo} appInfo app info
 */
module.exports = appInfo => {
  /**
   * built-in config
   * @type {Egg.EggAppConfig}
   **/
  const config = exports = {};

  // use for cookie sign key, should change to your own and keep security
  config.keys = appInfo.name + '_1556127582848_8657';

  // 💡💡💡 配置添加中间件 💡💡💡
  config.middleware = [ 'auth' ];

  // 配置模版引擎
  config.view = {
    mapping: {
      '.html': 'ejs',
    },
  };

  // add your user config here
  const userConfig = {
    // myAppName: 'egg',
  };

  return {
    ...config,
    ...userConfig,
  };
};
```

### 控制器配置

配置 /app/controller/home.js 文件：
```JavaScript
'use strict';

const Controller = require('egg').Controller;

class HomeController extends Controller {

  // 显示表单页面
  async index() {

    // 显示表单页面，加载并渲染 HTML 模版文件
    // ⚠️ csrf 会在全局进行配置，无需每个表单单独配置
    await this.ctx.render('home');
  }

  // 接收 post 提交的数据
  async add() {

    // 获取提交数据
    console.log(this.ctx.request.body);
  }
}

module.exports = HomeController;
```

### 模版文件

配置模版文件，因为在提交表单时，需要验证返回动态的 CSRF 值：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Document</title>
</head>
<body>

    <form action="/add" method="post">

    <!-- 通过隐藏表单域的方式回传 csrf 值 -->
    <input type="hidden" name="_csrf" value="<%=csrf%>" />
    用户名：<input type="text" name="username" /> <br><br>
    密码：<input type="password" name="password" /> <br><br>
    <button type="submit">提交</button>

  </form>
  
</body>
</html>
```

