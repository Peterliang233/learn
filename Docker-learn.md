### Docker简述

#### Docker不是容器，它是处理容器的引擎

#### Docker的优点

+ Docker更加轻量化，可移植性好，处理时秒级的。
+ 我们使用docker run的时候首先看本地是否有对应的镜像，有的话就不进行下载，否则的话就从镜像库里面进行下载，然后镜像里面有对应的容器，容器里面有相关的程序，就是这样一层一层而来的。

#### Docker服务启动

```docker
启动：systemctl start docker或者service docker start
停止：systemctl stop docker 或者service docker stop
重启：systemctl restart docker 或者service docker restart
检查docker的运行状态:systemctl status docker或者service docker status
查看docker的进程:ps -ef | grep docker
查看docker的系统信息:docker info
查看所有的命令信息:docker
查看某一个具体的命令的使用:docker command --help
搜索镜像:docker search + 镜像名称
拉取镜像:docker pull +镜像名称
运行docker的时候，我们可以用docker run -d -p 8080（linux的端口):8080(容器的端口) +镜像名称或者镜像ID
-d指的是在后台运行docker容器，-p指的是映射端口值，将linux服务器的端口映射到docker容器的端口。
进入容器：docker exec -it (容器的ID) bash 这个命令的意思是打开容器，进入虚拟的控制台。
```

#### docker架构

+ Docker主要分为客户端（Client），服务端（Docker Host）,镜像仓库(Registry),镜像（Images)和容器（Containers)这五个部分。我们运行docker命令是在客户端发起的，然后服务端进行接受相关的命令，寻找对应的仓库和对应的仓库下面的容器，最后对容器执行相关的操作。

##### Docker的核心三部分

我们通过启动镜像来启动容器，同时在启动镜像的时候指定我们的网络端口和显示的形式，启动镜像之后，也会启动镜像里面的那个容器，我们可以利用容器id进入容器的后台或者前台部分进行相关的操作。

###### 镜像

+ 镜像可以理解为一个模板，也可以是一个做好的产品，它里面已经为我们封装打包好了很多个软件，比如环境变量，操作系统，还有一些内置的软件等等。
+ 镜像是由很多层的文件构成的。
+ 删除镜像：Docker rmi 镜像名称或者镜像ID（注意，这是镜像名，不是容器名）

###### 容器

+ 容器相当于我们的一个小型的操作系统，
+ 查看所有的容器 docker ps -a
+ **通过镜像启动容器 docker run -d 镜像名称**
+ docker stop 容器 id 或容器的名称
+ docker start 容器 id或者容器的名称
+ 删除容器： docker rm +容器的ID或者名称  删除容器之前要将容器先停止
+ 停止所有的容器： docker stop $(docker ps -q)
+ 删除镜像内全部容器：docker rm $(docker ps -aq)

###### 仓库

+ 仓库里面存放了很多个镜像，每个镜像对应很多个tag标签，docker先当与git，然后docker注册服务器相当于我们的github仓库。
+ docker kill image 强制停止容器的运行
+ docker stop image 温柔停止容器的运行
+ docker exec -it 容器id 远程对容器进行操作(进入未打开的容器)
+ docker attch 容器id  远程对容器进行操作(可以进入一个正在开启的容器)
+ 容器内的数据拷贝打主机上面
+ -p 指定端口
+ -P 随机分派端口
+ docker commit -a="提交人" -m="信息" 容器id  版本名（适用于基于某一个镜像进行修改，形成一个新的镜像）
+ 将容器内的文件拷贝到主机上面 docker cp 容器id:容器文件路径  宿主机文件路径
+ docker log -f -t --tail 容器id 
  + -t是加入时间戳
  + -f跟随最新的日志打印
  + --tail num  显示最后的多少条

#### docker安装运行mysql

```
拉取mysql镜像：docker pull mysql:latest
开启一个mysql镜像：docker run -p 3306:3306 -e(配置环境变量) MYSQL_DATABASE=workdb(这是初始化一个数据库名) -e MYSQL_ROOT_PASSWORD=123456(初始化数据库的登录密码) -d mysql:latest
注意：
如果这里提示这个端口被占用的话，可以通过
1、sudo netstat -nplt |grep 3306 查找对应的端口占用情况
2、sudo kill `sudo lsof -t -i：3306 杀死占用该端口的进程
这样，我们就可以直接成功进入数据库镜像里面了。
执行docker exec -it (容器ID) bash 进入相关的容器
如果想远程连接mysql，我们需要授予远程访问的权限
CREATE USER `wkcto`@`%` IDENTIFIED WITH mysql_native_password BY `123456`;  //创建远程访问的用户 
CGRANT ALL PRIVILEGES ON *.* TO `wkcto`@`%`;  //给该用户设置权限
```

#### 将宿主机上的文件拷贝到我们的容器目录

+ 首先，我们先打开一个镜像，获取这个容器的id。
+ 然后，利用`docker cp 本地文件目录 容器id:要拷贝的文件的目录`即可将我们的文件拷贝到该目录下面。

#### 容器数据卷

+ 作用：数据持久化，容器与主机间的数据共享
+ 命令：docker run -it -v /宿主机的绝对路径：/容器内目录 镜像名
+ 容器停止后，主机修改依然同步
+ 添加权限，docker run -it -v /宿主机的绝对路径:/容器内的目录:ro(只读) 镜像名
+ 如果想要添加多个容器卷，可以在编写Dockerfile文件的时候添加VOLUME命令，这个时候不能指定主机的路径名，但是系统会自动给宿主机分配关联的路径，这个可以通过docker inspect +容器Id的命令查找
+ 如果在挂载主机目录的时候出现了Permission denied的报错，可以在挂载目录的时候添加一个--privileged=true参数即可

##### 数据容器卷

+ 作用就是将容器的数据同步到本地，防止删除容器的时候出现数据丢失的情况。这个过程也称为容器的持久化（在启动一个尽享的时候进行挂载）
+ 可以理解为容器卷数据的传递，这一个容器可以继承上一个容器的容器卷的数据
+ 命令： docker run -it --name(指定容器名) xxx --volume-from （容器Name） 镜像名、
+ 只要是存在继承的关系的，一律可以进行数据的共享，子容器的数据修改会影响到父容器的数据的修改。
+ 这里不要拘泥于继承这个关系，可以理解为一个集合，不存在等级之间的关系。
+ `docker -v 本地的目录：容器的目录 容器名 命令参数`
+ exp：`docker run -it -v /home/peterliang:/home nginx bash`
+ 挂载的两个目录会实现同步，容器里面的数据修改会同步到本地，本地的修改也会同步到容器。
+ 查看容器卷的所有情况`docker volume ls`

##### 具名挂载和匿名挂载

+ 匿名挂载就是我们在挂载容器的时候不指定对应的容器名`docker run -d -P -v  容器内路径名 容器id`
+ 具名挂载就是我们在挂载容器的时候指定对应的容器名 `docker run -d -P -v 容器名：容器id`
+ 



#### docker安装运行Nginx

+ 安装 docker pull nginx
+ 进入nginx : docker exec -it 容器id bash
+ 进入之后可以进行找到nginx.conf这个文件，然后找到/etc/nginx/conf.d/*.conf，里面有一个default.conf这个文件，里面存放了nginx的配置信息。
+ 上传本地的文件到nginx容器里：docker cp 文件名的绝对路径 容器id:容器的root文件的路径

#### Docker自定义镜像

##### Dockerfile的组成

+ 基础镜像信息
+ 维护者信息
+ 镜像操作信息
+ 容器启动时执行的命令

##### Dockerfile指令

+ FORM 格式为FORM <image> 或者FORM <image>:<tag>,这是dockerfile文件的最开始的命令，也必须是这个命令。多个镜像的话可以用多个FORM语句执行操作.
+ MAINTAINER<name> 指明了这个镜像的维护者，这个可有可无
+ ADD <src> <dst> 把本地的源复制到容器里面的地址里面去
+ ENV <key> <value> 指定一个环境变量，会被后续的RUN指令使用，并在容器运行时保持。配置多个环境变量可以使用多个ENV指令
+ RUN<COMMAND> 指定Dockerfile运行改命令，并且构成新的Dockerfile命令
+ CMD指定容器执行时的指令，这个只能有一条指令，这个一般是最后一条命令。
+ EXPOSE告诉docker服务端容器暴露的端口号，供互联网使用
+ 构建镜像：docker build -t 镜像名称 .(注意，这里有一个点)
+ 修改容器保存：docker commit 容器ID 镜像名

#### 把镜像发布到阿里云

```
FORM ubuntu
MAINTAINER peterliang<ncuyanping666@126.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH
RUN apt-get install vim
RUN apt-get install net-tools

EXPOSE 80
CMD /bin/bash
```

+ 提交镜像命令`docker commit -a="提交者" -m="提交信息" 容器id 镜像名：镜像版本`，这个命令类似于git的commit的命令。

#### sshfs挂载远程文件

+ `yay -S sshfs`
+ `sshfs user@remotehost:远程目录 本地目录`
+ 取消挂载 `umount 本地目录`

#### Docker-compose

+ docker compose 负责实现对Docker容器集群的快速编排，我们的应用可能需要mysql，redis等等，这样我们就需要进行对容器启动顺序的编排。
+ 只适用于定义和运行多个docker容器。
+ 文件名建议命名为docker-compose.yml
+ compose核心概念
  + 服务(service)，一个服务就相当于一个容器，服务可以存在多个。
  + 项目(project)，一组关联的容器组成一个完整的业务单元。
+ 安装docker-compose,`yay -S docker-compose``
+ ``docker-compose up` 启动一个docker-compose

##### docker-compose 语法

```yaml
version: "3.0" #版本信息
services: #创建某一个服务
	tomcats: #指定服务名称
		container_name: #指定容器的名称
		images: #指定进行名
		ports: #指定端口一映射，完成host与容器的断端口的映射(建议用字符串)
		volumes: #制定挂在的容器卷，完成宿主机和容器的目录的挂载
		
volumes: #声明服务使用过的容器卷
	tomcatwebapps01: #声明卷名，compose自动创建该卷名，但是卷名的前面会自动加入项目名
		external: # 为true的话就要自己手动创建好卷名，通过docker volume 相关命令
		
networks: #定义服务用到桥
	hello: #定义上面的服务哟难道的网桥的名称，默认创建就是bridge
		external: # 使用外部网桥，如果为true就要通过docker network create创建
```

