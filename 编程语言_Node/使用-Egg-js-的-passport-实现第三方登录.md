使用 [egg-passport](https://github.com/eggjs/egg-passport#readme) 插件实现登录到 GitHub 的第三方客户端。
egg-passport 插件把初始化、鉴权成功后的回调处理等通用逻辑封装掉，使得开发者仅需调用几个 API 即可方便的使用 Passport。

### 安装
```bash
$ npm i --save egg-passport
$ npm i --save egg-passport-github
``` 

### 配置
开启插件：
```js
/** @type Egg.EggPlugin */
module.exports = {

  // 配置 egg-passport 插件
  passport: {
    enable: true,
    package: 'egg-passport',
  },

  // 配置 egg-passport-github 插件
  passportGithub: {
    enable: true,
    package: 'egg-passport-github',
  },
};
```
创建一个 [GitHub OAuth Apps](https://github.com/settings/applications/new)，得到 `clientID` 和 `clientSecret` 信息，就是第三方应用向 GitHub 提出申请，作为第三方应用接入到 GitHub，然后用户就可以通过 GitHub 登录第三方应用，实现免密登录。这背后使用的技术就是 OAuth 2.0。

```js
  // 创建 GitHub OAuth Apps，并配置客户端密钥信息
  // 客户端ID、客户端密钥在 Github 上申请 OAuth Apps 时由 GitHub 自动提供
  config.passportGithub = {
    key: '83407b622f7e0c9a2b16', // GitHub 分配的客户端 id
    secret: '88bb2513960d214cdd97663961ba772145ddbc43', // GitHub 分配的客户端密钥
    callbackURL: '/passport/github/callback',
    // proxy: false, // 应用部署在 Nginx/HAProxy 之后，需设置插件 proxy 选项为 true
  };
```

### 挂载路由
```js
/**
 * @param {Egg.Application} app - egg application
 */
module.exports = app => {
  const { router, controller } = app;
  // 测试环境下，浏览器访问 http://127.0.0.1:7001/user 的路由
  router.get('/', controller.home.render);
  router.get('/user', controller.home.render);
  // 用户登录成功后，登出的路由
  router.get('/logout', controller.user.logout);

  // 挂载鉴权路由
  // Authorization callback URL: http://127.0.0.1:7001/passport/github/callback
  app.passport.mount('github');

  // 上面的 mount 是语法糖，等价于
  // const github = app.passport.authenticate('github', {});
  // router.get('/passport/github', github);
  // router.get('/passport/github/callback', github);
};
```

### 主页登录示例

这里的 `if` 语句执行的逻辑是：
1. 如果该请求已经授权，则直接打印显示获取到的用户信息到主页上；
2. 如果未授权，则跳转到 GitHub 页码获取授权码；

```js
const Controller = require('egg').Controller;

class HomeController extends Controller {
  async render() {
    const { ctx } = this;

    // ctx.user - 获取当前已登录的用户信息
    // ctx.isAuthenticated() - 检查该请求是否已授权
    // ctx.session.returnTo= - 在跳转验证前设置，可以指定成功后的 redirect 地址
    if (ctx.isAuthenticated()) {
      ctx.body = `<div>
        <h2>${ctx.path}</h2>
        <hr>
        Logined user: <img src="${ctx.user.photo}"> ${ctx.user.displayName} / ${
  ctx.user.id
} | <a href="/logout">Logout</a>
        <pre><code>${JSON.stringify(ctx.user, null, 2)}</code></pre>
        <hr>
        <a href="/">Home</a> | <a href="/user">User</a>
      </div>`;
    } else {
      ctx.session.returnTo = ctx.path;
      ctx.body = `
        <div>
          <h2>${ctx.path}</h2>
          <hr>
          Login with
          <a href="/passport/weibo">Weibo</a> | <a href="/passport/github">Github</a> |
          <a href="/passport/bitbucket">Bitbucket</a> | <a href="/passport/twitter">Twitter</a> |
          <a href="/passport/yuque">YuQue 语雀</a>
          <hr>
          <a href="/">Home</a> | <a href="/user">User</a>
        </div>
      `;
    }
  }
}

module.exports = HomeController;
```

### 登出路由示例

```js
const Controller = require('egg').Controller;

class UserController extends Controller {
  async logout() {
    const { ctx } = this;

    // ctx.logout() - 退出，将用户信息从 session 中清除
    ctx.logout();
    // 重定向页码
    ctx.redirect(ctx.get('referer') || '/');
  }
}

module.exports = UserController;
```

### 参考

注：本文参考 [Egg 官方文档](https://eggjs.org/zh-cn/tutorials/passport.html) 和官方示例代码，记录以加深理解。
