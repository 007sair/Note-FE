# 调试微信jssdk

> 最近在做一个h5的项目，需要用到微信jssdk的图片上传功能。踩坑记录~

## 一、配置

1. 进入微信公众号，找到`基本配置`，获取到`AppID`、`AppSecret`，后端需要使用这两个数据生成对应的`tooken`和`tiket`，此处省略。然后填写好`IP白名单`（重要）；

2. 在`公众号设置 - 功能设置 - JS接口安全域名` 处设置需要访问页面的域名（注：有坑，见后文），如：`sair.example.com`。

## 二、调试

填好配置，接下来就是调试。微信要求前端传入的url必须为动态线上的url，且端口必须为`80`、`443`，这样一来，我们在本地进行调试时就会涉及如何将本地url变成线上url的问题了。

本地调试环境除了电脑端的`微信开发者工具`，就是真机的微信环境调试。但电脑端无法调试某些接口，如`chooseImage`、`getLocalImgData`，所以，真机调试就变得尤为重要了。现将思路整理如下:

### 思路1，代理变更url地址

前提：有`Apache`环境，项目能通过`127.0.0.1`被访问到。然后通过`charles`代理，使手机也能访问电脑端的域名。

**工具：**

1. 一台`Mac`、一个`iPhone`；
2. 开启代理和抓包的工具[Charles](https://www.charlesproxy.com/)；
3. 修改`Hosts`工具[SwitchHosts](https://github.com/oldj/SwitchHosts)或者[Gas Mask](https://github.com/2ndalpha/gasmask)。

**步骤：**

1. `Mac`与`iPhone`需在同一个局域网内；
2. 打开`charles`设置代理服务；
3. iPhone上打开`设置 - 无线局域网 - 选中当前网络 - HTTP代理 - 配置代理`，在`服务器`一栏输入Mac的ip地址，端口填写charles上的端口号（默认为`8888`）；
4. 修改Mac的hosts，如：`127.0.0.1  sair.example.com`；
5. 在手机上访问`sair.example.com`即可进行调试。

### 思路2，代理变更+端口转发

另类场景，由于本人一直在开发环境使用`webpack`，本地访问地址一般都是`localhost:8080`，而微信仅支持`80`、`443`端口，方案如下：

> 提前了解：Mac禁止了普通用户访问1024以下的端口，包括80端口，因为Mac会用这些端口来提供文件共享等等很多服务。

停掉Mac自带的占用`80`端口的程序(其实就是一个`apache`)，然后再设置端口转发，将`80端口`的请求转发到`8080`或`9090`端口。

具体操作：

1. 关闭占用80端口的apache：`sudo apachectl stop`；

2. 修改`/etc/pf.conf`，设置端口转发，操作如下：

``` bash
$ sudo vi /etc/pf.conf
# 在 rdr-anchor "com.apple/*" 后添加 
rdr on lo0 inet proto tcp from any to 127.0.0.1 port 80 -> 127.0.0.1 port 8080
```

修改后的效果：

``` bash
#
# Default PF configuration file.
#
# This file contains the main ruleset, which gets automatically loaded
# at startup.  PF will not be automatically enabled, however.  Instead,
# each component which utilizes PF is responsible for enabling and disabling
# PF via -E and -X as documented in pfctl(8).  That will ensure that PF
# is disabled only when the last enable reference is released.
#
# Care must be taken to ensure that the main ruleset does not get flushed,
# as the nested anchors rely on the anchor point defined here. In addition,
# to the anchors loaded by this file, some system services would dynamically
# insert anchors into the main ruleset. These anchors will be added only when
# the system service is used and would removed on termination of the service.
#
# See pf.conf(5) for syntax.
#

#
# com.apple anchor point
#
scrub-anchor "com.apple/*"
nat-anchor "com.apple/*"
rdr-anchor "com.apple/*"
rdr on lo0 inet proto tcp from any to 127.0.0.1 port 80 -> 127.0.0.1 port 9090
dummynet-anchor "com.apple/*"
anchor "com.apple/*"
load anchor "com.apple" from "/etc/pf.anchors/com.apple"
```

3. 使修改生效，依次执行以下命令：

```
$ sudo pfctl -d
$ sudo pfctl -f /etc/pf.conf  
$ sudo pfctl -e 
```

4. 设置`hosts`

```
127.0.0.1  sair.example.com
```

ok，启动`webpack`后，访问`sair.example.com`时我们实际访问的是`127.0.0.1:80`，而由于设置了转发，所以我们最后访问的实际是`127.0.0.1:8080`。

### 思路3，内网穿透

以上两种方案均通过代理的方式实现，过程比较绕，但也能解决问题。本人在开发中遇到了`http`强行重定向到`https`的坑，所以有内网穿透这个方法：通过`ngrok`将内网ip映射到外网，然后手机直接访问外网地址即可~简单粗暴直接了当。

**步骤：**

1. 下载[Sunny-Ngrok](https://ngrok.cc/) 或者 [natapp](https://natapp.cn/)

这两个工具都适合在国内使用，对比如下：

    sunny-ngrok：

    a. 完全免费 
    b. 可以定义多条隧道 
    c. 可以完全自定义域名 
    d. 需要自己申请域名并备案（算是一个缺点吧）

    natapp：

    a. 基本免费，高级功能收费（如自定义域名） 
    b. 免费版每个协议只能申请一条隧道 
    c. 域名随机生成，不能完全自定义域名。收费版也只能修改域名的前缀 
    d. 不需要单独申请域名（优点） 
    e. 运行简单，下载执行程序直接运行即可，默认监听80端口。如果要监听其它端口，没有注册帐号的前提下，需要用web服务器做反向代理

2. 运行软件，获取到外网映射地址直接访问

3. 填入安全域名，分两种情况：

a. 有[公众平台测试账号](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)

此时只需要填入`接口配置信息`的URL（后端根据此页面提供的appid，appsecret写好的接口）、约定好的token、`JS接口安全域名`处填写`ngrok`生成的外网地址。

前端调取接口获取config信息时也需要根据测试和线上环境获取不同的信息。

此方法适合使用`natapp`，生成的外网地址不固定，可随时更改使用。

b. 没有公众平台测试账号

我们可以在`公众号设置 - 功能设置 - JS接口安全域名`处填入使用`sunny-ngrok`生成的固定的外网域名，将验证的配置文件`xxxx.txt`文件放入本地服务器，保证`外网地址/xxx.txt`能被访问到即可。

推荐使用a方法，稳定，但需要最开始做一些配置。


## 参考

- [微信JS-SDK说明文档](https://mp.weixin.qq.com/wiki?action=doc&id=mp1421141115&t=0.6318919454950129)
- [解决Mac 80端口被占用](https://blog.csdn.net/toocruel/article/details/79987388)
- [微信分享JSSDK-invalid signature签名错误的解决方案](https://www.cnblogs.com/vipstone/p/6732660.html)
- [ngrok 内网穿透，实现微信开发本地化](https://blog.gavinzh.com/2016/11/05/ngrok/)
