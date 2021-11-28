## 创建主题

* `POST /api/v2/topics`
* 请求体：
  ```json
  {
    accesstoken: 'string',
    title: 'string',
    tab: { type: 'enum', values: [ 'ask', 'share', 'job' ], required: false },
    content: 'string',
  }
  ```
* 响应状态码：201
* 响应体：
  ```json
  {
    "topic_id": "57ea257b3670ca3f44c5beb6"
  }
  ```


## 实现

### 初始化项目

```shell
$ npm i egg-init -g    # 安装Egg脚手架
$ egg-init cnode-api --type=simple
$ cd cnode-api
$ npm i
```

### 开启 validate 插件

```javascript
// config/plugin.js
exports.validate = {
  enable: true,
  package: 'egg-validate',
};
```

### 注册路由

```javascript
// app/router.js
module.exports = app => {
  // app.resources('routerName', 'pathMatch', controller)
  app.router.resources('topics', '/api/v2/topics', app.controller.topics);
};
```

通过 `app.resources` 方法，将 topics 这个资源的增删查改接口映射到了 `app/controller/topics.js` 文件。

### controller 开发

```javascript
// app/controller/topics.js
const Controller = require('egg').Controller;

class TopicsController extends Controller {
  constructor(ctx) {
    super(ctx);

    // 定义创建接口的请求参数规则
    this.createRule = {
      accesstoken: 'string',
      title: 'string',
      tab: { type: 'enum', values: [ 'ask', 'share', 'job' ], required: false },
      content: 'string',
    };
  }

  // ------ 新建 ------
  async create() {
    const { ctx } = this;

    // 1. 调用 validate 方法对请求参数进行验证。
    // 校验 `ctx.request.body` 是否符合我们预期的格式
    // 如果参数校验未通过，将会抛出一个 status = 422 的异常
    ctx.validate(this.createRule, ctx.request.body);

    // 2. 用验证过的参数调用 service 封装的业务逻辑来创建一个 topic。
    // 调用 service 创建一个 topic
    const id = await ctx.service.topics.create(ctx.request.body);

    // 3. 按照接口约定的格式设置响应状态码和内容。
    // 设置响应体和状态码
    ctx.body = {
      topic_id: id,
    };
    ctx.status = 201;
  }

module.exports = TopicsController;
```

### service 开发

```javascript
const Service = require('egg').Service;

class TopicService extends Service {
  constructor(ctx) {
    super(ctx);
    this.root = 'https://cnodejs.org/api/v1';
  }

  async request(url, opts) {
    url = `${this.root}${url}`;
    opts = Object.assign({
      timeout: [ '30s', '30s' ],
      dataType: 'json',
    }, opts);
    return this.ctx.curl(url, opts);
  }

  async create(params) {
    // 调用 CNode V1 版本 API
    const result = await this.request('/topics', {
      method: 'post',
      data: params,
      contentType: 'json',
    });

    // 检查调用是否成功，如果调用失败会抛出异常
    this.checkSuccess(result);
    // 返回创建的 topic 的 id
    return result.data.topic_id;
  }

  // 封装统一的调用检查函数，可以在查询、创建和更新等 Service 中复用
  checkSuccess(result) {
    if (result.status !== 200) {
      const errorMsg = result.data && result.data.error_msg ? result.data.error_msg : 'unknown error';
      this.ctx.throw(result.status, errorMsg);
    }
    if (!result.data.success) {
      // 远程调用返回格式错误
      this.ctx.throw(500, 'remote response error', { data: result.data });
    }
  }
}

module.exports = TopicService;
```

### 统一错误处理

```javascript
// app/middleware/error_handler.js
module.exports = (option, app) => {
  return async function errorHandler(ctx, next) {
    try {
      await next();
    } catch (err) {

      // 所有的异常都在 app 上触发一个 error 事件，框架会记录一条错误日志
      app.emit('error', err, this);

      const status = err.status || 500;
      // 生产环境时 500 错误的详细错误内容不返回给客户端，因为可能包含敏感信息
      const error = (( status === 500 ) && ( app.config.env === 'prod' ))
        ? 'Internal Server Error'
        : err.message;

      // 从 error 对象上读出各个属性，设置到响应中
      ctx.body = { error };
      if (status === 422) {
        ctx.body.detail = err.errors;
      }
      ctx.status = status;
    }
  };
};
```

通过配置文件（`config/config.default.js`）加载中间件：

```javascript
module.exports = appInfo => {
  const config = {};

  // should change to your own
  config.keys = appInfo.name + '_1490750627161_5967';

  // 加载 errorHandler 中间件
  config.middleware = [ 'errorHandler' ];

  // 配置 errorHandler 中间件：只对 /api 前缀的 url 路径生效
  config.errorHandler = {
    match: '/api',
  };

  return config;
};
```



## 参考

* [Egg.js 教程：实现 RESTful API](https://eggjs.org/zh-cn/tutorials/restful.html)
* [CNode 社区 API](https://cnodejs.org/api)
