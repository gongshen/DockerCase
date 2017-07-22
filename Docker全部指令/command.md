## 下载镜像
1. docker pull
## 运行容器
1. docker run
## 列出镜像
1. docker images
2. docker images -a 									——列出中间层镜像
3. docker images ubuntu									——列出部分镜像
4. docker images -f dangling=true   					——列出虚悬镜像
5. docker images -f since=mongo:3.2						——列出`mongo:3.2`之后的镜像
6. docker images -f after=mongo:3.2						——列出`mongo:3.2`之前的镜像
7. docker images -f label=com.example.version=0.1		——列出指定`label`的镜像
8. docker images -q 									——列出所有镜像的`ID`
9. docker images --format "{{.ID}}:{{.Repository}}"		——自定义结构列出镜像(只显示ID和仓库名)
## 删除镜像
1. docker rmi $(docker images -q -f dangling=true) 		——删除虚悬镜像(需要使用镜像的`ID`)
