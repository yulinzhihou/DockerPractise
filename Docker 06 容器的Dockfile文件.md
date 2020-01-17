# Docker 06 容器的存储卷

### 1、写时复制

- Docker镜像由多个只读层叠加而成。启动容器时，Docker会加载只读镜像层并在栈顶部添加一个读写层
- 运行中的容器修改了现有的一个已存在的文件，那该文件将会从读写层下面的只读层复制到读写层，该文件的只读版本仍然存在，只是已经被读写层中该文件的副本所隐藏。这就叫写时复制

### 2、数据存储的条件

- 有状态，需要持久存储：mysql
- 有状态，无需持久存储：redis
- 无状态，需要持久存储：极少见
- 无状态，无需持久存储：nginx

### 3、数据存储卷

- 关闭并重启容器，数据不受影响。但删除容器，会全部丢失
- 存在的问题
  - 存储于联合文件系统 中，不易于宿主机访问
  - 容器间数据共享不方便
  - 删除容器数据会丢失
- 解决方案
  - 卷，卷是容器上的一个或者多个目录，此类目录可以绕过联合文件系统 ，与宿主机上的某个目录进行绑定（关联）
- 在启动或者创建容器的时候，创建卷
- 卷为docker 提供了独立于容器的数据管理机制
- 卷的类型
  - bind mount volume：手动指定宿主机的目录，与容器内的目录建立绑定关系
  - Docker-managed volume：只需要指定容器内某个目录需要绑定宿主机目录，宿主机目录可以不需要指定

### 4、在容器中使用卷

```shell
# 由Docker 容器管理的挂载卷。不需要指定非容器目录
docker run --name b1 -it -v /data busybox:latest
docker container create  --name b2 -it -v /data busybox:latest
# 查看绑定的信息
docker inspect -f {{.Mounts}} b1

# 指定容器与非容器目录的绑定关系挂载
# docker run --name NAME -it -v HOSTDIR:VOLUMEDIR busybox:latest
docker run --name b3 -it -v /data:/data busybox:latest
# 查看绑定的信息
docker inspect -f {{.Mounts}} b1
```

### 5、共享存储卷

```shell
# 初始化容器时可以去复制别的容器的存储卷
docker run --name b1 -it -v /docker/volumes/v1:/data busybox:latest
docker run --name b2 -it -v /docker/volumes/v1:/data busybox:latest

#复制使用其他容器的卷，为docker run 命令使用 --volumes-from  选项
docker run --name b3 -it -v /data/volumes/v2:/data/html busybox:latest
docker run --name b4 -it --volumes-from b3 busybox:latest
```

### 6、容器编排工具

docker-compose

