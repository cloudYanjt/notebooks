# Eureka配置文件

```yaml
spring:
  application:
    # 应用名称，将会显示在Eureka界面的应用名称列
    name: config-service

server:
  # 应用端口，Eureka服务端默认为：8761
  port: 8761

eureka:
  # eureka.server前缀的配置项
  server:
    # 是否允许开启自我保护模式，缺省：true
    # 当Eureka服务器在短时间内丢失过多客户端时，自我保护模式可使服务端不再删除失去连接的客户端
    enable-self-preservation: false
    # Peer节点更新间隔，单位：毫秒 ,缺省：600000
    peer-eureka-nodes-update-interval-ms: 600000
    # Eureka服务器清理无效节点的时间间隔，单位：毫秒，缺省：60000，即60秒
    eviction-interval-timer-in-ms: 60000

  # eureka.instance前缀的配置项
  instance:
    # 服务名，默认取 spring.application.name 配置值，如果没有则为 unknown
    appname: config-service
    # 实例ID
    instance-id: eureka-client-instance
    # 应用实例主机名
    hostname: localhost
    # 客户端在注册时使用自己的IP而不是主机名，缺省：false
    prefer-ip-address: false
    # 应用实例IP
    ip-address: 127.0.0.1
    # 服务失效时间，失效的服务将被剔除。单位：秒，缺省：90
    lease-expiration-duration-in-seconds: 90
    # 服务续约（心跳）频率，单位：秒，缺省30
    lease-renewal-interval-in-seconds: 30
    # 状态页面的URL，相对路径，默认使用 HTTP 访问，如需使用 HTTPS则要使用绝对路径配置，缺省：/info
    status-page-url-path: /info
    # 健康检查页面的URL，相对路径，默认使用 HTTP 访问，如需使用 HTTPS则要使用绝对路径配置，缺省：/health
    health-check-url-path: /health

  # eureka.client前缀
  client:
    # Eureka服务器的地址，类型为HashMap，缺省的Key为 defaultZone；缺省的Value为 http://localhost:8761/eureka
    # 如果Eureka设置了安全验证，url应为http://userName:password@localhost:8761/eureka
    # 如果服务注册中心为高可用集群时，多个注册中心地址以逗号分隔。
    service-url.defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka
    # 是否向注册中心注册自己，缺省：true
    # 一般情况下，Eureka服务端是不需要再注册自己的
    register-with-eureka: true
    # 是否从Eureka获取注册信息，缺省：true
    # 一般情况下，Eureka服务端是不需要的
    fetch-registry: true
    # 客户端拉取服务注册信息间隔，单位：秒，缺省：30
    registry-fetch-interval-seconds: 30
    health-check:
      # 是否启用客户端健康检查
      enabled: true
    # 初始化实例信息到Eureka服务端的间隔时间，单位：秒，缺省：300
    eureka-service-url-poll-interval-seconds: 300
    # 连接Eureka服务器的超时时间，单位：秒，缺省：5
    eureka-server-connect-timeout-seconds: 5
    # 从Eureka服务器读取信息的超时时间，单位：秒，缺省：8
    eureka-server-read-timeout-seconds: 8
    # 获取实例时是否只保留状态为 UP 的实例，缺省：true
    filter-only-up-instances: true
    # Eureka服务端连接空闲时的关闭时间，单位：秒，缺省：30
    eureka-connection-idle-timeout-seconds: 30
    # 从Eureka客户端到所有Eureka服务端的连接总数，缺省：200
    eureka-server-total-connections: 200
    # 从Eureka客户端到每个Eureka服务主机的连接总数，缺省：50
    eureka-server-total-connections-per-host: 50
```
