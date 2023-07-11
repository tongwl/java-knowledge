# Spring注解 - @Import注解

`@Import`可以导入三种类

* 普通的配置类
* 实现`ImportSelector`的接口的类
* 实现`ImportBeanDefinitionRegistrar`接口的类



## 导入普通的配置类

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





## 导入实现`ImportSelector`的接口的类

`ImportSelector`接口有一个方法叫做`selectImports`，该方法返回一个字符串数组，**该字符串的内容是某个类的全限定名，这些类就会被注册成Bean被Spring管理**。

例子：
MyImportSelector.java

```
package com.william3.ipt;

import com.william3.bean.OtherBean;
import org.springframework.context.annotation.ImportSelector;
import org.springframework.core.type.AnnotationMetadata;

import java.util.function.Predicate;

public class MyImportSelector implements ImportSelector {
    public String[] selectImports(AnnotationMetadata annotationMetadata) {
        return new String[]{OtherBean.class.getName()};
    }
}
```



OtherBean.java

```
package com.william3.bean;

public class OtherBean {
    public OtherBean() {
        System.out.println("OtherBean init");
    }
}
```



SpringConfig.java

```java
package com.william3;

import com.alibaba.druid.pool.DruidDataSource;
import com.william3.ipt.ImportTest1;
import com.william3.ipt.MyImportSelector;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

@Configuration
@ComponentScan({"com.william3"})
@PropertySource("classpath:jdbc.properties")
@MapperScan("com.william3.mapper")
@Import({ImportTest1.class, MyImportSelector.class}) // 导入MyImportSelector.class
public class SpringConfig {

    @Bean
    public DataSource dataSource(
            @Value("${driverClassName}") String driver,
            @Value("${url}") String url,
            @Value("${username}") String username,
            @Value("${password}") String password
            ) {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(@Autowired DataSource dataSource) {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        return sqlSessionFactoryBean;
    }
}
```



Application.java

```java
package com.william3;

import com.william3.popo.User;
import com.william3.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import java.util.List;

public class App 
{
    public static void main( String[] args ) {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
    }
}
```





## 导入实现`ImportBeanDefinitionRegistrar`接口的类

`ImportBeanDefinitionRegistrar`接口有一个方法叫做registerBeanDefinitions，在此方法中可以实现注册beanDefinition对象从而实现创建Bean。

例子：

MyImportBeanDefinitionRegister.java

```java
package com.william3.ipt;

import com.william3.bean.OtherBean;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.beans.factory.support.RootBeanDefinition;
import org.springframework.context.annotation.ImportBeanDefinitionRegistrar;
import org.springframework.core.type.AnnotationMetadata;

public class MyImportBeanDefinitionRegister implements ImportBeanDefinitionRegistrar {
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry, BeanNameGenerator importBeanNameGenerator) {
        // 注册beanDefinition
        BeanDefinition beanDefinition = new RootBeanDefinition();
        beanDefinition.setBeanClassName(OtherBean.class.getName());
        registry.registerBeanDefinition("otherBean", beanDefinition);
    }
}
```



OtherBean.java

```java
package com.william3.bean;

public class OtherBean {
    public OtherBean() {
        System.out.println("OtherBean init");
    }
}
```



SpringConfig.java

```java
package com.william3;

import com.alibaba.druid.pool.DruidDataSource;
import com.william3.ipt.ImportTest1;
import com.william3.ipt.MyImportBeanDefinitionRegister;
import com.william3.ipt.MyImportSelector;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

@Configuration
@ComponentScan({"com.william3"})
@PropertySource("classpath:jdbc.properties")
@MapperScan("com.william3.mapper")
@Import({ImportTest1.class, MyImportBeanDefinitionRegister.class}) // 导入MyImportBeanDefinitionRegister
public class SpringConfig {

    @Bean
    public DataSource dataSource(
            @Value("${driverClassName}") String driver,
            @Value("${url}") String url,
            @Value("${username}") String username,
            @Value("${password}") String password
            ) {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(@Autowired DataSource dataSource) {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        return sqlSessionFactoryBean;
    }
}
```



Application.java

```java
package com.william3;

import com.william3.popo.User;
import com.william3.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import java.util.List;

public class App 
{
    public static void main( String[] args ) {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
    }
}
```

