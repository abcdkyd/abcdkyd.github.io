---
title: lnmp环境搭建
date: 2018-05-31 22:09:43
tags: 
categories: 环境配置
---

### CentOS6.5

#### 1.查看环境

``` 
# cat /etc/redhat-release 
```

#### 2.关掉防火墙

```
# chkconfig iptables off
```

#### 3.配置CentOS 6.0 第三方yum源

```
# wget http://www.atomicorp.com/installers/atomic
# sh ./atomic
# yum check-update
```

#### 4.安装开发包和库文件

```
# yum -y install ntp make openssl openssl-devel pcre pcre-devel libpng libpng-devel libjpeg-6b libjpeg-devel-6b freetype freetype-devel gd gd-devel zlib zlib-devel gcc gcc-c++ libXpm libXpm-devel ncurses ncurses-devel libmcrypt libmcrypt-devel libxml2 libxml2-devel imake autoconf automake screen sysstat compat-libstdc++-33 curl curl-devel
```

#### 5.卸载已安装的apache、mysql、php

```
# yum remove httpd
# yum remove mysql
# yum remove php
```

#### 6.安装nginx

```
# yum install nginx
# service nginx start
# chkconfig --levels 235 nginx on
//设2、3、5级别开机启动
```

#### 7.安装mysql

```
# yum install mysql mysql-server mysql-devel
# service mysqld start
# chkconfig --levels 235 mysqld on

登陆MySQL删除空用户，修改root密码
mysql>select user,host,password from mysql.user;
 
mysql>drop user ''@localhost;
 
mysql>update mysql.user set password = PASSWORD('*********') where user='root';
 
mysql>flush privileges;
```

#### 8.安装php

```
# yum install php lighttpd-fastcgi php-cli php-mysql php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-mcrypt php-mssql php-snmp php-soap

# yum install php-tidy php-common php-devel php-fpm php-mysql
# service php-fpm start
# chkconfig --levels 235 php-fpm on
```

#### 9.配置nginx支持php

```
在 /etc/nginx 下添加文件夹vhost，修改nginx.conf，在最后添加 “include vhost/*.conf;”

在vhost里面添加新增的配置

test.conf 如下：
server {
    listen 80;
    server_name test.local;
    
    charset utf-8;
    access_log /usr/local/var/log/nginx/test.local.access.log;
    error_log /usr/local/var/log/nginx/test.local.error.log;
    
    root /Users/vin/Code/test;
    
    location / {
         autoindex on;
     autoindex_exact_size off;
     autoindex_localtime on;
     try_files $uri $uri/ /index.php?$args;
     index index.html index.htm index.php;
    }
    
    location ~ \.php$ {
         fastcgi_pass 127.0.0.1:9000;
         fastcgi_index index.php;
         fastcgi_param SCRIPT_FILENAME /Users/vin/Code/test$fastcgi_script_name;
         include fastcgi_params;
         try_files $uri =404;
    }
}
```

#### 10.重启nginx php-fpm

```
# service nginx restart
# service php-fpm restart
```


### Ubuntu

```
# sudo apt-get update // 更新apt-get
```

#### 1.Nginx

```
# sudo apt-get install nginx
// 启动nginx
# sudo /etc/init.d/nginx start 
# sudo service nginx start

// 用socket进行通讯
location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    # With php7.0-cgi alone:
    # fastcgi_pass 127.0.0.1:9000;
    # With php7.0-fpm:
    fastcgi_pass unix:/run/php/php7.1-fpm.sock;
}

// 然后再修改 PHP-FPM的配置文件 /etc/php/7.1/fpm/pool.d 
;  与 Nginx监听同一个 sock
listen = /run/php/php7.1-fpm.sock
```

#### 2.安装 PHP 7.1 与 PHP7.1-FPM

```
# sudo apt-add-repository ppa:ondrej/php   //使用PPA ppa:ondrej/php 库
# sudo apt-get update
# sudo apt-get install php7.1 php7.1-fpm

// php必备模块
# apt-get install php7.1-curl php7.1-xml php7.1-mcrypt php7.1-json php7.1-gd php7.1-mbstring
```

#### 3.安装Mysql

```
# sudo apt-get –y install MySQL-server mysql-client php7.1-mysql
```

### Mac

#### 1.安装Homebrew

```
// 需要客户端安装ruby
# ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

#### 2.安装 Nginx 服务器

```
# brew install nginx

// 需要安装其他版本的nginx可以用以下命令来修改内容
# brew edit nginx

// 启动 nginx服务
# sudo nginx

// 重新加载配置|重启|停止|退出 nginx
# nginx -s reload|reopen|stop|quit

// 测试配置是否有语法错误
# nginx -t

// 指定某个配置文件启动
# nginx -c /usr/local/etc/nginx/nginx.conf

// 开机自启动nginx服务设置
# mkdir -p ~/Library/LaunchAgents
# cp /usr/local/Cellar/nginx/1.10.0/homebrew.mxcl.nginx.plist ~/Library/LaunchAgents/
# launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.nginx.plist

// 权限
# sudo chown root:wheel /usr/local/Cellar/nginx/1.10.0/sbin/nginx
# sudo chmod u+s /usr/local/Cellar/nginx/1.10.0/sbin/nginx

```

#### 3.安装和配置 MySQL 服务器

```
# brew install mysql

// 配置命令
# mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql
# mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp

// 启动
# ps -ef | grep mysql
# /usr/local/Cellar/mysql/5.7.12/bin/mysqld
# which mysqld

// 开机启动
# mkdir -p ~/Library/LaunchAgents/
# cp /usr/local/Cellar/mysql/5.7.12/homebrew.mxcl.mysql.plist ~/Library/LaunchAgents/
# launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist

// 停止服务
# launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist
```

#### 4.安装 PHP56 和 PHP-fpm

```
# brew install php56 --with-imap --with-tidy --with-debug --with-pgsql --with-mysql --with-fpm
# brew install php70 --with-imap --with-tidy --with-debug --with-pgsql --with-mysql --with-fpm

# export PATH="$(brew --prefix php56)/bin:$PATH"

// php－fpm开机启动 能在安装信息找到
# mkdir -p ~/Library/LaunchAgents
# cp /usr/local/opt/php56/homebrew.mxcl.php56.plist ~/Library/LaunchAgents/
# launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist

// php版本切换
# brew install php-version
# source $(brew --prefix php-version)/php-version.sh
# php-version		//查看版本
# php-version 5.6.5		//切换版本

// php扩展

# brew install php56-apcu php56-intl php56-redis php56-uuid php56-zookeeper 
    php56-thrift php56-solr php56-ssh2 php56-gmagick php56-kafka php56-libevent 
    php56-imagick php56-msgpack php56-geoip php56-mcrypt php56-swoole 
    php56-scrypt php56-xdebug php56-yaf php56-yaml php56-xhprof 
    php56-memcache php56-memcached php56-gearman

```