### GET 传值
获取 URL 路径中的 GET 传值。
```JavaScript
// app/controller/home.js
'use strict';

const Controller = require('egg').Controller;

class HomeController extends Controller {
  async index() {
    // 获取 get 传值
    const query = this.ctx.query;
    // 返回响应信息
    this.ctx.response.body = query;
  }
}

module.exports = HomeController;

// app/router.js
// Router 主要用来描述请求URL和具体承担执行动作的 Controller 的对应关系
'use strict';

module.exports = app => {
  const { router, controller } = app;
  router.get('/', controller.home.index);
};
```

测试请求：<http://127.0.0.1:7001/?name=andy&password=123456   
页面显示：{"name":"andy","password":"123456"}


### 动态路由
根据不同的 URL 路径目录，响应不同的值。
```JavaScript
// app/controller/home.js
'use strict';

const Controller = require('egg').Controller;

class HomeController extends Controller {

  async list() {
    // 获取动态路由值
    const result = this.ctx.params.id;
    // 返回响应信息
    this.ctx.response.body = result;
  }
}

module.exports = HomeController;


// app/router.js
// Router 主要用来描述请求URL和具体承担执行动作的 Controller 的对应关系
'use strict';

module.exports = app => {
  const { router, controller } = app;
  router.get('/list/:id', controller.home.list);
};
```

测试请求：<http://127.0.0.1:7001/list/123>
页面显示：123

