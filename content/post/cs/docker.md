---
title: "Docker"
date: 2022-01-29T23:12:51+08:00
categories: ["original"]
tags: ["Cloud Native"]
draft: false
---

# Docker

## 安装Docker(Ubuntu)

```Shell
  sudo apt install docker.io
```

- 配置加速地址

```Shell
  # 在Ubuntu16.04和CentOS7.0及以上

  vim /etc/docker/daemon.json(不存在就新建)

  {

    "registry-mirrors": [

      "https://registry.docker-cn.com"

    ]

  }

  # 里面的镜像地址就是自己的加速地址

  sudo systemctl daemon-reload

  sudo systemctl restart docker

  # 查看加速地址是否生效

  docker info

          Registry Mirrors:

           https://xxx.mirror.aliyuncs.com/

  # 这样就代表已经生效了
```

## 查看docker信息

```
docker info
```

## 查找镜像

- docker hub

```
https://hub.docker.com
```

- 命令
  - `docker search [OPTIONS] TERM`

- `--automated=false` 只选出自动化构建出的docker镜像

- `no-trunc=false`

- `-s --stars=0` 限制star的最小星数

## 列出镜像

- 命令
  - `docker images [OPTIONS] [REPOSITORY]`

- 参数
  - `-a` 显示所有镜像(默认并不显示中间层的镜像)

- `-f` 过滤时的过滤条件

- `--no-trunc=false` 不使用截断的形式显示镜像id

- `-q` 只显示镜像的唯一id

## 查看镜像详细信息

- 命令
  - `docker inspect [OPTIONS] CONTAINER|IMAGE [CONTAINER|IMAGE]`

## 删除镜像

- 命令 `docker rmi [OPTIONS] IMAGE [IMAGE...]`

- 选项
  - `-f` 强制删除

## 拉取镜像

- 命令
  - `docker pull [OPTIONS] NAME [:TAG]`

- 参数
  - `-d` –all-tags=false 下载所有标签

## 推送镜像

- 命令

```
docker push NAME[:TAG]
```

## 构建镜像

- 命令
  - `docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]` 通过容器构建

- `-a --author=` 作者

- `-m --message` 提交信息

- `-p --pause=true` 提交时暂停容器(默认是暂停，改为false可禁止此行为)

- `docker build [OPTIONS] PATH | URL| -` 通过dockerfile构建

- `--force-rm=false`

- `--no-cache=false`

- `pull=false`

- `-q --quiet=false`

- `--rm=true`

- `-t --tag=""`

- 示例

```Nginx
  docker commit container001 image_name # 基于现有容器修改后，构建一个镜像，然后在docker images列表里可以看到这个镜像
```

## 启动容器

- 命令
  - `docker run IMAGE [COMMAND][ARG...]`

- 参数
  - `-i` 启动交互式环境

- `-t` 启动一个伪TTY终端

- `-d` 启动一个守护式容器

- `--name=自定义名`给容器起一个自定义名字

- 示例
  - 直接启动并执行命令(执行完成后退出)

```Shell
docker run ubuntu echo 'echo world'
```

- 启动交互式环境

```Dockerfile
docker run -it IMAGE /bin/bash
```

- 示例

```shell
postgres `docker run --name notifier_db -d -p 5432:5432 -e POSTGRES_PASSWORD=postgres -v /home/jzhan/data/notifier_db:/var/lib/postgresql/data postgres`
```

## 退出容器

- 命令
  - `exit`

- `ctrl + p + q` 继续在后台运行

## 启动停止的容器

- 命令
  - `docker start [-i] 容器名`

- 示例
  - `docker start -i container01`

## 重启容器

- 命令
  - `docker restart 容器名`

## 查看容器

- 命令
  - `docker ps [-a][-l]`

- 参数
  - `-a` 查看所有容器

- `-l`

- `a` 只返回容器id

## 删除停止的容器

- 命令
  - `docker rm 容器名`

- `docker rm -f $(docker ps -qa)` 删除所有容器

## 守护式容器

- 命令
  - 运行交互式容器时以`Ctrl + P + Q`退出，容器便会转为守护状态，在后台运行

- `docker run -d 镜像名 [COMMAND] [ARG...]`

- 重新进入守护式容器
  - `docker attach 容器名/id`

## 查看容器日志

- 命令 `docker logs [-f] [-t] [-tail] 容器名`
  - 参数

- `-f` follow 滚动

- `-t` 显示时间戳

- `--tail` = all

## 查看容器内进程

- 命令
  - `docker top 容器名`

## 在运行的容器中启动新的进程

- 命令
  - `docker exec [-d][-i][-t] 容器名 [COMMAND][ARG...]`

- 示例
  - `docker exec -i -t container01 /bin/bash` 启动一个新的bash

- `docker exec -it nginx001 /bin/bash` 进入到nginx001容器

- `docker exec -t container001 ls -l /tmp`

## 停止容器

### 停止守护式容器

- 命令
  - `docker stop 容器名` 发送信号给容器，等待停止

- `docker kill 容器名` 强制关闭

## 拷贝文件

```
docker cp 容器id:容器内路径 目的主机路径
```

## 容器的端口映射

- 命令
  - `docker run [-p][-P]` 在运行容器时指定端口

- `-P`为容器暴露的所有端口进行映射

```Shell
  docker run -P -it ubuntu /bin/bash
```

- `-p` 指定映射容器的哪些端口

```Shell
  docker run -p 80 -it ubuntu /bin/bash  # 只指定容器端口，宿主端口随机映射

  docker run -p 8080:80 -it ubuntu /bin/bash  # 宿主机端口和容器端口

  docker run -p 0.0.0.0:80 -it ubuntu /bin/bash

  docker run -p 0.0.0.0:8080:80 -it ubuntu /bin/bash
```

- `docker port 容器名` 查看容器的端口映射情况

## 容器数据卷

- 命令
  - `docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名`

- `docker run -it -v /myDataVolumn:/容器内目录 镜像名`

- `docker run -it -v /宿主机绝对路径目录:容器内目录:ro 镜像名` 容器内只读

## Dockerfile

- `ENV` 用来在构建镜像过程中设置环境变量
  - 示例

```Dockerfile
ENV MY_PATH /usr/mytest        
```

- `FROM` 基础镜像，当前镜像是基于哪个镜像的

- `RUN 容器构建时需要运行的命令`

- `WORKDIR` 指定在创建容器后，终端默认登录进来的工作目录，一个落脚点
  - 示例

```Dockerfile
WORKDIR $MY_PATH
```

- `EXPOSE` 当前容器对外暴露的端口

- `CMD` 指定一个容器启动时要运行的命令
  - dockerfile中可以有多个CMD命令，但只有最后一个会生效，CMD会被docker run之后的参数替换

- `ENTRYPOINT` 指定一个容器启动时要运行的命令
  - ENTRYPOINT 的目的和CMD一样，都是在指定容器启动程序及参数, docker run 之后的参数会被当作参数传给ENTRYPOINT，之后形成新的命令组合

- `COPY` 类似ADD，拷贝文件和目录到镜像中，将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>位置
  - 示例

```Dockerfile
COPY src desc

COPY ["src", "dest"]
```

- `VOLUMN` 容器数据卷，用于数据保存和持久化工作

- `MAINTAINER` 镜像维护者的姓名和邮箱地址

- `ADD` 将宿主机下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包
  - 示例

```Dockerfile
ADD test.tar /
```

- `LABEL`

- `ONBUILD`

### 基础知识

1. 每条保留字指令都必须为大写字母且后面要跟至少一个参数
2. 指令按照从上到下，顺序执行
3. `#` 表示注释
4. 每条指令都会创建一个新的镜像层，并对镜像进行提交

### docker build

```
docker build -f dockerfile
```

### 示例

```Dockerfile
docker build -f /mydocker/dockerfile2 -t mycentos:1.3 .
FROM centos

VOLUMN ["/dataVolumnContainer1", "/dataVolumnContainer2"]  # 根目录下新建两个容器卷

CMD echo 'finished...'
CMD /bin/bash
FROM centos
ENV mypath /tmp
WORKDIR $mypath
RUN yum -y install vim
RUN yum -y install net-tools
EXPOSE 80
CMD /bin/bash
```


# 一些问题

## 什么是容器

- 容器包括应用程序及所有的依赖项，作为操作系统的独立进程运行。

## ONBUILD命令

当镜像用作另一个镜像构建的基础时，ONBUILD指令向镜像添加将在稍后执行的触发指令。

## 如何在生产中监控docker

- docker stats和docker事件等工具来监控生产中的docker。

## 批量清理临时镜像文件

```Bash
sudo docker rmi $(sudo docker images -q -f danging=true)
```

## 停止所有运行中的容器

```Bash
docker kill $(sudo docker ps -q)
```

## 批量清理后台停止容器

```Bash
docker rm$(sudo docker ps -a -q)
```

## 查看镜像支持的环境变量

```Bash
sudo docker run IMAGE env
```

## 本地的镜像文件存在哪里

docker相关的本地资源存在/var/lib/docker/目录下，其中container目录存放容器信息，graph目录存放镜像信息，aufs目录下存放具体的镜像底层文件。

## 访问宿主机网络

https://jingsam.github.io/2018/10/16/host-in-docker.html

## 非root用户操作docker

```Dockerfile
sudo groupadd docker

sudo gpasswd -a {USER} docker

sudo systemctl restart docker

newgrp - docker
```

## 跨平台build

```Shell
docker build -f deploy/Dockerfile --platform linux/amd64 -t registry.cn-hangzhou.aliyuncs.com/burnook/next_burnook_amd64:latest .
```