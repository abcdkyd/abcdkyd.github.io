---
title: hexo+github 创建个人博客
date: 2017-04-03 16:34:43
tags: hexo
categories: 效率
---

大概流程

> 1. 创建仓库，abcdkyd.github.io；
> 2. 创建两个分支：master 与 hexo；
> 3. 设置hexo为默认分支（因为我们只需要手动管理这个分支上的Hexo网站文件）；
> 4. 使用git clone git@github.com:abcdkyd/abcdkyd.github.io.git拷贝仓库；
> 5. 在本地 abcdkyd.github.io 文件夹下通过Git bash依次执行npm install hexo-cli、hexo init、npm install 和 npm install hexo-deployer-git（此时当前分支应显示为hexo）;
> 6. 修改_config.yml中的deploy参数，分支应为master；
> 7. 使用git init 、git remote add origin git@github.com:sylujia/sylujia.github.io.git以及git pull命令重新关联远端库。
> 8. 使用git checkout hexo命令切换到hexo分支然后依次执行git add .、git commit -m “…”、git push origin hexo提交网站相关的文件；
> 9. 执行hexo generate -d生成网站并部署到GitHub上。

<!-- more -->

## GitHub

1. 创建一个仓库，命名为 username.github.io  GitHub Page类型为 User Pages
2. 创建两个分支 master 和 hexo ，hexo为默认修改代码，master为发布代码
3. 用 ssh 建立连接

```
# ssh-keygen -t rsa -C "email@email.com"
添加公钥到github上
# ssh -T git@github.com
# git config --global user.name "username"
# git config --global user.email "email@email.com"
```

## Hexo

1. 需要安装 node.js

2. 使用hexo在本地建站

	```
		# npm install -g hexo-cli
		# git clone git@github.com:abcdkyd/abcdkyd.github.io.git
		# cd abcdkyd.github.io.git
		# hexo init
		# npm install
		# hexo generate
		# hexo server
	```

3. 部署博客到GitHub上修稿配置文件 _config.yml  为：

	```
	deploy:
	type: git
	repository:git@github.com:abcdkyd/abcdkyd.github.io.git
	branch: master
	```

## 上传和部署

```
# npm install hexo-deployer-git —save  //安装部署的插件
# hexo clean         //清空public文件夹下生成的静态文件和db.json文件
# hexo generate   //重新生成静态文件和db.json
# hexo deploy      //按照站点配置文件部署到github上
```
	
提交到hexo分支，由于上面执行了hexo init 命令，所以要重新关联远程库
	
```
# git init
# git remote add origin git@github.com:abcdkyd/abcdkyd.github.io.git
# git checkout hexo
# git add -A
# git commit -m 'blog start'
# git push origin hexo
```

## 更换主题
如果想要使用其他主题，可以使用git clone将别人的主题拷贝到H:\sylujia.github.io\themes下，然后将_config.yml中的theme: landscape改为对应的主题名字。

```
# git clone https://github.com/iissnan/hexo-theme-next.git themes/next
# git clone https://github.com/litten/hexo-theme-yilia.git
```

## 常用命令

```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本

hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy

// 头部信息
title: 使用Hexo搭建个人博客
layout: post
date: 2014-03-03 19:07:43
comments: true
categories: Blog
tags: [Hexo]
keywords: Hexo, Blog
description:
```
