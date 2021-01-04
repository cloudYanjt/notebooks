# CentOS 下安装 Docker

## 1、查看内核版本

目前，Docker 运行在 CentOS 7 上，要求系统为64位、系统内核版本为 3.10 以上。
查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。
通过 `uname -r` 命令查看你当前的内核版本

## 2、确保 yum 包更新到最新。

```
sudo yum update
```

## 3、移除旧的版本（如有）

```shell
sudo yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-selinux \
    docker-engine-selinux \
    docker-engine
```

## 4、安装一些必要的系统工具

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

## 5、设置国内 yum 源

```shell
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 6、安装docker

```shell
sudo yum  install docker-ce
```

### 6.1 或者 ...

不管是在 Ubuntu 或 CentOS 都只需要执行下面指令就会自动安装最新版 Docker。
`curl -fsSL "https://get.docker.com/" | sh`
*国内下载慢可以指定阿里源*
`curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun`

## 7、启动 Docker 后台服务

```shell
sudo systemctl start docker
```

## 8、加入开机启动

```shell
sudo systemctl enable docker
```

## 9、测试docker是否正常安装和运行

```shell
sudo docker run hello-world
```

由于本地没有hello-world这个镜像，所以会下载一个hello-world的镜像，并在容器内运行。
打印结果：
*Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
.......*

## 10、添加普通用户执行权限
### 1、创建docker 用户组

```
sudo groupadd docker
```

### 2、普通用户加入docker用户组

```
sudo usermod -aG docker ${USER}
```

### 3、切换到docker用户组

```
newgrp docker
```

### 4、重启docker服务

```
sudo systemctl restart docker
```
