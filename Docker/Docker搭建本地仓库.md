### Docker搭建本地仓库

**1、下载镜像registry**

```
docker pull registry
```

1.1 下载稍后测试用的镜像

​		docker pull hello-world

**2、运行registry容器**

```
docker run -itd -v /data/registry:/var/lib/registry -p 5000:5000 --restart=always --name registry registry:latest 
```

参数说明
-itd：在容器中打开一个伪终端进行交互操作，并在后台运行；
-v：把宿主机的/data/registry目录绑定 到 容器/var/lib/registry目录(这个目录是registry容器中存放镜像文件的目录)，来实现数据的持久化；
-p：映射端口；访问宿主机的5000端口就访问到registry容器的服务了；
--restart=always：这是重启的策略，假如这个容器异常退出会自动重启容器；
--name registry：创建容器命名为registry，可以随便命名；
registry:latest：这个是刚才pull下来的镜像；

*注：如果出现`WARNING: IPv4 forwarding is disabled. Networking will not work.`* 

*解决办法：在宿主机上面将 net.ipv4.ip_forward=1 追加插入到 /usr/lib/sysctl.d/00-system.conf 文件中*

*然后重启network和docker服务*

```bash
systemctl restart network && systemctl restart docker
```

**3、测试镜像仓库中所有的镜像**

```
curl http://127.0.0.1:5000/v2/_catalog
```

{"repositories":[]}

现在是空的，因为才刚运行，里面没有任何镜像内容。

**4、修改docker client pc里面的/etc/docker/daemon.json文件，如果没有该文件，可以手工创建**

```
vi /etc/docker/daemon.json
```

插入如下内容：

{
  "insecure-registries": [ "192.168.1.128:5000"]
}

**5、重启docker服务**

```
systemctl restart docker
```

**6、为镜像打标签**

```
docker tag hello-world:latest  192.168.1.128:5000/hello-world:v1
```

格式说明：Usage: docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]

**7、上传到镜像服务器**

```
docker push 192.168.1.128:5000/hello-world:v1
```

**8、测试下载镜像**

*8.1 先删除主机上的镜像*

```
docker rmi -f $(docker images -aq)
```

*8.2 从registry服务器上下载hello-world镜像*

```
docker pull 192.168.1.128:5000/hello-world:v1
```



 **other**

```
docker commit :从容器创建一个新的镜像。
# docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
-a :提交的镜像作者；
-c :使用Dockerfile指令来创建镜像；
-m :提交时的说明文字；
-p :在commit时，将容器暂停。
```

