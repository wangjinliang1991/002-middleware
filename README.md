# 002-middleware


## cnode-api
先做一个conode-api

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

