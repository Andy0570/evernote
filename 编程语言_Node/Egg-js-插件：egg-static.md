[GitHub 源码](https://github.com/eggjs/egg-static)

## 安装

`egg-static` 是 Egg.js 内置的静态服务器插件，它基于 [koa-static-cache](https://github.com/koajs/static-cache)。线上环境建议部署到 CDN，无需该插件。

`egg-static` 插件在 Egg.js 中已默认开启。也就是说，在 Egg.js 项目中， `$baseDir/app/public` 目录是默认的静态文件目录。我们可以把需要用到的 `.js`或者 `.css` 文件放到该目录下，然后在项目中可以通过 `/public/YourStaticFile` 的方式使用静态文件。

## 配置

`egg-static` 支持  [koa-static-cache](https://github.com/koajs/static-cache) 中的所有配置，并且默认配置如下：

* 前缀（prefix）：`'/public/'`
* 目录（dir）：`path.join(appInfo.baseDir, 'app/public')`
* 动态（dynamic）：`true`
* 预加载（preload）：`false`
* 最大生命周期（maxAge）：产品环境中为 `31536000`，其他环境中为 `0`
* 缓存（buffer）：产品环境中为 `true`，其他环境中为 `false`

另外，`egg-static`  支持更多选项：

* `maxFiles`：缓存文件的最大数量，仅在 `dynamic` 参数为 `true` 时有效，默认值为 `1000`。

**可以使用前缀 `/public` 访问 `$baseDir/app/public` 目录中的所有静态文件，并且所有文件都是延迟加载（Lazy Loading）的。**

* 在非生产环境中，生成的静态文件不会被缓存，您的修改可以立即生效。
* 在生产环境中，`egg-static` 将在访问后缓存静态文件，您需要重新启动应用以更新的静态文件。
* Dir 的默认值为 `$baseDir/app/public`，但您也可以使用 `dir:[dir1, dir2, ...]` 或 `dir:[dir1，{prefix：'/static2'，dir：dir2}]` 这种通过数组的方式定义**多个目录**，静态服务器将使用所有这些目录。


## 在 Egg.js 项目中使用 Bootstrap 框架

前端 Web 页面的 CSS 样式使用的是 [Bootstrap](https://v4ing.bootcss.com/
) 客户端框架。

### 方法一：手动导入

如果项目中所有的静态文件本身你都放在 `/public` 目录下，就无需做额外的配置，直接使用默认项即可。
因此，你可以去官网**下载 Bootstrap 编译好的源码**，把需要用到的 Bootstrap 源码文件拖到项目的 `/public` 目录下，把需要用到的样式或脚本添加到模板文件中国即可：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>前端页面标题</title>
  <link rel="stylesheet" href="public/bootstrap.css">
</head>
<body>
  <h1>前端页面内容</h1>
</body>
</html>
```

### 方法二：CDN

生产环境中一般推荐使用 CDN 来分发静态文件，当只需要包含 Bootstrap 编译的 CSS 或 JS 文件时，也可以使用官方 BootstrapCDN。

**仅使用 CSS 文件**：
```html
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
```

**使用 JS, Popper.js, 和 jQuery 文件**
```html
<script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.15.0/umd/popper.min.js" integrity="sha384-L2pyEeut/H3mtgCBaUNw7KWzp5n9+4pDQiExs933/5QfaTh8YStYFFkOzSoXjlTb" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
```

### 方法三：npm

通过 npm 的方式使用 Bootstrap 框架：

```bash
npm install bootstrap --save
```

安装完成后，Bootstrap 的源文件会放在项目的 `node_modules ` 目录下：
![node_modules 目录下的 Bootstrap 文件](https://upload-images.jianshu.io/upload_images/2648731-ddb6e747ecd3eff5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/300)

配置 `egg-static` 插件的多目录：
```javascript
  // {app_root}/config/config.default.js
  // ...
  // 静态文件配置
  config.static = {
    prefix: '/public/',
    dir: [
      path.join(appInfo.baseDir, 'app/public'),
      path.join(appInfo.baseDir, 'node_modules/bootstrap/dist/'),
    ],
  };
```
配置完成后，即可在模板文件中通过 `/public` 前缀的方式直接使用 `node_modules/bootstrap/dist/` 目录下的文件。
比如我现在需要使用 `node_modules/bootstrap/dist/css/bootstrap.css` 这个 CSS 样式文件，模板中可以通过 `public/css/bootstrap.css` 的方式使用，示例如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>客户端页面标题</title>
  <link rel="stylesheet" href="public/css/bootstrap.css">
</head>
<body>
  <h1>客户端页面内容</h1>
</html>
```

### 参考

* [npm：egg-static](https://www.npmjs.com/package/egg-static)
* [简书：egg 静态资源及缓存设置](https://www.jianshu.com/p/7e23f1c69279)


