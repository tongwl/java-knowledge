# Spring注解 - 其他注解



## `@Primary`

![image-20230425110613030](/Users/weitong/Library/Application Support/typora-user-images/image-20230425110613030.png)

`@Primary` 注解是 Spring Framework 中的一个注解，它用于指示当存在多个相同类型的 Bean 时，哪个 Bean 应该具有首要优先级。当多个 Bean 实现了同一个接口，而需要将其中一个 Bean 注入到另一个 Bean 时，使用 `@Primary` 注解可以告诉 Spring 框架优先选择哪个 Bean。

举个例子，假设我们有一个接口 `MyInterface` 和两个实现类 `MyInterfaceImpl1` 和 `MyInterfaceImpl2`。我们可以在 `MyInterfaceImpl1` 上使用 `@Primary` 注解，表示在多个实现中，它应该被优先选择。

下面是一个示例：

```java
public interface MyInterface {
    // ...
}

@Component
@Primary
public class MyInterfaceImpl1 implements MyInterface {
    // ...
}

@Component
public class MyInterfaceImpl2 implements MyInterface {
    // ...
}

@Component
public class MyComponent {
    @Autowired
    private MyInterface myInterface;
    // ...
}
```

在这个例子中，`MyInterfaceImpl1` 被标记为 primary，当 Spring 自动装配 `MyInterface` 时，它将被选择作为优先 Bean，而不是 `MyInterfaceImpl2`。

需要注意的是，当标记了多个 Bean 为 `@Primary` 时，会抛出 `NoUniqueBeanDefinitionException` 异常，因为 Spring 不知道应该选择哪个 Bean。因此，在一个上下文中只能使用一个 `@Primary` 注解。



例子：

Application.java

```java
import com.william.config.SpringConfig;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
        UserService userService = applicationContext.getBean(UserService.class);
        userService.show();
    }
}

// 输出
userDaoImpl2
```



UserServiceImpl.java

```java
package com.william.service.impl;

import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service("userService")
public class UserServiceImpl implements UserService {

    @Resource
    private UserDao userDao;

    public void show() {
        userDao.show();
    }
}
```



UserDao.java

```java
package com.william.dao;

public interface UserDao {
    public void show();
}
```



UserDaoImpl.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;


@Repository
public class UserDaoImpl implements UserDao {

    public void show() {
        System.out.println("userDaoImpl");
    }
}
```



UserDaoImpl2.java

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Repository;

@Repository
@Primary // 因为此处使用了@Primary，所以它会被优先选择
public class UserDaoImpl2 implements UserDao {
    public void show() {
        System.out.println("userDaoImpl2");
    }
}
```





## `@Profile`

![image-20230425111516027](/Users/weitong/Library/Application Support/typora-user-images/image-20230425111516027.png)



`@Profile`注解指定只有在当前环境下该类会被注册到Spring容器中，例如：

```java
@Repository
@Profile("test") // 只有当在test环境下，UserDaoImpl2才会被注入到Spring容器中
public class UserDaoImpl2 implements UserDao {
}
```



例子：

UserDaoImpl.java

// 没有被`@Profile`标记，它在任何环境下都会被注册到Spring容器中去

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Repository;


@Repository
public class UserDaoImpl implements UserDao {

    public void show() {
        System.out.println("userDaoImpl");
    }
}
```



UserDaoImpl2.java
// 只会在test环境注册到Spring容器中

```java
package com.william.dao.impl;

import com.william.dao.UserDao;
import org.springframework.context.annotation.Primary;
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Repository;

@Repository
@Profile("test")
public class UserDaoImpl2 implements UserDao {
    public void show() {
        System.out.println("userDaoImpl2");
    }
}
```



Application.java

// 虽然有两个类实现了接口UserDao，但是因为其中一个标记了`@Profile("test")`，意味着它只会在test环境起作用，所以在获取`getBean(UserDao.class)`的时候并不会报错，因为只存在一个UserDaoImpl Bean。

```java
import com.william.config.SpringConfig;
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Application {
    public static void main(String[] args) {
        // System.setProperty("spring.profiles.active", "test");
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
        UserDao userDao = applicationContext.getBean(UserDao.class);
        System.out.println(userDao);
    }
}

//输出com.william.dao.impl.UserDaoImpl@5ad851c9
```



Application2.java

// 因为此时设置了当前环境是test环境，那么UserDaoImpl2也会被注入到Spring容器中，此时存在两个UserDao Bean实例，所以报错了。

```java
import com.william.config.SpringConfig;
import com.william.dao.UserDao;
import com.william.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Application {
    public static void main(String[] args) {
        System.setProperty("spring.profiles.active", "test"); // 设定当前环境为test环境
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);
        UserDao userDao = applicationContext.getBean(UserDao.class);
        System.out.println(userDao);
    }
}

//报错了
```

