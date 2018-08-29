# 获取hash值

应用场景：每次构建时获取到当前构建的hash值，这个值要能在业务代码中使用

插件：https://github.com/webpack/docs/wiki/list-of-plugins#extendedapiplugin

代码如下：

```js
// webpack.config.js
const webpack = require('webpack')

module.exports = {
    plugins: [
        new webpack.ExtendedAPIPlugin()
    ]
}

// index.js
console.log(__webpack_hash__)
```

**注意：** 建议在`dev`和`build`环境z中都添加，否则会出现`__webpack_hash__ is not defined`的错误