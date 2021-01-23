## Draw.io

### 1、获取 draw.io 镜像

```shell
docker pull jgraph/drawio:latest
```
### 2、启动 draw.io 容器

```shell
docker run --detach \
  --hostname draw.example.com \
  --publish 8080:8080 --publish 8443:8443 \
  --name draw \
  --restart always \
  jgraph/drawio:latest
```

### 3、获取容器日志

```shell
docker logs -f draw
```

## asciiflow2

### 1、获取 asciiflow2 镜像

```shell
docker pull flexo3001/asciiflow2:latest
```

### 2、启动 asciiflow2 容器

```shell
docker run --detach \
  --hostname asciiflow2.example.com \
  --publish 80:80 \
  --name asciiflow2 \
  --restart always \
  flexo3001/asciiflow2:latest
```

### 3、获取容器日志

```shell
docker logs -f asciiflow2
```

## excalidraw

### 1、获取 excalidraw 镜像

```shell
docker pull excalidraw/excalidraw:latest
```

### 2、启动 excalidraw 容器

```shell
docker run --detach \
  --hostname excalidraw.example.com \
  --publish 80:80 \
  --name excalidraw \
  --restart always \
  excalidraw/excalidraw:latest
```

### 3、获取容器日志

```shell
docker logs -f excalidraw
```

## GitLab

### 1、获取 gitlab 镜像

```shell
docker pull gitlab/gitlab-ce:13.5.4-ce.0
```

### 2、创建目录及设置环境变量

```shell
mkdir -p /host/path/gitlab & \
export GITLAB_HOME=/host/path/gitlab
```

### 3、启动 gitlab 容器

```shell
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

```shell
docker logs -f gitlab
```

## Nexus

### 1、获取 nexus 镜像

```shell
docker pull sonatype/nexus3:3.28.1
```

### 2、创建目录，由于 sonatype/nexus3:3.28.1 内部使用的是 nexus:200 的用户，所以需要更改目录权限

```shell
mkdir -p /host/path/nexus-data & \
chown -R 200 /host/path/nexus-data
```

### 3、启动 nexus 容器

```shell
docker run --detach \
  --hostname nexus.example.com \
  --publish 8081:8081 \
  --name nexus \
  --restart always \
  --volume /host/path/nexus-data:/nexus-data \
  sonatype/nexus3:3.28.1
```

### 4、获取容器日志

```shell
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

```shell
docker pull sonarqube:7.9.5-community
```

### 2、启动 sonarqube 容器

```shell
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

```SHELL
docker logs -f sonarqube
```
默认用户和密码 `admin`,`admin`

### 4、启动遇到的问题

​	出现如下错误
​	max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

​	解决办法
​	切换到 root 用户，执行 sysctl -w vm.max_map_count=262144 临时修改，然后再 vi /etc/sysctl.conf 文件最后添加 vm.max_map_count=262144 使永久生效。

## wordpress

### 1、获取 wordpress 镜像

```shell
docker pull wordpress:php7.4
```

### 2、启动 wordpress 容器

```shell
docker run --detach \
  --hostname wordpress.example.com \
  --publish 8080:80 \
  --name wordpress \
  --restart always \
  --volume wordpress_html:/var/www/html \
  wordpress:php7.4
```

### 3、获取容器日志

```shell
docker logs -f wordpress
```

## registry

### 1、获取 registry 镜像

```shell
docker pull registry:2.7.1
```

### 2、启动 registry 容器

```shell
docker run --detach \
  --hostname registry.example.com \
  --publish 5000:5000 \
  --name registry \
  --restart always \
  --volume registry:/var/lib/registry \
  registry:2.7.1
```

### 3、获取容器日志

```shell
docker logs -f registry
```

## mysql

### 1、获取 mysql 镜像

```shell
docker pull mysql:5.7.32
```

### 2、启动 mysql 容器

```shell
docker run --detach \
  --hostname mysql.example.com \
  --publish 3306:3306 --publish 33060:33060 \
  --name mysql \
  --restart always \
  -e MYSQL_ROOT_PASSWORD=mysecretpassword \
  --volume mysql_data:/var/lib/mysql \
  mysql:5.7.32
```

### 3、获取容器日志

```shell
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

```shell
docker pull postgres:9.6.20
```

### 2、启动 postgresql 容器

```shell
docker run --detach \
  --hostname postgresql.example.com \
  --publish 5432:5432 \
  --name postgresql \
  --restart always \
  -e POSTGRES_PASSWORD=mysecretpassword \
  --volume postgresql_data:/var/lib/postgresql/data \
  postgres:9.6.20
```

### 3、获取容器日志

```shell
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

```shell
docker pull redis:5.0.10
```

### 2、启动 redis 容器

```shell
docker run --detach \
  --hostname redis.example.com \
  --publish 6379:6379 \
  --name redis \
  --restart always \
  --volume redis_data:/data \
  redis:5.0.10 \
  redis-server --appendonly yes
```

### 3、获取容器日志

```shell
docker logs -f redis
```

### 4、打开 redis 客户端

```shell
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

## mongodb

### 1、获取 mongodb 镜像

```shell
docker pull mongo:4.4.2
```

### 2、启动 mongodb 容器

```shell
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

```shell
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

```shell
docker pull rabbitmq:3.8.9
```

### 2、启动 rabbitmq 容器

```shell
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

```shell
docker logs -f rabbitmq
```

### 4、带管理插件的镜像 rabbitmq-management

```shell
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

```shell
docker pull nginx:1.19.5
```

### 2、启动 nginx 容器

使用 `--volume /host/path/nginx.conf:/etc/nginx/nginx.conf:ro` 参数把指定配置文件映射到容器。

```shell
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

```shell
docker logs -f nginx
```

## zookeeper

### 1、获取 zookeeper 镜像

```shell
docker pull zookeeper:3.6.2
```

### 2、启动 zookeeper 容器

```shell
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

```shell
docker logs -f zookeeper
```

## jumpserver

### 1、获取 jumpserver 镜像

```shell
docker pull jumpserver/jms_all:v2.5.3
```

### 2、设置环境变量

```shell
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

```shell
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

```shell
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

```shell
docker logs -f jms_all
```

## portainer

### 1、获取 portainer 镜像

```shell
docker pull portainer/portainer-ce:2.0.0
```

### 2、启动 portainer 容器

```shell
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

```shell
docker logs -f portainer
```

## elasticsearch

### 1、获取 elasticsearch 镜像

```shell
docker pull elasticsearch:7.6.2
```

### 2、创建用户定义的网络

（可用于连接到同一网络的其他服务（例如 Kibana））

```shell
docker network create elasticnetwork
```

### 3、启动 elasticsearch 容器

可以使用 `--volume /host/path/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml` 指定映射配置文件。

```shell
docker run --detach \
  --hostname elasticsearch.example.org \
  --net elasticnetwork \
  --publish 9200:9200 --publish 9300:9300 \
  --name elasticsearch \
  --restart always \
  -e "discovery.type=single-node" \
  --volume elasticsearch_data:/usr/share/elasticsearch/data \
  elasticsearch:7.6.2
```

### 4、获取容器日志

```shell
docker logs -f elasticsearch
```

### 生产环境部署使用 docker-compose 参考：

```yaml
version: '2.2'
services:
  es01:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.2
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
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.2
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
    image: docker.elastic.co/elasticsearch/elasticsearch:7.5.2
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

```shell
docker pull kibana:7.6.2
```

### 2、创建用户定义的网络

（可用于连接到同一网络的其他服务（例如 Elasticsearch））

```shell
docker network create elasticnetwork
```

### 3、启动 kibana 容器

Kibana 可以快速启动并连接到本地 Elasticsearch 容器
(`--link YOUR_ELASTICSEARCH_CONTAINER_NAME_OR_ID:elasticsearch`)
或者指定环境变量 `-e "elasticsearch.hosts=http://elasticsearch_url:9200"`
可以使用 `--volume /host/path/kibana.yml:/usr/share/kibana/config/kibana.yml` 指定映射配置文件

```shell
docker run --detach \
  --hostname kibana.example.org \
  --net elasticnetwork \
  --publish 5601:5601 \
  --name kibana \
  --restart always \
  --link elasticsearch:elasticsearch \
  kibana:7.6.2
```

### 4、获取容器日志

```shell
docker logs -f kibana
```

### 生产环境部署使用 docker-compose 参考：

```yaml
version: '2'
services:
  kibana:
    image: docker.elastic.co/kibana/kibana:7.10.0
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

```shell
docker pull logstash:7.6.2
```

### 2、创建用户定义的网络

（可用于连接到同一网络的其他服务（例如 Elasticsearch））

```shell
docker network create elasticnetwork
```

### 3、启动 logstash 容器

Logstash 可以快速启动并连接到本地 Elasticsearch 容器
(`--link YOUR_ELASTICSEARCH_CONTAINER_NAME_OR_ID:elasticsearch`)；
或者指定环境变量 `-e "monitoring.elasticsearch.hosts=http://elasticsearch_url:9200"`
可以使用 `--volume /host/path/logstash.yml:/usr/share/logstash/config/logstash.yml` 指定映射配置文件。
指定采集相关的配置文件 `--volume /host/path/pipeline/logstash.conf:/usr/share/logstash/pipeline/logstash.conf`

```shell
docker run --detach \
  --hostname logstash.example.org \
  --net elasticnetwork \
  --publish 5044:5044 --publish 9600:9600 \
  --name logstash \
  --restart always \
  --link elasticsearch:elasticsearch \
  --volume /host/path/pipeline/logstash.conf:/usr/share/logstash/pipeline/logstash.conf \
  logstash:7.6.2
```

### 4、获取容器日志

```shell
docker logs -f logstash
```

使用 Docker 映像时，以下设置具有不同的默认值：

| `http.host`                      | `0.0.0.0`                   |
| -------------------------------- | --------------------------- |
| `monitoring.elasticsearch.hosts` | `http://elasticsearch:9200` |

## filebeat

### 1、获取 filebeat镜像

```
docker pull elastic/filebeat:7.6.2
```
