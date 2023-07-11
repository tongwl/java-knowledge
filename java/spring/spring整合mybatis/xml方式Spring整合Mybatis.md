# xml方式Spring整合Mybatis

![image-20230402150225969](/Users/weitong/Library/Application Support/typora-user-images/image-20230402150225969.png)

## 第一步：导入Mybatis整合Spring相关的坐标

pom.xml

```xml
    <!-- mybatis包 -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.10</version>
    </dependency>
    
		<!-- mysql连接包 -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.49</version>
    </dependency>

		<!-- spring相关包 -->
		<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.3.18</version>
    </dependency>
		<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.7</version>
    </dependency>

		<!-- mybatis-spring包 -->
		<dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>2.0.7</version>
    </dependency>
```



## 第二步：配置Mapper类和Mapper.xml

在java下创建com.william3.mapper包，并创建UserMapper.java

```java
package com.william3.mapper;

import com.william3.pojo.User;

import java.util.List;

public interface UserMapper {
    public List<User> listUser();
}
```

并在resources下创建相同目录的com.william3.mapper(注意：必须要和Mapper java相同路径)，并创建UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.william3.mapper.UserMapper">
    <select id="listUser" resultType="com.william3.pojo.User">
        select * from user
    </select>
</mapper>
```

创建User pojo

```java
package com.william3.pojo;

public class User {
    private int id;
    private String user;
    private String password;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUser() {
        return user;
    }

    public void setUser(String user) {
        this.user = user;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

创建UserService

```java
package com.william3.service;

import com.william3.mapper.UserMapper;
import com.william3.pojo.User;

import java.util.List;

public class UserServiceImpl {

    private UserMapper userMapper;

    public void setUserMapper(UserMapper userMapper) {
        this.userMapper = userMapper;
    }

    public List<User> getUsers () {
        return userMapper.listUser();
    }

}
```



## 第三步：配置SqlSessionFactoryBean和MapperScannerConfigurer

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd
    http://www.springframework.org/schema/aop
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
    ">
    <!-- 数据库连接 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://tidb-lab.webex.com:4000/ceshi?useUnicode=true&amp;characterEncoding=UTF-8"></property>
        <property name="username" value="udp"></property>
        <property name="password" value="K$6Uu8U$JlcJWXeT"></property>
    </bean>
    <!-- 将SqlSessionFactoryBean存储到spring容器 -->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!-- 扫描指定的包，产生Mapper对象存储到Spring容器 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.william3"></property>
    </bean>

    <bean id="userService" class="com.william3.service.UserServiceImpl">
        <property name="userMapper" ref="userMapper"></property>
    </bean>
</beans>
```



## 第四步：启动类测试

```java
package com.william3;

import com.william3.pojo.User;
import com.william3.service.UserServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.util.List;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext4.xml");
        UserServiceImpl userService = applicationContext.getBean(UserServiceImpl.class);
        List<User> list =  userService.getUsers();
        for(User user : list) {
            System.out.println(user);
        }
    }
}
```
