# `git diff`排除某些文件或目录

做项目时经常需要`diff`查看文件变化。但是文件中经常包含一些构建出来的文件，如`dist目录`、`index.min.js`、`style.min.css`等，此类文件多数为压缩后的代码，基本无法`diff`查看。

所以，就需要对这类文件进行排除，只`diff`开发文件。目前有如下两种方法：

## 一、使用`shell`命令

命令如下：

```bash
$ git status| grep modified | grep -v 'dist' | awk '{print $2}' | xargs git diff
```

此方法来自博文老师。

缺点：抓不到删除、冲突文件，所以，推荐使用下面方法。

## 二、使用`.gitattributes`文件

> 原文: https://www.jianshu.com/p/8ec1b32f2e13

### 1. 设置`git-diff driver`

`Git`支持[自定义diff驱动器](https://git-scm.com/docs/git-config)（见其中`diff.driver.command`的说明），意思是说`git-diff`的时候可以指定一个命令去跑，而不是跑内置的。有了这个配置支持，我们就可以设置如下`nodiff`的指令（在项目根路径）：

```bash
$ git config diff.nodiff.command /usr/bin/true
```

这个指令干嘛呢？这个指令什么都不干，直接返回`true`（`/usr/bin/true`），这样就有了类似跳过的效果。

### 2. 设置`git attributes`

关于`git attributes`可以直接看[官方文档说明](https://git-scm.com/docs/gitattributes) ，我们利用这个特性在`项目根目录`中，创建一个`.gitattributes`文件，并添加如下配置：

```bash
# 忽略文件
build.css diff=nodiff
build.js  diff=nodiff
*.css diff=nodiff

# 忽略目录
dist/** diff=nodiff

# 忽略某个目录下的某种类型文件
dist/**/*.css diff=nodiff
```

好了现在我们就可以连起来理解了，当我们运行`git diff`命令的时候，`git`就会读取`.gitattributes`的配置，然后发现遇到`build.css`和`build.js`文件的时候，需要执行`nodiff指令`，而`nodiff指令`被我们此前配置成了`/usr/bin/true`直接返回`true`，于是，就直接“跳过”了。

这里文件路径的配置和`.gitignore`是一样的，支持目录，文件，通配符之类的，可以轻松实现忽略一批文件或者整个目录。
