# `Invalid Host Header`

修改本地hosts后，访问指向域名时页面报`Invalid Host Header`

官方提供了两个解决方案：

- 执行 `webpack-dev-server` 命令时手动添加 `--public` 选项，取值为授权的 `host`，这是官方建议的做法，目的是为了安全。
- 设置 `webpack-dev-server` 的配置项 `disableHostCheck` 为 `true` 以禁用这一检测，如果开发者使用了代理，或在开发环境中不 `care` 这些安全问题，该设置可以直接斩草除根。

参考链接：[https://tonghuashuo.github.io/blog/webpack-dev-server-invalid-host-header.html](https://tonghuashuo.github.io/blog/webpack-dev-server-invalid-host-header.html)