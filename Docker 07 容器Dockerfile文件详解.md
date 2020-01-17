# Docker 07 容器Dockerfile文件详解

### 1、自制镜像

- 基于容器制作镜像
- Dockerfile配置文件制作镜像

### 2、Dockerfile详解

- Dockerfile就是配置文件用来制作Docker镜像
- Dockerfiile语法格式
  - `#` 注释
  - `INSTRUCTION` 参数
  - `INSTRUCTION` 不区分大小写
  - 运行`INSTRUCTION` 按从上往下的顺序执行
  - `INSTRUCTION` 第一个结构语句必须 为 `FROM` ，镜像基于哪个基础镜像进行构建
- 编写 `Dockerfile` 的逻辑
  - 单独的工作目录
  - 相关文件与都必须放入到工作目录
  - `.dockerignore` 文件，用来打包时忽略的文件即目录
  - `Dockerfile`  文件名称首字母必须大写
  - `Dockerfile` 里面使用的 `shell` 命令必须是底层镜像启动容器所能提供的，不是宿主机的命令
  - 可以使用环境变量进行来替换，是在制作镜像时候使用的环境变量，不是在使用镜像的时候的环境变量
    - ${variable:-word}：如果 `variable`  环境变量没有设置，则使用 `word`
    - ${variable:+word}：如果 `variable`  环境变量有设置，则使用 `word`

### 3、Dockerfile 结构

- `FROM`  开篇的第一个非注释行。用于为映像文件构建 过程指定基准镜像，后续指令运行于此基准镜像所提供的运行环境

  ```shell
# 语法：
  FROM <repository>[:<tag>]或者
  FROM <repository>@<digets> //hash码
  # <repository>:表示指定作为 base image 的名称
# <tag>: base image 的标签，为可选项，省略时默认为latest
  # <digets>: 表示 base image 对应tag的hash编码的值。可以确定 base image 的唯一性
  ```
  
- `MAINTANIER` (deprecated) docker 1.17版本以后：已经废弃，使用 `LABEL` 标签

- `LABEL` 

  ```SHELL
  # 语法
  LABEL KEY=VALUE
  ```

- `COPY` : 用于从 `Docker` 主机复制文件到创建的新映像文件中

  ```shell
  # 语法
  COPY <SRC> ... <DEST> 或
  copy ["<src>", ... "<dest>"]
  # <src> : 要复制的源文件和目录，支持使用通配符
  # <dest> : 目标路径，即正在创建的镜像的文件系统路径；建议<dest>使用绝对路径，否则，copy指定则以WORKDIR为其起始路径
  # 注意：在路径中有空白字符时，通常使用第二种格式
  ```

  - 文件复制准则：
    - \<src> 必须是build上下文中的路径，不能是其父目录中的文件
    - 如果 \<src> 是目录，则其内部文件或者子目录会被递归复制，但\<src>目录自身不会被复制
    - 如果指定了多个\<src>，或者\<src>中使用了通配符，则\<dest>必须是一个目录，并且必须以 `\` 结尾
    - 如果 \<dest>事先不存在，它将会被自动创建，这包括其父目录路径

- `ADD`  指令类似于 `COPY` 指令，`ADD` 支持使用 `tar` 文件和URL路径

  ```SHELL
  # 语法
  ADD  <SRC> ... <DEST> 或
  ADD  ["<src>", ... "<dest>"] 
  ```

  - 文件操作准则：
    - 同 `COPY` 指令
    - 如果 \<src> 为 url 且 \<dest> 不以 `/` 结尾，则\<src> 指定的文件将被下载并直接被创建为 \<dest>; 如果\<dest> 以 `/` 结尾，则文件名URL指定的文件被直接下载并保存为\<dest>/\<filename>
    - 如果 \<src>是一个本地系统 上的压缩格式的tar文件，它将被展开为一个目录，其行为类型于 `tar -xf` 命令；然而，通过URL获取到的tar文件将不会自动展开
    - 如果 \<src> 有多个，或者其间接或者直接使用了通配符，则\<desc> 必须是一个以 `/` 结尾的目录路径；如果 \<dest> 不以 `/` 结尾，则其被视作一个普通文件，\<src> 的内容将被直接写入到\<dest>；











### 4、实例

- 创建一个 `Dockerfile` 文件

```SHELL
# Description:test image
FROM busybox:latest
#MAINTAINER "yulinzhihou<yulinzhihou@gmail.com>"
LABEL maintainer="yulinzhihou<yulinzhihou@gmail.com>"
COPY index.html /data/html/www/
COPY yum.repos.d /etc/yum.repos.d/
ADD http://nginx.org/download/nginx-1.17.7.tar.gz /usr/local/src/
```

- 制作镜像

```shell
docker build -t tinyhttpd:v0.1 ./
# 运行当前镜像的一个窗口
docker run --name h1 --rm tinyhttpd:v0.1 cat /data/html/www/index.html
```

