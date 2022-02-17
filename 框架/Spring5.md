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

![](images/Spring5/IOC(2).png)

### 2.2 IOC 接口（BeanFactory） 

1、IOC思想基于IOC容器完成，IOC容器底层就是对象工厂。

2、Spring提供IOC容器实现两种方式：（两个接口）

（1）BeanFactory：IOC容器基本实现，是Spring的内部使用接口，不推荐开发人员进行使用；

- 懒加载：加载配置文件时不会创建对象，在获取（使用）对象时才去创建对象

（2）ApplicationContext：BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员进行使用；

- 加载配置文件时，就会把在配置文件中的对象进行创建
- 把耗时耗资源的操作都在项目启动的时候进行处理更合适，比如 web+tomacat 就是优先使用ApplicationContext更适合。

3、ApplicationContext接口所有实现类：

![image-20220214194127737](images/Spring5/ApplicationContext实现类.png)

### 2.3 IOC容器-Bean管理

Bean管理主要就是两个操作：

- Spring创建对象；
- Spring注入属性；

#### 基于xml配置文件

##### bean管理

> 创建对象

```xml
<!-- User 对象创建 -->
<bean id="user" class="com.wdn.bean.User"></bean>
```

（1）在Spring配置文件中，使用 bean 标签，标签里面添加对应属性，就可以实现对象创建。

（2）在 bean 标签中有很多属性，常用的属性：

- id 属性：唯一标识，不能包含特殊符号
- class 属性：类全路径（包类路径）
- name 属性：可以为Bean指定多个名称，每个名称之间用逗号或分号隔开，名称可以包含特殊符号

（3）==创建对象时候，默认也是执行无参数构造方法完成对象创建==



> 注入属性

**第一种方式：set方法注入属性**

（1）创建类，定义属性和对应的set方法

```java
public class User {
    String name;
    String pwd;

    public void setName(String name) {
        this.name = name;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }
}
```

（2）使用 property 完成属性注入

- name：类里面属性名称
- value：向属性注入的值

```xml
<bean id="user" class="com.wdn.bean.User">
    <property name="name" value="小明"/>
    <property name="pwd" value="1234546"/>
</bean>
```



**第二种方式：使用有参数构造进行注入**

（1）创建类，定义属性和对应的有参数构造方法

```java
public class User {
    String name;
    String pwd;

    public User(String name, String pwd) {
        this.name = name;
        this.pwd = pwd;
    }
}
```

（2）使用有参数构造注入属性

```xml
<bean id="user1" class="com.wdn.bean.User">
    <constructor-arg name="name" value="小欧"></constructor-arg>
    <constructor-arg name="pwd" value="12435"></constructor-arg>
</bean>
```



**第三种方式：p 名称空间注入（了解）**

使用 p 名称空间注入，本质也是使用 set 方法注入，只是可以简化基于xml配置方式！

（1）添加 p 名称空间在配置文件中（第3行是新添的）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:p="http://www.springframework.org/schema/p"  
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
```

（2）进行属性注入，在 bean 标签里面进行操作（算是set方式注入的简化操作）

```xml
<bean id="user2" class="com.wdn.bean.User" p:name="小吴" p:pwd="13587"></bean>
```



> xml注入其他类型属性

1、字面量

（1）null值

```xml
<bean id="user3" class="com.wdn.bean.User">
    <property name="name">
        <null/>
    </property>
</bean>
```

效果等价于

```xml
<bean id="user3" class="com.wdn.bean.User"></bean>
```

输出 user3 的 name 属性，都会得到 null

（2）特殊符号

```xml
<!--属性值包含特殊符号
 1、把<>进行转义 &lt;  &gt;
 2、把带特殊符号内容写到 CDATA
-->

<bean id="user4" class="com.wdn.bean.User">
    <property name="pwd">
        <value>
            <![CDATA[此处写入包含特殊符号的值，比如<<>>]]>
        </value>
    </property>
</bean>
```



2、外部bean

（1）创建两个类 service 类和 dao 类 

```java
public interface UserDaoImpl {
    public void add();
}


public class UserDao implements UserDaoImpl{
    @Override
    public void add() {
        System.out.println("UserDao add .......");
    }
}
```

```java
public class UserService {

    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void add(){
        userDao.add();
        System.out.println("UserService add ......");
    }

}
```

（2）在 spring 配置文件中进行配置

```xml
<bean id="userDaoImpl" class="com.wdn.dao.UserDao"></bean>

<bean id="userService" class="com.wdn.service.UserService">
    <!--
		注入 userDao 对象
	 	name 属性：类里面属性名称
 		ref 属性：创建 userDao 对象 bean 标签 id 值
	-->
    <property name="userDao" ref="userDaoImpl"></property>
</bean>
```

（3）测试

```java
@Test
public void testUserService(){
    // 1.加载spring配置文件
    ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");
    BeanFactory context1 = new ClassPathXmlApplicationContext("application.xml");
    // 2.获取配置创建的对象
    UserService userService = context.getBean("userService", UserService.class);
    userService.add();
}
```



3、内部bean

（1）一对多关系：部门和员工。一个部门有多个员工，一个员工属于一个部门。部门是一，员工是多。

（2）在实体类之间表示一对多关系，员工表示所属部门，使用对象类型属性进行表示

```java
// 部门类
public class Dept {
    private String dname;

    public void setDname(String dname) {
        this.dname = dname;
    }
}

// 员工类
public class Emp {
    private String ename;
    private String gender;
    private Dept dept;  // 员工属于某一个部门，使用对象形式表示

    public void setEname(String ename) {
        this.ename = ename;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public void setDept(Dept dept) {
        this.dept = dept;
    }

    @Override
    public String toString() {
        return "Emp{" +
                "ename='" + ename + '\'' +
                ", gender='" + gender + '\'' +
                ", dept=" + dept +
                '}';
    }
}
```

```xml
<bean id="emp" class="com.wdn.bean.Emp">
    <property name="ename" value="老杨"></property>
    <property name="gender" value="男"></property>
    <property name="dept">
        <bean id="dept" class="com.wdn.bean.Dept">
            <property name="dname" value="安保部"></property>
        </bean>
    </property>
</bean>
```



4、级联赋值

（1）第一种写法

跟外部bean注入一样

```xml
<bean id="emp1" class="com.wdn.bean.Emp">
    <property name="ename" value="老李"></property>
    <property name="gender" value="男"></property>
    <property name="dept" ref="dept"></property>
</bean>

<bean id="dept" class="com.wdn.bean.Dept">
    <property name="dname" value="技术部"></property>
</bean>
```



（2）第二种写法

在 Emp 员工类中生成 dept 的get方法

```java
// 员工类
public class Emp {
    private String ename;
    private String gender;
    private Dept dept;  // 员工属于某一个部门，使用对象形式表示

    public Dept getDept() {
        return dept;
    }
    
    ...
}
```

```xml
<bean id="dept" class="com.wdn.bean.Dept"></bean>

<bean id="emp2" class="com.wdn.bean.Emp">
    <property name="ename" value="老吴"></property>
    <property name="gender" value="男"></property>
    <property name="dept" ref="dept"></property>
    <!--级联赋值-->
    <property name="dept.dname" value="财务部"></property>
</bean>
```



5、注入集合属性

（1）注入数组类型属性

（2）注入List集合类型属性

（3）注入Map集合类型属性

```java
package com.wdn.bean;

import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.Set;

public class Stu {
    // 1. 数组类型属性
    private String[] courses;
    // 2. list集合类型属性
    private List<String> list;
    // 3. map集合类型属性
    private Map<String, String> maps;
    // 4. set集合类型属性
    private Set<String> sets;

    public void setCourses(String[] courses) {
        this.courses = courses;
    }

    public void setList(List<String> list) {
        this.list = list;
    }

    public void setMaps(Map<String, String> maps) {
        this.maps = maps;
    }

    public void setSets(Set<String> sets) {
        this.sets = sets;
    }

    @Override
    public String toString() {
        return "Stu{" +
                "courses=" + Arrays.toString(courses) +
                ", list=" + list +
                ", maps=" + maps +
                ", sets=" + sets +
                '}';
    }
}
```

```xml
<bean id="stu" class="com.wdn.bean.Stu">
    <!-- 数组类型属性注入 -->
    <property name="courses">
        <array>
            <value>java课程</value>
            <value>数据库课程</value>
        </array>
    </property>

    <!-- list类型属性注入 -->
    <property name="list">
        <list>
            <value>张三</value>
            <value>法外狂徒</value>
        </list>
    </property>

    <!-- map类型属性注入 -->
    <property name="maps">
        <map>
            <entry key="JAVA" value="java"></entry>
            <entry key="PHP" value="php"></entry>
        </map>
    </property>

    <!-- set类型属性注入 -->
    <property name="sets">
        <set>
            <value>MYSQL</value>
            <value>Redis</value>
        </set>
    </property>
</bean>
```



6、在集合里面设置对象类型值

新建一个Course类

```java
// 课程名称
public class Course {
    private String cname; // 课程名称

    public void setCname(String cname) {
        this.cname = cname;
    }
}
```

```xml
<bean id="course1" class="com.wdn.bean.Course">
    <property name="cname" value="Spring框架"></property>
</bean>

<bean id="course2" class="com.wdn.bean.Course">
    <property name="cname" value="SpringBoot框架"></property>
</bean>

<bean id="stu" class="com.wdn.bean.Stu">
    ...
    
    <property name="courseList">
        <list>
            <ref bean="course1"></ref>
            <ref bean="course2"></ref>
        </list>
    </property>
</bean>
```



7、把集合注入部分提取出来

（1）在 spring 配置文件中引入名称空间 util

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/util
        http://www.springframework.org/schema/util/spring-util.xsd">
```



（2）使用 util 标签完成 list 集合注入提取

```java
public class Book {

    private List<String> list;

    public List<String> getList() {
        return list;
    }

    public void setList(List<String> list) {
        this.list = list;
    }
}
```

```xml
<util:list id="bookList">
    <value>易筋经</value>
    <value>一指神功</value>
    <value>九阴真经</value>
</util:list>

<bean id="book" class="com.wdn.bean.Book">
    <property name="list" ref="bookList"></property>
</bean>
```

##### FactoryBean

1、Spring 有两种类型 bean，一种普通 bean（默认单实例），另外一种工厂 bean（FactoryBean，默认多实例） 

2、普通 bean：在配置文件中定义，bean 类型就是返回类型

3、工厂 bean：在配置文件定义，bean 类型可以和返回类型不一样

第一步 创建类，让这个类作为工厂 bean，实现接口 FactoryBean

```java
import org.springframework.beans.factory.FactoryBean;

public class MyBean implements FactoryBean<Course> {
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```



第二步 实现接口里面的方法，在实现的方法中定义返回的 bean 类型

```xml
<bean id="myBean" class="com.wdn.bean.MyBean"></bean>
```



第三步 测试

```java
@Test
public void testMyBean(){
    // 1.加载spring配置文件
    ApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    // 2.获取配置创建的对象
    Course myBean = context.getBean("myBean", Course.class);
    System.out.println(myBean);
}
```

##### bean作用域

1、在 Spring 里面，默认情况下，bean 是单实例对象

2、如何设置bean对象的作用域

（1）在 spring 配置文件 bean 标签里面有属性（scope）用于设置单实例还是多实例

（2）scope 属性值

- 默认值，singleton，表示是单实例对象

- prototype，表示是多实例对象
- request
- session

（3）singleton 和 prototype 区别

- singleton 单实例，prototype 多实例
- 设置 scope 值是 singleton 时候，加载 spring 配置文件时候就会创建单实例对象；
- 设置 scope 值是 prototype 时候，不会在加载 spring 配置文件时候创建对象，而在调用
  getBean 方法时候创建多实例对象

##### bean生命周期

1、生命周期：从对象创建到对象销毁的过程

2、bean 生命周期

（1）通过构造器创建 bean 实例（无参数构造）

（2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）

（3）调用 bean 的 init-method 指定的方法（需要进行配置初始化的方法）

（4）bean 可以使用了（对象获取到了）

（5）当容器关闭时候，调用 bean 的 destroy-method 指定的销毁方法（需要进行配置销毁的方法）

3、演示 bean 生命周期

（1）新建Orders实体类

```java
// 订单类
public class Orders {
    private String oname;

    public void setOname(String oname) {
        this.oname = oname;
        System.out.println("第二步 调用 set 方法设置属性值");
    }

    // 无参构造器
    public Orders(){
        System.out.println("第一步 执行无参数构造器创建 bean 实例");
    }

    // 创建执行的初始化的方法
    public void myInitMethod(){
        System.out.println("第三步 执行初始化的方法");
    }

    // 创建执行的销毁的方法
    public void myDestroyMethod(){
        System.out.println("第五步 执行销毁的方法");
    }
}
```

（2）配置xml

```xml
<bean id="orders" class="com.wdn.bean.Orders" init-method="myInitMethod" destroy-method="myDestroyMethod">
    <property name="oname" value="手机"></property>
</bean>
```

（3）测试

```java
@Test
public void testOrders(){
    // 1.加载spring配置文件
    ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean.xml");
    // 2.获取配置创建的对象
    Orders orders = context.getBean("orders", Orders.class);
    System.out.println("第四步 获取创建 bean 实例对象");
    System.out.println(orders);

    // 需要手动销毁bean实例，才会执行 destroy-method 指定的方法
    context.close();
}
```

输出：

```
第一步 执行无参数构造器创建 bean 实例
第二步 调用 set 方法设置属性值
第三步 执行初始化的方法
第四步 获取创建 bean 实例对象
com.wdn.bean.Orders@32eebfca
第五步 执行销毁的方法
```



4、bean 的后置处理器

完整的 bean 生命周期其实有七步：

（1）通过构造器创建 bean 实例（无参数构造）

（2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）

（3）执行后置处理器的方法 postProcessBeforeInitialization

（4）调用 bean 的 init-method 指定的方法（需要进行配置初始化的方法）

（5）执行后置处理器的方法 postProcessAfterInitialization

（6）bean 可以使用了（对象获取到了）

（7）当容器关闭时候，调用 bean 的 destroy-method 指定的销毁方法（需要进行配置销毁的方法）



5、演示添加后置处理器效果

（1）创建后置处理器类，实现接口 BeanPostProcessor；

```java
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPost implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之前执行的方法");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之后执行的方法");
        return bean;
    }
}
```

（2）添加配置

```xml
<!-- 配置后置处理器 -->
<bean id="myBeanPost" class="com.wdn.bean.MyBeanPost"></bean>
```

##### xml自动装配

1、什么是自动装配

（1）根据指定装配规则（属性名称或者属性类型），Spring 自动将匹配的属性值进行注入

2、演示自动装配过程

bean 标签属性 autowire 实现自动装配，autowire 属性常用两个值：

- byName 根据属性名称注入 ，注入值 bean 的 id 值和类属性名称一样
- byType 根据属性类型注入

（1）根据属性名称自动注入

```xml
<bean id="emp" class="com.wdn.bean.Emp" autowire="byName"></bean>
<bean id="dept" class="com.wdn.bean.Dept"></bean>
```

（2）根据属性类型自动注入

```xml
<bean id="emp" class="com.wdn.bean.Emp" autowire="byType"></bean>
<bean id="dept" class="com.wdn.bean.Dept"></bean>
```

##### 外部属性文件

方式一：直接配置数据库信息

（1）引入Druid德鲁伊连接池依赖

```xml
<!--pom.xml-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.17</version>
</dependency>
```

（2）配置德鲁伊连接池

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="com.mysql.jdbc.Driver"></property>
    <property name="username" value="root"></property>
    <property name="password" value="123456"></property>
</bean>
```



方式二：引入外部属性文件，配置数据库连接池

（1）创建外部属性文件，properties 格式文件，写数据库信息

```properties
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc:mysql://localhost:3306/userDb
prop.userName=root
prop.password=123456
```

（2）把外部 properties 属性文件引入到 spring 配置文件中

需要先引入 context 名称空间，再在 spring 配置文件使用标签引入外部属性文件

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    
    <!--引入外部属性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.userName}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
</beans>
```

### 基于注解



## 3. AOP



## 4. JDBCTemplate



## 5. 事务管理



## 6. Spring5新特性



```


```





