> 学习了docker,除了用来装一些环境让他跑起来能访问，最为关键的当然是和应用结合，因为他的便利性就在于他的集成交付的快速、成本低、效率高，今天就来这里踩踩坑

> 本人对抛开版本谈实现的行为，都觉得很流氓，如果是从头开始扎扎实实学习docker的，可能了解的比较深入，遇到问题会少一些困惑，可是像我半路基础没学扎实就想来搞项目的，坑就是一个接着一个

```text
背景：
docker 信息
Server Version: 18.09.2
Storage Driver: overlay2
Kernel Version: 4.9.125-linuxkit
Operating System: Docker for Mac
OSType: linux
Architecture: x86_64
CPUs: 4
Total Memory: 1.952GiB
Product License: Community Engine

springboot信息
版本：2.0.4.release
```
### 很简单的一个项目
- 一个简单的springboot项目，访问另一个容器的MySQL ，访问在另一容器的Redis
```text
- controller 
    - VistorController
- domain
    - User
- repository
- ConnectionApp
```
- 一个接口验证数据库访问和缓存访问
- pom.xml
```text
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.4.RELEASE</version>
    </parent>


    <properties>
        <docker.image.prefix>springboot</docker.image.prefix>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>connectiontest</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```
- Dockerfile
```text
# 基础镜像使用java
FROM openjdk:8-alpine

MAINTAINER claire <632307140@qq.com>

# VOLUME 指定了临时文件目录为/tmp。
# 其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp
VOLUME /tmp 

# 将jar包添加到容器中并更名为app.jar
RUN rm -rf /usr/local/opt/apps/connection/*
ADD ./target/connectiontest.jar /usr/local/opt/apps/connection/app.jar

EXPOSE 8080
WORKDIR /usr/local/opt/apps/connection/

CMD ["java", "-jar", "app.jar"]


//========================================================================

FROM openjdk:8-alpine
VOLUME /tmp
ADD ./target/connectiontest.jar app.jar
RUN sh -c ‘touch /app.jar’
ENV JAVA_OPTS=””
ENTRYPOINT [ “sh”, “-c”, “java $JAVA_OPTS -Djava.security .egd=file:/dev/./urandom -jar /app.jar” ]

```
- 这边docker打包有两种方式，一种是原生docker命令实现，一种是依赖docker-maven-plugin的插件来完成，日后在做总结，本次使用方法一
- maven 将项目打包
```text
mvn clean install -Dmaven.test.skip=true
```
- docker打包镜像
```text
到项目的根目录，包含Dockerfile的地方
docker build -t connectiontest:1.0 . (注意最后一个点指当前目录的意思，不要遗漏)
```
- 以上步骤完成后，能够看到项目打包镜像成功
```text
docker images
能够看到刚才的镜像connectiontest:1.0
```
### 差别最大的就是在run的地方
- 当前需求：在另外的两个容器中，分别运行了MySQL和Redis两个应用，我需要将现有的springboot项目运行到一个新的容器，并访问这两个容器的服务
- docker-compose的处理是将多个应用放置在同一个容器中，暂时不符合当前需求
- 最初的问题就是容器中访问不通，connection refused的问题，这本来就是一个比较简单的问题，网络不通，要么网络连接，要么网络共用，可是网上的说法实在五花八门
- 网上总结了三种方法：第一种,使用内部服务的IP，即docker中的虚IP进行服务的访问/第二种,使用link进行网络访问，内部使用容器name进行服务访问/第三种，使用--net=host，直接拿宿主机通信
#### 第一种：内部服务IP
```text
spring.datasource.url=jdbc:mysql://172.17.0.2:3306/testdb

redis 也是用虚IP

```
- 就是这样，写虚IP地址
- docker run --rm -d -p 8080:8080 --name test1 connectiontest:1.0
- 通过docker logs -f containerid 查看日志，启动成功
- 访问127.0.0.1:8080/swagger-ui.html测试，可以出现内容


#### 第二种，使用link 容器name （推荐）
- 利用name进行通信，可以避免镜像启停造成的虚拟IP的改变
```text
spring.datasource.url=jdbc:mysql://mysql:3306/testdb

redis 也使用name :redis-server

```
- 注意修改配置后，镜像需要重新打包，或者将配置文件指向本机可访问的地址，进行动态修改
- 如果没有支持动态修改，需要重新打包使用：mvn clean install -Dmaven.test.skip=true    docker build -t connectiontest:1.0 . 
- docker run --rm -d -p 8080:8080 --name test2 --link mysql:mysql --link redis-server redis-server  connectiontest:1.0
- 通过docker logs -f containerid 查看日志，启动成功
- 访问127.0.0.1:8080/swagger-ui.html测试，可以出现内容

#### 第三种，通过net host，借用宿主机进行通信
- 内部配置使用127.0.0.1，像本地测试一样进行就可以了
- docker run --rm -d -p 8080:8080 --name test3  connectiontest:1.0
- 同样查看日志，确实访问数据库是没有问题，可是似乎就没有办法通过127.0.0.1:8080/swagger-ui.html 进行访问，出现访问不了的情况，暂时没有研究为什么，日后补充


#### 附录
- [参考1](https://segmentfault.com/a/1190000016557755)
- [参考2](https://blog.csdn.net/begin1013/article/details/80860224)
- [参考3](https://blog.csdn.net/xiaoxiangzi520/article/details/78775503)
- [参考4](https://www.cnblogs.com/ityouknow/p/8661644.html)
