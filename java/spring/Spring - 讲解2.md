# Spring - 讲解2



## Spring配置非自定义Bean

在实际开发中，有些功能类不是我们自定义的，而是使用第三方jar包，那么这些Bean想要让Spring进行管理，也需要对其进行配置。

配置非自定义Bean需要考虑以下两个问题：

* 配置非自定义的Bean的实例化方式是什么？是无参构造，有参构造，静态工厂方式还是实例工厂方式；
* 被配置的Bean是否需要注入必要属性；



### 例子1：配置Druid数据源交由Spring管理。

我们正常使用Druid的方式是

```java
DruidDataSource dataSource = new DruidDataSource();
dataSource.setDriverClassName("com.mysql.jdbc.Driver");
dataSource.setUrl("jdbc:mysql://tidb-sjc.webex.com:4000/prod_205?useSSL=true&enabledTLSProtocols=TLSv1.2&allowPublicKeyRetrieval=true&autoReconnect=true");
dataSource.setUsername("pda");
dataSource.setPassword("123456");
```

由上述代码可以看出DruidDataSource这个类可以通过`setDriverClassName`, `setUrl`, `setUsername`, `setPassword`来设置数据库属性，那么我们可以根据此set方式来使DruidDataSource由spring管理，如下步骤：

第一步：maven导入Druid和mysql连接的坐标

```xml
<dependency>
  <groupId>com.alibaba</groupId>
  <artifactId>druid</artifactId>
  <version>1.1.23</version>
</dependency>
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>5.1.49</version>
</dependency>
```

第二步：配置xml

```xml
<bean id="datasource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://tidb-lab.webex.com:4000/ds3?useUnicode=true&amp;characterEncoding=UTF-8&amp;enabledTLSProtocols=TLSv1.2"></property>
    <property name="username" value="udp"></property>
    <property name="password" value="123456"></property>
</bean>
```

第三步：通过getBean方式获取到DruidDataSource的bean实例

```java
package com.william;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class application {

    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext2.xml");
        DruidDataSource dataSource = applicationContext.getBean("datasource", DruidDataSource.class);
        try {
            Connection connection = dataSource.getConnection();
            PreparedStatement ps = connection.prepareStatement("select * from t_ds_etljob_definition");
            ResultSet result = ps.executeQuery();
            while(result.next()){
                System.out.println(result.getRow());
            }

        } catch (Exception e) {
        }
    }
}
```



### 例子2：配置Connection交由Spring管理(静态工厂方法)

通过`DriverManager.getConnection()`的静态方法获取`Connection`对象。
我们正常的使用方式是：

```java
Connection connection = DriverManager.getConnection("url", "user", "password");
```

由spring方式创建Connection bean实例：
application.xml

```xml
<bean id="connection" class="java.sql.DriverManager" factory-method="getConnection">
    <constructor-arg name="url" value="jdbc:mysql://tidb-sjc.webex.com:4000/prod_205?useSSL=true&amp;enabledTLSProtocols=TLSv1.2&amp;allowPublicKeyRetrieval=true&amp;autoReconnect=true"></constructor-arg>
    <constructor-arg name="user" value="pda"></constructor-arg>
    <constructor-arg name="password" value="123456"></constructor-arg>
</bean>
```

application.java

```java
package com.william;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.sql.Connection;

public class application {

    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext2.xml");
        try {
            Connection connection = applicationContext.getBean("connection", Connection.class);
            System.out.println(connection);
        } catch(Exception e) {
        }
    }
}
```



### 例子3：配置日期对象交由Spring管理(实例工厂方法)

正常使用的方法

```java
SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
Date date = simpleDateFormat.parse("2023-08-07 07:02:01");
```

当使用spring进行管理的时候，代码如下
xml

```xml
<bean id="simpleDateFormat" class="java.text.SimpleDateFormat">
    <constructor-arg name="pattern" value="yyyy-MM-dd HH:mm:ss"></constructor-arg>
</bean>
<bean id="date" factory-bean="simpleDateFormat" factory-method="parse">
    <constructor-arg name="source" value="2023-08-07 07:02:01"></constructor-arg>
</bean>
```

application.java

```java
package com.william;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import java.text.ParseException;
import java.util.Date;

public class application {

    public static void main(String[] args) throws ParseException {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext2.xml");
        Date date = applicationContext.getBean("date", Date.class);
        System.out.println(date);
    }
}
```



### 例子4 配置Mybatis

正常用法

```java
InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(inputStream);
```

当使用spring进行管理的时候，代码如下

先引入pom中

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.5.5</version>
</dependency>
```

mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://tidb-sjc.webex.com:4000/prod_205?useSSL=true&amp;enabledTLSProtocols=TLSv1.2&amp;allowPublicKeyRetrieval=true&amp;autoReconnect=true"/>
                <property name="username" value="pda"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

application.java

```java
package com.william;

import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.io.InputStream;

public class application {

    public static void main(String[] args) throws Exception {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext2.xml");
        InputStream inputStream = applicationContext.getBean("inputStream", InputStream.class);
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = applicationContext.getBean("sqlSessionFactoryBuilder", SqlSessionFactoryBuilder.class);
        SqlSessionFactory sqlSessionFactory = applicationContext.getBean("sqlSessionFactory", SqlSessionFactory.class);
    }
}
```

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
        <bean id="inputStream" class="org.apache.ibatis.io.Resources" factory-method="getResourceAsStream">
            <constructor-arg name="resource" value="mybatis-config.xml"></constructor-arg>
        </bean>
        <bean id="sqlSessionFactoryBuilder" class="org.apache.ibatis.session.SqlSessionFactoryBuilder"></bean>
        <bean id="sqlSessionFactory" factory-bean="sqlSessionFactoryBuilder" factory-method="build">
            <constructor-arg name="inputStream" ref="inputStream"></constructor-arg>
        </bean>
</beans>

```





