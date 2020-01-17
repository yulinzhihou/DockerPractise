# Docker 03  容器的镜像管理

## 一、Docker 镜像架构

### 1、镜像实现方式

- 分层构建，最底层bootfs,其之为rootfs，
  - bootfs:用于引导文件系统，包括bootloader和kernel，容启动完会被卸载。
  - rootfs：位于bootfs之上，表现为docker容器的根文件系统 ，只读模式，
  - 最上层：可以读写层。其他均为只读层
- Aufs: 高级多层统一文件系统。Advanced multi-layered unification filesystem
- btrfs
- devicemapper
- overlayfs

### 2、docker Registry docker镜像仓库，默认指hub.docker.com

- Sponsor Registry
- Mirror Registry
- Vendor Registry
- Pribate Registry

### 3、Registry由两部分组成。Repository+index

- Repository
- index

### 4、Docker Registry 镜像

- docker hub  \<dockerfile\>
  - Image Repository：镜像仓库
  - Automated Builds：自动构建
  - Webhooks：钩子
  - Organizations：组织
  - GitHub and Bitbucket Integration：

- quay.io

### 5、镜像相关的操作。

- 镜像生成的途径
  - Dockerfile
  - 基于容器制作
  - Docker Hub Automated Builds

### 6、基于容器创建镜像

```bash
#运行基于busybox镜像的容器一个
docker run --name b1server -it busybox:latest
#启动容器，进行相应的修改。不要关闭容器
#创建一个httpdserver
mkdir -p /data/www/html
vi /data/www/html/index.html
·
·
·
<h1> b1 server httpd </h1>

#保存退出 
# 新开一个窗口，保持原窗口在sh中运行状态
docker commmit b1server -a "yulinzhihou@gmail.com" -p b1server
# 给镜像打标签
docker tag imageId yulinzhihou/b1server:v0.1
# 给原打了标签的镜像再打一个标签。
docker tag yulinzhihou/b1server:v0.1 yulinzhihou/b1server:latest
# 删除一个标签
docker image rm yulinzhihou/b1server:latest
# 查看镜像
docker image ls
docker images
# 查看镜像里面的详情信息
docker inspect yulinzhihou/b1server:v0.1
```

- 创建镜像时改变镜像启动后容器运行的名称

```bash
# 启动之前 创建的镜像为一个新容器
docker run --name b1s1 -it yulinzhihou/b1server:v0.1
# 直接使用docker commit -c 
docker commit -a "yulinzhihou@gmail.com" -c 'CMD ["/bin/httpd","-f","-h","/data/www/html"]' -p b1s1 yulinzhihou/b1server:v0.2
# 查看制作了的镜像文件
docker image ls 
docker images 
# 运行刚刚创建的镜像启动为一个容器,运行到后台，不要前台交互
docker run --name b1s2 -d yulinzhihou/b1server:v0.2
# 查看容器的详情情况
docker inspect b1s2
# 找到IP地址，直接使用curl命令访问
curl 172.17.0.3
```

- 将制作好的镜像，推送到hub.docker.com仓库

```bash
# 登录hub.dokcer
docker login -u yulinzhihou
# 登录到其他平台。如阿里云，
docker login --username=yulinzhihou_luolu@foxmail.com registry.cn-hangzhou.aliyuncs.com

# 推送到指定仓库
docker push yulinzhihou/b1server
```

- 镜像的导入与导出

```BASH
# 打包镜像文件
docker save -o myimages.gz yulinzhihou/b1server:v0.1 yulinzhihou/b1server:v0.2
# 使用scp复制到需要使用镜像的机器
scp myimages.gz node02:/root

# 在node02机器上还原镜像
docker load -i myimages.gz
# 查看导入的镜像
docker image ls
docker images
# 此方式有一定的缺陷，如果再基于此镜像再进行镜像操作，推送的位置还是原来的用户空间
```

