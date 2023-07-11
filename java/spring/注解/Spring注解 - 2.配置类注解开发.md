# Spring注解 - 2.配置类注解开发

* 本章涉及到的注解
  * `@Configuration`
  * `@ComponentScan`
  * `@PropertySource`
  * `@Import`



![image-20230425100541161](/Users/weitong/Library/Application Support/typora-user-images/image-20230425100541161.png)

![image-20230425100601475](/Users/weitong/Library/Application Support/typora-user-images/image-20230425100601475.png)

配置类的作用就是代替原有的`applicationContext.xml`。

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"

       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 指定读取的properties文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
    <!-- 配置注解扫描范围 -->
    <context:component-scan base-package="com.william" />
</beans>
```



## 启动

在加载xml配置阶段，我们使用`ClassPathXmlApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");`来加载xml配置文件，但是使用注解配置类后，我们使用`ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);`来加载。

例子：

```java
import com.william.config.SpringConfig;
import com.william.controller.UserController;
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
        Object object = applicationContext.getBean("dataSource");
        System.out.println(object);
    }
}
```



## `@Configuration`

`@Configuration`注解的主要作用就是标记此类是一个配置类(同时@Configuration内部是被@Component所标记，所以也是标记了这个类也是一个Bean)，相当于代替了原有的`applicationContext.xml`

例子：

```java
package com.william.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@Configuration // 标注当前类是一个配置类(替代配置文件) + @Component
public class SpringConfig {
}
```



## `@ComponentScan`

`@ComponentScan` 相当于`<context:component-scan base-package="" />`， 指定了注解扫描范围，它的value是一个数组。

```java
@ComponentScan({"com.william"})  // 指定扫描com.william包
@ComponentScan({"com.william", "com.william2"}) // 指定扫描com.william和com.william2包
```

例子：

```java
package com.william.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@Configuration // 标注当前类是一个配置类(替代配置文件) + @Component
@ComponentScan({"com.william"})  //相当于<context:component-scan base-package="com.william" />，设置注解扫描范围
public class SpringConfig {
}
```



## `@PropertySource`

`@PropertySource`相当于`<context:property-placeholder location=""></context:property-placeholder>`，指定读取的properties文件。

例子：

```java
package com.william.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

@Configuration // 标注当前类是一个配置类(替代配置文件) + @Component
@ComponentScan({"com.william"})  //相当于<context:component-scan base-package="com.william" />，设置注解扫描范围
@PropertySource("classpath:jdbc.properties") // 相当于<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>，指定指定读取的properties文件
public class SpringConfig {
}
```



## `@Import`

`@Import`相当于`<import resource=""></import>`，引入另外一个类作为配置类。

例子：

SpringConfig.java

```java
package com.william.config;

import com.william.beans.OtherBean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

@Configuration // 标注当前类是一个配置类(替代配置文件) + @Component
@ComponentScan({"com.william"})  //相当于<context:component-scan base-package="com.william" />，设置注解扫描范围
@PropertySource("classpath:jdbc.properties") // 相当于<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>，指定指定读取的properties文件
@Import(OtherBean.class)
public class SpringConfig {
}
```

OtherBean.java

```java
package com.william.beans;

import com.alibaba.druid.pool.DruidDataSource;
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;

// 在此处并没有标记上@Component，但是因为在SpringConfig.java中使用@Import(OtherBean.class)使得此类变成配置类，同时也会加上@Component注解
public class OtherBean {
    @Bean("dataSource")
    public DataSource dataSource(@Qualifier("userDao2") UserDao userDao) {
        System.out.println(userDao);
        DruidDataSource dataSource = new DruidDataSource();
        return dataSource;
    }
}
```
