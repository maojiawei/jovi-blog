---
title: "Spring Boot 的加密方式"
date: 2018-09-28T12:51:17+08:00
draft: false
tags: ["spring"]
categories: "microservice"
---
# 简介
数据库密码的明文是软件开发的大忌，因此我们需要在平时的开发中就特别注意，数据库的加密。
本文将介绍spring boot如何采用jasypt的方式来介绍如何在平时的开发中加密数据库密码。

# pom依赖
在pom.xml中添加依赖
```yaml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>1.5.10.RELEASE</version>
    </parent>
    <groupId>io.jovi.voltorb</groupId>
    <artifactId>voltorb-sort</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.github.ulisesbocchio</groupId>
            <artifactId>jasypt-spring-boot-starter</artifactId>
            <version>1.8</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.6</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```
# 配置文件
在src/main/resource中添加application.yml文件，填入一下参数
```yaml
jasypt:
  encryptor:
    password: jovi
```

# 加密密码
在src/test/java中添加test文件
```java
@Slf4j
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT,classes = {Application.class})
public class JasyptTest {
    @Autowired
    StringEncryptor stringEncryptor;

    @Test
    public void encryptPwd() {
        String result = stringEncryptor.encrypt("maojiawei7750");
        log.info("result:{}",result);
    }
}
```
# 添加加密配置
在数据库中添加如下配置，注意需要在密码上面添加ENC()，里面为密码
```yaml
spring:
    datasource:
     url: jdbc:postgresql://127.0.1.1:3306/public
     username: jovi
     password: 'ENC(oZ+fpvtnXJlorWQZDJUYDrJ1EvX3LrPO)'
     driverClassName: org.postgresql.Driver
```

}


