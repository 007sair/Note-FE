# `SPA`在服务器中的配置

## 背景

开发`SPA`应用时，如果不配置服务器，会出现刷新页面时`404`的情况。这是因为`HTTP`服务器默认情况下访问的是对应目录下的`index.html`，如需正常访问子页面，需要将路由映射到`index.html`，意思是如果`URL`匹配不到任何静态资源，则应该返回同一个`index.html`页面，这个页面就是您`app`依赖的页面。

## `Apache`配置

打开`/Applications/XAMPP/xamppfiles/etc/extra/httpd-vhosts.conf`

将：

``` php
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host2.example.com
    DocumentRoot "/Applications/XAMPP/htdocs/fashion-static/deploy/static"
    ServerName local.dfq.com
    ErrorLog "logs/dummy-host2.example.com-error_log"
    CustomLog "logs/dummy-host2.example.com-access_log" common
</VirtualHost>
```

改为：

``` php
# To host on root path just use  "<Location />" for http://mydomainname.in
# To host on non-root path use "<Location /myreactapp>" for http://mydomainname.in/mypath
# If non-root path, don't forgot to add "homepage": "/myreactapp" in your app's package.json
<VirtualHost *:80>
	ServerName local.dfq.com
	DirectoryIndex index.html
	DocumentRoot "/Applications/XAMPP/htdocs/fashion-static/deploy/static"

	ErrorLog "logs/dummy-host2.example.com-error_log"
    CustomLog "logs/dummy-host2.example.com-access_log" common

	<Location /imp >
		RewriteEngine on
		RewriteCond %{REQUEST_FILENAME} !-d
		RewriteCond %{REQUEST_FILENAME} !-f
		RewriteRule . /imp/index.html [L]
	</Location>
</VirtualHost>
```

这样，我们在本地访问`local.dfq.com/imp/`时就不会因为刷新而导致页面`404`的问题。

## `Nginx`配置

``` nginx
# To host on root path just use  "location /" for http://mydomainname.in
# To host on non-root path use "location /myreactapp" for http://mydomainname.in/mypath
# If non-root path, don't forgot to add "homepage": "/myreactapp" in your app's package.json 
server {
    server_name mydomainname.in;
    index index.html;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /usr/share/nginx/html;
  
    location /myreactapp {
        try_files $uri $uri/ /myreactapp/index.html;
    }
}
```
