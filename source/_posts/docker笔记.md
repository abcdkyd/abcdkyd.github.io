---
title: docker笔记
date: 2019-05-07 10:44:01
tags: 
    - docker
categories: docker
---



## docker操作

### docker run

```
// -i标志保证容器中STDIN是开启的 -t创建的容器分配一个伪tty终端
docker run --name bob_the_container -i -t ubuntu /bin/bash
	
// 创建守护式容器，	
docker run --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

### docker start | stop | restart | rm
### docker attach
	
```
// 重新回到了容器的Bash提示符
docker attach bob_the_container
```

<!-- more -->

### docker logs

```
docker logs bob_the_container

// 以打开文档形式
docker logs -f bob_the_container

// 获取日志的最后10行内容
docker logs --tail 10 bob_the_container

// 跟踪某个容器的最新日志而不必读取整个日志文件
docker logs --tail 0 -f bob_the_container
```

### docker run --log-driver="syslog"

> (还有一个可用的选项是none，这个选项将会禁用所有容器中的日志，导致docker logs命令也被禁用)

### docker top

```
docker top bob_the_container

// 输出结果
PID USER COMMAND
977 root /bin/sh -c while true; do echo hello world; sleep 1;
　done
1123 root sleep 1
```

### docker stats

```
// 显示一个或多个容器的统计信息
docker stats bob_the_container1 bob_the_container2 ...
```

### docker exec

```
// 在容器中运行后台任务
docker exec -d bob_the_container touch /etc/new_config_file

// 在容器内运行交互命令
docker exec -t -i bob_the_container /bin/bash
```

### docker run --restart=always 

> 自动重启容器 只有当容器的退出代码为非0值的时候，才会自动重启，可传参数为接受重启次数：--restart=on-failure:5 

### docker inspect

```
// docker inspect命令会对容器进行详细的检查，然后返回其配置信息，包括名称、命令、网络配置以及很多有用的数据
docker inspect bob_the_container

// 可以用-f或者--format标志来选定查看结果，根据json层级关系选取
docker inspect --format '{{.Name}} {{.State.Running}}' bob_the_container1 bob_the_container2
```