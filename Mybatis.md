# Mybatis

![image-20220116200244217](images/Mybatis/image-20220116200244217.png)

知识前提：

- JDBC
- MySQL
- Java基础
- Maven
- Junit

官网：[https://mybatis.org/mybatis-3/zh/getting-started.html](https://mybatis.org/mybatis-3/zh/getting-started.html)

## 1. 简介

### 1.1 什么MyBatis

- MyBatis 是一款优秀的**持久层框架**
- 它支持自定义 SQL、存储过程以及高级映射
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
- MyBatis本是apache的一个[开源项目](https://baike.baidu.com/item/开源项目/3406069)iBatis，2010年这个[项目](https://baike.baidu.com/item/项目/477803)由apache software foundation迁移到了[google code](https://baike.baidu.com/item/google code/2346604)，并且改名为MyBatis。2013年11月迁移到[Github](https://baike.baidu.com/item/Github/10145341)。



如何获得Mybatis？

- maven仓库

  ```xml
  <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>x.x.x</version>
  </dependency>
  ```

- Github：https://github.com/mybatis/mybatis-3

## 2. 第一个Mybatis程序

思路：搭建环境 -> 导入Mybatis -> 编写代码 -> 测试

### 2.1 搭建数据库

```sql
CREATE DATABASE `mybatis`;

USE `mybatis`;

DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
`id` int(20) NOT NULL,
`name` varchar(30) DEFAULT NULL,
`pwd` varchar(30) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert  into `user`(`id`,`name`,`pwd`) values 
(1,'狂神','123456'),
(2,'张三','abcdef'),
(3,'李四','987654');
```

### 2.2 新建Maven项目

> 1、导入依赖

```xml
<dependency>
   <!-- mybatis -->
   <groupId>org.mybatis</groupId>
   <artifactId>mybatis</artifactId>
   <version>3.5.2</version>
</dependency>
<dependency>
    <!-- mysql驱动 -->
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <version>5.1.47</version>
</dependency>
```



> 2、编写Mybatis的核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--核心配置文件-->
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
      </dataSource>
    </environment>
  </environments>
   
  <!-- 每一个Mapper.xml 文件都需要在Mybatis核心配置文件中注册！ -->
  <!-- 不然会报错：org.apache.ibatis.binding.BindingException: Type interface com.kuang.dao.UserDao is not known to the MapperRegistry.-->
  <mappers>
    <mapper resource="com/kuang/dao/UserMapper.xml"/>
  </mappers>
</configuration>
```



> 3、编写工具类

每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。

从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。 但也可以使用任意的输入流（InputStream）实例，比如用文件路径字符串或 `file://` URL 构造的输入流。MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，使得从类路径或其它位置加载资源文件更加容易。

```java
String resource = "org/mybatis/example/mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。例如：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```

工具类：

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {

   private static SqlSessionFactory sqlSessionFactory;

   static {
       try {
           String resource = "mybatis-config.xml";
           InputStream inputStream = Resources.getResourceAsStream(resource);
           sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
      } catch (IOException e) {
           e.printStackTrace();
      }
  }

   //获取SqlSession连接
   public static SqlSession getSession(){
       return sqlSessionFactory.openSession();
  }

}
```



### 2.3 编写代码

> 1、User类

```Java
public class User {
   
   private int id;  //id
   private String name;   //姓名
   private String pwd;   //密码
   
   //构造,有参,无参
   //set/get
   //toString()
   
}
```



> 2、Mapper接口类

```java
import com.kuang.pojo.User;
import java.util.List;

public interface UserMapper {
   List<User> selectUser();
}
```



> 3、编写Mapper.xml配置文件（由原来的UserDaoImpl转变为一个Mapper配置文件）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
       PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--namespace 绑定一个指定的Dao/Mapper接口-->
<mapper namespace="com.kuang.dao.UserMapper">
  <!-- id为Mapper接口里的方法名  resultType是返回类型 -->
  <select id="selectUser" resultType="com.kuang.pojo.User">
    select * from user
  </select>
</mapper>
```



### 2.4 测试

Junit测试：

```java
public class MyTest {
   @Test
   public void selectUser() {
       // 1.获取SqlSession对象
       SqlSession session = MybatisUtils.getSession();
       
       // 2.执行SQL
       // 方式一：getMapper
       UserMapper mapper = session.getMapper(UserMapper.class);
       List<User> users = mapper.selectUser();
       
       // 方式二：不推荐
       // List<User> users = session.selectList("com.kuang.mapper.UserMapper.selectUser");

       for (User user: users){
           System.out.println(user);
       }
       
       // 3. 关闭sqlSession
       session.close();
  }
}
```



> 可能出现问题说明：Maven静态资源过滤问题

maven由于他的约定大于配置，我们之后可能遇到我们写的配置文件，无法被导出或者生效的问题，解决方案：

```xml
<!-- 
  build中配置resources，防止我们资源导出失败的问题 
  约定xml文件是放在resources文件夹下的，如果放在其他地方，可能会有导出失败的问题
-->
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
           		<include>**/*.properties</include>
           		<include>**/*.xml</include>
       		</includes>
            <filtering>false</filtering>
        </resource>
        <resource>
       		<directory>src/main/resources</directory>
       		<includes>
           		<include>**/*.properties</include>
           		<include>**/*.xml</include>
       		</includes>
       		<filtering>false</filtering>
   		</resource>
    </resources>
</build>
```

