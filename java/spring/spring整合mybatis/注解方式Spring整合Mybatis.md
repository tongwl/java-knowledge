# 注解方式Spring整合Mybatis



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

创建UserServiceImpl.java

```java
package com.william3.service.impl;

import com.william3.mapper.UserMapper;
import com.william3.popo.User;
import com.william3.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import java.util.List;

@Service("userServiceImpl")
public class UserServiceImpl implements UserService {

    @Autowired
    private UserMapper userMapper;

    public List<User> listUsers() {
        return userMapper.listUser();
    }
}

```



## 第三步：配置SqlSessionFactoryBean和MapperScannerConfigurer

```java
package com.william3;

import com.alibaba.druid.pool.DruidDataSource;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

@Configuration
@ComponentScan({"com.william3"})
@PropertySource("classpath:jdbc.properties")
@MapperScan("com.william3.mapper") // 配置mapper扫描类
public class SpringConfig {

  	// 配置DataSource
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

  	// 配置SqlSessionFactoryBean
    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(@Autowired DataSource dataSource) {
        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        return sqlSessionFactoryBean;
    }
}
```



## 第四步：启动类测试

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
        UserService userService = applicationContext.getBean("userServiceImpl", UserService.class);
        List<User> list = userService.listUsers();
        System.out.println(list);
    }
}
```
