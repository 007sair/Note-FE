
# `html-webpack-plugin`与`html-loader`冲突

## 两个插件的使用场景

- `html-webpack-plugin`用于在页面中动态插入`link`、`script`标签，添加修改版本号，也可根据不同环境（开发、生产）配置不同的资源路径，下面会提到。
- `html-loader`用于将html以字符串形式回传，说白了就是可以在js中`require`一个html，而不用拼接html字符串。此处作用为**修改html后自动刷新浏览器**。

了解了两个插件的使用，再来说下业务场景，先上代码：

``` html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="<%= htmlWebpackPlugin.options.staticPath %>/morris.css">
    <title><%= htmlWebpackPlugin.options.title %></title>
</head>
```

``` js
// entry.js
if(__DEV__) { // FOR HMR
    require("src/index.html");
}

// webpack.dev.config.js
const HtmlWebpackPlugin = require('html-webpack-plugin');
const TARGET = process.env.npm_lifecycle_event;

module.exports = {
    context: __dirname,
    entry: "./entry.js",
    output: {
        path: path.join(__dirname, "dist/webpack-" + webpackMajorVersion),
        publicPath: "",
        filename: "bundle.js"
    },
    module: {
        loaders: [
            { test: /\.html$/, loader: "html-loader" }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            title: 'this is title',
            filename: "index.html",
            template: "./src/index.html",
            staticPath: TARGET !== 'dev' ? './css/lib' : '../static/lib'
        }),
    ]
}
```

## 场景1

同时使用两个插件，报错如下：

``` js
Refused to apply style from '<URL>' because its MIME type ('text/html') is not a supported stylesheet MIME type, and strict MIME checking is enabled.
```

错误原因：页面样式路径引用不合法，即`html-webpack-plugin`的资源替换未起作用，但可以正常引入`entry.js`。

## 场景2

删除`html-loader`后，页面资源路径能被替换，但报错如下：

``` js
Uncaught Error: Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type.
| <!DOCTYPE html>
| <html lang="en">
| <head>
```

错误原因：语法错误，不能解析html，是因为我们的入口文件中有如下代码：

``` js
if(__DEV__) { // FOR HMR
    require("src/index.html");
}
```

删掉此代码后`html-webpack-plugin`起作用，但无法保存html后刷新浏览器

## 填坑

我们既要保证保存html后刷新浏览器，又要html内能正常解析资源路径。办法如下：

1. 去掉`webpack.config.js`中的`html-loader`
2. `html-webpack-plugin`正常配置
3. `entry.js`中的`require("src/index.html")`改为`require("html-loader!@/index.html")`

ok，问题解决~~

当我们以后要使用`html-loader`时，加前缀`html-loader!`即可。虽然我使用得并不多~

`tip:` 如果对`html-loader`依赖较大，只想替换`index.html`中的资源路径，可以考虑增加其他插件，请自行google。