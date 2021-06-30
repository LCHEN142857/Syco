- 新建maven工程，目录结构如下
![MEMO](https://img-blog.csdnimg.cn/20210701003547545.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTAzOTM2Nw==,size_16,color_FFFFFF,t_70#pic_center)



- pom.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.memo</groupId>
    <artifactId>memo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>memo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.3.7.RELEASE</spring-boot.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.19</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.3</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.3.7.RELEASE</version>
                <configuration>
                    <mainClass>com.memo.MemoApplication</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <id>repackage</id>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

</project>
```
- application.properties
```
# 应用名称
spring.application.name=memo
# 应用服务 WEB 访问端口
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/testsql?serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

mybatis.config-location= classpath:mybatis-config.xml
mybatis.type-aliases-package= com.memo.entity
mybatis.mapper-locations=- classpath:mapper/*.xml
```
- mybatis-config.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <settings>
  	<!-- 开启二级缓存 -->
  	<setting name="cacheEnabled" value="true"/>
  	<!-- 关闭属性之间的粘连性 -->
  	<setting name="aggressiveLazyLoading" value="false"/>
  	<!-- 指定使用日志的类型 -->
  	<setting name="logImpl" value="STDOUT_LOGGING"/>
  </settings>
</configuration>
```

- MemoApplication
```
package com.memo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(MemoApplication.class, args);
    }

}

```

- User
> 新建一个数据库表与之对应
```
@Data
public class User {

    private int uid;
    private String uname;
    private String pwd;
}
```

- UserMapper
```
@Mapper
@Repository
public interface UserMapper {

    @Select("select * from user")
    List<User> getAllUsers();

    @Delete("delete from user where uid = #{uid}")
    int deleteUser(@Param("uid") int uid);
}
```

- UserService
```
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;

    public List<User> getAllUsers(){
        return userMapper.getAllUsers();
    }

    public int deleteUser(int uid){
        return userMapper.deleteUser(uid);
    }
}
```

- UserController
```
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @RequestMapping("/all")
    public String getAllUsers(){
        return userService.getAllUsers().toString();
    }

    @RequestMapping("/del/{uid}")
    public int deleteUser(@PathVariable("uid") int uid){
        return userService.deleteUser(uid);
    }
}
```
