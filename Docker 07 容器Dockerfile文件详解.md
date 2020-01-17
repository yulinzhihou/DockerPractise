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

  - 语法：

    ```shell
    FROM <>
    ```

    