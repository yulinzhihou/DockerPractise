# Docker 02 容器的基础用法

## 一、Docker 安装

### 1、Docker C/S架构

- client
  - docker build
  - docker pull 
  - docker run
- Docker_HOST (server)
  - Docker daemon
  - Containers
  - Images
- Registry
  - hub.docker.com
  - quay.io

### 2、镜像与容器

- 镜像是静态的，不会改变（类似windows ISO镜像文件一样）
- 容器是动态的，运行镜像起来的（由ISO镜像文件安装成的系统）

### 3、安装及使用docker

- 依赖的基础环境
  - 64位CPU
  - Linux kernel 3.10+
  - Linux Kernel  cgroups and namespaces
- Centos 7.*
- Docker Daemon
  - systemctl start docker
- Docker client
  - docker [OPTIONS] COMMAND [arg....]

### 4、安装配置，及镜像拉取与删除操作

```bash
#查看当前yum源中docker的安装源
yum repolist 
#使用清华大学镜像源
wget https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
#编辑下载下来的repo的安装源镜像文件
vim ./docker-ce.repo
·
·
·
#将里面默认的安装地址替换掉为国内的安装源地址
:%s@https://download.docker.com/linux/centos@ \
https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos@
#将修改好的源文件，扔到/etc/yum.repos.d/
mv docker-ce.repo /etc/yum.repos.d/
#执行安装 
yum -y install docker-ce
#安装完后，生成配置文件
mkdir -p /etc/docker
vim /etc/docker/daemon.json
·
·
·
{
	"registry-mirrors":[
		"https://registry.docker-cn.com",
		"https://f0tv1cst.mirror.aliyuncs.com/"
		]
}

#启动docker 
systemctl start docker
#停止docker 
systemctl stop docker
#重启docker 
systemctl restart docker

#查看docker版本信息
docker version
#查看docker详情信息
docker info

#搜索镜像
docker search redis

#拉取镜像
docker image pull nginx:1.15alpine
docker pull busybox[:latest]

#查看镜像
docker image ls
docker image ls -a
docker images

#删除镜像，如果镜像启动了容器的话，不能删除镜像，即使窗口已经停止了。也删除不了镜像
docker image rm busybox[:latest]
docker rmi redis:1.17-alpine
```



### 5、镜像仓库

- alpine:最小化的镜像发行版本

### 6、容器启动

```bash
#查看容器状态
docker ps 
docker ps -a 
docker container ls 
docker container ls -a

#创建容器 方式一：
docker container create --name b2 busybox:latest
#启动创建了的容器
docker container start b2
docker start b2

#创建容器 方式二：
#docker run [OPTIONS] image [arg....]
#基于镜像busybox:latest 创建一个名为b1的容器。并启动交互式界面
docker run --name b1 -it busybox:latest

#停止容器
#docker kill <容器名>
#docker stop <容器名>
docker kill b1
docker container kill b1
docker stop b1
docker container stop b1

#查看容器详情信息
#docker inspect <容器名>
docker inspect b1
docker container inspect b1

#查看容器网络
docker network ls 

#删除容器
docker rm b1
docker container rm b1

#在容器中执行命令
docker exec -it b1 /bin/sh
docker container exec -it b1 /bin/sh

```

