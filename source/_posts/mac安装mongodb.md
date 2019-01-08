#### 下载安装

1. `curl -O https://fastdl.mongodb.org/osx/mongodb-osx-ssl-x86_64-4.0.5.tgz` 或者 [官网下载tar包](https://www.mongodb.com/dr/fastdl.mongodb.org/osx/mongodb-osx-ssl-x86_64-4.0.5.tgz/download) 
2. 解压 `tar -zxvf mongodb-osx-ssl-x86_64-4.0.5.tgz`
3. 重命名 `mv ./mongodb-osx-ssl-x86_64-4.0.5.tgz ./mongodb`
4. 移动到合适位置 `sudo mv ./mongodb /usr/local/opt/`
5. 在.bashrc文件添加 `export PATH=/usr/local/opt/mongodb/bin:$PATH`

#### brew安装
- `sudo brew install mongodb`
- `sudo brew install mongodb --with-openssl` // 要安装支持 TLS/SSL 命令
- `sudo brew install mongodb --devel` // 安装最新开发版本

#### 运行数据库

1. 创建数据库目录和日志目录

	`sudo mkdir -p /usr/local/var/mongo/db /usr/local/var/mongo/logs`
	
2. 启动数据库，指定数据库目录和日志目录，日志采用分文件存储

	`mongod --dbpath /usr/local/var/mongo/db --logpath /usr/local/var/mongo/logs/mongodb.log --logRotate rename &`

#### 连接数据库

连接命令：`mongo`

但是新版本会提示：‘WARNING: Access control is not enabled for the database.’，这是MongDB增加了安全性设计，推荐用户进行身份验证。

1. 创建管理员账号和密码，并设置权限，在mongo命令行输入

	```
	use admin
	db.createUser(
	  {
	    user: "admin",
	    pwd: "123123",
	    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
	  }
	)
	```

2. 重启MongoDB服务器
	
	```
	// 先kill了原来的进程，然后加--auth参数再启动mongo
	mongod --auth --dbpath /usr/local/var/mongo/db --logpath /usr/local/var/mongo/logs/mongodb.log --logRotate rename &
	```

3. 用户名登录

	`mongo -u admin -p 123123 --authenticationDatabase admin`

#### 其他

添加额外权限用户

```
use test
db.createUser(
  {
    user: "tester",
    pwd: "123",
    roles: [ { role: "readWrite", db: "test" },
             { role: "read", db: "reporting" } ]
  }
)
```

