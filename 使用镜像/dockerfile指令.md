```shell
$ mkdir mynginx
$ cd mynginx
$ touch Dockerfile
```
内容是：
```txt
FROM nginx
RUN echo '<h1>Hello Docker!</h1>' > /usr/share/nginx/html/index.html
```
# `FROM`：指定基础镜像
如果你以`scratch(表示空白镜像)`为开始的话，那么你接下来的指令将作为镜像的第一层。
```txt
FROM scratch
```
# `RUN`：执行命令行命令，有两种格式
- shell格式：
```txt
RUN echo '<h1>Hello Docker!</h1>' > /usr/share/nginx/html/index.html
```

- exec格式：
```txt
RUN ["可执行文件","参数1","参数2"]
```
## 优化Dockfile
错误写法：
```txt
FROM debain:jessie

RUN apt-get update
RUN apt-get install -y gcc libc6-dev make
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz"
RUN mkdir -p /usr/src/redis
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install
```
正确写法：
```txt
FROM debain:jessie

#编译、安装redis可执行文件
RUN buildDeps='gcc libc6-dev make' \
	&& apt-get update \
	&& apt-get install $buildDeps \
	&& wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
	&& mkdir -p /usr/src/redis \
	&& tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
	&& make -C /usr/src/redis \
	&& make -C /usr/src/redis install \
	&& rm -rf /var/lib/apt/lists/* \				#清理缓存
	&& rm redis.tar.gz \							#清理下载文件
	&& rm -r /usr/src/redis \						
	&& apt-get purge -y --auto-remove $buildDeps	#清理为了编译构建所需软件
```
## 构建镜像
```shell
$ docker build -t nginx:v3 .
Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM nginx
 ---> e4e6d42c70b3
Step 2/2 : RUN echo '<h1>Hello Docker!</h1>' > /usr/share/nginx/html/index.html
 ---> Running in 48ed6a61a80e
 ---> 74fe963dff36
Removing intermediate container 48ed6a61a80e
Successfully built 74fe963dff36
Successfully tagged nginx:v3
```
可以看到：显示RUN启动一个容器`48ed6a61a80e`，执行要求的指令，最后提交给这一层`74fe963dff36`，最后删掉所用的容器。

## 其他构建方法
### 1.直接用Git repo构建
```shell
$ docker build https://github.com/twang2218/gitlab-ce-zh.git#:8.14
```

### 2.给定的tar压缩包构建
Docker引擎会自动下载，并解压缩，作为上下文，构建镜像
```shell
$ docker build http://server/context.tar.gz
```

### 3.从标准输入中读取Dockerfile进行构建
```shell
$ docker build - < Dockerfile
```
或
```shell
$ cat Dockerfile | docker build -
```

### 4.从标准输入中读取上下文压缩包进行构建
```shell
$ docker build - < context.tar.gz
```

# `COPY`：复制文件
格式：
- COPY <源路径>...<目标路径>
- COPY ["<源路径1>",..."<目标路径>"]

```dockerfile
 COPY package.json /usr/src/app
#可以是通配符
 COPY hom* /mydir/
 COPY hom?.txt /mydir/
```
**注意**：源文件的各种元数据（权限，变更时间）都会保留。

# `ADD`：更高级的复制文件（需要自动解压缩时用）
有个功能非常有用：如果源路径是压缩文件，它会自动解压到目标路径去。
```dockerfile
FROM scratch
ADD ubuntu-xenial-core-cloudimg-amd64-root.tar.gz /
```

# `CMD`：指定容器启动程序和参数
格式：
- shell格式：CMD <命令>
- exec格式：CMD ["可执行文件","参数1","参数2"...]
- 参数列表格式：CMD ["参数1","参数2"...]，在指定了`ENTRYPOINT`指令后，用`CMD`指定具体的参数。

```dockerfile
CMD echo $HOME
# 其实就等于
CMD ["sh","-c","echo $HOME"]
```
那么如果你要将nginx程序在前台运行；
```dockerfile
CMD ["nginx","-g","daemon off;"]
```

# `ENTYRPOINT`：入口点

### 使用一：让镜像变成像命令一样使用
得知自己当前公网ip的镜像
```dockerfile
FROM ubuntu:14.04
RUN apt-get update \
	&& apt-get install -y curl \
	&& rm -rf /var/lib/apt/list/* 
	CMD ["curl","-s","http://ip.cn"]
```
使用`docker build -t myip .`来构建镜像，如果需要查询ip：
```shell
$ docker run myip
当前 IP：116.224.202.178 来自：上海市 电信
```
如果你想希望加入`-i`参数：
错误做法：
```shell
#因为加了参数-i的话，运行时会默认替换掉CMD的默认值
$ docker run myip -i
```