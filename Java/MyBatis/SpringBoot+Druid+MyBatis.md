1、导入依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.22</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
</dependency>

<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>
```

2、配置application.yml文件

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    username: admin
    password: 123456
    url: jdbc:mysql://localhost:3306/product?useUnicode=true&characterEncoding=utf-8&useSSL=false
    type: com.alibaba.druid.pool.DruidDataSource

mybatis:
  mapper-locations: classpath:com/example/gdemo/dal/mapper/*Mapper.xml
  type-aliases-package: com.example.gdemo.dal.model
```

3、配置类上贴mapper接口扫描注解

```java
@MapperScan("com.example.gdemo.dal.mapper")
```

4、DataSource和SqlSessionFactory交由Spring管理

```java
@Autowired
private DataSource dataSource;

@Bean
@ConfigurationProperties(prefix = "spring.datasource")
public DataSource druidDataSource() {
    DruidDataSource dataSource = new DruidDataSource();
    return dataSource;
}

@Bean
public SqlSessionFactory sqlSessionFactoryBean() {
    try {
        //  注入DataSource，此处是我们使用的Druid
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dataSource);

        return bean.getObject();
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```

5、使用MyBatis代码生成插件

```xml
<!-- MyBatis代码生成插件 -->
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.4.0</version>
    <configuration>
        <!--配置文件的位置-->
        <configurationFile>src/main/resources/MybatisGenerator.xml</configurationFile>
        <verbose>true</verbose>
        <overwrite>true</overwrite>
    </configuration>
    <executions>
        <execution>
            <id>Generate MyBatis Artifacts</id>
            <goals>
                <goal>generate</goal>
            </goals>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-core</artifactId>
            <version>1.4.0</version>
        </dependency>
    </dependencies>
</plugin>
```

6、创建MybatisGenerator.xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
    PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
    "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <!--<classPathEntry location="/Program Files/IBM/SQLLIB/java/db2java.zip" />-->

    <context id="Mybatis3Context" targetRuntime="MyBatis3">
        
        <!-- 自动实现Serializable接口 -->
        <plugin type="org.mybatis.generator.plugins.SerializablePlugin"/>
        
        <!-- 去除自动生成的注释 -->
        <commentGenerator>
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        
        <!-- 生成的Java文件的编码 -->
        <property name="javaFileEncoding" value="UTF-8"/>
        
        <!--数据库连接信息-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/MyBatisCodeHelper?useLegacyDatetimeCode=false&amp;serverTimezone=UTC&amp;nullCatalogMeansCurrent=true"
                        userId="root"
                        password="">
        </jdbcConnection>

        <!-- java类型处理器用于处理DB中的类型到Java中的类型，默认使用JavaTypeResolverDefaultImpl；注意一点，默认会先尝试使用Integer，Long，Short等来对应DECIMAL和NUMERIC数据类型 -->
        <javaTypeResolver>
            <!-- true：使用BigDecimal对应DECIMAL和 NUMERIC数据类型
            false：默认,
                scale>0;length>18：使用BigDecimal;
                scale=0;length[10,18]：使用Long；
                scale=0;length[5,9]：使用Integer；
                scale=0;length<5：使用Short
            -->
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成实体类的位置以及包名 -->
        <javaModelGenerator targetPackage="model" targetProject="D:/SourceCode/IdeaProjects/gdemo/src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="true"/>
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!-- 生成映射文件的存放位置 -->
        <sqlMapGenerator targetPackage="mapperxml" targetProject="D:/SourceCode/IdeaProjects/gdemo/src/main/resources">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="true"/>
        </sqlMapGenerator>

        <!-- mapper接口生成的位置 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="mapper" targetProject="D:/SourceCode/IdeaProjects/gdemo/src/main/java">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!-- 配置表信息 -->
        <!-- schema即为数据库名 tableName为对应的数据库表 domainObjectName是要生成的实体类 enable*ByExample
            是否生成 example类 -->
        <table tableName="Customer" domainObjectName="Customer" enableCountByExample="false"
               enableDeleteByExample="false" enableUpdateByExample="false" enableSelectByExample="false">
        </table>
    </context>
</generatorConfiguration>
```

7、通过mybatis-generator插件执行代码生成，或用maven命令生成

```shell
mvn mybatis-generator:generate
```

