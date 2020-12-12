# Eureka Server添加安全认证

## 1、配置注册中心的Maven的pom文件

```xml
<!--设置登录密码需要用到Spring Security-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## 2、配置注册中心的application.yml

```yaml
server:
  port: 8761

#设置eurekaServer的登录密码
spring:
  security:
    user:
      name: admin  # 用户名
      password: admin   # 用户密码

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false #不向eurekaServer注册   因为自己就是server
    fetchRegistry: false      #不获取注册中心的服务     因为自己就是server
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/   #服务注册中心的位置

```

## 3、Eureka注册中心关闭Spring Security的CSRF验证

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.http.SessionCreationPolicy;

/**
 * eurekaServer设置了登录密码,就必须关闭Spring Security的CSRF验证,否则客户端连接不上
 */
@Configuration
@EnableWebSecurity
public class WebSecurityConfigure extends WebSecurityConfigurerAdapter {

    /**
     * 高版本的丢弃了
     * security:
     *   basic:
     *    enabled: true
     * 配置，应该使用以下方式开启
     *
     * @param http
     * @throws Exception
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // Configure HttpSecurity as needed (e.g. enable http basic).
        http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.NEVER);
        //关闭CSRF
        http.csrf().disable();
        //注意：为了可以使用 http://${user}:${password}@${host}:${port}/eureka/ 这种方式登录,所以必须是httpBasic,
        // 如果是form方式,不能使用url格式登录
        super.configure(http);
    }
}
```

## 4、配置Client的application.yml

```yaml
server:
    port: 8762

spring:
    application:
        name: eureka-client   #配置在eurekaServer中注册的名字

eureka:
    instance:
        prefer-ip-address: true   #在eurekaServer中服务地址以ip显示
    client:
        serviceUrl:
            #defaultZone: http://localhost:8761/eureka/   #设置eurekaServer的uri
            defaultZone: http://admin:admin@localhost:8761/eureka/    #eurekaServer设置了登录验证
```
