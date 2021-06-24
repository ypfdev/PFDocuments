### 0. 准备工作

###### 0.1 备份文件
备份文件的命令：

* 备份服务器配置文件

```
cd /etc/apache2
sudo cp httpd.conf httpd.conf.backup
```

* 备份php初始化文件

```
cd /etc
sudo cp php.ini.default php.ini.default.backup
```

从备份恢复文件的命令：
```
// 与上面反向拷贝
sudo cp httpd.conf.backup httpd.conf
```

###### 0.2 修改服务器配置

先创建一个文件夹，后面将用作Apache的文件根目录

```
cd /Users/ypf
mkdir Apache
```

打开Apache配置文件`/etc/Apache2/httpd.conf`（推荐用Sublime Text）

```
cd /etc/Apache2
open httpd.conf
```

* 设置服务器名称

搜索`ServerName`，找到`#ServerName www.example.com:80`，在下面添加一行`ServerName localhost:80`

* 修改根目录

搜索`DocumentRoot`，找到

```
DocumentRoot "/Library/WebServer/Documents"
<Directory "/Library/WebServer/Documents">
```

将这里的路径替换为刚才创建的文件路径

```
DocumentRoot "/Users/ypf/Apache"
<Directory "/Users/ypf/Apache">
```

* 未知作用

搜索`FollowSymLinks`，找到
`Options FollowSymLinks Multiviews`，替换为`Options Indexes FollowSymLinks Multiviews`

* 设置支持php

搜索`libphp7.so`，找到`#LoadModule mime_magic_module libexec/apache2/mod_mime_magic.so`，取消注释该行（删掉行首的`#`）

* 设置允许直接访问`.php`文件，而不必下载

搜索`mod_mime.so`，找到`#LoadModule mime_module libexec/apache2/`，取消注释该行

* 设置支持CORS（跨域资源共享，可选）

a. 搜索`mod_headers.so`，找到`LoadModule headers_module libexec/apache2/mod_headers.so`，取消注释该行（删掉行首的`#`）

b. 搜索`<Directory />`，找到

```
<Directory />
    AllowOverride none
    Require all denied
</Directory>
```

修改其中的权限配置为

```
<Directory />
　　AllowOverride none
　　Require all denied
　　Header set Access-Control-Allow-Origin *
</Directory>
```

### 1. 开启/关闭/重启Apache服务

* 开启
```
sudo apachectl -k start
```

* 关闭
```
sudo apachectl -k stop
```

* 重启
```
sudo apachectl -k restart
```

### 2. 测试

终端启动Apache，用浏览器打开`http://localhost`，就可以看到Apache下的文件目录了

### 3. 注意

###### 3.1 访问Apache文件报错
* 报错信息

`Forbidden，You don't have permission to access / on this server.`，错误码`403`

* 解决办法

检查`Users/ypf/Apache`下的文件权限（右键→显示简介→共享与权限），修改为`只读`或`读与写`