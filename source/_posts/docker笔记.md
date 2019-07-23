---
title: docker笔记
date: 2019-05-07 10:44:01
tags: 
    - docker
categories: docker
---

## docker操作

### docker run

-i 标志保证容器中STDIN是开启的 -t创建的容器分配一个伪tty终端

`docker run --name bob_the_container -i -t ubuntu /bin/bash`
	
创建守护式容器

`docker run --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"`

### docker start | stop | restart | rm | rmi

#### 批量删除镜像或者容器

删除全部标签为none的镜像

```
docker rmi `docker images -a | awk '/^<none>/ { print $3 }'`
```

删除除某个容器ID外的其他容器

```
docker rm `docker ps -a -q | awk '!/81df01422b69/'`
```

### docker attach
	
重新回到了容器的Bash提示符

`docker attach bob_the_container`

<!-- more -->

### docker logs

`docker logs bob_the_container`

以打开文档形式：

`docker logs -f bob_the_container`

获取日志的最后10行内容：

`docker logs --tail 10 bob_the_container`

跟踪某个容器的最新日志而不必读取整个日志文件：

`docker logs --tail 0 -f bob_the_container`

### docker run --log-driver="syslog"

> (还有一个可用的选项是none，这个选项将会禁用所有容器中的日志，导致docker logs命令也被禁用)

### docker top

`docker top bob_the_container`

输出结果

```
PID USER COMMAND
977 root /bin/sh -c while true; do echo hello world; sleep 1;
　done
1123 root sleep 1
```

### docker stats

显示一个或多个容器的统计信息

`docker stats bob_the_container1 bob_the_container2 ...`

### docker exec

在容器中运行后台任务

`docker exec -d bob_the_container touch /etc/new_config_file`

在容器内运行交互命令

`docker exec -t -i bob_the_container /bin/bash`

### docker run --restart=always 

> 自动重启容器 只有当容器的退出代码为非0值的时候，才会自动重启，可传参数为接受重启次数：--restart=on-failure:5 

### docker inspect

docker inspect命令会对容器进行详细的检查，然后返回其配置信息，包括名称、命令、网络配置以及很多有用的数据

`docker inspect bob_the_container`

可以用-f或者--format标志来选定查看结果，根据json层级关系选取

```
docker inspect --format '{{.Name}} {{.State.Running}}' bob_the_container1 bob_the_container2
```

## docker仓库和镜像

### docker pull

`docker pull fedora:20`

### docker search

通过docker search命令来查找所有Docker Hub上公共的可用镜像

`docker search fedora`

### docker commit

`docker commit 4aab3ce3cb76 jamtur01/apache2`

首先-m选项用来指定新创建的镜像的提交信息。同时还指定了--a选项，用来列出该镜像的作者信息。接着指定了想要提交的容器的ID。最后的jamtur01/apache2指定了镜像的用户名和仓库名，并为该镜像增加了一个webserver标签。

`docker commit -m"A new custom image" -a"James Turnbull" 4aab3ce3cb76 jamtur01/apache2:webserver`

### DockerFile

```
# Version: 0.0.1　
FROM ubuntu:14.04　
MAINTAINER James Turnbull "james@example.com"　
RUN apt-get update && apt-get install -y nginx　
RUN echo 'Hi, I am in your container' \　
　　 >/usr/share/nginx/html/index.html　
EXPOSE 80
```

Docker大体上按照如下流程执行Dockerfile中的指令。

- Docker从基础镜像运行一个容器。
- 执行一条指令，对容器做出修改
- 执行类似docker commit的操作，提交一个新的镜像层。
- Docker再基于刚提交的镜像运行一个新容器。
- 执行Dockerfile中的下一条指令，直到所有指令都执行完毕。

每条RUN指令都会创建一个新的镜像层，如果该指令执行成功，就会将此镜像层提交，之后继续执行Dockerfile中的下一条指令。

默认情况下，RUN指令会在shell里使用命令包装器`/bin/sh -c`来执行。如果是在一个不支持shell的平台上运行或者不希望在shell中运行（比如避免shell字符串篡改），也可以使用exec格式的RUN指令，在这种方式中，我们使用一个数组来指定要运行的命令和传递给该命令的每个参数

`RUN [ "apt-get", " install", "-y", "nginx" ]`

#### docker build

-t 选项为新镜像设置了仓库和名称

`docker build -t="jamtur01/static_web" .`

也可以在构建镜像的过程中为镜像设置一个标签，其使用方法为“镜像名:标签”

`docker build -t="jamtur01/static_web:v1" .`

上面命令中最后的`.`告诉Docker到本地目录中去找Dockerfile文件。也可以指定一个Git仓库的源地址来指定Dockerfile的位置

`docker build -t="jamtur01/static_web:v1" git@github.com:jamtur01/docker-static_web`

> 自Docker 1.5.0开始，也可以通过-f标志指定一个区别于标准Dockerfile的构建源的位置。例如，`docker build -t "jamtur01/static_web" -f path/to/file`，这个文件可以不必命名为Dockerfile，但是必须要位于构建上下文之中。

> 如果在构建上下文的根目录下存在以`.dockerignore`命名的文件的话，那么该文件内容会被按行进行分割，每一行都是一条文件过滤匹配模式。这非常像`.gitignore`文件，该文件用来设置哪些文件不会被当作构建上下文的一部分，因此可以防止它们被上传到Docker守护进程中去。该文件中模式的匹配规则采用了Go语言中的filepath

一个指令失败时，可以用docker run命令来基于这次构建到目前为止已经成功的最后一步创建一个容器ID，在容器中再次运行构建命令

`docker run -t -i 997485f46ec4 /bin/bash`

#### Dockerfile和构建缓存

> 由于每一步的构建过程都会将结果提交为镜像，所以Docker通常会将之前的镜像层看作缓存。然而，有些时候需要确保构建过程不会使用缓存，可以使用`docker build`的`--no-cache`标志

构建缓存带来的一个好处就是，我们可以实现简单的Dockerfile模板

```
FROM ubuntu:14.04　
MAINTAINER James Turnbull "james@example.com"　
ENV REFRESHED_AT 2014-07-01　
RUN apt-get -qq update
```

上面使用了一条新出现的指令ENV来在镜像中设置环境变量，通过ENV指令来设置了一个名为REFRESHED_AT的环境变量，这个环境变量用来表明该镜像模板最后的更新时间。最后，使用了RUN指令来运行`apt-get -qq update`命令。该指令运行时将会刷新APT包的缓存，用来确保我们能将要安装的每个软件包都更新到最新版本。

#### docker history

想深入探求镜像是如何构建出来的，可以使用`docker history`命令

`docker history 22d47c8cb6e5`

#### docker port

以下命令中我们指定了想要查看映射情况的容器的ID和容器的端口号，这里是80。

`docker port 6751b94bb5c0 80`

运行容器是直接绑定特定端口，这条命令会将容器中的80端口绑定到宿主机的8080端口上：

`docker run -d -p 8080:80 --name static_web jamtur01/static_web nginx -g "daemon off;"`

这里，我们将容器内的80端口绑定到了本地宿主机的127.0.0.1这个IP的8080端口上：

`docker run -d -p 127.0.0.1:8080:80 --name static_web jamtur01/static_web nginx -g "daemon off;"`

Docker还提供了一个更简单的方式，即`-P`参数，该参数可以用来对外公开在Dockerfile中通过`EXPOSE`指令公开的所有端口

#### Dockerfile指令

1. CMD
	
	CMD指令用于指定一个容器启动时要运行的命令。这有点儿类似于RUN指令，只是RUN指令是指定镜像被构建时要运行的命令，而CMD是指定容器被启动时要运行的命令。
	
	`docker run -i -t jamtur01/static_web /bin/true` 等价于Dockerfile中使用`CMD ["/bin/true"]`
	
	最后，还需牢记，使用docker run命令可以覆盖CMD指令。如果我们在Dockerfile里指定了CMD指令，而同时在docker run命令行中也指定了要运行的命令，命令行中指定的命令会覆盖Dockerfile中的CMD指令。
	
	> 在Dockerfile中只能指定一条CMD指令。如果指定了多条CMD指令，也只有最后一条CMD指令会被使用。如果想在启动容器时运行多个进程或者多条命令，可以考虑使用类似Supervisor这样的服务管理工具。
	
2. ENTRYPOINT
	
	ENTRYPOINT指令与CMD指令非常类似，而ENTRYPOINT指令提供的命令则不容易在启动容器时被覆盖。实际上，docker run命令行中指定的任何参数都会被当做参数再次传递给ENTRYPOINT指令中指定的命令。
	
	`ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]`
	
	> 如果确实需要，用户也可以在运行时通过docker run的--entrypoint标志覆盖ENTRYPOINT指令。
	
3. WORKDIR

	WORKDIR指令用来在从镜像创建一个新容器时，在容器内部设置一个工作目录，ENTRYPOINT和/或CMD指定的程序会在这个目录下执行。
	
	```
	WORKDIR /opt/webapp/db　
	RUN bundle install　
	WORKDIR /opt/webapp　
	ENTRYPOINT [ "rackup" ]
	```

    这里，我们将工作目录切换为/opt/webapp/db后运行了bundle install命令，之后又将工作目录设置为/opt/webapp，最后设置了ENTRYPOINT指令来启动rackup命令。

    可以通过`-w`标志在运行时覆盖工作目录，该命令会将容器内的工作目录设置为`/var/log`：
	
	`sudo docker run -ti -w /var/log ubuntu pwd`
	
4. ENV

	ENV指令用来在镜像构建过程中设置环境变量
	
	`ENV RVM_PATH /home/rvm/`
	
	这个新的环境变量可以在后续的任何RUN指令中使用，这就如同在命令前面指定了环境变量前缀一样
	
	`RUN gem install unicorn`
	
	该指令会以以下方式执行：
	
	`RVM_PATH=/home/rvm/ gem install unicorn`
	
	在这里我们设定了一个新的环境变量TARGET_DIR，并在WORKDIR中使用了它的值。因此实际上WORKDIR指令的值会被设为`/opt/app`：
	
	```
	ENV TARGET_DIR /opt/app
WORKDIR $TARGET_DIR
	```

	也可以使用docker run命令行的-e标志来传递环境变量，这些变量将只会在运行时有效

	`docker run -ti -e "WEB_PORT=8080" ubuntu env`
	
5. USER

	USER指令用来指定该镜像会以什么样的用户去运行，我们可以指定用户名或UID以及组或GID，甚至是两者的组合
	
	```
	USER user
	USER user:group
	USER uid
	USER uid:gid
	USER user:gid
	USER uid:group
	```

	也可以在docker run命令中通过-u标志来覆盖该指令指定的值。
	
	> 如果不通过USER指令指定用户，默认用户为root。
	
6. VOLUME

	VOLUME指令用来向基于镜像创建的容器添加卷。一个卷是可以存在于一个或者多个容器内的特定的目录，这个目录可以绕过联合文件系统，并提供如下共享数据或者对数据进行持久化的功能。
	
	- 卷可以在容器间共享和重用。
	- 一个容器可以不是必须和其他容器共享卷。
	- 对卷的修改是立时生效的。
	- 对卷的修改不会对更新镜像产生影响。
	- 卷会一直存在直到没有任何容器再使用它。

	`VOLUME ["/opt/project"]`
	
	这条指令将会为基于此镜像创建的任何容器创建一个名为/opt/project的挂载点。
	
	> `docker cp`是和VOLUME指令相关并且也是很实用的命令。该命令允许从容器复制文件和复制文件到容器上。
	
7. ADD

	ADD指令用来将构建环境下的文件和目录复制到镜像中。比如，在安装一个应用程序时。ADD指令需要源文件位置和目的文件位置两个参数。
	
	`ADD software.lic /opt/application/software.lic`
	
	这里的ADD指令将会将构建目录下的`software.lic`文件复制到镜像中的`/opt/application/software.lic`。指向源文件的位置参数可以是一个URL，或者构建上下文或环境中文件名或者目录。不能对构建目录或者上下文之外的文件进行ADD操作。
	
	在ADD文件时，Docker通过目的地址参数末尾的字符来判断文件源是目录还是文件。如果目标地址以/结尾，那么Docker就认为源位置指向的是一个目录。如果目的地址不是以/结尾，那么Docker就认为源位置指向的是文件。
	
	最后，如果目的位置不存在的话，Docker将会为我们创建这个全路径，包括路径中的任何目录。新创建的文件和目录的模式为0755，并且UID和GID都是0。
	
	> ADD指令会使得构建缓存变得无效，这一点也非常重要。如果通过ADD指令向镜像添加一个文件或者目录，那么这将使Dockerfile中的后续指令都不能继续使用之前的构建缓存。
	
8. COPY

	COPY指令非常类似于ADD，它们根本的不同是COPY只关心在构建上下文中复制本地文件，而不会去做文件提取（extraction）和解压（decompression）的工作。
	
	`COPY conf.d/ /etc/apache2/`
	
	文件源路径必须是一个与当前构建环境相对的文件或者目录，本地文件都放到和Dockerfile同一个目录下。不能复制该目录之外的任何文件，因为构建环境将会上传到Docker守护进程，而复制是在Docker守护进程中进行的。任何位于构建环境之外的东西都是不可用的。COPY指令的目的位置则必须是容器内部的一个绝对路径。
	
	如果目的位置不存在，Docker将会自动创建所有需要的目录结构，就像mkdir -p命令那样
	
9. LABEL

	LABEL指令用于为Docker镜像添加元数据。元数据以键值对的形式展现。
	
	```
	LABEL version="1.0"
	LABEL location="New York" type="Data Center" role="Web Server"
	```

	LABEL指令以`label="value"`的形式出现。可以在每一条指令中指定一个元数据，或者指定多个元数据，不同的元数据之间用空格分隔。推荐将所有的元数据都放到一条LABEL指令中，以防止不同的元数据指令创建过多镜像层。可以通过`docker inspect`命令来查看Docker镜像中的标签信息。

10. STOPSIGNAL

	STOPSIGNAL指令用来设置停止容器时发送什么系统调用信号给容器。这个信号必须是内核系统调用表中合法的数，如9，或者SIGNAME格式中的信号名称，如SIGKILL。

11. ARG

	ARG指令用来定义可以在docker build命令运行时传递给构建运行时的变量，我们只需要在构建时使用--build-arg标志即可。用户只能在构建时指定在Dockerfile文件中定义过的参数。

	```
	ARG build
	ARG webapp_user=user
	```

	上面例子中第二条ARG指令设置了一个默认值，如果构建时没有为该参数指定值，就会使用这个默认值。

	`docker build --build-arg build=1234 -t jamtur01/webapp .`

	要想使用这些预定义的变量，只需要给docker build命令传递--build-arg <variable>=<value>标志就可以了。

12. ONBUILD

	ONBUILD指令能为镜像添加触发器（trigger）。当一个镜像被用做其他镜像的基础镜像时（比如用户的镜像需要从某未准备好的位置添加源代码，或者用户需要执行特定于构建镜像的环境的构建脚本），该镜像中的触发器将会被执行。

	触发器会在构建过程中插入新指令，我们可以认为这些指令是紧跟在FROM之后指定的。触发器可以是任何构建指令

	```
	ONBUILD ADD . /app/src　
	ONBUILD RUN cd /app/src && make
	```

	上面的代码将会在创建的镜像中加入ONBUILD触发器，ONBUILD指令可以在镜像上运行docker inspect命令来查看

	比如，我们为Apache2镜像构建一个全新的Dockerfile，该镜像名为jamtur01/ apache2

	```
	FROM ubuntu:14.04　
	MAINTAINER James Turnbull "james@example.com"　
	RUN apt-get update && apt-get install -y apache2　
	ENV APACHE_RUN_USER www-data　
	ENV APACHE_RUN_GROUP www-data　
	ENV APACHE_LOG_DIR /var/log/apache2　
	ONBUILD ADD . /var/www/　
	EXPOSE 80　
	ENTRYPOINT ["/usr/sbin/apache2"]　
	CMD ["-D", "FOREGROUND"]
	```

	在新构建的镜像中包含一条ONBUILD指令，该指令会使用ADD指令将构建环境所在的目录下的内容全部添加到镜像中的/var/www/目录下。我们可以轻而易举地将这个Dockerfile作为一个通用的Web应用程序的模板，可以基于这个模板来构建Web应用程序。

	ONBUILD触发器会按照在父镜像中指定的顺序执行，并且只能被继承一次（也就是说只能在子镜像中执行，而不会在孙子镜像中执行）。如果我们再基于jamtur01/webapp构建一个镜像，则新镜像是jamtur01/apache2的孙子镜像，因此在该镜像的构建过程中，ONBUILD触发器是不会被执行的。

	> 这里有好几条指令是不能用在ONBUILD指令中的，包括FROM、MAINTAINER和ONBUILD本身。之所以这么规定是为了防止在 Dockerfile构建过程中产生递归调用的问题。

#### docker push

`docker push jamtur01/static_web`

#### docker rmi

指定一个镜像名列表来删除多个镜像

`docker rmi jamtur01/apache2 jamtur01/puppetmaster`

```
docker rmi `docker images -a -q`
```

#### 运行自己的Docker Registry

从容器运行Registry

`docker run -p 5000:5000 registry:2`

该命令将会启动一个运行Registry应用2.0版本的容器，并将5000端口绑定到本地宿主机。

为了指定新的Registry目的地址，需要在镜像名前加上主机名和端口前缀。

`docker tag 22d47c8cb6e5 docker.example.com:5000/jamtur01/static_web`

为镜像打完标签之后，就能通过docker push命令将它推送到新的Registry中去了

`docker push docker.example.com:5000/jamtur01/static_web`

`sudo docker run -t -i docker.example.com:5000/jamtur01/static_web /bin/bash`

#### docker network

Docker Networking允许用户创建自己的网络，容器可以通过这个网上互相通信。

`docker network create app`

这里用docker network命令创建了一个桥接网络，命名为app，这个命令返回新创建的网络的网络ID。

然后可以用docker network inspect命令查看新创建的这个网络

`docker network inspect app`

可以使用`docker network ls`命令列出当前系统中的所有网络

可以使用`docker network rm`命令删除一个Docker网络

也可以将正在运行的容器通过docker network connect命令添加到已有的网络中。因此，我们可以将已经存在的容器添加到app网络中。假设已经存在的容器名为db2，这个容器里也运行着Redis，让我们将这个容器添加到app网络中去

`docker network connect app db2`

`docker network disconnect app db2`

#### --volumes-from

卷是在一个或多个容器中特殊指定的目录，卷会绕过联合文件系统，为持久化数据和共享数据提供几个有用的特性。

- 卷可以在容器间共享和重用。
- 共享卷时不一定要运行相应的容器。
- 对卷的修改会直接在卷上反映出来。
- 更新镜像时不会包含对卷的修改。
- 卷会一直存在，直到没有容器使用它们。

我们启动了一个叫作james_blog的新容器，把本地的james_blog目录作为/data/卷挂载到容器里。

`docker run -v /home/james/james_blog:/data/ --name james_blog jamtur01/jekyll`

如果想在另一个容器里使用/var/www/html/卷里编译好的网站，可以创建一个新的链接到这个卷的容器

`docker run -d -P --volumes-from james_blog jamtur01/apache`

#### 备份卷

这里我们运行了一个已有的Ubuntu容器，并把james_blog的卷挂载到该容器里。这会在该容器里创建`/var/www/html`目录。然后我们使用-v标志把当前目录（通过$(pwd)命令获得）挂载到容器的`/backup`目录。

```
docker run --rm --volumes-from james_blog \
-v $(pwd):/backup ubuntu \
tar cvf /backup/james_blog_backup.tar /var/www/html
```

> 我们还指定了--rm标志，这个标志对于只用一次的容器，或者说用完即扔的容器，很有用。这个标志会在容器的进程运行完毕后，自动删除容器。对于只用一次的容器来说，这是一种很方便的清理方法。