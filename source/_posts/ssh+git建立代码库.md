---

title: ssh+git建立代码库
date: 2017-06-14 11:47:09
tags: git
categories: 笔记

---

### 1.生成ssh密钥

```
# ssh-keygen -t rsa [-f test -C "test key"]

-b：指定密钥长度； 
-e：读取openssh的私钥或者公钥文件； 
-C：添加注释； 
-f：指定用来保存密钥的文件名； 
-i：读取未加密的ssh-v2兼容的私钥/公钥文件，然后在标准输出设备上显示openssh兼容的私钥/公钥； 
-l：显示公钥文件的指纹数据； 
-N：提供一个新密语； 
-P：提供（旧）密语；
-q：静默模式； 
-t：指定要创建的密钥类型。
```

密钥生成在`.ssh`文件夹里面，id_rsa为私钥，id_rsa.pub为公钥

### 2.生成裸git库

```
# sudo git init --bare sample.git
```

### 3.配置.ssh

```
//服务器新建用户git，并配置权限
# sudo adduser git
# sudo chown -R git:git sample.git
//修改/etc/passwd
git:x:1001:1001:,,,:/home/git:/bin/bash
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell

把公钥 id_rsa.pub 内容追加到 .ssh/authorized_keys 文件里面
vi 或者 ssh-copy-id -i ~/.ssh/id_rsa.pub "-p 22 root@192.168.0.1"

// 配置config
Host vin
User vin
Hostname 192.168.1.105
Port 22
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa

Host pubgit
User pubgit
Hostname 192.168.1.101
Port 22
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa

Host git-page
User git
Hostname www.github.com
Port 22
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa

注意：
要保证.ssh和authorized_keys都只有用户自己有写权限。否则验证无效。
```

#### 4.连接服务器

```
# ssh vin
# git clone ssh://pubgit/home/pubgit/Code/uzise/uzise.git
# git clone ssh://root@192.168.0.1:22/home/pubgit/Code/uzise/uzise.git
```