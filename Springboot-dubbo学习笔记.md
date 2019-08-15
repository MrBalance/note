# Springboot-dubbo学习笔记

## 1. 模块架构

dubbo推荐采用三层架构api 、service、consumer的三个单独模块来编写

### 1.1. api层

api层仅是一个接口层记录对外提供的接口和model

![截图1](/img/spring-boot-dubbo/截图1.jpg)

### 1.2. Service层

#### 1.2.1. 结构

service层主要提供相应的服务包括：实现类、dao层接口、mapping和util，其启动类放在最外层以便自动扫描

![截图2](/img/spring-boot-dubbo/截图2.jpg)

#### 1.2.2. pom依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.7.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.balance</groupId>
    <artifactId>springboot-dubbo-service</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-dubbo-service</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <!--redis依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <!--web依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>


        <!-- 导入Mysql数据库链接jar包 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.11</version>
        </dependency>
        <!--mysql依赖-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.1</version>
        </dependency>

        <!--测试-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!--api接口依赖-->
        <dependency>
            <groupId>com.balance</groupId>
            <artifactId>spring-dubbo-api</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <!--zookeeper驱动-->
        <dependency>
            <groupId>com.101tec</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.10</version>
        </dependency>

        <!--dubbo依赖-->
        <dependency>
            <groupId>com.alibaba.spring.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>
    </dependencies>

    <build>
        <!--防止idea中maping层中的xml和web资源无法打入target包中-->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/webapp</directory>
                <targetPath>META-INF/resources</targetPath>
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

#### 1.2.3. 配置文件

```properties
# 内嵌tomcat端口
server.port=8081

# Dubbo配置
spring.dubbo.application.name=springboot-dubbo-service
spring.dubbo.application.registry=zookeeper://192.168.67.129:2181

# mybatis配置
mybatis.mapper-locations=classpath:/com/balance/mapping/*.xml
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql:///message_board?useSSL=false&autoReconnect=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=12345678

# reidis 配置
spring.redis.host=192.168.67.129
spring.redis.port=6379
# 日志sql输出处理
logging.level.com.mrbalance.mapping=debug
```

#### 1.2.4. 启动类

其启动类上需要加上注解

```java
// 启动dubbo注解
@EnableDubboConfiguration
// dao层扫描
@MapperScan(basePackages = "com.balance.dao")
```

### 1.3. Consumer层

#### 1.3.1. 结构

consumer层主要是消费service层的接口，其主要由controller层和web资源组成

![截图3](/img/spring-boot-dubbo/截图3.jpg)

#### 1.2.2. pom依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.7.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.balance</groupId>
    <artifactId>springboot-dubbo-consumer</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>springboot-dubbo-consumer</name>
    <description>Demo project for Spring Boot</description>

    <dependencies>
        <!--web依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--测试依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!--api接口依赖-->
        <dependency>
            <groupId>com.balance</groupId>
            <artifactId>spring-dubbo-api</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <!--zookeeper驱动-->
        <dependency>
            <groupId>com.101tec</groupId>
            <artifactId>zkclient</artifactId>
            <version>0.10</version>
        </dependency>

        <!--dubbo依赖-->
        <dependency>
            <groupId>com.alibaba.spring.boot</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>

        <!--jsp页面使用jstl标签-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
        </dependency>

        <!--用于编译jsp-->
        <dependency>
            <groupId>org.apache.tomcat.embed</groupId>
            <artifactId>tomcat-embed-jasper</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <!--防止idea中maping层中的xml和web资源无法打入target包中-->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/webapp</directory>
                <targetPath>META-INF/resources</targetPath>
                <includes>
                    <include>**/*.*</include>
                </includes>
            </resource>
        </resources>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

#### 1.2.3. 配置文件

```properties
# 内嵌tomcat端口
server.port=8082

# Dubbo配置
spring.dubbo.application.name=springboot-dubbo-consumer
spring.dubbo.application.registry=zookeeper://192.168.67.129:2181

# 静态资源路径
spring.resources.static-locations=classpath:/static

# web配置
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

#### 1.2.4. 启动类

其启动类上需要加上注解

```java
// 启动dubbo注解
@EnableDubboConfiguration
```

### 1.4 根pom

配置父级pom方便clean或者install

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.balance</groupId>
    <artifactId>spring-dubbo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>springboot-dubbo</name>

    <modules>
        <!--接口层-->
        <module>springboot-dubbo-api</module>
        <!--服务层-->
        <module>springboot-dubbo-service</module>
        <!--消费层-->
        <module>springboot-dubbo-consumer</module>
    </modules>

</project>
```

