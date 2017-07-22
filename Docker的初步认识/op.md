# 1.pull
## 下载
docker pull [选项] [Docker Registry地址]<仓库名>:<标签>

- Docker Registry地址：格式一般是<域名/IP>[:端口号]。默认地址为Docker Hub
- 仓库名：<用户名>/<软件名>。默认为 library。
```shell
docker pull ubuntu:14.04
```
# 2.run
- `-it`	：这是两个命令一个是`-i`：交互式操作，一个是`-t`终端。我们这里打算进入`bash`执行一些命令并查看返回结果，因此我们需要交互式终端。
- `--rm`：这个命令说明容器退出后随将之删除。一般情况下，退出不会自动删除。
## 运行
启动`ubuntu:14.04`中的`bash`终端，并且进行交互式操作：
```shell
$ docker run -it --rm ubuntu:14.04 bash
root@89f66aecaafc:/# cat /etc/os-release
NAME="Ubuntu"
VERSION="14.04.5 LTS, Trusty Tahr"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 14.04.5 LTS"
VERSION_ID="14.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
root@89f66aecaafc:/# exit
exit
```

# 3.images
- `-f`：过滤器参数。

## 列出镜像
```shell
$ docker images
```
列出部分镜像：只跟ubuntu有关的。
```shell
$ docker images ubuntu
```
显示中间层镜像
```shell
$ docker images -a
```
显示虚悬镜像（可以删除）
```shell
$ docker images -f dangling=true	
```

## 删除镜像
```shell
$ docker rmi $(docker imges -f dangling=true)
```