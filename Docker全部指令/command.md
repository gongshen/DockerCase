## 下载镜像
```shell
docker pull
```
## 运行容器
0. 参数
	- `-i`		：交互式操作
	- `-t`		：终端
	- `--rm`	：容器退出后将之删除。避免浪费空间。
	
1. 以ubuntu:14.04镜像为基础启动一个容器。并启动`bash`进行交互式操作。
```shell
docker run -it --rm ubuntu:14.04 bash 
```
## 列出镜像
0. 参数
	- `-a`		：列出中间层镜像
	- `-f`		：过滤操作
	- `-q`		：列出`ID`
	- `--format`：自定义输出
	
1. 列出所以镜像
```shell
docker images
```
2. 列出中间层镜像
```shell
docker images -a
```
3. 列出部分镜像
```shell
docker images ubuntu	
```
4. 列出虚悬镜像
```shell
docker images -f dangling=true 
```
5. 列出`mongo:3.2`之后的镜像
```shell
docker images -f since=mongo:3.2
```
6. 列出`mongo:3.2`之前的镜像
```shell
docker images -f after=mongo:3.2	
```
7. 列出指定`label`的镜像
```shell
docker images -f label=com.example.version=0.1
```
8. 列出所有镜像的`ID`
```shell
docker images -q 
```
9. 自定义结构列出镜像(只显示ID和仓库名)
```shell
docker images --format "{{.ID}}:{{.Repository}}"
```
## 删除镜像
1. 删除虚悬镜像(需要使用镜像的`ID`)
```shell
docker rmi $(docker images -q -f dangling=true)
```