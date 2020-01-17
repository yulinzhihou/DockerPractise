# Docker 命令练习手册

## 一、第一阶段：系统环境安装、镜像管理、容器管理相关命令

**1、首先，安装好系统后，配置相关系统安装源。以CentOS7.7x64 1810为例**

```Bash
# 查看当前yum源中docker的安装源
yum repolist 
# 使用清华大学镜像源
wget https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
```
```Bash
# 编辑下载下来的repo的安装源镜像文件
vim ./docker-ce.repo
```
```Bash
#将里面默认的安装地址替换掉为国内的安装源地址
:%s@https://download.docker.com/linux/centos@ \
https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos@
#将修改好的源文件，扔到/etc/yum.repos.d/
mv docker-ce.repo /etc/yum.repos.d/
```
```Bash
#执行安装 
yum -y install docker-ce
#安装完后，生成配置文件
mkdir -p /etc/docker
# 添加配置文件
vim /etc/docker/daemon.json
```
- 下面是添加配置文件的内容
```Bash
{
	"registry-mirrors":[
		"https://registry.docker-cn.com",
		"https://f0tv1cst.mirror.aliyuncs.com/"
		]
}
```
**2、Docker基本配置**

```Bash
#启动docker 
systemctl start docker
#停止docker 
systemctl stop docker
#重启docker 
systemctl restart docker
```
```Bash
#查看docker版本信息
docker version
#查看docker详情信息
docker info
# 查看网络接口
docker network ls

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
