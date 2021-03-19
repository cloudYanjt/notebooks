## Draw.io

### 1、获取 draw.io 镜像

```
docker pull jgraph/drawio:latest
```
### 2、启动 draw.io 容器

```
docker run --detach \
  --hostname draw.example.com \
  --publish 8080:8080 --publish 8443:8443 \
  --name draw \
  --restart always \
  jgraph/drawio:latest
```

### 3、获取容器日志

```
docker logs -f draw
```

## asciiflow2

### 1、获取 asciiflow2 镜像

```
docker pull flexo3001/asciiflow2:latest
```

### 2、启动 asciiflow2 容器

```
docker run --detach \
  --hostname asciiflow2.example.com \
  --publish 80:80 \
  --name asciiflow2 \
  --restart always \
  flexo3001/asciiflow2:latest
```

### 3、获取容器日志

```
docker logs -f asciiflow2
```

## excalidraw

### 1、获取 excalidraw 镜像

```
docker pull excalidraw/excalidraw:latest
```

### 2、启动 excalidraw 容器

```
docker run --detach \
  --hostname excalidraw.example.com \
  --publish 80:80 \
  --name excalidraw \
  --restart always \
  excalidraw/excalidraw:latest
```

### 3、获取容器日志

```
docker logs -f excalidraw
```

## GitLab

### 1、获取 gitlab 镜像

```
docker pull gitlab/gitlab-ce:13.5.4-ce.0
```

### 2、创建目录及设置环境变量

```
mkdir -p /host/path/gitlab & \
export GITLAB_HOME=/host/path/gitlab
```

### 3、启动 gitlab 容器

```
docker run --detach \
  --hostname gitlab.example.com \
  --publish 443:443 --publish 80:80 --publish 22:22 \
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  gitlab/gitlab-ce:13.5.4-ce.0
```

### 4、获取容器日志

```
docker logs -f gitlab
```

## Nexus

### 1、获取 nexus 镜像

```
docker pull sonatype/nexus3:3.28.1
```

### 2、创建目录，由于 sonatype/nexus3:3.28.1 内部使用的是 nexus:200 的用户，所以需要更改目录权限

```
mkdir -p /host/path/nexus-data & \
chown -R 200 /host/path/nexus-data
```

### 3、启动 nexus 容器

```
docker run --detach \
  --hostname nexus.example.com \
  --publish 8081:8081 \
  --name nexus \
  --restart always \
  --volume /host/path/nexus-data:/nexus-data \
  sonatype/nexus3:3.28.1
```

### 4、获取容器日志

```
docker logs -f nexus
```

### 5、nexus 默认用户名和密码

```
userName:admin
password:[nexus3 默认密码不再是 admin123了，需要登入容器查看密码：
sudo docker exec -it nexus /bin/bash
bash-4.4$ cat /opt/sonatype/sonatype-work/nexus3/admin.password]
```

## sonarqube

### 1、获取 sonarqube 镜像

```
docker pull sonarqube:7.9.5-community
```

### 2、启动 sonarqube 容器

```
docker run --detach \
  --hostname sonarqube.example.com \
  --publish 9000:9000 \
  --name sonarqube \
  --restart always \
  --volume sonarqube_conf:/opt/sonarqube/conf \
  --volume sonarqube_data:/opt/sonarqube/data \
  --volume sonarqube_logs:/opt/sonarqube/logs \
  --volume sonarqube_extensions:/opt/sonarqube/extensions \
  sonarqube:7.9.5-community
```

数据库未指定为空默认使用H2，使用如下参数可以指定为自定义的数据库
eg:
  -e sonar.jdbc.username=sonarqube \
  -e sonar.jdbc.password=mypassword \
  -e sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube \

### 3、获取容器日志

```
docker logs -f sonarqube
```
默认用户和密码 `admin`,`admin`

### 4、启动遇到的问题

   出现如下错误
​   max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

   解决办法
​   切换到 root 用户，执行 sysctl -w vm.max_map_count=262144 临时修改，然后再 vi /etc/sysctl.conf 文件最后添加 vm.max_map_count=262144 使永久生效。

## wordpress

### 1、获取 wordpress 镜像

```
docker pull wordpress:php7.4
```

### 2、启动 wordpress 容器

```
docker run --detach \
  --hostname wordpress.example.com \
  --publish 8080:80 \
  --name wordpress \
  --restart always \
  --volume wordpress_html:/var/www/html \
  wordpress:php7.4
```

### 3、获取容器日志

```
docker logs -f wordpress
```

## registry

### 1、获取 registry 镜像

```
docker pull registry:2.7.1
```

### 2、启动 registry 容器

```
docker run --detach \
  --hostname registry.example.com \
  --publish 5000:5000 \
  --name registry \
  --restart always \
  --volume registry:/var/lib/registry \
  registry:2.7.1
```

### 3、获取容器日志

```
docker logs -f registry
```

## mariadb(配置基本和mysql一样)

### 1、获取 mariadb 镜像

```
docker pull mariadb:10.5.9
```

### 2、启动 mariadb 容器

```
docker run --detach \
  --hostname mariadb.example.com \
  --publish 3306:3306 \
  --name mariadb \
  --restart always \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  --volume mariadb_data:/var/lib/mysql \
  mariadb:10.5.9
```

### 3、获取容器日志

```
docker logs -f mariadb
```

### 4、环境变量说明

```
MYSQL_ROOT_PASSWORD：必填，指定将为 MariaDB 超级用户帐户设置密码。
MYSQL_DATABASE：可选，允许您指定要在映像启动时创建的数据库的名称。
MYSQL_USER,MYSQL_PASSWORD：可选，用于创建新用户和设置该用户的密码。
MYSQL_ALLOW_EMPTY_PASSWORD：可选，设置为非空值（如 yes，以允许容器 root 用户的空白密码启动）。
MYSQL_RANDOM_ROOT_PASSWORD：可选，设置为非空值（如 yes，将为root用户生成随机初始密码，生成的root密码将打印到标准输出）。
MYSQL_ONETIME_PASSWORD：可选，将 root 设置为初始化完成后过期的用户，强制在首次登录时更改密码，任何非空值都将激活此设置。
MYSQL_INITDB_SKIP_TZINFO：默认情况下，入口点脚本会自动加载函数所需的时区数据。如果不需要，则设置任何非空值将禁用时区加载。
```

### 5、Other

```
1、自定义配置文件，“/my/custom/config-file.cnf”
--volume /my/custom:/etc/mysql/conf.d

2、启动命令 <mariadb:tag> 后面添加如下参数，即更改所有表的默认编码和排序规则
--character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

3、如果您想查看可用选项的完整列表，只需运行：
docker run -it --rm mariadb:10.5.9 --verbose --help
```

## mysql

### 1、获取 mysql 镜像

```
docker pull mysql:5.7.32
```

### 2、启动 mysql 容器

```
docker run --detach \
  --hostname mysql.example.com \
  --publish 3306:3306 --publish 33060:33060 \
  --name mysql \
  --restart always \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  --volume mysql_data:/var/lib/mysql \
  mysql:5.7.32
```

### 3、获取容器日志

```
docker logs -f mysql
```

### 4、环境变量说明

```
MYSQL_ROOT_PASSWORD：必填，指定将为 MySQL 超级用户帐户设置密码。
MYSQL_DATABASE：可选，允许您指定要在映像启动时创建的数据库的名称。
MYSQL_USER,MYSQL_PASSWORD：可选，用于创建新用户和设置该用户的密码。
MYSQL_ALLOW_EMPTY_PASSWORD：可选，设置为非空值（如 yes，以允许容器 root 用户的空白密码启动）。
MYSQL_RANDOM_ROOT_PASSWORD：可选，设置为非空值（如 yes，将为root用户生成随机初始密码，生成的root密码将打印到标准输出）。
MYSQL_ONETIME_PASSWORD：可选，将 root 设置为初始化完成后过期的用户，强制在首次登录时更改密码，任何非空值都将激活此设置。
MYSQL_INITDB_SKIP_TZINFO：默认情况下，入口点脚本会自动加载函数所需的时区数据。如果不需要，则设置任何非空值将禁用时区加载。
```

### 5、Other

```
1、自定义配置文件，“/my/custom/config-file.cnf”
--volume /my/custom:/etc/mysql/conf.d

2、启动命令 <mysql:tag> 后面添加如下参数，即更改所有表的默认编码和排序规则
--character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

3、如果您想查看可用选项的完整列表，只需运行：
docker run -it --rm mysql:5.7.32 --verbose --help
```

## postgres

### 1、获取 postgresql 镜像

```
docker pull postgres:9.6.20
```

### 2、启动 postgresql 容器

```
docker run --detach \
  --hostname postgresql.example.com \
  --publish 5432:5432 \
  --name postgresql \
  --restart always \
  -e POSTGRES_PASSWORD=my-secret-pw \
  --volume postgresql_data:/var/lib/postgresql/data \
  postgres:9.6.20
```

### 3、获取容器日志

```
docker logs -f postgresql
```

### 4、环境变量说明

```
POSTGRES_PASSWORD：必填，此环境变量为 PostgreSQL 设置超级用户密码，默认超级用户由环境变量 POSTGRES_USER 定义。
POSTGRES_USER：可选，此变量将创建具有超级用户功能的指定用户和具有相同名称的数据库。如果未指定，则将使用默认用户 postgres。
POSTGRES_DB：可选，用于为首次启动映像时创建的默认数据库定义其他名称。如果未指定，则将使用 POSTGRES_USER 的值
```

## redis

### 1、获取 redis 镜像

```
docker pull redis:latest
```

### 2、启动 redis 容器

```
docker run --detach \
  --hostname redis.example.com \
  --publish 6379:6379 \
  --name redis \
  --restart always \
  --volume redis_data:/data \
  redis:latest \
  redis-server --appendonly yes
```

### 3、获取容器日志

```
docker logs -f redis
```

### 4、打开 redis 客户端

```
docker exec -it redis redis-cli
```

### 5、参数说明

```
1、启用持久化
redis-server --appendonly yes

2、使用自定义的配置文件：
    a、添加配置文件挂载
    --volume /myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf
    b、启动命令 <resid:tag> 后面添加如下参数
    redis-server /usr/local/etc/redis/redis.conf
```

## redisInsight

### 1、获取 redisInsight 镜像

```
docker pull edisinsight:latest
```

### 2、启动 redisInsight 容器

```
docker run --detach \
  --hostname edisinsight.example.com \
  --publish 8001:8001 \
  --name edisinsight \
  --restart always \
  --volume redisinsight:/db \
  edisinsight:latest
```

### 3、获取容器日志

```
docker logs -f edisinsight
```

## mongodb

### 1、获取 mongodb 镜像

```
docker pull mongo:4.4.2
```

### 2、启动 mongodb 容器

```
docker run --detach \
  --hostname mongo.example.com \
  --publish 27017:27017 \
  --name mongo \
  --restart always \
  --volume mongo_data:/data/db \
  --volume mongo_conf:/data/configdb \
  mongo:4.4.2
```

### 3、获取容器日志

```
docker logs -f mongo
```

### 4、设置用户名密码，docker run 添加参数

```
  -e MONGO_INITDB_ROOT_USERNAME=mongoadmin
  -e MONGO_INITDB_ROOT_PASSWORD=secret
```

### 5、使用自定义配置文件 “/my/custom/mongod.conf”

```
1、添加配置文件挂载
--volume /my/custom:/etc/mongo
2、启动命令 <mongo:tag> 后面添加如下参数
--config /etc/mongo/mongod.conf
```

## rabbitmq

### 1、获取 rabbitmq 镜像

```
docker pull rabbitmq:3.8.9
```

### 2、启动 rabbitmq 容器

```
docker run --detach \
  --hostname rabbitmq.example.com \
  --publish 4369:4369 --publish 5671:5671 \
  --publish 5672:5672 --publish 15691:15691 \
  --publish 15692:15692 --publish 25672:25672 \
  --name rabbitmq \
  --restart always \
  --volume rabbitmq_data:/var/lib/rabbitmq \
  rabbitmq:3.8.9
```

### 3、获取容器日志

```
docker logs -f rabbitmq
```

### 4、带管理插件的镜像 rabbitmq-management

```
docker pull rabbitmq:3.8.9-management

docker run --detach \
  --hostname rabbitmq.example.com \
  --publish 4369:4369 --publish 5671:5671 \
  --publish 5672:5672 --publish 15691:15691 \
  --publish 15692:15692 --publish 25672:25672 \
  --publish 15671:15671 --publish 15672:15672 \
  --name rabbitmq \
  --restart always \
  -e RABBITMQ_DEFAULT_USER=user \
  -e RABBITMQ_DEFAULT_PASS=password \
  --volume rabbitmq_data:/var/lib/rabbitmq \
  rabbitmq:3.8.9-management

## 使用环境变量 RABBITMQ_DEFAULT_USER,RABBITMQ_DEFAULT_PASS指定用户名密码，未指定默认为 guest guest
```

## nginx

### 1、获取 nginx 镜像

```
docker pull nginx:1.19.5
```

### 2、启动 nginx 容器

使用 `--volume /host/path/nginx.conf:/etc/nginx/nginx.conf:ro` 参数把指定配置文件映射到容器。

```
docker run --detach \
  --hostname nginx.example.com \
  --publish 80:80 \
  --name nginx \
  --restart always \
  --volume nginx_content:/usr/share/nginx/html:ro \
  --volume nginx_conf:/etc/nginx \
  nginx:1.19.5
```

### 3、获取容器日志

```
docker logs -f nginx
```

## httpd

### 1、获取 httpd 镜像

```
docker pull httpd:2.4
```

### 2、启动 httpd 容器

使用 `--volume ./my-httpd.conf:/usr/local/apache2/conf/httpd.conf` 参数把指定配置文件映射到容器。

```
docker run --detach \
  --hostname httpd.example.com \
  --publish 80:80 \
  --name httpd \
  --restart always \
  --volume "$PWD":/usr/local/apache2/htdocs/ \
  httpd:2.4
```

### 3、获取容器日志

```
docker logs -f httpd
```

## zookeeper

### 1、获取 zookeeper 镜像

```
docker pull zookeeper:3.6.2
```

### 2、启动 zookeeper 容器

```
docker run --detach \
  --hostname zookeeper.example.com \
  --publish 2181:2181 --publish 2888:2888 \
  --publish 3888:3888 --publish 8080:8080 \
  --name zookeeper \
  --restart always \
  --volume zoo_data:/data \
  --volume zoo_datalog:/datalog \
  --volume zoo_logs:/logs \
  zookeeper:3.6.2
```

### 3、获取容器日志

```
docker logs -f zookeeper
```

## Kafka

### 1、获取 Kafka 镜像

```
docker pull wurstmeister/kafka:2.12-2.5.0
```

### 2、Kafka 需要 Zookeeper 管理，所以需要先启动 Zookeeper 然后启动 Kafka

```
docker run --detach \
  --hostname kafka.example.com \
  -p 9092:9092 \
  --name kafka \
  --restart always \
  -e KAFKA_BROKER_ID=0 \
  -e KAFKA_ZOOKEEPER_CONNECT=192.168.111.130:2181/kafka \
  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.111.130:9092 \
  -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
  -v kafka:/kafka \
  -v /etc/localtime:/etc/localtime \
  wurstmeister/kafka:2.12-2.5.0
```

  -e KAFKA_BROKER_ID=0  在kafka集群中，每个kafka都有一个BROKER_ID来区分自己
  -e KAFKA_ZOOKEEPER_CONNECT=192.168.111.130:2181 配置zookeeper管理kafka的路径
  -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.111.130:9092  把kafka的地址端口注册给zookeeper
  -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 配置kafka的监听端口
  -v /etc/localtime:/etc/localtime 容器时间同步宿主机的时间

### 3、获取容器日志

```
docker logs -f kafka
```

## kafka-manager

### 1、获取 kafka-manager 镜像

```
docker pull hlebalbau/kafka-manager:3.0.0.5
```

### 2、启动 kafka-manager 容器

```
docker run --detach \
  --hostname kafkamanager.example.com \
  -p 9000:9000 \
  --name kafka-manager \
  --restart always \
  -e ZK_HOSTS="192.168.111.130:2181" \
  -v kafka-manager_conf:/kafka-manager/conf \
  -v /etc/localtime:/etc/localtime \
  hlebalbau/kafka-manager:3.0.0.5
```

如果要使用基本身份验证保护 Web UI，请添加以下 env 变量：‎

-e KAFKA_MANAGER_AUTH_ENABLED: "true" \
-e KAFKA_MANAGER_USERNAME: username \
-e KAFKA_MANAGER_PASSWORD: password \

### 3、获取容器日志

```
docker logs -f kafka-manager
```

## jumpserver

### 1、获取 jumpserver 镜像

```
docker pull jumpserver/jms_all:v2.5.3
```

### 2、设置环境变量

```
if [ ! "$SECRET_KEY" ]; then
  SECRET_KEY=`cat /dev/urandom | tr -dc A-Za-z0-9 | head -c 50`;
  echo "SECRET_KEY=$SECRET_KEY" >> ~/.bashrc;
  echo $SECRET_KEY;
else
  echo $SECRET_KEY;
fi
if [ ! "$BOOTSTRAP_TOKEN" ]; then
  BOOTSTRAP_TOKEN=`cat /dev/urandom | tr -dc A-Za-z0-9 | head -c 16`;
  echo "BOOTSTRAP_TOKEN=$BOOTSTRAP_TOKEN" >> ~/.bashrc;
  echo $BOOTSTRAP_TOKEN;
else
  echo $BOOTSTRAP_TOKEN;
fi
```

### 3、启动 jumpserver 容器

```
docker run --detach \
  --hostname jumpserver.example.org \
  --publish  80:80 --publish  2222:2222 \
  --name jms_all \
  --restart always \
  -e SECRET_KEY=$SECRET_KEY \
  -e BOOTSTRAP_TOKEN=$BOOTSTRAP_TOKEN \
  --volume /opt/jumpserver/data:/opt/jumpserver/data \
  --volume /opt/jumpserver/mysql:/var/lib/mysql \
  --volume /opt/jumpserver/config/guacamole/data:/config/guacamole/data \
  --volume /opt/jumpserver/koko/data:/opt/koko/data \
  --privileged=true \
  jumpserver/jms_all:v2.5.3
```

#### 或者使用外部数据库

```
docker run --name jms_all -d \
  -v /opt/jumpserver/data:/opt/jumpserver/data \
  -p 80:80 \
  -p 2222:2222 \
  -e SECRET_KEY=xxxxxx \
  -e BOOTSTRAP_TOKEN=xxx \
  -e DB_HOST=192.168.x.x \
  -e DB_PORT=3306 \
  -e DB_USER=root \
  -e DB_PASSWORD=xxx \
  -e DB_NAME=jumpserver \
  -e REDIS_HOST=192.168.x.x \
  -e REDIS_PORT=6379 \
  -e REDIS_PASSWORD=xxx \
  --privileged=true \
  jumpserver/jms_all:v2.5.3
```

### 4、获取容器日志

```
docker logs -f jms_all
```

## portainer

### 1、获取 portainer 镜像

```
docker pull portainer/portainer-ce:2.0.0
```

### 2、启动 portainer 容器

```
docker run --detach \
  --hostname portainer.example.com \
  --publish 9000:9000 --publish 8000:8000 \
  --name portainer \
  --restart always \
  --volume portainer_data:/data \
  --volume /var/run/docker.sock:/var/run/docker.sock \
  --privileged=true \
  portainer/portainer-ce:2.0.0
```

### 3、获取容器日志

```
docker logs -f portainer
```

## elasticsearch

### 1、获取 elasticsearch 镜像

```
docker pull docker.elastic.co/elasticsearch/elasticsearch:7.11.2
```

### 2、创建用户定义的网络

（可用于连接到同一网络的其他服务（例如 Kibana））

```
docker network create --subnet=172.19.0.0/16 elastic-network
```

### 3、启动 elasticsearch 容器

*遇到启动报访问权限错误可以使用 `sudo chmod g+rwx -R $PWD/data` 来授予宿主机对应的映射文件夹的访问权限*

可以使用 `--volume="$(pwd)/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro"` 指定映射配置文件。

#### 1) 仅单节点启动

```
docker run --detach \
  --hostname elasticsearch.example.org \
  --net elastic-network \
  --publish 9200:9200 --publish 9300:9300 \
  --name elasticsearch \
  --restart always \
  -e "discovery.type=single-node" \
  --volume="$(pwd)/data:/usr/share/elasticsearch/data" \
  docker.elastic.co/elasticsearch/elasticsearch:7.11.2
```

#### 2)集群启动 (参照如下命令启动多个)

```
docker run --detach \
  --hostname elasticsearch.virtual.org \
  --net elastic-network \
  --ip 172.19.0.11 \
  --publish 9200:9200 --publish 9300:9300 \
  --name elasticsearch \
  --restart always \
  --volume="$(pwd)/elastic/elasticsearch/data:/usr/share/elasticsearch/data" \
  --volume="$(pwd)/elastic/elasticsearch/logs:/usr/share/elasticsearch/logs" \
  --volume="$(pwd)/elastic/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro" \
  docker.elastic.co/elasticsearch/elasticsearch:7.11.2
```

elasticsearch.yml 参考：集群的其余节点 node.name 和 network.host 保持不一致

```
cluster.name: es-cluster                         #指定集群名称，同一集群必须相同
node.name: node-1                                #指定节点的名称，同一集群的节点名称不能相同
node.master: true                                #是否允许成为主节点
node.data: true                                  #是否用做数据节点
path.data: /usr/share/elasticsearch/data         #数据存放路径
path.logs: /usr/share/elasticsearch/logs         #日志路径
network.host: 172.19.0.11                        #设置绑定ip
transport.tcp.port: 9300                         #ES集群之间通讯端口号
transport.tcp.compress: true 
http.port: 9200                                  #暴露ES RESTful接口端口号
http.max_content_length: 100mb 
bootstrap.memory_lock: false                     #启动后锁定物理内存，避免es使用swap交换分区。
discovery.seed_hosts: ["172.19.0.11","172.19.0.12","172.19.0.13"] #集群节点地址
cluster.initial_master_nodes: ["172.19.0.11","172.19.0.12","172.19.0.13"] #初始化集群列表
gateway.recover_after_data_nodes: 2              #只要N个节点已加入群集，就可恢复。
gateway.recover_after_time: 5m                   #如果未实现预期节点数，则恢复过程将等待配置的时间量，然后再尝试恢复
gateway.expected_data_nodes: 3                   #集群中预期的节点数。
```

### 5、获取容器日志

```
docker logs -f elasticsearch
```

* 生产环境部署使用 docker-compose 参考：

```yaml
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.11.2
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.11.2
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data02:/usr/share/elasticsearch/data
    networks:
      - elastic
  es03:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.11.2
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic

volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

## kibana

### 1、获取 kibana 镜像

```
docker pull docker.elastic.co/kibana/kibana:7.11.2
```

### 2、创建用户定义的网络

（可用于连接到同一网络的其他服务（例如 Elasticsearch））

```
docker network create --subnet=172.19.0.0/16 elastic-network
```

### 3、启动 kibana 容器

*遇到启动报访问权限错误可以使用 `sudo chmod g+rwx -R $PWD` 来授予宿主机对应的映射文件夹的访问权限*

Kibana 可以快速启动并连接到本地 Elasticsearch 容器
(`--link YOUR_ELASTICSEARCH_CONTAINER_NAME_OR_ID:elasticsearch`)
或者指定环境变量 `-e "elasticsearch.hosts=http://elasticsearch_url:9200"`
可以使用 `--volume="$(pwd)/kibana.yml:/usr/share/kibana/config/kibana.yml:ro"` 指定映射配置文件

```
docker run --detach \
  --hostname kibana.example.org \
  --net elastic-network \
  --publish 5601:5601 \
  --name kibana \
  --restart always \
  --link elasticsearch:elasticsearch \
  docker.elastic.co/kibana/kibana:7.11.2
```

kibana.yml 配置文件示例：

```
server.name: kibana
server.host: 172.19.0.3
server.port: 5601
elasticsearch.hosts: [ "http://172.19.0.11:9200", "http://172.19.0.12:9200", "http://172.19.0.13:9200"]
i18n.locale: zh-CN
```

### 4、获取容器日志

```
docker logs -f kibana
```

### 生产环境部署使用 docker-compose 参考：

```yaml
version: '2'
services:
  kibana:
    image: docker.elastic.co/kibana/kibana:7.11.2
    environment:
      SERVER_NAME: kibana.example.org
      ELASTICSEARCH_HOSTS: http://elasticsearch.example.org:9200
```

使用 Docker 映像时，以下设置具有不同的默认值：

| `server.name`                                   | `kibana`                    |
| ----------------------------------------------- | --------------------------- |
| `server.host`                                   | `"0"`                       |
| `elasticsearch.hosts`                           | `http://elasticsearch:9200` |
| `monitoring.ui.container.elasticsearch.enabled` | `true`                      |

## logstash

### 1、获取 logstash 镜像

```
docker pull docker.elastic.co/logstash/logstash:7.11.2
```

### 2、创建用户定义的网络

（可用于连接到同一网络的其他服务（例如 Elasticsearch））

```
docker network create --subnet=172.19.0.0/16 elastic-network
```

### 3、启动 logstash 容器

*遇到启动报访问权限错误可以使用 `sudo chmod g+rwx -R $PWD` 来授予宿主机对应的映射文件夹的访问权限*

Logstash 可以快速启动并连接到本地 Elasticsearch 容器
(`--link YOUR_ELASTICSEARCH_CONTAINER_NAME_OR_ID:elasticsearch`)；
或者指定环境变量 `-e "monitoring.elasticsearch.hosts=http://elasticsearch_url:9200"`
可以使用 `--volume="$(pwd)/logstash.yml:/usr/share/logstash/config/logstash.yml:ro"` 指定映射配置文件。
指定采集相关的配置文件 `--volume="$(pwd)/logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro"`

```
docker run --detach \
  --hostname logstash.example.org \
  --net elastic-network \
  --publish 5044:5044 --publish 9600:9600 \
  --name logstash \
  --restart always \
  --link elasticsearch:elasticsearch \
  --volume="$(pwd)/logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro" \
  docker.elastic.co/logstash/logstash:7.11.2
```

logstash.conf 配置文件示例：

```
input {
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 5047
    codec => json_lines
    type => "info"
  }
  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 5048
    codec => json_lines
    type => "error"
  }
}

filter{}

output {
    elasticsearch {
      hosts => ["172.19.0.2:9200"]
      index => "logstash-%{type}-%{+yyyy.MM.dd}"
    }
}

```



### 4、获取容器日志

```
docker logs -f logstash
```

使用 Docker 映像时，以下设置具有不同的默认值：

| `http.host`                      | `0.0.0.0`                   |
| -------------------------------- | --------------------------- |
| `monitoring.elasticsearch.hosts` | `http://elasticsearch:9200` |

## filebeat

### 1、获取 filebeat 镜像

```
docker pull docker.elastic.co/beats/filebeat:7.11.2
```

### 2、创建用户定义的网络

（可用于连接到同一网络的其他服务（例如 Elasticsearch））

```
docker network create --subnet=172.19.0.0/16 elastic-network
```

### 3、启动 filebeat 容器

*遇到启动报访问权限错误可以使用 `sudo chmod g+rwx -R $PWD` 来授予宿主机对应的映射文件夹的访问权限*

```
docker run --detach \
  --hostname filebeat.example.org \
  --net elastic-network \
  --name filebeat \
  --restart always \
  --volume="$(pwd)/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro" \
  docker.elastic.co/beats/filebeat:7.11.2 filebeat -e -strict.perms=false \
  -E output.elasticsearch.hosts=["elasticsearch:9200"]
```

filebeat.yml 配置文件示例：例如采集系统日志（需要先把 /var/log/messages 映射到容器）
`--volume="/var/log/messages:/logs/messages:ro"`

```
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /logs/messages

output.elasticsearch:
  hosts: '172.19.0.2:9200'
  indices:
    - index: "elk-system-log-%{+yyyy.MM.dd}"
```

### 4、获取容器日志

```
docker logs -f filebeat
```

