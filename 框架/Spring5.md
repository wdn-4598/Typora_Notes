# Spring5

## 1. 框架概述

### 1.1 简介

- Spring 是轻量级的开源的 JavaEE 框架
- Spring 可以解决企业应用开发的复杂性
- Spring 有两个核心部分：IOC 和 Aop
  - IOC：控制反转，把创建对象过程交给 Spring 进行管理
  - Aop：面向切面，不修改源代码进行功能增强
- Spring 特点
  - 方便解耦，简化开发
  - Aop 编程支持
  - 方便程序测试
  - 方便和其他框架进行整合
  - 方便进行事务操作
  - 降低 API 开发难度

### 1.2 入门案例

环境：

- JDK8
- maven3.8.4
- spring5

1、新建maven项目，并导入依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
        <version>5.2.6.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>5.2.6.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.6.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-expression</artifactId>
        <version>5.2.6.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

2、创建实体类User.java

```java
public class User {
    String name;
    String pwd;
    // get set
}
```

3、在resources下创建application.xml

Spring配置文件使用xml格式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- 配置 User 对象创建 -->
    <bean id="user" class="com.wdn.bean.User">
        <property name="name" value="小明"/>
        <property name="pwd" value="1234546"/>
    </bean>
</beans>
```

4、测试

```java
import com.wdn.bean.User;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestSpring5 {
    @Test
    public void testUser(){
        // 1.加载spring配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
        // 2.获取配置创建的对象
        User user = context.getBean("user", User.class);
        System.out.println(user.getName());
    }
}
```

## 2. IOC

### 2.1 IOC 底层原理

> 1、什么是IOC

（1）控制反转，把创建对象和对象之间的调用过程，交给Spring进行管理

（2）使用IOC目的：为了耦合度降低

（3）入门案例执行过程就是利用了IOC实现

> 2、IOC底层原理

（1）知识：xml解析、工厂模式、反射

（2）画图讲解IOC底层原理

![](images/Spring5/IOC(1).png)













IOC 接口（BeanFactory） 

IOC 操作 Bean 管理（基于 xml） 

IOC 操作 Bean 管理（基于注解）

## 3. AOP



## 4. JDBCTemplate



## 5. 事务管理



## 6. Spring5新特性



