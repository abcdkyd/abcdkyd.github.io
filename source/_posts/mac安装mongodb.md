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

db.updateUser("jhpm",
  {
    user: "jhpm",
    pwd: "123123",
    roles: [ { role: "readWrite", db: "pht_log" },
             { role: "dbAdmin", db: "pht_log" } ]
  }
)
```

1. 数据库用户角色：read、readWrite;
2. 数据库管理角色：dbAdmin、dbOwner、userAdmin；
3. 集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
4. 备份恢复角色：backup、restore；
5. 所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
6. 超级用户角色：root
// 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
7. 内部角色：__system

- Read：允许用户读取指定数据库 
- readWrite：允许用户读写指定数据库 
- dbAdmin：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile 
- userAdmin：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户 
- clusterAdmin：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限。 
- readAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读权限 
- readWriteAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的读写权限 
- userAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限 
- dbAdminAnyDatabase：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限。 
- root：只在admin数据库中可用。超级账号，超级权限