# Spring注解 - 1.基本注解

* 本章涉及到的注解

  * `@Component`
  * `@Controller`
  * `@Service`
  * `@Repository`
  * `@Scope`
  * `@Lazy`
  * `@PostConstruct`
  * `@PreDestroy`
  * `@Value`
  * `@Autowired`
  * `@Qualifier`
  * `@Resource`
  * `@Bean`

* 关键信息

  * **要使用注解，首先必须要添加上注解扫描范围**

    * xml方式
      ```xml
      <!-- 配置注解扫描范围 -->
      <context:component-scan base-package="com.william" />
      ```

    * 注解方式
      ```java
      @Configuration
      @ComponentScan({"com.william"}) // 配置注解扫描范围
      public class Config {}
      ```

  * `@Value`和`@Autowired`不仅可以使用在变量上，也可以使用在参数只有一个的方法上

    ```java
    @Value("${jdbc.driver}")
    private String driver;
    
    @Value("${jdbc.driver}")
    private void testDriver(String driver) {
        System.out.println("我在创建阶段就被调用了");
        System.out.println(driver);
    }
    ```

    ```java
    @Autowired
    @Qualifier("userDaoImpl2")
    private UserDao userDao;
    
    
    @Autowired
    @Qualifier("userDaoImpl2")
    private void testUserDao(UserDao userDao) {
        userDao.test();
    }
    ```

  * `@Autowired` vs `@Resource`

    * `@Autowired`是spring的注解，**默认先根据类型进行匹配，如果匹配的有多个bean，那么再根据名字匹配，如果还匹配不到则报错。**

      ```java
      @Autowired  // 先根据类型匹配，如果存在多个UserDao bean类，再根据名称"userDao"进行匹配，如果还匹配不到，则报错
      private void testUserDao(UserDao userDao) {
          userDao.test();
      }
      ```

      如果我们需要根据名字匹配bean，那么可以和`@Qualifier`配合使用。
      ```java
      @Autowired
      @Qualifier("userDaoImpl2") // 指定名称匹配，找不到则报错
      private void testUserDao(UserDao userDao) {
          userDao.test();
      }
      ```

    * `@Resource`是 JavaEE 规范中的注解。`Resource`默认按照byName的方式进行自动装配，即根据属性的名称在容器中查找匹配的 bean 进行注入，**但是如果容器中只有一个bean，即使bean名称不相同，也会被注入**。如果容器中存在多个匹配的 bean，可以通过 `name` 属性指定 bean 的名称。也可以通过`type`设置指定类型注入。

      ```java
      @Resource // 默认按照byName注入，如果只有一个UserDao的bean，即使bean名称不相同，也会成功被注入
      private UserDao userDao;
      ```

      ```java
      @Resource(name="userDao") // 指定名称注入，如果不存在一个bean名为userDao的，即使只有一个UserDao bean，也报错
      private UserDao userDao;
      ```

      ```java
      @Resource(type= UserDaoImpl2.class) // 指定byType方式注入
      private UserDao userDao;
      ```

      

  * Tet



## Bean基本注解开发

![image-20230410101452853](/Users/weitong/Library/Application Support/typora-user-images/image-20230410101452853.png)



![image-20230410101748326](/Users/weitong/Library/Application Support/typora-user-images/image-20230410101748326.png)

**要使用注解，首先需要在xml中加上注解生效的扫描范围**

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
    <!-- 配置注解扫描范围 -->
    <context:component-scan base-package="com.william" />
</beans>
```



### `@Component`

被该注解标识的类，会在指定扫描范围内被Spring加载并实例化。

#### 常例

例子：

```java
@Component(value = "userDao")
public class UserDaoImpl implements UserDao {
}
```

**注解知识：当注解只写一个属性，且属性名是value的时候，可以省略属性名：**

```java
@Component("userDao")
public class UserDaoImpl implements UserDao {
}
```



```java
@Component(value = "userDao")
```

转换为bean就是

```java
<bean id="userDao" class="xx.xx.xx.UserDaoImpl"></bean>
```



#### 不写value的情况

**不写value的情况相当于默认指定bean name是类名的首字母小写的值(userDaoImpl)。**相当于：

```xml
<bean id="userDaoImpl" class="xx.xx.xx.UserDaoImpl"></bean>
```

例子：

UserServiceImpl.java

```java
package com.william.service.impl;

import com.william.service.UserService;
import org.springframework.stereotype.Component;

@Component
public class UserServiceImpl implements UserService {
}
```

Application.xml

```java
import com.william.dao.UserDao;
import com.william.dao.impl.UserDaoImpl;
import com.william.service.UserService;
import com.william.service.impl.UserServiceImpl;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object userService = applicationContext.getBean("userDaoImpl");
        System.out.println(userService);
    }
}

```



![image-20230410131630097](/Users/weitong/Library/Application Support/typora-user-images/image-20230410131630097.png)

### `@Scope`, `@Lazy`, `@PostConstruct`, `@PreDestroy`

例子：

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.context.annotation.Lazy;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

@Component(value = "userDao")
@Scope(value="singleton")
@Lazy(true)
public class UserDaoImpl implements UserDao {
    public UserDaoImpl() {
        System.out.println("构造函数");
    }

    @PostConstruct
    private void init() {
        System.out.println("初始化方法");
    }

    @PreDestroy
    public void destroy() {
     System.out.println("destroy方法");
    }
}
```



`@Component`衍生出来的3个注解：

* `@Controller` 在web层类上使用
* `@Service` 在Service层类上使用
* `@Repository` 在Dao层类上使用



上述能做到更好的语义化。在开发中可能还会碰到一些Bean并不属于Controller, Service, Repository的任何一层，那我们就直接使用`@Component`就可以了。

![image-20230410134311734](/Users/weitong/Library/Application Support/typora-user-images/image-20230410134311734.png)





### Bean依赖注入注解开发

![image-20230410135209043](/Users/weitong/Library/Application Support/typora-user-images/image-20230410135209043.png)

  

#### `@Value`注入普通属性

例子1：
UserDaoImpl.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;


@Repository(value="userDao")
public class UserDaoImpl implements UserDao {

    @Value("zhangsan")
    private String username;

    public void show() {
        System.out.println(username);
    }
}
```

Application.java

```java
import com.william.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = applicationContext.getBean(UserDao.class);
        userDao.show();
    }
}

//输出
//zhangsan
```

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
    <!-- 配置注解扫描范围 -->
    <context:component-scan base-package="com.william" />
</beans>
```

例子2：

**当`@Value`注入到只有一个参数的方法中时(不一定是网上说的set方法)，此方法会在bean创建阶段就被调用，且`@Value`会注入参数。**

UserDaoImpl.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;


@Repository(value="userDao")
public class UserDaoImpl implements UserDao {

    private String username;

    @Value("lisi")
    public void setUsername(String username) {
      	System.out.println("我会在bean创建阶段就被调用");
        this.username = username;
    }

    public void show() {
        System.out.println(username);
    }
}
```

Application.java

```java
import com.william.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = applicationContext.getBean(UserDao.class);
        userDao.show();
    }
}

//输出
//lisi
```

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
    <!-- 配置注解扫描范围 -->
    <context:component-scan base-package="com.william" />
</beans>
```

例子3：`@Value`从properties文件读取
格式

```java
@Value("${username}")
public void setUsername(String username) {
    this.username = username;
}
```

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

jdbc.properties

```
username=tongwl
pwd=123456
isAdmin=true
```

UserDaoImpl.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;


@Repository(value="userDao")
public class UserDaoImpl implements UserDao {

    private String username;

    @Value("${username}")
    public void setUsername(String username) {
        this.username = username;
    }

    public void show() {
        System.out.println( username);
    }
}
```

Application.java

```java
import com.william.dao.UserDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = applicationContext.getBean(UserDao.class);
        userDao.show();
    }
}

// 输出：tongwl
```



#### `@Autowired`注入对象引用

**特点：默认先根据类型进行匹配，如果匹配的有多个bean，那么再根据名字匹配，如果还匹配不到则报错。**

格式:

```java
@Autowired
private UserDao userDao;
```

Or 方法式的注入

```java
@Autowired 
public void setUserDao(UserDao userDao) {
	this.userDao = userDao;
}
```

 例子：

UserDaoImpl.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;


@Repository(value="userDao")
public class UserDaoImpl implements UserDao {

    public void show() {
        System.out.println("william");
    }
}
```

UserServiceImpl.java

```java
package com.william.service.impl;

import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;

@Service("userService")
public class UserServiceImpl implements UserService {

    @Autowired  // 根据类型注入UserDao对象
    private UserDao userDao;

    public void show() {
        userDao.show();
    }
}
```

Application.java

```java
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = applicationContext.getBean(UserService.class);
        userService.show();
    }
}

//输出：william
```

注意`@Autowired`是根据类型注入的，如果同一类型的Bean有多个，那么会尝试根据名字进行二次匹配，如果还匹配不成功才报错。

例子：

有两个类实现了UserDao.java的接口
UserDaoImpl.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;


@Repository(value="userDao")
public class UserDaoImpl implements UserDao {

    public void show() {
        System.out.println("userDao");
    }
}
```

UserDaoImpl2.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.stereotype.Repository;

@Repository
public class UserDaoImpl2 implements UserDao {
    public void show() {
			System.out.println("userDao2");
    }
}
```

UserServiceImpl.java

```java
package com.william.service.impl;

import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;

@Service("userService")
public class UserServiceImpl implements UserService {

    @Autowired // 根据类型注入，如果同一类型的bean有多个，那么会尝试根据名字进行二次匹配，如果还匹配不成功就会报错
    private UserDao userDao;

    public void show() {
        userDao.show();
    }
}
```

Application.java

```java
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = applicationContext.getBean(UserService.class);
        userService.show();
    }
}

// 输出
// userDao
```

上述之所以没有报错的原因是因为存在一个bean为userDao的UserDao bean，如果UserServiceImpl定义的UserDao名字是其他的，就会报错。

```java
@Autowired // 报错，因为存在两个UserDao类型的bean，且并不存在一个bean名是userDao222的UserDao类
private UserDao userDao222;
```



如果我们需要根据名字匹配bean，那么可以和`@Qualifier`配合使用。

#### `@Qualifier`

##### `@Autowired` + `@Qualifier`

使用`@Qualifier`指定名字匹配

例子：

UserServiceImpl.java

```java
package com.william.service.impl;

import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

@Service("userService")
public class UserServiceImpl implements UserService {

    @Autowired
    @Qualifier("userDaoImpl") //指定根据名称userDaoImpl去匹配，如果匹配不到就报错
    private UserDao userDao;

    public void show() {
        userDao.show();
    }
}
```



#### `@Resource`

`@Resource`也能实现bean的注入， `@Resource` 虽然是 JavaEE 规范中的注解，但是Spring也实现了对它的解析。

`Resource`默认按照byName的方式进行自动装配，即根据属性的名称在容器中查找匹配的 bean 进行注入，但是如果容器中只有一个bean，即使bean名称不相同，也会被注入。如果容器中存在多个匹配的 bean，可以通过 `name` 属性指定 bean 的名称。也可以通过`type`设置指定类型注入。

格式：

```java
@Resource // 默认根据名称进行自动装配
private UserDao userDao;
```

```java
@Resource(name="userDao2") // 根据名称注入
private UserDao userDao;
```



**`@Authwired`和`@Resource`相比，一般用`@Autowired`。**



#### `@Autowired` vs `@Resource`

* `@Autowired`是spring的注解，**默认先根据类型进行匹配，如果匹配的有多个bean，那么再根据名字匹配，如果还匹配不到则报错。**

  ```java
  @Autowired  // 先根据类型匹配，如果存在多个UserDao bean类，再根据名称"userDao"进行匹配，如果还匹配不到，则报错
  private void testUserDao(UserDao userDao) {
      userDao.test();
  }
  ```

  如果我们需要根据名字匹配bean，那么可以和`@Qualifier`配合使用。

  ```java
  @Autowired
  @Qualifier("userDaoImpl2") // 指定名称匹配，找不到则报错
  private void testUserDao(UserDao userDao) {
      userDao.test();
  }
  ```

* `@Resource`是 JavaEE 规范中的注解。`Resource`默认按照byName的方式进行自动装配，即根据属性的名称在容器中查找匹配的 bean 进行注入，**但是如果容器中只有一个bean，即使bean名称不相同，也会被注入**。如果容器中存在多个匹配的 bean，可以通过 `name` 属性指定 bean 的名称。也可以通过`type`设置指定类型注入。

  ```java
  @Resource // 默认按照byName注入，如果只有一个UserDao的bean，即使bean名称不相同，也会成功被注入
  private UserDao userDao;
  ```

  ```java
  @Resource(name="userDao") // 指定名称注入，如果不存在一个bean名为userDao的，即使只有一个UserDao bean，也报错
  private UserDao userDao;
  ```

  ```java
  @Resource(type= UserDaoImpl2.class) // 指定byType方式注入
  private UserDao userDao;
  ```



#### 注入List

使用`@Autowired`和`@Resource`都可以

```java
@Autowired
private List<UserDao> userDaos; // 会注入所有的UserDao到list中
```

```java
@Resource
private List<UserDao> userDaos;
```



`@Autowired`配合`@Qualifier`注入指定名称的bean进入list

```java
@Autowired
@Qualifier("userDaoImpl") // 此处的List只会注入名为userDaoImpl的bean
private List<UserDao> userDaos;
```



`@Resource`的name和type的使用

```java
@Resource(name="userDaoImpl") // 只会注入名为userDaoImpl的bean进入List
private List<UserDao> userDaos;
```

```java
// 以下报错
@Resource(type = UserDaoImpl.class) // 注意：此处会报错，不能按照类型注入!!!!
private List<UserDao> userDaos;
```



## 非自定义Bean注解开发 `@Bean`

![image-20230413152931351](/Users/weitong/Library/Application Support/typora-user-images/image-20230413152931351.png)

![image-20230413154753092](/Users/weitong/Library/Application Support/typora-user-images/image-20230413154753092.png)

 

**对于一个非自定义类，如果想要将他变成Bean交由Spring管理，需要以下两步：**

* 第一步：先创建一个Bean类
* 在这个Bean类中写一个方法，给这个方法加上注解`@Bean`，此方法返回的非自定义类就会变成Bean。

`@Bean`玩法：

* `@Bean("datasource")`的写法表示创建一个bean名为datasource的bean

  ```java
  @Bean("datasource")
  public DataSource aaa() {
  	DruidDataSource dataSource = new DruidDataSource();
  	return dataSource;
  }
  ```

  bean名就会根据方法名而来，也就是创建一个bean名为aaa的bean

* 传递普通的参数使用`@Value`

  ```java
  @Bean("dataSource")
  public DataSource dataSource(@Value("${driverClassName}") String driverClassName) {
      DruidDataSource dataSource = new DruidDataSource();
      dataSource.setDriverClassName(driverClassName);
      return dataSource;
  }
  ```

* 传递Bean对象作为对象，如果使用`@Autowired`进行按类型注入，`@Autowired`也可以省略

  ```java
  @Bean("dataSource")
  public DataSource dataSource(UserService userService) {
      System.out.println(userService);
      DruidDataSource dataSource = new DruidDataSource();
      return dataSource;
  }
  ```

  or

  ```java
  @Bean("dataSource")
  public DataSource dataSource(@Autowired UserService userService) {
      System.out.println(userService);
      DruidDataSource dataSource = new DruidDataSource();
      return dataSource;
  }
  ```

* 传递Bean对象作为对象，如果使用`@Autowired`进行按名称注入，请使用`@Qualifier`

  ```java
  @Bean("dataSource")
  public DataSource dataSource(@Qualifier("userDao2") UserDao userDao) {
      System.out.println(userDao);
      DruidDataSource dataSource = new DruidDataSource();
      return dataSource;
  }
  ```

  



例子1：

首先创建一个beans包，在这个包下比如建立OtherBean.java，代码如下

```java
package com.william.beans;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;

// 使用@Component是为了将此类交给Spring管理，这样内部的@Bean才会起作用
@Component
public class OtherBean {

    @Bean("datasource")
    public DataSource datasource() {
        DruidDataSource dataSource = new DruidDataSource();
        return dataSource;
    }
}
```

Application.java

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object object = applicationContext.getBean("datasource");
        System.out.println(object);
    }
}
```



例子2:

OtherBean.java

```java
package com.william.beans;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;

// 使用@Component是为了将此类交给Spring管理，这样内部的@Bean才会起作用
@Component
public class OtherBean {

		// 创建的bean名就是aaa
    @Bean
    public DataSource aaa() {
        DruidDataSource dataSource = new DruidDataSource();
        return dataSource;
    }
}
```

Application.java

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object object = applicationContext.getBean("aaa");
        System.out.println(object);
    }
}
```



例子3：传递普通参数使用`@Value`

OtherBean.java

```java
package com.william.beans;

import com.alibaba.druid.pool.DruidDataSource;
import com.william.service.UserService;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;

// 使用@Component是为了将此类交给Spring管理，这样内部的@Bean才会起作用
@Component
public class OtherBean {
@Bean("dataSource")
    public DataSource dataSource(@Value("${driverClassName}") String driverClassName) {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driverClassName);
        return dataSource;
    }
}
```

Application.java

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object object = applicationContext.getBean("dataSource");
        System.out.println(object);
    }
}
```



例子4：Bean对象作为参数，按照类型注入

OtherBean.java

```java
package com.william.beans;

import com.alibaba.druid.pool.DruidDataSource;
import com.william.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;

// 使用@Component是为了将此类交给Spring管理，这样内部的@Bean才会起作用
@Component
public class OtherBean {
    @Bean("dataSource")
    public DataSource dataSource(@Autowired UserService userService) {
        System.out.println(userService);
        DruidDataSource dataSource = new DruidDataSource();
        return dataSource;
    }
}
```

Application.java

```java
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object object = applicationContext.getBean("dataSource");
        System.out.println(object);
    }
}
```



例子5：Bean对象作为参数，按照名称注入，使用`@Qualifier`注入。

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

// 使用@Component是为了将此类交给Spring管理，这样内部的@Bean才会起作用
@Component
public class OtherBean {
    @Bean("dataSource")
    public DataSource dataSource(@Qualifier("userDao2") UserDao userDao) {
        System.out.println(userDao);
        DruidDataSource dataSource = new DruidDataSource();
        return dataSource;
    }
}
```

Application.java

```java
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Object object = applicationContext.getBean("dataSource");
        System.out.println(object);
    }
}
```
