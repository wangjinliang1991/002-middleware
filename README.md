# 002-middleware


## cnode-api
先做一个conode-api, 参考 cnode-api https://www.eggjs.org/zh-CN/tutorials/restful

`GET /api/v2/topics`

router-controller-service

- 安装插件
yarn add egg-validate

- 开启插件
```js
exports.validate = {
  enable: true,
  package: 'egg-validate',
};
```

- 注册路由
通过`app.resources`方法，将topics资源的CRUD接口映射到`app.controller.topics`文件
```js
module.exports = (app) => {
  app.router.resources('topics', '/api/v2/topics', app.controller.topics);
};
```

- controller开发

...

config.default.js 处理跨域问题

```js
config.security = {
    csrf: {
        enable: false,
    },
};
```



 app/middleware/gzip.js

```js
const isJSON = require('koa-is-json');
const zlib = require('zlib');

module.exports = options => {
  return async function gzip(ctx, next) {
    // koa的洋葱头模型，response的gzip在next之后执行
    await next();

    // 后续中间件执行完成后将响应体转换成 gzip
    let body = ctx.body;
    if (!body) {
      return;
    }

    // 支持options.threshold
    if (options.threshold && ctx.length < options.threshold) {
      return;
    }

    if (isJSON(body)) {
      body = JSON.stringify(body);
    }

    // set gzip body
    const stream = zlib.createGzip();
    stream.end(body);
    ctx.body = stream;
    ctx.set('Content-Encoding', 'gzip');
  };
};
```



config.default.js

```js
config.middleware = [ 'gzip' ];
```

match指定中间件的匹配条件，使中间件只在特定的请求路径、方法或其他条件满足时生效，从而更灵活地管理中间件的执行

```js
config.gzip = {
    match: '/api',
  }
```

![image-20230722184948019](https://raw.githubusercontent.com/wangjinliang1991/mypic/master/image-20230722184948019.png)

match改为static

![image-20230722185041769](https://raw.githubusercontent.com/wangjinliang1991/mypic/master/image-20230722185041769.png)



```js
config.gzip = {
    match: '/api',
    threshold: 20480,
  }
```

### 渐进式开发

lib/middleware/gzip

```
- package.json
- index.js
```

```
{
  "name": "koa-gzip",
  "description": "Koa gzip middleware",
  "version": "0.0.1",
  "files": [
    "index.js"
  ]
}
```
转移到lib的index.js

原来的gzip引入lib

```js
module.exports = require('../../lib/middleware/gzip');
```


























