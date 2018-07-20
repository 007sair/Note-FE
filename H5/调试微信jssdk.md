# 调试微信jssdk

> 最近在做一个h5的项目，需要用到微信jssdk的图片上传功能。踩坑记录~

## 一、配置

1. 进入微信公众号，找到`基本配置`，获取到`AppID`、`AppSecret`，后端需要使用这两个数据生成对应的`tooken`和`tiket`，此处省略。然后填写好`IP白名单`（重要）；

2. 在`公众号设置 - 功能设置 - JS接口安全域名` 处设置需要访问页面的域名（注：有坑，见后文），如：`sair.example.com`。

## 二、调试

前端引用`https://res.wx.qq.com/open/js/jweixin-1.2.0.js`，调用后端接口后在`wx.config`中填写配置信息。
电脑端可使用`微信开发者工具`，选择`微信公众号项目`。此时会出现一个类似chrome开发者工具的界面，有地址栏和debug界面。
但是电脑端调试时某些api无法使用，如：`wx.chooseImage`，需要用到手机端的微信内置浏览器。

### 线上

打开`wx.config`的`debug`直接使用`JS接口安全域名`的地址访问，查看配置信息是否ok。只要后端签名与前端传入的url没有问题，一般都是ok的

### 本地

#### 思路1

前提：有Apache环境，项目能通过`127.0.0.1`被访问到。然后通过`charles`代理，使手机也能访问电脑端的域名。

**工具：**

1. 一台`Mac`、一个`iPhone`；
2. 开启代理和抓包的工具[Charles](https://www.charlesproxy.com/)；
3. 修改`Hosts`工具[SwitchHosts](https://github.com/oldj/SwitchHosts)或者[Gas Mask](https://github.com/2ndalpha/gasmask)。

**步骤：**

1. `Mac`与`iPhone`需在同一个局域网内
2. 打开charles代理服务
3. iPhone上打开`设置 - 无线局域网 - 选中当前网络 - HTTP代理 - 配置代理`，在`服务器`一栏输入Mac的ip地址，端口填写charles上的端口号，默认为`8888`
4. 修改Mac的hosts，如：`127.0.0.1    sair.example.com`
5. 在手机上访问`sair.example.com`即可进行调试

好处是可以直接使用

#### 思路2

另类场景，由于本人一直在开发环境使用`webpack`，本地访问地址一般都是`localhost:8080`，而微信仅支持`80`、`443`端口，方案如下：

> 提前了解：Mac禁止了普通用户访问1024以下的端口，包括80端口，因为Mac会用这些端口来提供文件共享等等很多服务。

停掉Mac自带的占用`80`端口的程序(其实就是一个`apache`)，然后再设置端口转发，将`80端口`的请求转发到`8080`或`9090`端口。

具体操作：

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

4. 设置`hosts`

```
127.0.0.1  sair.example.com
```

ok，启动`webpack`后，访问`sair.example.com`时我们实际访问的是`127.0.0.1:80`，而由于设置了转发，所以我们最后访问的实际是`127.0.0.1:8080`。


## 参考

- [微信JS-SDK说明文档](https://mp.weixin.qq.com/wiki?action=doc&id=mp1421141115&t=0.6318919454950129)
- [解决Mac 80端口被占用](https://blog.csdn.net/toocruel/article/details/79987388)
- [微信分享JSSDK-invalid signature签名错误的解决方案](https://www.cnblogs.com/vipstone/p/6732660.html)