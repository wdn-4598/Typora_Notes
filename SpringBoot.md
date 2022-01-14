# SpringBoot2

## 1. 简介及快速搭建

### 1.1 简介

SpringBoot基于Spring开发，继承了Spring框架原有的优秀特性。其设计目的是通过提供默认配置等方式，简化Spring应用的初始搭建以及开发过程。

SpingBoot 1.0基于Spring 4.0 开发

SpingBoot 2.0基于Spring 5.0 开发

**约定大于配置**

### 1.2 微服务





### 1.3 快速搭建



## 2. spingboot的配置和自动配置原理



## 3. 热部署与日志



## 4. springboot与web开发



## 5. 集成MyBatis



## 6. 启动原理源码剖析



## 7. 自定义starters



## 8. 集成常用中间件



## 9. 常用注解

@SpringBootApplication：标记成SpringBoot的启动类

```java
// 主程序启动类
// 该类中使用了一个组合注解 @SpringBootApplication，用来开启 Spring Boot 的自动配置
// 另外该启动类中包含一个 main() 方法，用来启动该项目。
@SpringBootApplication
public class HelloWorldApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApplication.class, args);
    }
}

// 注意：Spring Boot 内部集成了 Tomcat，不需要人为手动配置 Tomcat，
// 开发者只需要关注具体的业务逻辑即可。
```



@RestController：@Controller + @ ResponseBody

@RequestMapping



