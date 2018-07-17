# 调试微信jssdk

> 最近在做一个h5的项目，需要用到微信jssdk的图片上传功能。踩坑正式开始

## 一、配置

1. 首先进入微信公众号，找到"基本配置"，获取到`AppID`、`AppSecret`，后端需要使用这两个数据生成对应的`tooken`和`tiket`，此处省略。然后填写好`IP白名单`（重要）；

2. 在`公众号设置` -> `功能设置` -> `JS接口安全域名` 处填好访问页面的域名（注：此处有巨坑，见解决方案），域名据说可以使用二级域名，如：`jd.com`。

## 二、调试

前端引用`https://res.wx.qq.com/open/js/jweixin-1.2.0.js`，调用后端接口后在`wx.config`中填写配置信息。

打开`微信开发者工具`，选择`微信公众号项目`。此时会出现一个类似chrome开发者工具的界面，有地址栏和debug界面。

### 线上调试

打开`wx.config`的`debug`直接使用`JS接口安全域名`的地址访问，查看配置信息是否ok。只要后端签名与前端传入的url没有问题，一般都是ok的

### 本地调试

由于本地调试的域名限制，坑会略多，现总结思路：

1. 配置`apache`，启动一个本地能访问`local.demo.jd.com`的地址，将项目扔到`apache`中，微信仅支持`80`、`443`端口访问，所以此方案有效。

2. 另类场景，由于我一直在开发环境使用`webpack`，本地访问地址一般都是`localhost:8080`，而微信又是不支持`8080`端口的，方案如下：

## 解决方案

> 提前了解：mac禁止了普通用户访问1024以下的端口，包括80端口，因为mac会用这些端口来提供文件共享等等很多服务。

停掉Mac自带的占用`80`端口的程序(其实就是一个`apache`)，然后再设置端口转发，将`80端口`的请求转发到`8080`或`9090`端口。

具体操作：

1. 关闭占用80端口的apache：`sudo apachectl stop`
2. 修改/etc/pf.conf，设置端口转发：

`sudo vi /etc/pf.conf` 在 `rdr-anchor "com.apple/*"` 后添加 `rdr on lo0 inet proto tcp from any to 127.0.0.1 port 80 -> 127.0.0.1 port 8080`

修改后的效果：

```
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
sudo pfctl -d
sudo pfctl -f /etc/pf.conf  
sudo pfctl -e 
```

4. 设置`hosts`

```
127.0.0.1   local.demo.jd.com
```

ok，启动`webpack`后，访问`local.demo.jd.com`时我们实际访问的是`127.0.0.1:80`，而由于设置了转发，所以我们最后访问的实际是`127.0.0.1:8080`。
