# 使用 Filebeat 采集 nginx 日志

使用 filebeat 采集 nginx 服务器日志，并通过 logstash 输出到 elasticsearch 

## 搭建 nginx 服务器

```
docker run --detach \
  --hostname nginx.virtual.com \
  --net devops-network \
  --ip 172.19.0.4 --publish 80:80 \
  --name nginx \
  --restart always \
  --volume nginx_logs:/var/log/nginx/ \
  --volume nginx_html:/usr/share/nginx/html \
  --volume nginx_conf:/etc/nginx \
  nginx:1.19.8
```

这里启动时把 nginx log 给挂载出来（--volume nginx_logs:/var/log/nginx/），后面filebeat 启动时挂载 nginx log 到容器中就可以采集到了。

nginx 配置文件挂载到了 nginx_conf 卷，可以使用 `docker volume inspect nginx_conf` 命令查看 nginx_conf 的所在位置。

## elasticsearch 集群搭建

先创建一个 docker 网络 `docker network create --subnet=172.19.0.0/16 elastic-network`

elasticsearch.yml

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
discovery.seed_hosts:                            #集群节点地址
  - elasticsearch2
  - elasticsearch3
cluster.initial_master_nodes:                    #初始化集群列表
  - node-1
  - node-2
  - node-3
gateway.recover_after_data_nodes: 2              #只要N个节点已加入群集，就可恢复。
gateway.recover_after_time: 5m                   #如果未实现预期节点数，则恢复过程将等待配置的时间量，然后再尝试恢复
gateway.expected_data_nodes: 3                   #集群中预期的节点数。
http.cors.enabled: true                          #允许跨域访问
http.cors.allow-origin: "*"
```

elasticsearch2.yml

```
cluster.name: es-cluster                         #指定集群名称，同一集群必须相同
node.name: node-2                                #指定节点的名称，同一集群的节点名称不能相同
node.master: true                                #是否允许成为主节点
node.data: true                                  #是否用做数据节点
path.data: /usr/share/elasticsearch/data2        #数据存放路径
path.logs: /usr/share/elasticsearch/logs2        #日志路径
network.host: 172.19.0.12                        #设置绑定ip
transport.tcp.port: 9300                         #ES集群之间通讯端口号
transport.tcp.compress: true
http.port: 9200                                  #暴露ES RESTful接口端口号
http.max_content_length: 100mb
bootstrap.memory_lock: false                     #启动后锁定物理内存，避免es使用swap交换分区。
discovery.seed_hosts:                            #集群节点地址
  - elasticsearch
  - elasticsearch3
cluster.initial_master_nodes:                    #初始化集群列表
  - node-1
  - node-2
  - node-3
gateway.recover_after_data_nodes: 2              #只要N个节点已加入群集，就可恢复。
gateway.recover_after_time: 5m                   #如果未实现预期节点数，则恢复过程将等待配置的时间量，然后再尝试恢复
gateway.expected_data_nodes: 3                   #集群中预期的节点数。
http.cors.enabled: true                          #允许跨域访问
http.cors.allow-origin: "*"
```

elasticsearch3.yml

```
cluster.name: es-cluster                         #指定集群名称，同一集群必须相同
node.name: node-3                                #指定节点的名称，同一集群的节点名称不能相同
node.master: true                                #是否允许成为主节点
node.data: true                                  #是否用做数据节点
path.data: /usr/share/elasticsearch/data3        #数据存放路径
path.logs: /usr/share/elasticsearch/logs3        #日志路径
network.host: 172.19.0.13                        #设置绑定ip
transport.tcp.port: 9300                         #ES集群之间通讯端口号
transport.tcp.compress: true
http.port: 9200                                  #暴露ES RESTful接口端口号
http.max_content_length: 100mb
bootstrap.memory_lock: false                     #启动后锁定物理内存，避免es使用swap交换分区。
discovery.seed_hosts:                            #集群节点地址
  - elasticsearch
  - elasticsearch2
cluster.initial_master_nodes:                    #初始化集群列表
  - node-1
  - node-2
  - node-3
gateway.recover_after_data_nodes: 2              #只要N个节点已加入群集，就可恢复。
gateway.recover_after_time: 5m                   #如果未实现预期节点数，则恢复过程将等待配置的时间量，然后再尝试恢复
gateway.expected_data_nodes: 3                   #集群中预期的节点数。
http.cors.enabled: true                          #允许跨域访问
http.cors.allow-origin: "*"
```

使用`sudo chmod g+rwx -R <dir>`命令赋予宿主机上用于挂载到容器的文件夹读写权限，否则容器启动时会抛出文件访问权限不足的错误。

基于上面的 elasticsearch ymal 配置启动三个 ES 容器：

```
docker run --detach \
  --hostname elasticsearch.virtual.org \
  --net elastic-network --ip 172.19.0.11 \
  --publish 9200:9200 \
  --name elasticsearch \
  --restart always \
  --volume="$(pwd)/elastic/elasticsearch/data:/usr/share/elasticsearch/data" \
  --volume="$(pwd)/elastic/elasticsearch/logs:/usr/share/elasticsearch/logs" \
  --volume="$(pwd)/elastic/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro" \
  docker.elastic.co/elasticsearch/elasticsearch:7.11.2

docker run --detach \
  --hostname elasticsearch2.virtual.org \
  --net elastic-network \
  --ip 172.19.0.12 \
  --publish 9201:9200 \
  --name elasticsearch2 \
  --restart always \
  --volume="$(pwd)/elastic/elasticsearch/data2:/usr/share/elasticsearch/data" \
  --volume="$(pwd)/elastic/elasticsearch/logs2:/usr/share/elasticsearch/logs" \
  --volume="$(pwd)/elastic/elasticsearch/elasticsearch2.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro" \
  docker.elastic.co/elasticsearch/elasticsearch:7.11.2


docker run --detach \
  --hostname elasticsearch3.virtual.org \
  --net elastic-network \
  --ip 172.19.0.13 \
  --publish 9202:9200 \
  --name elasticsearch3 \
  --restart always \
  --volume="$(pwd)/elastic/elasticsearch/data3:/usr/share/elasticsearch/data" \
  --volume="$(pwd)/elastic/elasticsearch/logs3:/usr/share/elasticsearch/logs" \
  --volume="$(pwd)/elastic/elasticsearch/elasticsearch3.yml:/usr/share/elasticsearch/config/elasticsearch.yml:ro" \
  docker.elastic.co/elasticsearch/elasticsearch:7.11.2
```

启动时遇到 the `vm.max_map_count` kernel setting must be set to at least `262144` for production use. 错误

解决方案：

1、临时修改系统配置，执行命令：
`sysctl -w vm.max_map_count=262144`

在 /etc/sysctl.conf 文件最后添加如下配置可使永久生效
`vm.max_map_count=262144`

## kibana 搭建

kibana.yml

```
server.name: kibana
server.host: 172.19.0.3
server.port: 5601
elasticsearch.hosts:
  - http://elasticsearch:9200
  - http://elasticsearch2:9200
  - http://elasticsearch3:9200
i18n.locale: zh-CN
```

基于上面的 kibana ymal 配置启动容器

```
docker run --detach \
  --hostname kibana.virtual.org \
  --net elastic-network --ip 172.19.0.3 \
  --publish 5601:5601 \
  --name kibana \
  --restart always \
  --volume="$(pwd)/elastic/kibana/kibana.yml:/usr/share/kibana/config/kibana.yml:ro" \
  docker.elastic.co/kibana/kibana:7.11.2
```

## logstash 搭建

logstash.conf

```
input {
  beats {
    host => "0.0.0.0"
    port => 5044
  }

  tcp {
    mode => "server"
    host => "0.0.0.0"
    port => 5047
  }
}

filter{
  if [type] == "nginx_access" {
    grok {
      match => {
        "message" => '%{IPORHOST:remote_addr} - %{USERNAME:remote_user} \[%{HTTPDATE:time_local}\] "%{WORD:method} %{URIPATHPARAM:path} HTTP/%{NUMBER:http_version}" %{INT:status} %{INT:body_bytes_sent} "%{URI:http_referer}" %{QS:http_user_agent} "%{DATA:http_x_forwarded_for}"'
      }
    }

    date {
      match => ["time_local", "dd/MMM/YYYY:HH:mm:ss Z"]
      target => "@timestamp"
      remove_field => "time_local"
    }

    useragent {
      source => "http_user_agent"
      target => "useragent"
      remove_field => "http_user_agent"
    }
  }


  if [type] == "nginx_error" {
    grok {
      match => {
        "message" => "(?<time_local>\d{4}/\d{2}/\d{2}\s{1,}\d{2}:\d{2}:\d{2}) \[%{LOGLEVEL:level}\] %{INT:process_id}#%{INT:thread_id}: \*(%{INT:connection_id})? %{DATA:error_message}(?:,\s{1,}client:\s{1,}%{IPORHOST:client})(?:,\s{1,}server:\s{1,}%{IPORHOST:server})(?:, request: %{QS:request})?(?:, upstream: %{QS:upstream})?(?:, host: %{QS:host})?(?:, referrer: \"%{URI:referrer})?"
      }
    }

    date {
      match => ["time_local", "YYYY/MM/dd HH:mm:ss"]
      target => "@timestamp"
      remove_field => "time_local"
    }
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200", "http://elasticsearch2:9200", "http://elasticsearch3:9200"]
    index => "%{type}_log"
  }
}
```

基于上面的 logstash.conf 配置启动容器

```
docker run --detach \
  --hostname logstash.virtual.org \
  --net elastic-network --ip 172.19.0.2 \
  --publish 5047:5047 --publish 5044:5044 \
  --name logstash \
  --restart always \
  --volume="$(pwd)/elastic/logstash/logstash.conf:/usr/share/logstash/pipeline/logstash.conf:ro" \
  docker.elastic.co/logstash/logstash:7.11.2
```

## filebeat 搭建

filebeat.yml

```
filebeat.inputs:
  - type: log
    tail_files: true
    scan_frequency: 5s
    backoff: 1s
    max_backoff: 10s
    paths:
      - /nginx_logs/access.log
    fields:
      type: nginx_access
    fields_under_root: true

  - type: log
    tail_files: true
    scan_frequency: 5s
    backoff: 1s
    max_backoff: 10s
    paths:
        - /nginx_logs/error.log
    fields:
      type: nginx_error
    fields_under_root: true

output.logstash:
  hosts: ["172.19.0.2:5044"]
```

基于上面的 filebeat.yml 配置启动容器

```
docker run --detach \
  --hostname filebeat.virtual.org \
  --name filebeat \
  --restart always \
  --volume="$(pwd)/filebeat/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro" \
  --volume="nginx_logs:/logs/nginx" \
  docker.elastic.co/beats/filebeat:7.11.2
```

