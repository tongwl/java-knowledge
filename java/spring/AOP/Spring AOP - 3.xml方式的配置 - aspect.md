#  Spring AOP - 3.xml方式的配置 - aspect

![image-20230526142859943](/Users/weitong/Library/Application Support/typora-user-images/image-20230526142859943.png)

![image-20230526143424202](/Users/weitong/Library/Application Support/typora-user-images/image-20230526143424202.png)





![image-20230526145131705](/Users/weitong/Library/Application Support/typora-user-images/image-20230526145131705.png)

![image-20230526145339962](/Users/weitong/Library/Application Support/typora-user-images/image-20230526145339962.png)



## 使用xml方式配置AOP

第一步：导入pom包

```xml
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.9.7</version>
</dependency>
```

> spring吸收了org.aspectj的精华，所以spring的AOP需要导入aspectjweaver包。



第二步：准备目标类、准备增强类，并配置给Spring管理

目标类 UserServiceImpl.java

```java
package com.tongwl.service.impl;

import com.tongwl.service.UserService;
import org.springframework.stereotype.Component;


@Component("userService")
public class UserServiceImpl implements UserService {
    public void show1() {
        System.out.println("show1...");
    }

    public void show2() {
        System.out.println("show2...");
    }
}
```

增强类 MyAdvice.java

```java
package com.tongwl.advice;

//增强类，内部提供增强方法

import org.springframework.stereotype.Component;

@Component("myAdvice")
public class MyAdvice {
    public void beforeAdvice () {
        System.out.println("前置的增强");
    }


    public void afterAdvice () {
        System.out.println("后置的增强");
    }

}
```



第三步：配置xml

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"

       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <context:component-scan base-package="com.tongwl"></context:component-scan>

    <!--配置aop -->
    <aop:config>
        <!-- 配置切点表达式，目的是指定哪些方法需要被增强 -->
        <aop:pointcut id="myPointcut" expression="execution(void com.tongwl.service.impl.UserServiceImpl.show1())"/>
        <aop:pointcut id="myPointcut2" expression="execution(void com.tongwl.service.impl.UserServiceImpl.show2())"/>
        

        <!-- 配置织入：目的是执行哪些切点和哪些通知进行结合, ref指定通知类是谁 -->
        <aop:aspect ref="myAdvice">
            <!-- method指定增强方法是什么，pointcut-ref指定增强在哪个方法(pointcut)上 -->
            <aop:before method="beforeAdvice" pointcut-ref="myPointcut"></aop:before>
            <aop:after method="afterAdvice" pointcut-ref="myPointcut2"></aop:after>
        </aop:aspect>
    </aop:config>

</beans>
```



第四步：运行测试
application.java

```java
package com.tongwl;

import com.tongwl.config.MyConfig;
import com.tongwl.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class application {
    public static void main(String[] args) {
        // ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyConfig.class);
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = applicationContext.getBean(UserService.class);
        userService.show1();
        userService.show2();
    }
}

// 输出
前置的增强
show1...
show2...
后置的增强
```



## xml方式配置详解

![image-20230526154445384](/Users/weitong/Library/Application Support/typora-user-images/image-20230526154445384.png)





### 切点表达式的配置方式

1. 将切点表达式配置在外边，然后使用`pointcut-ref`引用，例子：

   ```xml
   <!--配置aop -->
   <aop:config>
       <!-- 切点表达式配置在外边 -->
       <aop:pointcut id="myPointcut" expression="execution(void com.tongwl.service.impl.UserServiceImpl.show1())"/>
       <aop:pointcut id="myPointcut2" expression="execution(void com.tongwl.service.impl.UserServiceImpl.show2())"/>
   
   
       <aop:aspect ref="myAdvice">
           <!-- 使用pointcut-ref引入切点表达式 -->
           <aop:before method="beforeAdvice" pointcut-ref="myPointcut"></aop:before>
           <aop:after method="afterAdvice" pointcut-ref="myPointcut2"></aop:after>
       </aop:aspect>
   </aop:config>
   ```

2. 将切点表达式使用`pointcut`直接配置在配置织入上。

   ```xml
   <aop:aspect ref="myAdvice">
       <!-- 使用pointcut直接配置 -->
       <aop:after method="afterAdvice" pointcut="execution(void com.tongwl.service.impl.UserServiceImpl.show2())"></aop:after>
   </aop:aspect>
   ```

​		



### 切点表达式的语法

![image-20230528200927872](/Users/weitong/Library/Application Support/typora-user-images/image-20230528200927872.png)

例子1：

![image-20230528201455642](/Users/weitong/Library/Application Support/typora-user-images/image-20230528201455642.png)





### 通知类型

![image-20230528202105912](/Users/weitong/Library/Application Support/typora-user-images/image-20230528202105912.png)



```xml
<aop:before method="beforeAdvice" pointcut="execution(public void com.tongwl.service..UserServiceImpl.show2())"></aop:before>
<aop:after method="afterAdvice" pointcut="execution(public void com.tongwl.service..UserServiceImpl.show2())"></aop:after>
<aop:after-returning method="afterAdvice" pointcut="execution(public void com.tongwl.service..UserServiceImpl.show2())"></aop:after-returning>
<aop:after-throwing method="afterAdvice" pointcut="execution(public void com.tongwl.service..UserServiceImpl.show2())"></aop:after-throwing>
<aop:around method="aroundAdvice" pointcut="execution(public void com.tongwl.service..UserServiceImpl.show2())"></aop:around>
```



对于`<aop:before>`、`<aop:after>`、`<aop:after-returning>`、`<aop:after-throwing>`，他们的method都可以是一个无参的方法，例如：

```java
public void beforeAdvice () {
    System.out.println("前置的增强");
}

public void afterAdvice () {
    System.out.println("后置的增强");
}

public void afterReturning() {
    System.out.println("后置的增强");
}

public void afterThrowing() {
    System.out.println("后置的增强");
}
```

但是对于`<aop:around>`，因为它是一个环绕方法，所以需要指定方法执行前和执行后做的操作，所以它的method不能是一个简单的无参方法，例子如下：

```xml
	<aop:aspect ref="myAdvice">
    <aop:around method="aroundAdvice" pointcut="execution(public void com.tongwl.service..UserServiceImpl.show2())"></aop:around>
</aop:aspect>
```

```java
/**
 * proceedingJoinPoint指的是正在执行的连接点，也就是切入点
 * @param proceedingJoinPoint
 * @return
 */
public Object aroundAdvice(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    System.out.println("环绕前的增强方法");
    Object result = proceedingJoinPoint.proceed(); // 执行目标方法, 并且执行返回可能有返回值
    System.out.println("环绕后的增强方法");
    return result; //返回目标方法的返回值
}
```



### 通知方法的参数

![image-20230529204759522](/Users/weitong/Library/Application Support/typora-user-images/image-20230529204759522.png)



#### JoinPoint例子：

```java
/**
 *
 * @param joinPoint 连接点
 */
public void beforeAdvice (JoinPoint joinPoint) {
    System.out.println(joinPoint.getTarget()); // 获取当前的目标对象
    System.out.println(joinPoint.getArgs()); // 获取参数
    System.out.println(joinPoint.getStaticPart()); // 获取表达式execution(void com.tongwl.service.UserService.show1()) 
    System.out.println("前置的增强"
    );
}
```



#### Throwable例子：

如果想要使用Throwable，不仅仅在通知方法上加上Throwable参数，而且需要在xml中进行配置。

applicationContext.xml

```xml
<aop:aspect ref="myAdvice">
    <aop:after-throwing method="afterThrowing" pointcut-ref="myPointcut" throwing="throwable"></aop:after-throwing>
</aop:aspect>
```

通知方法：

```java
public void afterThrowing(Throwable throwable) {
	System.out.println("异常对象是"+ throwable + ", 异常信息是" + throwable.getMessage());
}
```



