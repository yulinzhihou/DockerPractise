# Docker 04 容器虚拟化网络概述

## 一、Docker容器网络类型

### 1、Overlay Network 叠加网络

### 2、Docker 网络

- bridge模式，使用–net=bridge指定，默认设置。
- none模式，使用–net=none指定。
- host模式，使用–net=host指定。
- container模式，使用–net=container:容器名称或ID指定

```Bash
#查看网络卡桥的信息
yum -y install bridge-utils
brctl show 
#查看网卡连接状态
ip link show 
#查看防火墙信息
iptables -t nat -vnL
#测试web服务器
wget -O - -q http://172.17.0.2

# 查看容器网络桥接的详细信息
docker network inspect bridge
# 创建容器的时候，指定网络
docker run --name bserver --network ***
```