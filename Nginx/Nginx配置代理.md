# Nginx 配置代理

nginx.conf 部分配置，eg：

```
#禁止直接使用ip访问
server {
    listen 80 default_server;
    server_name _;
    return 403;
}

#例如：将 localhost 访问转发到自定义的gitlab
server {
    listen       80;
    listen  [::]:80;
    server_name  localhost;

    location / {
        proxy_pass   http://gitlab.example.com:8088;
    }
}

#将 nexus.example.com 域名访问进来的请求转发到阿里云 maven
server {
    listen       80;
    listen  [::]:80;
    server_name  nexus.example.com;

    location / {
        proxy_pass   http://maven.aliyun.com;
    }
}
```