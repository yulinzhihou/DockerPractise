# Docker 01 容器概述

## 一、LXC

### 1、主机级虚拟化

- #### 类型一：虚拟机软件

- #### 类型二：物理机装虚拟化软件(云主机)

### 2、jail,vserver(chroot)，namespaces+chroot 实现空间隔离。

- **UTS**：主机名和域名
- **Mount**:挂载点（文件系统）
- **IPC**：信号量、消息队列和共享内存
- **User**：用户和用户组
- **Net**：网络设备、网络栈、端口等
- **PID**：进程编号

### 3、Cgroups(control groups)

- blkio:
- cpu:
- cpuacct:
- cpuset:
- devices:
- freezer:
- memroy:
- perf_event:
- net_cls:

### 4、LXC （Linux Container）

- lxc-create
- template

### 5、Docker(LXC 封装发行版，基于LXC)

- 镜像：分层构建，联合挂载
- 仓库

### 6、Docker 编排工具

- machine+swarm+compose
- mesos+marathon
- kubernetes->k8s
- libcontainer->runC

