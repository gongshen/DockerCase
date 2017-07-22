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

# `COPY`：复制