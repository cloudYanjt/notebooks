# docker 更换为国内镜像仓库

## 国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。

* 创建或修改 `/etc/docker/daemon.json`

### 1、创建文件夹

```shell
sudo mkdir -p /etc/docker
```

### 2、配置 daemon.json

```shell
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://1nj0zren.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://registry.docker-cn.com"
    ]
}
EOF
```

### 3、重新加载配置文件

```shell
sudo systemctl daemon-reload
```

### 4、重启 docker 服务

```shell
sudo systemctl restart docker
```
