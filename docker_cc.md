# 1. basic check command
# 基础命令
## show all images / 显示所有镜像
```
$: docker images

## show all running container (looking for id) / 显示所有运行的容器 (查看id)

$: docker ps
```
### -a flag 
show all container included stoped / 显示所有容器包括已停止的

## inspect container by id / 通过id检视容器
displat all low level infors like ip/mac/config

显示所有底层信息例如 ip/mac地址/配置
```
$: docket inspect [container_id]
```

## show docker logs / 显示容器日志
```
$: docker logs [container_id]
```
## show image layers / 显示镜像层级关系
```
$: docker history [repository_name]:[repository_tag]
```

## List all docker machine / 显示所有的docker机机器
usually use to check state and ip / 通常可以用来查看状态和ip

```
$: docker-machine ls
```
## List all container network / 显示容器网络
```
$: docker network ls
```
## inspect network / 检视网络
```
$: docker network inspect [networktype]
```


# 2. Build and Run/构建和运行 


## docker run/download repository [optional linux command] / 运行/下载藏剑【可添加额外的linux命令】
doanload images from docker hub docker 

从 docker hub 上下载镜像

```
$: docker run [repository_name]:[repository_tag]
```
##### example:

``` 
docker run busybox:1.29.3 echo "hello world"
```
### -i flag 
starts an iteractive container.
与启动容器容器进行交互
### -t flag 
creates a pseduo-TTY that attaches stdin and stdout
启动虚拟终端以及输入输出功能
##### example: 
```
docker run -i -t busybox:1.29.3 (quit with exit)
```
### -rm flag
flag delete container after exit/ 在运行结束后删除容器
### --name flag 
add name to container. / 给容器起名
### -d flag 
run containers with detached model / 在分离模式下运行
##### example: 
```
docker run -it -d - p 8888:8080 tomcat:8.0
```
### -p flag 
set running port / 设置运行端口
##### example: 
```
docker run -it --rm - p 8888:8080 tomcat:8.0
```

## networks / 网络
#### create a network
```
docker network create -d [netowkr_type] [network_name]
```
##### example
```
docker network create -d bridge my-bridge
```
#### run under a network
```
$: --net [network_name]
```
##### example
```
docker run -d --net none busybox
```
### --net none flag
only one loopback interface, isolated from internet

只有本地环回借口，完全隔离外部网络环境

### --net bridge flag (default)
two interface, loopback and private network to bridge network. ip default in range (172.17.0.0 - 172.17.255 255). The containers CAN communicate each other in same range and same network. 

存在两个借口，本体环回借口和私有桥架网络。ip 默认 in range （172.17.0.0 - 172.17.255.255)。容器可以在相同的网络和域之下通信。

### --net host flag
least protected network model, contaners deployed on the host have full access to host's interface

安全等级最低，容器在部署以后可以访问所有主机的网络接口
 
### --net overlay flag
support multi-host networking out of the box. Usually configure by default.[Click here for more detail](https://docs.docker.com/engine/userguide/networking/overlay-standalone-swarm/#create-a-swarm-cluster)

支持多主机间网络通信，通常情况下使用默认配置[点击这里查看更多详情](https://docs.docker.com/engine/userguide/networking/overlay-standalone-swarm/#create-a-swarm-cluster)
 
 
## create network / 创建网络
container can not communicate under differnt network unless connect by connect command
```
$: docker network create --driver [networktype] [network_name]
```

## network connect / 网络连接
```
$: docker network connect [netowrk_name] [container_name]
```

## network disconnect / 断开网络连接
```
$: docker network disconnect [netowrk_name] [container_name]
```


## docker stop container / 停止容器进程
```
$: docker stop [container_id]
```
## Docker commit / 提交
```
$: docker commit container_ID [repository_name]:[repository_tag]
```

## Delete image / 删除镜像
```
$: docker rmi  <IMAGE ID>
```
### -f flag 
set force delete / 容器删除

## Docker build / 创建镜像

docker build will run Dockerfile to build the image
if tag is not specified, it will be latest by default
path is where Dockerfile located, use "." if current under the path

根据创建的Dockerfile脚本，docker会安装以及创建image
如果没有指明tag，默认 latest
path 为Dockerfile的路径，在当前路径下用 "."
```
$: docker build -t [repository_name]:[repository_tag] [path]
```


## Docker execute / Docker 执行命令
```
$ docker exec -it [id] [command]
```
##### example
```
$ docker exec -it b8a9cd93452x bash
```
the above example will start bash command terminal for the container
以上示例会启动容器的bash控制器


# create Dockerfile / 创建 Dockerfile
Dockerfile is the file contains a list of command for build image
for example, user can set installation for all application and create envrioment for the image
all command has to write in to Dockerfile, and the file name "Dockerfile" can not be changed
[click here for more details](https://docs.docker.com/engine/reference/builder/#escape)

Dockerfile 存储一系列指令用于构建镜像
例如用户可以直接为即将构建的镜像设定好安装需要的应用程序和环境
所有命令必须写入Dockerfile，且必须保证文件名为"Dockerfile"
[点击这里查看更多详情](https://docs.docker.com/engine/reference/builder/#escape)


```
FROM [image]
RUN [Linux Command with -y]
```
##### example 1
```
FROM debian:jessie
RUN apt-get update
RUN apt-get install -y git
```

##### example 2
```
FROM python:3.5
RUN pip install Flask==0.11.1 redis
RUN useradd -ms /bin/bash admin
USER admin
WORKDIR /app
COPY app /app
CMD ["python", "app.py"] 
```

reduce duplicated command chain action 

减少重复指令 链指令
##### example
```
FROM debian:jessie
RUN apt-get update && apt-get install -y \
git \
python
```

## CMD run when container start / 容器启动执行
default as bash command 

默认 bash command
##### example
```
FROM debian:jessie
CMD ["echo","hello world"]
```

##！warning - Aggressive Caching! / 滥用缓存
Docker will store the cache for future build, if there no change for the command line, docker will use cache instead of fetch from internet. This feature greatly increase the build speed, but may cause some issue.

Docker 会自动缓存之前的构造，如果未来的构造中部分命令和之前的命令没有变化，Docker会自动读取缓存来提升构造速度，但是缓存机制可能会导致一些潜在的问题


```
FROM debian:jessie			->	FROM debian:jessie    (NOT execute, from Docker Catch / 不会执行)
RUN apt-get update			->  RUN apt-get update 	  (NOT execute, from Docker Catch / 不会执行)
RUN apt-get install -y git  ->  RUN apt-get install -y git curl  (execute, but may out of data update/ 执行)
```
### solution 1:
using chain / 使用链指令 

using chain to ensure the must execute command execute

用链指令确保指令不重复

### soulution 2:
disable catch / 禁用缓存
```
$: docker build -t [repository_name]:[repository_tag] [path] --no-cache=true
```

## Copy action / 复制
```
$: Copy [file] [path]
```
##### example
```
$: Copy abc.txt /src/abc.txt
```
## Add action / 添加
Copy is preffered unless add is really needed
add can download file from internet, actumaticly unpack compressed file

通常情况下推荐使用复制指令，除非不得不使用添加指令
添加指令可以从网络上下载文件，自动解压缩文件

```
$: Add [file] [path]
```
## Link / 连接
use --link to creates a secure tunnel for independent containers without expose port externally

使用 --link 可以在相互独立的容器间，不适用任何外部端口的情况下，建立通讯通道

```
$: --link [application] 
```
##### example
```
$: docker run -d -p 5000:5000 --link redis dockerapp:v0.3
```



# Docker Hub

# login/登录
```
$: docker login --username=[username]
```
##### example
```
$: docker login --username=sampleusername
```
# push/推送
do not push tag as latest (by default) is recommanded

推荐不要使用默认tag latest

```
$: docker push [repository_name]:[repository_tag]
```
##### example
```
$: docker push myrepo/debian:1.00
```

# Docker-compose
simular to Dockerfile, Docker-compose provide the automate workflow, build the docker environment up and running. it use yaml files to store the configureation of containers. Auto link will work for version '2' [click here for more details](https://docs.docker.com/compose/)

类似于Dockerfile， Docker-compose 提供自动化的工作流程，例如自动构建环境和运行环境。Docker-compose使用yaml文件来储存配置和命令。在使用版本2以上的配置命令会自动为容器创建link关系。[点击这里查看更多详情](https://docs.docker.com/compose/)

##### example

```
version: '3'
services: 
  dockerapp:
    build: .
    ports:  
      - "5000:5000"
    depends_on:
      - redis
    networks:
      -my_net
  redis:
    image: redis:3.2.0
  networks:
    my_net:
      driver:bridge
```
## run / 运行
```
$: docker-compose up
```

### -d flag 
running back end

在后台运行

### create and use networks/service 创建使用网络服务

refer example, it use two networks for back and front, the transition is handle by application.

参考例子，前端后端使用了两个隔离开的网络，数据处理传输全部由应用程序执行

```
version: '2'

services:
  proxy:
    build: ./proxy
    networks:
      - front
  app:
    build: ./app
    networks:
      - front
      - back
  db:
    image: postgres
    networks:
      - back
 networks:
   front:
     driver: custom-driver-1
   back: 
     driver: custom-driver-2
     driver_opts:
       foo: '1'
       bar: '2'
```


## outpus colored and aggregated logs for compose-managed containers / 显示容器的整合历史日志
```
$: docker-compose logs
```
### -f flag 
print appended log when it grows

持续显示更新日志
## print log for specific one container / 单独显示特定容器日志
```
$: docker-compose logs [container_name]
```
## stop containers without removing them / 停止所有容器（不删除）
```
$: docker_compose stop
```
## complete remove all containers / 删除所有容器
```
$: docker-compose rm
```
## enforce docker build / 容器构造
```
$: docker-compose build
```
## run service application / 运行服务应用
service_name refer docker-compose.yml file

service_name 参考 docker-compose.yml 文件
```
$: docker-compose run [service_name] [commands]
```
##### example
```
$: docker-compose run dockerapp python test.py
```
