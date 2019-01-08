---

title: 添加php额外配置，引入php扩展
date: 2018-12-14 21:11:41
tags: php
categories: 环境配置

---

__`phpinfo`__ 中 `Scan this dir for additional .ini files` 配置参数是php扩展配置路径。

你可以在该路径下，配置文件结尾为`.ini`的文件作为你的额外配置参数。

我们可以利用它来更好的管理我们php的扩展的相关配置：

引用pdo_pgsql扩展
`extension="/usr/local/opt/php71-pdo-pgsql/pdo_pgsql.so"`

### PHP扩展安装

1. brew、yum或者apt-get安装
2. 使用pecl安装
	
	#### mac安装pecl
	
	1. 下载pecl `curl -O https://pear.php.net/go-pear.phar`
		
	2. 安装pecl
	
		```
		php -d detect_unicode=0 go-pear.phar
		
		执行以上命令后会进行安装过程，会有一些配置选项：
		输入 1，将安装根目录修改为 /usr/local/pear； 
		输入 4，将命令安装到 /usr/local/bin 目录； 
		回车两次，其他让其默认，安装完成
		```
	
	3. 检测是否安装成功 `pear version`
		
	#### pecl安装扩展
	
	1. pecl install mongodb
	2. 配置php.ini，通过运行 php --ini查找php.ini文件位置，然后在文件中添加extension=memcached.so

3. phpize安装

	> phpize运行可能需要安装autoconf：`brew install autoconf`

	1. 下载PHP扩展包：[pecl库搜索](http://pecl.php.net/package-search.php)
	2. 解压缩并进入扩展包目录

		```
		tar -xzvvf redis-4.0.0.tgz
		cd redis-4.0.0
		```
	3. 执行phpize `phpize`
	4. 执行./configure `./configure --with-php-config=/usr/bin/php-config`
	5. `make && make install`
	6. 配置php.ini，添加so文件到ini
		