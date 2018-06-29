# 在mac上安装XWAMP

## XWAMP介绍

> 适用于 Mac OS X 的 XAMPP 是 Mac OS X 上最简单，最实用，也最完整的网络服务器解决方案。该发行版包括整合了最新的 MySQL、PHP，和 Perl 的 Apache 2 服务器。它以 Mac OS X 安装包的方式发布，包含所有必须的文件，无需下载其它东西。<br>
 如果您是一位有经验的网络开发人员，或者是需要运行服务器、创建的动态网页或使用数据库的 Mac 爱好者，这就是您要找的东西！  <br>
 该版本需要 Mac OS X 10.4 (Intel&PPC) 或更高。

## 安装

 去[官网](https://www.apachefriends.org/download.html?xampp-macosx-1.7.3.dmg)下载安装文件，选择`XAMPP for OS X` -> `php5`的版本。

 一路`next`至安装结束

## 配置

1、将需要访问的项目文件夹放入`/Applications/XAMPP/xamppfiles/htdocs`中

2、在`hosts`中配置域名(方法见最下面)

``` bash
127.0.0.1  local.dfq.com
```

3、在`/Applications/XAMPP/xamppfiles/etc`中找到`httpd.conf`文件中搜索`Directory`关键词，把前面两行注释掉，添加后面四行

``` bash
<Directory />
    # AllowOverride none
    # Require all denied

    Options all
    AllowOverride all
    Order allow,deny
    Allow from all
</Directory>
```

4、再搜索`Virtual hosts`关键字,把下面的`Include etc....`前面的`#`去掉，点击保存

``` bash
# Virtual hosts
Include etc/extra/httpd-vhosts.conf
```

5、在`/Applications/XAMPP/xamppfiles/etc/extra`文件夹,找到`httpd-vhosts.conf`，添加一个如下代码，注意修改访问路径和服务器名称

``` bash
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "/Applications/XAMPP/htdocs/fashion-static/deploy/static"
    ServerName local.dfq.com
    ErrorLog "logs/dummy-host2.example.com-error_log"
    CustomLog "logs/dummy-host2.example.com-access_log" common
</VirtualHost>
```

设置完毕后**重启`XWAMP`**

## 配置域名方法

**方法1：**

1、点击`Finder`，在顶部菜单栏选择`前往` -> `前往文件夹`，输入`/private/etc/`这个路径

2、找到`hosts`文件，复制一份到桌面。用`Mac OS X`系统自带的文本编辑器就能编辑`hosts`文件。添加好你要访问（或者拦截）的网站相关`hosts`信息后保存，拖回`Finder`里的`/private/etc/`文件夹下即可。拖回去的时候，Mac 会弹出报警说无法移动项目。点击`认证`按钮然后输入电脑密码即可。

**方法2：**

使用命令行修改`hosts`，在mac中打开终端，输入以下命令，需要熟悉使用`vim`命令

``` bash
$ sudo vi /etc/hosts
```

**方法3：**

使用第三方软件，如：[SwitchHosts](https://oldj.github.io/SwitchHosts/)
