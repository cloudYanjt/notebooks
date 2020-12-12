# docker常用命令

## 查看 docker 信息

`docker info`

## 查看容器信息

`docker inspect <容器Id>`

## 查看容器日志

`docker logs -f <容器Id>`

## docker volume

```shell
## 查看所有数据卷
docker volume ls
## 删除数据卷
docker volume rm
## 创建一个数据卷
docker volume create <volumeName>
## 查看指定数据卷详情信息
docker volume inspect <volumeName>
```

## docker stats

```shell
## 查看所有容器资源占用
docker stats -a
## 查看指定容器资源占用
docker stats <容器Id>
## 查看多个容器资源占用
docker stats <容器Id1>,<容器Id2>
```

## 修改容器启动参数

容器启动时忘记添加 `--restart=always` 参数，可以使用如下方式解决：

```
docker container update --restart=always <容器Id>或者<容器名>
```

* Docker 容器的重启策略：

        no，默认策略，在容器退出时不重启容器
        on-failure，在容器非正常退出时（退出状态非0），才会重启容器
        on-failure:3，在容器非正常退出时重启容器，最多重启3次
        always，在容器退出时总是重启容器
        unless-stopped，在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器
        2. Docker容器的退出状态码

* Docker run 的退出状态码：

        0，表示正常退出
        非0，表示异常退出（退出状态码采用chroot标准）
        125，Docker守护进程本身的错误
        126，容器启动后，要执行的默认命令无法调用
        127，容器启动后，要执行的默认命令不存在
        其他命令状态码，容器启动后正常执行命令，退出命令时该命令的返回状态码作为容器的退出状态码

* 通过 docker run 的 --restart 选项，可以设置容器的重启策略

## 清理镜像和容器

### 自动清理

`docker system prune`可对空间进行自动清理。
该命令所清理的对象如下：

    已停止的容器
    未被任何容器使用的卷
    未被任何容器所关联的网络
    所有悬空的镜像

对于上面提到的一些镜像或容器的状态，需要我们心里有点数：
已使用的镜像：指所有已被容器（包括stop的）关联的镜像，也就是docker ps -a所看到的所有容器对应的image。
未引用镜像：没有被分配或使用在容器中的镜像
悬空镜像（dangling image）：未配置任何Tag（也就是无法被引用）的镜像。通常是由于镜像编译过程中未指定-t参数配置Tag导致的。

`docker system prune`后可以加额外的参数，如：
`docker system prune -a `： 一并清除所有未被使用的镜像和悬空镜像。
`docker system prune -f `： 用以强制删除，不提示信息。

另外除了system级别的，还有针对容器或是镜像级别的删除命令：

`docker image prune`：删除悬空的镜像。

`docker container prune`：删除无用的容器。
      --默认情况下docker container prune命令会清理掉所有处于stopped状态的容器
      --如果不想那么残忍统统都删掉，也可以使用--filter标志来筛选出不希望被清理掉的容器。例子：清除掉所有停掉的容器，但24内创建的除外：
      --$ docker container prune --filter "until=24h"  

`docker volume prune`：删除无用的卷。
`docker network prune`：删除无用的网络

### 手动清理

```shell
## 删除所有悬空镜像，不删除未使用镜像：
docker rmi $(docker images -f "dangling=true" -q)


## 删除所有未使用镜像和悬空镜像
docker rmi $(docker images -q)


## 清理卷
## 如果卷占用空间过高，可以清除一些不使用的卷，包括一些未被任何容器调用的卷（-v 详细信息中若显示 LINKS = 0，则是未被调用）：
## 删除所有未被容器引用的卷：
docker volume rm $(docker volume ls -qf dangling=true)


## 容器清理
## 如果发现是容器占用过高的空间，可以手动删除一些：
## 删除所有已退出的容器：
docker rm -v $(docker ps -aq -f status=exited)

## 删除所有状态为dead的容器
docker rm -v $(docker ps -aq -f status=dead)
```
