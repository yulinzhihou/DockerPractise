# Docker 05 容器网络

## 一、设置容器网络

### 1、原理分析

```Bash
# 查看系统是否有iproute包
rpm -q iproute
# 使用ip命令设置网络名称
ip netns help
Usage: ip netns list
       ip netns add NAME
       ip netns set NAME NETNSID
       ip [-all] netns delete [NAME]
       ip netns identify [PID]
       ip netns pids NAME
       ip [-all] netns exec [NAME] cmd ...
       ip netns monitor
       ip netns list-id

# 添加网卡
ip netns add r1
ip netns add r2

# 查看添加的网卡
ip netns list

#在添加的网卡上执行命令
ip netns exec r1 ifconfig 
ip netns exec r1 ifconfig -a 
# 新版本系统使用下面命令查看
ip netns exec r1 ip addr

# 修改网卡名称
ip netns exec r1 ip link set dev veth1.2 name eth0

# 创建网卡对
ip link help

ip link add [link DEV] [ name ] NAME
                   [ txqueuelen PACKETS ]
                   [ address LLADDR ]
                   [ broadcast LLADDR ]
                   [ mtu MTU ] [index IDX ]
                   [ numtxqueues QUEUE_COUNT ]
                   [ numrxqueues QUEUE_COUNT ]
                   type TYPE [ ARGS ]

       ip link delete { DEVICE | dev DEVICE | group DEVGROUP } type TYPE [ ARGS ]
       
      ·
      ·
      ·
      
# 创建一对虚拟网卡
ip link add name veth1.1 type veth peer name veth1.2
# 查看网卡
ip addr 
ifconfig 
# 激活网卡
ip addr veth1.1 10.1.0.1/24 up
# 禁用网卡
ip addr veth1.1 down

# 将虚拟网卡veth1.1移动到名称空间r1上
ip link set dev veth1.1 netns r1
# 将虚拟网卡veth1.2移动到名称空间r2上
ip link set dev veth1.2 netns r2
# 设置虚拟网卡IP并启动
ip netns exec r1 ifconfig eth0 10.1.0.2/24 up
# 查看设置的Ip地址
ip netns exec r1 ifconfig 

```

### 2、容器创建指定对应网络模式

```shell
# 1:bridge模式，使用–net=bridge指定，默认设置
docker run --name t1 -it --rm  -network bridge busybox:latest
# 在容器内可以使用 ip addr 查看
ip addr 
# 指定主机名创建桥接网络
docker run --name t3 -it --network bridge -h yulinzhihou.com --rm busybox:latest
# 在容器内可以使用 hostname 查看主机名
hostname
# 在容器内查看 hosts 文件
cat /etc/hosts
# 在容器内查看 dns 服务器
cat /etc/resolv.conf
# 在创建容器的时候指定DNS服务器
docker run --name t3 -it \
--network bridge -h yulinzhihou.com \
--add-host www.baidu.com:192.168.1.1 \
--dns 114.114.114.114 \
-rm busybox:latest
# 在创建桥接网络的时候，使用-p参数，将容器内的商品暴露到桥接网络上
docker run --name t4 -it --network bridge -p 80 --rm busybox:latest
# 查看本地nat规划
iptables -t nat -vnL
# -p规则会自动创建nat规划，如果停止，如果容器停止，规划也会自动删除
# 在创接网络的时候，-p暴露端口时，固定指定的IP地址
docker run --name t5 -it --network bridge -p 172.17.0.12::80 busybox:latest
# 查看容器暴露的端口
docker port t5
# 在创建网络的时候， -p暴露端口时，固定端口
docker run --name t6 -it --network bridge -p 80:80 busybox:latest
# 在创建网络的时候，-p暴露端口时，指定IP与端口
docker run --name t7 -it --network bridge -p 172.17.0.20:80:80 busybox:latest
# 也可以在创建网络时，使用-P选项，将容器内产生的端口全部暴露出来
docker run --name t8 -it --network bridge -P busybox:latest

# 2:none模式，使用–net=none指定。
docker run --name t2 -it --network none --rm busybox:latest
# 在容器内可以使用 ip addr 查看
ip addr 

# 3:启动容器，container模式，使用–net=container:容器名称或ID指定
# 先创建一个普通容器
docker run --naem t1 -it --rm busybox:latest
# 再基于容器t1的网络，创建一个容器t2,使t2使用t1一样的网络
docker run --name t2 --network container:t1 -it --rm busybox:latest
# 这样创建的容器，网络是共用的，但是文件系统等其他都是隔离的

# 4:创建窗口也可以加入物理机的网络
docker run --name t3 --network host -it --rm busybox:latest
```

### 3、自定义Docker0桥的网络属性信息

- `/etc/docker/daemon.json` 文件

```shell
{
	"bip":"192.168.1.5/24",#会自动以该网络创建dhcp服务，将对应的容器归属于他下面
	"fixed-cidr":"10.20.0.0/16",
	"fixed-cidr-v6":"2001:db8::/64",
	"mtu":1500,
	"default-gateway":"10.20.1.1",
	"default-gateway-v6":"2001:db8:abcd::89",
	"dns":["10.20.1.2","10.20.1.3"],
	"hosts":["tcp://0.0.0.0:2375","unix:///var/run/docker.sock"]#用于连接本地使用
}
```



```SHELL
# 使用命令查看监听本地连接docker的端口是否连接
ss -tnl
# 查看其他主机上的 docker 容器信息
dockr -H 172.17.0.12 ps 
```



```shell
# 创建单独的网络
docker network create -d bridge --subnet "172.17.0.0/16" --gateway "172.17.0.1" mybr0
# 查看创建的网络接口
docker network ls
ip addr 

# 基于新创建的网络接口，创建容器加入到网络里面
docker run --name t1 -it --network br-*** --rm busybox:latest
docker run --name t2 -it --network bridge --rm busybox:latest
```

