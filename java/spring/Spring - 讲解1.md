# Spring - 讲解1

mybatis是一个框架；

ApplicationContext称为Spring容器，内部封装了BeanFactory，使用ApplicationContext进行开发时，xml配置文件的名称习惯写成applicationContext.xml。



BeanFactory的bean只会在第一次getBean的时候进行实例化；

**ApplicationContext的bean默认会在ApplicationContext对象初始化的时候就实例化；**

![image-20230307100057516](/Users/weitong/Library/Application Support/typora-user-images/image-20230307100057516.png)





![image-20230307100800934](/Users/weitong/Library/Application Support/typora-user-images/image-20230307100800934.png)





![image-20230307100820985](/Users/weitong/Library/Application Support/typora-user-images/image-20230307100820985.png)ApplicationContext的三个实现类：

`ClassPathXmlApplicationContext`

表示在类路径下加载xml文件

```java
// applicationContext.xml只需要放在项目的resources目录下：
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
```



`FileSystemXmlApplicationContext`

表示在磁盘路径下加载文件，注意：文件路径需要在最前面加上`file:`

```java
ApplicationContext applicationContext = new FileSystemXmlApplicationContext("file:/Users/weitong/java_project/spring_test/src/main/resources/applicationContext.xml");
```





## spring的xml学习

![image-20230307103629252](/Users/weitong/Library/Application Support/typora-user-images/image-20230307103629252.png)



### 基本配置

```xml
<! -- 配置UserServiceImpl由Spring容器负责管理 -->
<bean id="userService" class="com.tongwl.service.impl.UserServiceImpl"></bean>
```

```java
applicationContext.getBean("userService");
```

* id不能重复；

* `applicationContext.getBean("userService")`，这里的getBean获取的是bean name， 当<bean>有设置id的时候，id名就是bean名；

* 当bean不存在id的时候，其实name也是存在的，我们可以在applicationContext对象下找到一个beanFactory的属性，再往下找到singletonObjects map对象，可以发现bean名字是存在的。例子：

  ```java
   <bean class="com.tongwl.service.impl.UserServiceImpl"></bean>
  ```

  ```java
  UserService userService = (UserService) applicationContext.getBean("com.tongwl.service.impl.UserServiceImpl#0");
  // or
  applicationContext.getBean("com.tongwl.service.impl.UserServiceImpl"); // 在beanFactory ->aliasMap下可以看到
  ```

  ![image-20230307112004081](/Users/weitong/Library/Application Support/typora-user-images/image-20230307112004081.png)

  ![image-20230307125755364](/Users/weitong/Library/Application Support/typora-user-images/image-20230307125755364.png)

  



### bean的别名

可以使用name属性设置bean的别名，使用逗号隔开设置多个别名

```xml
<bean id="userService" name="aaa,bbb,ccc" class="com.tongwl.service.impl.UserServiceImpl"></bean>
```

```java
UserService userService = (UserService) applicationContext.getBean("userService");
UserService userService2 = (UserService) applicationContext.getBean("aaa");
UserService userService3= (UserService) applicationContext.getBean("bbb");
UserService userService4= (UserService) applicationContext.getBean("ccc");
```



### bean的范围配置

```xml
<bean id="userService" scope="prototype" class="com.tongwl.service.impl.UserServiceImpl"></bean>
```

* singleton: 单例模式，默认值，spring容器创建时，就会进行bean的实例化，并存储在容器内部的单例池中，每次getBean时都是从单例池中获取相同的Bean实例；
* prototype: 原型模式，Spring容器初始化时不会创建Bean实例，当调用getBean时才会实例化Bean，每次getBean都会创建一个新的Bean实例。



### bean的延迟加载

ApplicationContext默认会在创建ApplicationContext对象的阶段就创建好所有的bean对象。我们可以使用`lazy-init="true"`使bean对象延迟加载。

```xml
<bean id="userService" lazy-init="true" class="com.tongwl.service.impl.UserServiceImpl"></bean>
```



### bean的初始化方法和销毁方法配置

* 初始化方法：在实例创建后，调用初始化方法
* 销毁方法：在实例销毁前，调用销毁方法

初始化方法：

```xml
<bean id="userService" init-method="init" class="com.tongwl.service.impl.UserServiceImpl"></bean>
```

销毁方法：

```xml
<bean id="userService" destroy-method="destroy" class="com.tongwl.service.impl.UserServiceImpl"></bean>
```

```java
public class UserServiceImpl implements UserService {

    public UserServiceImpl() {
        System.out.println("创建UserServiceImpl实例" + this);
    }


    public void init() {
        System.out.println("init");
    }

    public void destroy() {
        System.out.println("destroy");
    }
}
```

另外一种在实例创建后，调用初始化的方法，就是bean实现类实现`InitializingBean`接口，方法`afterPropertiesSet`还会在`init-method`之前调用。例子：

```java
package com.tongwl.service.impl;

import com.tongwl.dao.UserDao;
import com.tongwl.service.UserService;
import org.springframework.beans.factory.InitializingBean;

public class UserServiceImpl implements UserService, InitializingBean {

    public UserServiceImpl() {
        System.out.println("创建UserServiceImpl实例" + this);
    }

    public void init() {
        System.out.println("init");
    }

    public void destroy() {
        System.out.println("destroy");
    }

    public void afterPropertiesSet() throws Exception {
        System.out.println("hahaha");
    }
}
```



### bean的实例化配置

Spring实例化方式主要有如下两种：

* 构造函数实例化：底层通过构造方法对Bean进行实例化。
* 工厂方式实例化：底层通过调用自定义的工厂方法对Bean进行实例化。



#### 构造函数实例化

##### 无参构造函数实例化

```java
public class UserServiceImpl implements UserService {

    public UserServiceImpl() {
        System.out.println("创建UserServiceImpl实例");
    }
}    
```

```xml
<bean id="userService" class="com.tongwl.service.impl.UserServiceImpl"></bean>
```



##### 有参构造函数实例化

```java
public class UserServiceImpl implements UserService {

    public UserServiceImpl(String username) {
        System.out.println("创建UserServiceImpl实例");
    }
}   
```

```java
<bean id="userService" class="com.tongwl.service.impl.UserServiceImpl">
    <constructor-arg name="username" value="tongwl"></constructor-arg>
</bean>
```

这里的`constructor-arg`标签不是指构造方法参数，乃是指构造参数，能够产生`bean`对象的方法的参数就是构造参数。



#### 工厂方式实例化

##### 静态工厂方法实例化Bean

静态工厂方法在产生Bean时，不需要有对象，直接`类.静态方法`就可以调用。
例子：

```java
// 工厂类

package com.tongwl.factory;

import com.tongwl.dao.UserDao;
import com.tongwl.dao.impl.UserDaoImpl;
import com.tongwl.service.impl.UserServiceImpl;

public class MyBeanFactory1 {
    public static UserDao getUserDao() {
        return new UserDaoImpl();
    }
}
```

```xml
<!-- 这里的class指向工厂类，factory-method指向工厂类的静态方法(该方法返回的对象就作为该bean对象) -->
<bean id="userDao2" class="com.tongwl.factory.MyBeanFactory1" factory-method="getUserDao"></bean>
```



传参数例子：
UserDaoImpl.java

```java
package com.tongwl.dao.impl;

import com.tongwl.dao.UserDao;

public class UserDaoImpl implements UserDao {
    public UserDaoImpl() {
        System.out.println("实例化UserDaoImpl");
    }

    public UserDaoImpl(String userName, int age) {
        System.out.println("调用了有参的构造函数");
    }
}
```

MyBeanFactory.java

```java
package com.tongwl.factory;

import com.tongwl.dao.UserDao;
import com.tongwl.dao.impl.UserDaoImpl;

public class MyBeanFactory1 {
    public static UserDao getUserDao(String username, int age) {
        UserDao userDao = new UserDaoImpl(username, age);
        return userDao;
    }
}
```

application.xml

```xml
<bean id="userDao2" class="com.tongwl.factory.MyBeanFactory" factory-method="getUserDao">
    <constructor-arg name="username" value="tongwl"></constructor-arg>
    <constructor-arg name="age" value="18"></constructor-arg>
</bean>
```



使用bean工厂的好处：

* 可以在bean对象实例化前和后添加相应的逻辑

  ```java
  package com.tongwl.factory;
  
  import com.tongwl.dao.UserDao;
  import com.tongwl.dao.impl.UserDaoImpl;
  import com.tongwl.service.impl.UserServiceImpl;
  
  public class MyBeanFactory1 {
      public static UserDao getUserDao() {
          System.out.println("实例化前添加逻辑");
          UserDao userDao = new UserDaoImpl();
          System.out.println("实例化后添加逻辑");
          return new UserDaoImpl();
      }
  }
  ```

* 可以将一些由外部类的静态方法创建的内部类变成bean实例
  例如：`DriverManager.getConnecton()`可以获取到Connection对象，如果我们想要Spring来管控Connection对象，那么就可以如下配置：

  ```xml
  <bean id="connection" class="com.XXX.DriverManager" factory-method="getConnecton"></bean>
  ```

  

##### 实例工厂方法实例化Bean

实例工厂方法在产生Bean时，因为是一个实例方法，所以需要有对象才能调用方法。

例子：

```java
// 工厂类

package com.tongwl.factory;

import com.tongwl.dao.UserDao;
import com.tongwl.dao.impl.UserDaoImpl;

public class MyBeanFactory2 {
    public UserDao getUserDao() {
        UserDao userDao = new UserDaoImpl();
        return new UserDaoImpl();
    }
}
```

```xml
<!-- 先有一个myBeanFactory的bean实例 -->
<bean id="myBeanFactory2" class="com.tongwl.factory.MyBeanFactory2"></bean>
<!-- 创建userDao3 bean实例，factory-bean指向的是工厂对象，factory-method指向的是工厂对象调用的方法(该方法的返回值就是该bean实例) -->
<bean id="userDao3" factory-bean="myBeanFactory2" factory-method="getUserDao"></bean>
```



传参数例子：

UserDaoImpl.java

```java
package com.tongwl.dao.impl;

import com.tongwl.dao.UserDao;

public class UserDaoImpl implements UserDao {
    public UserDaoImpl() {
        System.out.println("实例化UserDaoImpl");
    }

    public UserDaoImpl(String userName, int age) {
        System.out.println("调用了有参的构造函数");
    }
}
```

MyBeanFactory.java

```java
package com.tongwl.factory;

import com.tongwl.dao.UserDao;
import com.tongwl.dao.impl.UserDaoImpl;

public class MyBeanFactory2 {
    public UserDao getUserDao(String username, int age) {
        System.out.println("调用之前");
        UserDao userDao = new UserDaoImpl(username, age);
        System.out.println("调用之后");
        return userDao;
    }
}
```

application.xml

```xml
		<!-- 先有一个myBeanFactory的bean实例 -->
    <bean id="myBeanFactory2" class="com.tongwl.factory.MyBeanFactory2"></bean>
    <bean id="userDao3" factory-bean="myBeanFactory2" factory-method="getUserDao">
        <constructor-arg name="username" value="lily"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
    </bean>
```



使用bean工厂的好处：

* 可以在bean对象实例化前和后添加相应的逻辑

  ```java
  package com.tongwl.factory;
  
  import com.tongwl.dao.UserDao;
  import com.tongwl.dao.impl.UserDaoImpl;
  
  public class MyBeanFactory2 {
      public UserDao getUserDao(String username, int age) {
          System.out.println("调用之前");
          UserDao userDao = new UserDaoImpl(username, age);
          System.out.println("调用之后");
          return userDao;
      }
  }
  ```

* 可以将一些由外部类的方法创建的内部类变成bean实例



##### 实现FactoryBean规范延迟实例化Bean

有一个接口FactoryBean，当你去实现这个FactoryBean这个接口的时候，就不需要写`factory-method`方法了，因为spring能识别调用的`factory-method`是`getObject`，**且产生的bean并不会在ApplicationContext创建的时候实例化，而是在第一次使用到的时候才实例化。**

例子：
MyBeanFactory3.java

```java
package com.tongwl.factory;

import com.tongwl.dao.UserDao;
import com.tongwl.dao.impl.UserDaoImpl;
import org.springframework.beans.factory.FactoryBean;

public class MyBeanFactory3 implements FactoryBean {

    public Object getObject() throws Exception {
        return new UserDaoImpl();
    }

    public Class<?> getObjectType() {
        return UserDao.class;
    }
}
```

xml

```java
<bean id="userDao3" class="com.tongwl.factory.MyBeanFactory3"></bean>
```



### bean的依赖注入配置

* 通过Bean的set方法注入
* 通过构造Bean的方法进行注入



#### 通过Bean的set方法注入

语法：

```xml
<!-- 传递String, int, boolean等普通的参数 -->
<property name="userName" value="tongwl"></property>
<!-- 传递引用类型 -->
<property name="userDao" ref="userDao2"></property>
<!-- 传递list类型 -->
<property name="stringList">
    <list>
        <value>aaa</value>
        <value>bbb</value>
        <value>ccc</value>
    </list>
</property>
<!-- 传递List<对象类型> -->
<bean id="userService" class="com.william.service.UserServiceImpl">
    <property name="listUserDao">
        <list>
            <bean class="com.william.dao.UserDaoImpl"></bean>
            <bean class="com.william.dao.UserDaoImpl"></bean>
            <bean class="com.william.dao.UserDaoImpl"></bean>
        </list>
    </property>
</bean>

<!-- 传递List<对象类型> -->
<bean id="userDao" class="com.william.dao.UserDaoImpl"></bean>
<bean id="userDao2" class="com.william.dao.UserDaoImpl"></bean>
<bean id="userService" class="com.william.service.UserServiceImpl">
    <property name="listUserDao">
        <list>
            <ref bean="userDao"></ref>
            <ref bean="userDao"></ref>
            <ref bean="userDao2"></ref>
        </list>
    </property>
</bean>
```

例子1：

application.xml

```xml
<bean id="userDao2" class="com.tongwl.dao.impl.UserDaoImpl"></bean>
<bean id="userService" class="com.tongwl.service.impl.UserServiceImpl">
    <constructor-arg name="username" value="tongwl"></constructor-arg>
    <property name="userDao" ref="userDao2"></property>
</bean>
```

UserServiceImp.java

```java
package com.tongwl.service.impl;

import com.tongwl.dao.UserDao;
import com.tongwl.service.UserService;

public class UserServiceImpl implements UserService {
    public UserServiceImpl(String username) {
        System.out.println(username);
        System.out.println("创建UserServiceImpl实例" + this);
    }

    private UserDao userDao;

    // BeanFactory去调用该方法，从容器中获取userDao设置到此处
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
        System.out.println("BeanFactory去调用该方法获得userDao设置到此处：" + userDao);
    }
}
```

UserDaoImpl.java

```java
package com.tongwl.dao.impl;

import com.tongwl.dao.UserDao;

public class UserDaoImpl implements UserDao {
    public UserDaoImpl() {
        System.out.println("实例化UserDaoImpl");
    }

    public UserDaoImpl(String userName, int age) {
        System.out.println("调用了有参的构造函数," + userName + "," + age);
    }
}
```

例子2：
application.xml

```xml
<bean id="userService" class="com.william.service.UserServiceImpl">
    <property name="listUserDao">
        <list>
            <bean class="com.william.dao.UserDaoImpl"></bean>
            <bean class="com.william.dao.UserDaoImpl"></bean>
            <bean class="com.william.dao.UserDaoImpl"></bean>
        </list>
    </property>
</bean>
```

UserServiceImpl.java

```java
package com.william.service;

import com.william.dao.UserDao;

import java.util.List;

public class UserServiceImpl implements UserService {
    private List<UserDao> listUserDao;

    public void setListUserDao(List<UserDao> listUserDao) {
        this.listUserDao = listUserDao;
    }

    public void printListUserDao() {
        System.out.println(this.listUserDao);
    }
}
```



#### 通过构造Bean的方法进行注入

语法：

```xml
<!-- 传递String, int, boolean等普通的参数 -->
<constructor-arg name="username" value="tongwl"></constructor-arg>
<!-- 传递引用类型 -->
<constructor-arg name="userDao" ref="userDao"></constructor-arg>
```



#### 自动装配方式设置

如果被注入的属性类型是Bean引用的话，那么可以在<bean>标签中使用`autowire`属性去配置自动注入方式，属性值有两个：

* byName: 默认值，通过属性名自动装配，即去匹配setXXX与id="xxx" (name = "xxx")是否一致；
* byType: 通过bean的类型从容器中匹配，匹配出多个相同Bean类型时，报错。



byType的例子：
application.xml

```xml
<bean id="userService" class="com.william.service.UserServiceImpl" autowire="byType"></bean>
<bean id="userDao222222" class="com.william.dao.UserDaoImpl"></bean>
```

UserServiceImpl.java

```java
package com.william.dao;

public class UserDaoImpl implements UserDao {
    public UserDaoImpl () {
        System.out.println("UserDaoImpl初始化");
    }
}
```



错误例子：

application.xml

```xml
<bean id="userService" class="com.william.service.UserServiceImpl" autowire="byType"></bean>
<!-- 因为存在两个UserDaoImpl bean，所以上述使用autowire="byType"会报错的 -->
<bean id="userDao222222" class="com.william.dao.UserDaoImpl"></bean>
<bean id="userDao33333" class="com.william.dao.UserDaoImpl"></bean>
```





### 其他配置标签

xml标签分为两大类

* 默认标签：不用额外导入其他命名空间约束的标签，例如<bean>标签
  一共有四种：`<beans>`, `<bean>`, `<import>`, `<alias>`

  * `<beans>`

    一般作为xml配置根标签，其他标签是该标签的子标签

    例子：`beans`里面套`beans`

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
        <beans></beans>
        <beans></beans>
    </beans>  
    ```

    `<beans>`标签，除了经常用的作为根标签外，还可以嵌套在根标签内，使用profile属性切换开发环境
    例子：

    ```xml
    <!-- 指定在配置测试环境下，需要加载的Bean实例 -->
    <beans profile="test">
    </beans>
    
    <!-- 指定在配置开发环境下，需要加载的Bean实例 -->
    <beans profile="dev">
    </beans>
    ```

    可以使用以下两种方式指定被激活的环境：

    * 使用命令行动态参数，虚拟机参数位置加载 `-Dspring.profiles.active=test`
    * 使用代码的方式设置环境变量`System.setProperty("spring.profiles.active", "test")`

    

  * `<bean>`
    Bean的配置标签

  * `<import>`
    导入外部资源标签
    当你的项目变大之后，就会导致一个配置文件内容过多，所以一般会根据业务进行xml的拆分，拆分后，最终通过<import>的方式导入到一个主配置文件中。

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
        <import resource="test1.xml"></import>
        <import resource="test2.xml"></import>
    </beans>
    ```

  * `<alias>`

    指定Bean的别名标签，与在<bean>标签上使用name属性添加别名的方式一样
    例子：
    给UserDaoImpl只能了aaa,bbb,ccc,ddd四个别名

    ```xml
    <bean id="userService" name="aaa,bbb" class="com.william.service.UserServiceImpl"></bean>
    <alias name="userService" alias="ccc"></alias>
    <alias name="userService" alias="ddd"></alias>
    ```

    application.java

    ```java
    package com.william;
    
    import com.william.dao.UserDao;
    import com.william.service.UserService;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;
    
    public class application {
    
        public static void main(String[] args) {
            ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext2.xml");
            UserService userService = (UserService) applicationContext.getBean("userService");
            UserService userService2 = (UserService) applicationContext.getBean("aaa");
            UserService userService3 = (UserService) applicationContext.getBean("bbb");
            UserService userService4 = (UserService) applicationContext.getBean("ccc");
            UserService userService5 = (UserService) applicationContext.getBean("ddd");
        }
    }
    ```

    

* 自定义标签：需要额外引入其他命名空间约束，并通过前缀引用的标签，例如`<context:property-placeholder/> `

<img src="/Users/weitong/Library/Application Support/typora-user-images/image-20230308144246306.png" alt="image-20230308144246306" style="zoom:50%;" />



## Spring的三种getBean方法

* `Object getBean(String beanName)`
  根据beanName从容器中获取Bean实例，要求容器中Bean唯一，返回值是Object，需要强转
  例子：

  ```java
  UserService userService = (UserService) applicationContext.getBean("userService");
  ```

* `T getBean(Class type)`
  根据Class类型从容器中获取Bean实例，要求容器中Bean类型唯一，返回值为Class类型实例，无需强转
  例子：

  ```java
  UserService userService = applicationContext.getBean(UserService.class);
  ```

* `T getBean(String beanName, Class type)`
  根据beanName从容器中获得Bean实例，返回值为Class类型实例，无需强转
  例子：

  ```java
  UserService userService = applicationContext.getBean("userService", UserService.class);
  ```

  
