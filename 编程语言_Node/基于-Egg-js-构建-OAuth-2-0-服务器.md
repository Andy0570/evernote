
技术栈：Egg.js、MongoDB、EJS

* Egg.js 作为后台服务端框架；
* MongoDB 作为后台服务器的数据库，并且在 Egg.js 下使用 egg-mongoose 连接数据库；
* 需要使用到实现 OAuth 2.0 服务的两个 npm 模块：[node-oauth2-server](https://github.com/oauthjs/node-oauth2-server) 和 [egg-oauth2-server](https://github.com/Azard/egg-oauth2-server#readme)；
* EJS 模板引擎，仅用于实现 Web 登录页面，使用 Bootstrap 作为 CSS 样式表；

## egg-oauth2-server 插件

[node-oauth2-server](https://github.com/oauthjs/node-oauth2-server) 是一个 Node.js 下的 npm 模块，实现了 Node.js 下的 OAuth 2.0 服务，它经过完整，合规且良好的测试。该模块中定义好了实现各种授权的方法，我们只需要[实现对应的接口方法](https://oauth2-server.readthedocs.io/en/latest/model/overview.html)即可实现一个 OAuth 2.0 服务器。

[egg-oauth2-server](https://github.com/Azard/egg-oauth2-server#readme) 是对 [node-oauth2-server](https://github.com/oauthjs/node-oauth2-server) 的一个封装，我们可以在 Egg.js 项目下使用该插件。


## 安装配置插件

在 Egg.js 项目中，通过 npm 命令安装 [egg-oauth2-server](https://github.com/Azard/egg-oauth2-server#readme) 插件：

```bash
npm install --save egg-oauth2-server
```

在 `{app_root}/config/plugin.js` 中开启该插件：

```js
// {app_root}/config/plugin.js
module.exports = {
  // 省略其他无关配置项...
  
  // 开启 egg-oauth2-server 插件
  oAuth2Server: {
    enable: true,
    package: 'egg-oauth2-server',
  },
};
```

在配置文件（`{app_root}/config/config.default.js`）中配置该插件：

```js
// {app_root}/config/config.default.js
module.exports = appInfo => {
  const config = exports = {};
  // 省略其他无关配置项...

  // 配置 OAuth 插件，开启授权码模式和刷新 Token 模式
  config.oAuth2Server = {
    debug: true,
    grants: [ 'authorization_code', 'refresh_token' ],
  };

  return {
    ...config,
    ...userConfig,
  };
};
```
OAuth 2.0 规范中，定义了四种客户端授权模式，`grants` 参数项就是用来描述你想要实现哪种模式。

`grants` 可配置参数项说明：

* `authorization_code` ：授权码模式（Authorization Code Grant）；
* `refresh_token`：刷新 Token 模式，实现刷新访问令牌的功能；
* `password`：密码模式（Resource Owner Password Credentials Grant）；
* `client_credentials`：客户端模式（Client Credentials Grant）；
* `implicit`：简化模式（Implicit Grant）；


### 配置路由

```js
module.exports = app => {
  const { router, controller } = app;
  // 该接口模拟客户端「通过授权码获取 accessToken」操作。
  // 真实环境下 OAuth 服务不需要实现该接口
  router.get('/', controller.home.index);

  // OAuth 服务的前端登录页面
  router.get('/authorize', controller.user.authorize);

  // 获取授权码
  // authorize 是用来获取授权码的路由
  // 生命周期：getClient --> getUser --> saveAuthorizationCode
  router.all('/user/authorize', app.oAuth2Server.authorize());

  // 通过授权码获取 accessToken
  // token 是用来发放访问令牌的路由
  // 生命周期：getClient --> getAuthorizationCode --> saveToken --> revokeAuthorizationCode
  router.all('/user/token', app.oAuth2Server.token());

  // 通过 accessToken 获取用户信息
  // authenticate 是登录之后可以访问的路由
  // 生命周期：getAccessToken
  router.all('/user/authenticate', app.oAuth2Server.authenticate(), ctx => {
    ctx.body = ctx.state.oauth;
  });

  // `ctx.state.oauth` 在控制器中间件之后具有令牌或代码数据。
};
```

💡 因为这里开启和配置 egg-oauth2-server 插件时，使用的名字是 `oAuth2Server`，所以 `app` 调用中间件使用 `app.oAuth2Server` 方法即可。别的文档中会使用 `oauth` 作为名称，当然命名是随意的，统一即可。



## 定义数据表结构模型

本文使用 MongoDB 数据库保存数据。因此，当通过 `npm run dev` 运行测试时，你该先有一个可使用的 MongoDB 数据库。

### 用户模型

用户模型用于描述 OAuth 服务下的用户账户数据，是用户已经注册登记的数据。也就是说，当我们通过 `npm run dev` 运行该项目时，该集合下必须至少有一条可以登录的用户账户信息。

```js
// {app_root}/app/Model/user.js
'use strict';

module.exports = app => {
  const { mongoose } = app;
  const { Schema } = mongoose;
  const uuid = require('uuid/v4');

  // 用户模型
  const UserSchema = new Schema({
    userId: { type: String, default: uuid() },
    username: { type: String },
    password: { type: String },
    creatAt: { type: Date, default: Date.now },
  });

  return mongoose.model('User', UserSchema);
};
```


### 客户端模型

客户端模型描述的是 OAuth 服务下的第三方客户端数据，当第三方应用想要接入我们的 OAuth 服务时，需要提前申请一个接入账号，登记第三方客户端的信息，比如，第三方在登记时，必须要填写 `redirectUri` 回调 URL，当用户在 OAuth 上登录成功，并且同意授权后，OAuth 服务会通过这个回调 URL 返回此用户的授权码（authorization code）。

此外，当第三方客户端申请成功后，我们的 OAuth 服务还会生成并返回一个客户端 ID（`clientId`）和客户端密钥（`clientSecret`）给第三方客户端，客户端需要凭此来证明自己的身份。

```js
// {app_root}/app/Model/client.js
'use strict';

module.exports = app => {
  const { mongoose } = app;
  const { Schema } = mongoose;

  // 客户端模型，
  const ClientSchema = new Schema({
    clientId: { type: String, unique: true },
    clientSecret: { type: String },
    redirectUri: { type: String }, // 客户端的回调 URL
    grants: { type: String }, // 授权模式，比如授权码模式
  });

  return mongoose.model('Client', ClientSchema);
};
```

### 授权码模型

* 授权码在基于重定向的授权流程中使用。一个授权码只能使用一次。
* 官方建议授权码的有效期最多设置为 10 分钟。
* 授权码和客户端 ID 、客户端重定向 URL 是一一对应关系。

```js
// {app_root}/app/Model/authCode.js
'use strict';

module.exports = app => {
  const { mongoose } = app;
  const { Schema } = mongoose;
  // const uuid = require('uuid/v4');

  // 定义模式：授权码（authorization code）模型
  const AuthCodeSchema = new Schema({
    code: { type: String }, // 授权码
    expiresAt: { type: Date }, // 授权码有效期
    redirectUri: { type: String }, // 客户端回调 URL
    scope: { type: String }, // 授权范围
    clientId: { type: String },
    userId: { type: String },
  });

  return mongoose.model('AuthCode', AuthCodeSchema);
};
```


### 访问令牌模型

* 访问令牌是用于访问受保护资源的短期令牌。
* 客户端（通常是第三方服务器后台执行）需要通过授权码获取访问令牌。

```js
// {app_root}/app/Model/token.js
'use strict';

module.exports = app => {
  const { mongoose } = app;
  const { Schema } = mongoose;
  // const uuid = require('uuid/v4');

  // 定义模式：访问令牌（access token）模型
  const TokenSchema = new Schema({
    token: { type: String, unique: true }, // 访问令牌
    expiresAt: { type: Date }, // 访问令牌有效期
    scope: { type: String }, // 授权范围
    clientId: { type: String },
    userId: { type: String },
  });

  return mongoose.model('Token', TokenSchema);
};
```

### 刷新令牌模型

RefreshToken 模型用来存储刷新令牌，为了支持刷新访问令牌，通常将访问令牌的有效期设置较短，当访问令牌过期后，客户端可以通过刷新令牌重新获取一个新的访问令牌。

```js
// {app_root}/app/Model/refreshToken.js
'use strict';

module.exports = app => {
  const { mongoose } = app;
  const { Schema } = mongoose;
  // const uuid = require('uuid/v4');

  // 定义模式：刷新令牌（refresh token）模型
  const RefreshTokenSchema = new Schema({
    token: { type: String, unique: true },
    expiresAt: { type: Date },
    scope: { type: String }, // 授权范围
    clientId: { type: String },
    userId: { type: String },
  });

  return mongoose.model('RefreshToken', RefreshTokenSchema);
};
```

## 实现 OAuth 接口方法

node-oauth2-server 的接口文档：<https://oauth2-server.readthedocs.io/en/latest/model/overview.html>。

你需要实现哪种客户端授权模式，就需要实现该授权模式下的方法。以 `generator` 开头的方法是可选的，因为 node-oauth2-server 内置了生成随机字符串的方法，所以就没必要重写，对于特殊需求我们才重写这些方法，比如对用户的信息通过 JSON Web Token 进行签名。


### 授权码（authorization_code）模式

授权码是代表资源所有者授权（访问其受保护资源）的凭证，客户端使用该授权码来获取访问令牌。

按照 [egg-oauth2-server](https://github.com/Azard/egg-oauth2-server#readme) 中 README 文档的描述，实现授权码（authorization_code）模式需要实现 3 个指定的接口：

#### 1. `/authorize` 是服务端发放授权码的端口

授权码模式 `app.oauth.authorize()` 生命周期：
`getClient` --> `getUser` --> `saveAuthorizationCode`

也就是说，实现 `/authorize` 接口需要实现以上流程中的 3 个方法：`getClient()`、`getUser()` 和 `saveAuthorizationCode()`。

#### 2. `/token` 是客户端「通过授权码获取访问令牌」，服务端校验通过后，发放访问令牌的端口

授权码模式 `app.oauth.token()` 生命周期：
`getClient` --> `getAuthorizationCode` --> `saveToken` --> `revokeAuthorizationCode`

因此，实现 `/token` 接口功能时，`app.oauth.token()` 会调用以上四个方法。

`getClient()` 获取客户端信息的方法和上一个端口中用的是同一个方法，因此，我们还需要实现 `getAuthorizationCode()`、`saveToken()` 和 `revokeAuthorizationCode()`。

#### 3. `/authenticate` 是授权成功后，通过访问令牌获取用户信息的端口

授权码模式 `app.oauth.authenticate()` 生命周期：
仅仅需要实现 `getAccessToken` 方法即可。

### 创建 `oauth.js` 文件

在 `{app_root}/app/extend/` 目录下创建一个 `oauth.js` 文件，实现以上所需的 API。

```js
// {app_root}/app/extend/oauth.js
'use strict';

// 需要实现以下的函数
module.exports = () => {  
  class Model {
    constructor(ctx) {
      this.ctx = ctx;
    }
    
    async getClient(clientId, clientSecret) {}
    async getUser(username, password) {}
    async getAccessToken(bearerToken) {}
    async saveToken(token, client, user) {}
    async revokeToken(token) {}
    async getAuthorizationCode(authorizationCode) {}
    async saveAuthorizationCode(code, client, user) {}
    async revokeAuthorizationCode(code) {}
    async getRefreshToken(refreshToken) {}
  }  
  return Model;
};
```

#### 1. 实现 `getClient`

获取对应的客户端信息。

```js
/**
 * 获取客户端信息
 *
 * @param {*} clientId 要查询的客户端 id
 * @param {*} clientSecret 要校验的客户端密钥
 * @return {*} object
 * @memberof Model
 */
async getClient(clientId, clientSecret) {
  try {
    console.log('getClient() invoked...');
    // 1. 从数据库中查询客户端信息
    const client = await this.ctx.model.Client.findOne({
      clientId,
    });
    if (!client) return false;

    // 2. 校验客户端密钥
    if (clientSecret && (clientSecret !== client.clientSecret)) {
      return false;
    }

    // 3. 返回数据
    return {
      id: client.clientId,
      redirectUris: client.redirectUri.split(','),
      grants: client.grants.split(','),
    };
  } catch (error) {
    return false;
  }
}
```


#### 2. 实现 `getUser`

校验用户信息。

```js
/**
 * 实现用户认证
 * 授权码模式需要实现用户认证
 * user 对象对 oauth2-server 完全透明，并且仅用作其他模型函数的输入。
 *
 * @param {*} username 用户名
 * @param {*} password 密码
 * @return {*} 返回用户信息
 * @memberof Model
 */
async getUser(username, password) {
  try {
    console.log('getUser() invoked...');
    // 1. 从数据库中查询用户信息
    const user = await this.ctx.model.User.findOne({
      username,
    });
    if (!user) return false;

    // 2. 校验用户密码
    if (user.password !== password) {
      return false;
    }
    // 3. 返回用户信息
    return {
      id: user.userId,
    };
  } catch (error) {
    return false;
  }
}
```

#### 3. 实现 `saveAuthorizationCode`

保存授权码到数据库中。

```js
/**
 * 保存授权码信息
 *
 * @param {*} code 要保存的授权码信息
 * @param {*} client 要保存的客户端信息
 * @param {*} user 要保存的用户信息
 * @memberof Model
 */
async saveAuthorizationCode(code, client, user) {
  try {
    console.log('saveAuthorizationCode() invoked...');
    // 1. 保存授权码信息到数据库
    const authCode = await this.ctx.model.AuthCode.create({
      code: code.authorizationCode,
      expiresAt: code.expiresAt,
      redirectUri: code.redirectUri,
      scope: code.scope || '',
      clientId: client.id,
      userId: user.id,
    });

    // 2. 返回数据
    return {
      authorizationCode: authCode.code,
      expiresAt: authCode.expiresAt,
      redirectUri: authCode.redirectUri,
      scope: authCode.scope,
      client: { id: authCode.clientId },
      user: { id: authCode.userId },
    };
  } catch (error) {
    return false;
  }
}
```

#### 4. 实现 `getAuthorizationCode`

查询通过 `saveAuthorizationCode` 方法存储过的授权码并返回。

```js
/**
 * 获取授权码信息
 * 查询通过 saveAuthorizationCode() 方法存储过的授权码信息，并返回
 *
 * @param {*} authorizationCode 要查询的授权码 id
 * @return {*} object
 * @memberof Model
 */
async getAuthorizationCode(authorizationCode) {
  try {
    console.log('getAuthorizationCode() invoked...');
    // 1. 从数据库中查询授权码信息
    const authCode = await this.ctx.model.AuthCode.findOne({
      code: authorizationCode,
    });
    if (!authCode) return false;

    // 2. 从数据库中查询客户端信息、用户信息
    // const [ client, user ] = await Promise.all([
    //   this.ctx.model.Client.findOne({
    //     clientId: authCode.clientId,
    //   }),
    //   this.ctx.model.User.findOne({
    //     userId: authCode.userId,
    //   }),
    // ]);

    const user = await this.ctx.model.User.findOne({
      userId: authCode.userId,
    });
    if (!user) return false;

    // 3. 返回数据
    return {
      code: authCode.code,
      expiresAt: authCode.expiresAt,
      redirectUri: authCode.redirectUri,
      scope: authCode.scope,
      client: { id: authCode.clientId },
      user: { id: authCode.userId },
    };
  } catch (error) {
    return false;
  }
}
```


#### 5. 实现 `saveToken`

保存 Token 令牌，包括访问令牌和刷新令牌。


```js
/**
 * 保存 token 令牌，包括访问令牌和刷新令牌。
 *
 * @param {*} token 要保存的 token 令牌
 * @param {*} client 要保存的客户端信息
 * @param {*} user 要保存的用户信息
 * @return {*} object
 * @memberof Model
 */
async saveToken(token, client, user) {
  try {
    console.log('saveToken() revoked...');
    // 1.保存访问令牌
    const accessToken = await this.ctx.model.Token.create({
      token: token.accessToken,
      expiresAt: token.accessTokenExpiresAt,
      scope: token.scope || '',
      clientId: client.id,
      userId: user.id,
    });

    // 2.保存刷新令牌
    const refreshToken = await this.ctx.model.RefreshToken.create({
      token: token.refreshToken,
      expiresAt: token.refreshTokenExpiresAt,
      scope: token.scope || '',
      clientId: client.id,
      userId: user.id,
    });

    // 3.返回保存的令牌信息
    return {
      accessToken: accessToken.token,
      accessTokenExpiresAt: accessToken.expiresAt,
      refreshToken: refreshToken.token,
      refreshTokenExpiresAt: refreshToken.expiresAt,
      scope: accessToken.scope,
      client: { id: accessToken.clientId },
      user: { id: accessToken.userId },
    };
  } catch (error) {
    return false;
  }
}
```

#### 6. 实现 `revokeAuthorizationCode`

因为授权码只能使用一次，因此当客户端通过授权码获取访问令牌完成后，需要调用该方法以吊销授权码。

```js
/**
 * 吊销授权码信息
 *
 * @param {*} code 要吊销的授权码信息
 * @return {*} object
 * @memberof Model
 */
async revokeAuthorizationCode(code) {
  try {
    console.log('revokeAuthorizationCode() invoked...');
    // 从数据库中查询该授权码并删除
    return await this.ctx.model.AuthCode.findOneAndRemove({
      code: code.code,
    });
  } catch (error) {
    return false;
  }
}
```

#### 7. 实现 `getAccessToken`

当使用访问令牌时，需要查询数据库以验证令牌及有效期信息。

```js
/**
 * 获取访问令牌信息。
 *
 * @param {*} accessToken 要查询的访问令牌
 * @return {*} object
 * @memberof Model
 */
async getAccessToken(accessToken) {
  try {
    console.log('getAccessToken() invoked...');
    // 1. 查询数据库，获取访问密钥信息
    const token = await this.ctx.model.Token.findOne({
      token: accessToken,
    });
    // 2. 查询数据库，获取客户端信息
    // const client = await this.ctx.model.Client.findOne({
    //   clientId: accessToken.client.clientId,
    // });
    // 3. 查询数据库，获取用户信息
    // const user = await this.ctx.model.User.findOne({
    //   userId: accessToken.user.userId,
    // });

    if (!token) return false;

    // 4. 返回数据
    return {
      accessToken: token.token,
      accessTokenExpiresAt: token.expiresAt,
      scope: token.scope,
      client: { id: token.clientId }, // with 'id' property
      user: { id: token.userId },
    };
  } catch (error) {
    return false;
  }
}
```

#### 8. 实现 `revokeToken`

`revokeToken` 方法的功能是吊销刷新令牌。刷新令牌也只能使用一次。因此，当访问令牌过期后，需要使用 `refresh_token` 刷新令牌来获取新的访问令牌。此时，该刷新令牌已经被使用过了，需要吊销。

```js
/**
 * 吊销刷新令牌
 *
 * @param {*} token 要删除的刷新令牌 Object
 * @return {*} object
 * @memberof Model
 */
async revokeToken(token) {
  try {
    console.log('revokeToken() invoked...');
    // 查询数据库并删除刷新令牌
    return await this.ctx.model.RefreshToken.findOneAndRemove({
      token: token.refreshToken,
    });
  } catch (error) {
    return false;
  }
}
```


#### 9. 实现 `getRefreshToken`

实现刷新令牌接口。

```js
/**
 * 获取刷新令牌信息
 *
 * @param {*} refreshToken 要查询的刷新令牌 id
 * @return {*} object
 * @memberof Model
 */
async getRefreshToken(refreshToken) {
  try {
    console.log('getRefreshToken() invoked...');
    // 1. 查询数据库获取刷新令牌
    const token = await this.ctx.model.RefreshToken.findOne({
      token: refreshToken,
    });
    if (!token) return false;

    // 2. 返回数据
    return {
      refreshToken: token.token,
      refreshTokenExpiresAt: token.expiresAt,
      scope: token.scope,
      client: { id: token.clientId }, // with 'id' property
      user: { id: token.userId },
    };
  } catch (error) {
    return false;
  }
}
```


#### 10. 实现 `validateScope`

该方法用以检查请求的 `scope` 授权范围对于特定的 `client`/`user` 组合是否有效。

另外，该接口方法是**可选方法**。如果你没有实现它，则默认任何作用域范围都是有效的。

拒绝无效或仅支持部分有效的权限范围：

```js
// 列出有效的授权范围
const VALID_SCOPES = ['read', 'write'];

async validateScope(user, client, scope) {
  if (!scope.split(' ').every(s => VALID_SCOPES.indexOf(s) >= 0)) {
    return false;
  }
  return scope;
}
```

要接受部分有效的范围：

```js
// 列出有效的授权范围
const VALID_SCOPES = ['read', 'write'];

async validateScope(user, client, scope) {
  // 要求请求的 scope 在 client.scope 和全局的 scope 内。
  return scope
    .split(' ')
    .filter(s => client.scope.indexOf(s) >=0 && VALID_SCOPES.indexOf(s) >= 0)
    .join(' ');
}
```

#### 11. 实现 `verifyScope`

在请求身份验证期间调用，以检查所提供的访问令牌是否在已被授权的作用域内。

如果在 [OAuth2Server#authenticate()](https://oauth2-server.readthedocs.io/en/latest/api/oauth2-server.html#oauth2server-authenticate) 中使用了 `scope` 属性，那么该接口方法是必须实现的。

```js
async verifyScope(token, scope) {
  // 判断空 scope
  if (!token.scope) return false;
  
  const requestedScopes = scope.split(' ');
  const authorizedScopes = token.scope.split(' ');
  return requestedScopes.every(s => authorizedScopes.indexOf(s) >= 0);
```

## 向数据库中填充测试数据

为了方便测试，需要向空白的数据库中填一个测试用的客户端数据和用户数据。

创建测试的客户端数据：

```js
async index() {
  const { ctx } = this;
  const result = await this.app.model.Client.create({
    clientId: '123456',
    userId: '654321',
    clientSecret: 'qwerty',
    redirectUri: 'http://127.0.0.1:7001',
    grants: 'authorization_code,refresh_token', // 授权模式有两个！！！
  });
  console.log(result);
}
```

创建测试的用户数据：

```js
async index() {
  const { ctx } = this;

  const result = await this.ctx.model.User.create({
    username: 'andy',
    password: '123456',
  });
  console.log(result);
}
```


## 实现前端登录页面

在路由（`router.js`）中有一条路由：

```js
router.get('/authorize', controller.user.authorize);
```

当第三方客户端请求获取授权码时，便会带着必要的查询字符将它的页面重定向到该接口，然后我们需要显示一个登录页面，让用户登录以验证用户的身份并同意授权。

登录所需的 EJS 模板视图如下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>OAuth Account Login Page</title>
  <!-- Bootstrap CDN-->>
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</head>

<body>
  <h1><%= title %></h1>
  <!-- 用户点击「登录」按钮时，此页面发起 POST /users/authorize?{{query}} 请求并获取授权码-->>
  <!-- 此时，会调用 OAuth 服务器实现的该路由接口，验证客户端和用户身份后，创建并返回授权码-->>
  <!-- app.all('/users/authorize', app.oAuth2Server.authorize()); // 获取授权码-->>
  <!-- 此模式下，授权码会通过重定向 URL 返回给客户端-->>
  <form action="/user/authorize?<%=query%>" method="post">
    <div class="form-group">
      <label for="username">用户名</label>
      <input type="text" class="form-control" id="username" name='username'>
    </div>
    <div class="form-group">
      <label for="password">密码</label>
      <input type="password" class="form-control" id="password" name='password'>
    </div>
    <button type="submit" class="btn btn-primary">登录</button>
  </form>

</body>

</html>
```

路由有了、视图也有了，还需要一个提供一个控制器处理路由逻辑：

```js
// {app_root}/app/controller/user.js
'use strict';

const Controller = require('egg').Controller;

class UserController extends Controller {
  // 渲染登录页面
  async authorize() {
    const query = this.ctx.querystring;
    await this.ctx.render('login.ejs', {
      title: 'OAuth 账户登录',
      query,
    });
  }
}

module.exports = UserController;
```

1. 第三方客户端发起 `GET /authorize` 请求，并跳转到 OAuth 的登录页面，用户在该页面下输入用户名和密码登录自己的 OAuth 账户;
2. 用户点击登录按钮，登录页面发起 `POST /users/authorize?{{query}}` 请求以获取授权码，这时候就会开始执行授权码模式的 `app.oauth.authorize()` 生命周期。
3. OAuth 授权通过后，会向客户端的指定的重定向 URL 返回授权码。

这里，我们在 Egg.js 的默认 home `index` 路由下实现一个得到返回的授权码后，立即请求获取访问令牌的 HTTP 请求，模拟第三方的客户端后台服务器操作：

路由：
```js
router.get('/', controller.home.index);
```


控制器处理逻辑：

```js
'use strict';

const Controller = require('egg').Controller;

class HomeController extends Controller {
  async index() {
    const { ctx } = this;

    // 客户端实现
    // 访问 /user/token 端口获取 accessToken
    console.log(ctx.query);
    const result = await ctx.curl('http://127.0.0.1:7001/user/token', {
      dataType: 'json',
      contentType: 'application/x-www-form-urlencoded',
      method: 'POST',
      timeout: 3000,
      data: {
        grant_type: 'authorization_code',
        code: ctx.query.code,
        client_id: '123456',
        client_secret: 'qwerty',
        redirect_uri: 'http://127.0.0.1:7001/',
      },
    });
    ctx.body = result.data;
  }
}

module.exports = HomeController;
```


## 测试 API 接口

执行流程：
1. 先访问 `/user/authorize` 接口获取授权码；
2. 再访问 `/user/token` 使用授权码获取 AccessToken；
3. 访问 `/user/token` 刷新 AccessToken;
4. 最后访问 `/user/autherticate` 获取用户信息


### 1. 获取授权码

客户端发起一个 GET 请求：

```js
http://localhost:7001/authorize?response_type=code&client_id=123456&state=xyz&redirect_uri=http://127.0.0.1:7001/
```

测试时，可以在浏览器中输入上面的链接，然后浏览器会跳转到 OAuth 的登录页面：

![OAuth 登录页面](https://upload-images.jianshu.io/upload_images/2648731-bca5295f35211de4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/620)


填写用户名密码，点击登录，登录页面会访问 `/user/authorize` 接口来获取授权码。

OAuth 验证客户端和用户身份通过后，会向重定向 uri 的查询字符串中返回授权码：

```js
http://127.0.0.1:7001/?code=db6132c7820aa42d74c69b6fe4074db833163d38&state=xyz
```

为了方便测试，我们用 home 的 `index` 路由来接收返回的授权码。

OAuth 服务器返回的 `code` 字段就是授权码。

还有一个 `state` 字段是客户端在第一步中上传的参数，这里原样返回，用于第三方应用防止 CSRF 攻击。


### 2. 获取访问令牌

模拟客户端获取访问令牌的处理逻辑在 home 的 `index` 路由下实现：

```js
const result = await ctx.curl('http://127.0.0.1:7001/user/token', {
  dataType: 'json',
  contentType: 'application/x-www-form-urlencoded',
  method: 'POST',
  timeout: 3000,
  data: {
    grant_type: 'authorization_code',
    code: ctx.query.code,
    client_id: '123456',
    client_secret: 'qwerty',
    redirect_uri: 'http://127.0.0.1:7001/',
  },
});
ctx.body = result.data;
```

客户端通过授权码获取访问令牌调用的路由：
```js
router.all('/user/token', app.oAuth2Server.token());
```

验证通过后，OAuth 服务返回的访问令牌信息如下：

```js
{
    "access_token": "fa73d062db6d703aa5c57edb36fa1c39d622cb59",
    "token_type": "Bearer",
    "expires_in": 3599,
    "refresh_token": "9f36f4f0f90c62b6f775906cc0f7afa4f6362d37"
}
```


### 3. 刷新访问令牌


通过 `refresh_token` 获取新的 `access_token`。

客户端刷新访问令牌调用的路由端口和获取访问令牌的端口是同一个，即：
```js
router.all('/user/token', app.oAuth2Server.token());
```

**egg-oauth2-server** 的配置文件中之前也配置了会使用刷新令牌模式：

```js
config.oAuth2Server = {
  debug: true,
  grants: [ 'authorization_code', 'refresh_token' ],
};
```

相应获取刷新令牌和吊销刷新令牌的接口上面也已经写好了。

另外，测试该接口时，客户端模型的 `grants` 字段的值为：

`grants:"authorization_code,refresh_token"`，需要有 `refresh_token` 字段。



客户端 CURL 请求和响应示例：

```bash
curl --include \
     --request POST http://127.0.0.1:7001/user/token \
     --data 'grant_type=refresh_token&refresh_token=9f36f4f0f90c62b6f775906cc0f7afa4f6362d37&client_id=123456&client_secret=qwerty'
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
cache-control: no-store
pragma: no-cache
x-frame-options: SAMEORIGIN
x-xss-protection: 1; mode=block
x-content-type-options: nosniff
x-download-options: noopen
x-readtime: 53
keep-alive: timeout=5
content-length: 158
Date: Tue, 14 Jan 2020 06:37:38 GMT
Connection: keep-alive

{"access_token":"034ab4f6182290e4363e9c9f650fe8e13f8eb6b7","token_type":"Bearer","expires_in":3599,"refresh_token":"85c003ddad6edd656a891e6d9b313b8af7cfe4e4"}%
```

请求成功后，OAuth 服务端会返回一个 JSON 对象：

```json
{
    "access_token": "034ab4f6182290e4363e9c9f650fe8e13f8eb6b7",
    "token_type": "Bearer",
    "expires_in": 3599,
    "refresh_token": "85c003ddad6edd656a891e6d9b313b8af7cfe4e4"
}
```



### 4. 获取用户信息

客户端通过 `accessToken` 获取用户信息。

执行调用的 OAuth 路由为：

```js
router.all('/user/authenticate', app.oAuth2Server.authenticate(), ctx => {
    ctx.body = ctx.state.oauth;
  });
```

你可以执行实现自己所需的路由逻辑，这里我们只是在响应体中返回 `ctx.state.oauth` 信息。


客户端 CURL 请求和响应示例：

```bash
curl --include \
     http://localhost:7001/user/authenticate \
     -H "Authorization: Bearer 034ab4f6182290e4363e9c9f650fe8e13f8eb6b7"
HTTP/1.1 200 OK
content-type: application/json; charset=utf-8
x-frame-options: SAMEORIGIN
x-xss-protection: 1; mode=block
x-content-type-options: nosniff
x-download-options: noopen
x-readtime: 21
keep-alive: timeout=5
content-length: 207
Date: Tue, 14 Jan 2020 06:56:23 GMT
Connection: keep-alive

{"token":{"accessToken":"034ab4f6182290e4363e9c9f650fe8e13f8eb6b7","accessTokenExpiresAt":"2020-01-14T07:37:38.174Z","scope":"","client":{"id":"123456"},"user":{"id":"cc7e237c-b83f-49ec-b6be-167672a12eef"}}}%

```

OAuth 服务器返回的 `ctx.state.oauth` 数据如下：

```json
{
    "token": {
        "accessToken": "034ab4f6182290e4363e9c9f650fe8e13f8eb6b7",
        "accessTokenExpiresAt": "2020-01-14T07:37:38.174Z",
        "scope": "",
        "client": {
            "id": "123456"
        },
        "user": {
            "id": "cc7e237c-b83f-49ec-b6be-167672a12eef"
        }
    }
}
```

## 参考

* [Doc - oauth2-server](https://oauth2-server.readthedocs.io/en/latest/index.html)
* [腾讯开放平台 - OAuth2.0 简介](https://wiki.open.qq.com/wiki/website/OAuth2.0%E7%AE%80%E4%BB%8B)
* [使用 Egg.js 快速开发 OAuth 2.0 授权服务](https://cnodejs.org/topic/592b2aedba8670562a40f60b)
* [nodejs 实现 OAuth2.0 授权服务](https://www.cnblogs.com/vipzhou/p/8125924.html)



