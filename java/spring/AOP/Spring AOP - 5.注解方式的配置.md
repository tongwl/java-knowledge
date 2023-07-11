# Spring AOP - 5.注解方式的配置

在使用xml配置的时候，我们aspect的xml配置如下：

applicationContext.xml

```xml
<!-- 配置目标对象 -->
<bean id="userService" class="com.tongwl.service.impl.UserServiceImpl"></bean>

<!-- 配置通知(增强)对象 -->
<bean id="myAdvice" class="com.tongwl.advice.MyAdvice"></bean>

<!--配置aop -->
<aop:config>
    <!-- 配置切点表达式，目的是指定哪些方法需要被增强 -->
    <aop:pointcut id="myPointcut" expression="execution(void com.tongwl.service.impl.UserServiceImpl.show1())"/>
    <aop:pointcut id="myPointcut2" expression="execution(void com.tongwl.service.impl.UserServiceImpl.show2())"/>


    <!-- 配置织入：目的是执行哪些切点和哪些通知进行结合, ref指定通知类是谁 -->
    <aop:aspect ref="myAdvice">
        <!-- method指定增强方法是什么，pointcut-ref指定增强在哪个方法(pointcut)上 -->
        <aop:before method="beforeAdvice" pointcut-ref="myPointcut"></aop:before>
        <aop:after-throwing method="afterThrowing" pointcut-ref="myPointcut" throwing="throwable"></aop:after-throwing>
    <!--            <aop:around method="aroundAdvice" pointcut="execution(public void com.tongwl.service..UserServiceImpl.show2())"></aop:around>-->
    </aop:aspect>
</aop:config>
```



**在使用注解方式配置的时候，我们也需要三部分：**

* 目标对象 (被增强的对象)
* 通知对象 (增强对象)
* 配置aop织入
* 使用`@EnableAspectJAutoProxy`注解在配置类上表示启用注解

目标对象很简单，只需要把这个对象交给Spring管理即可(使用`@Component`等)；

通知对象也通过`@Component`等方式将其交由Spring管理，同时注解方式的织入是配置在通知对象上的；

以下例子：

目标对象 UserServiceImpl.java

```java
package com.tongwl.service.impl;

import com.tongwl.service.UserService;
import org.springframework.stereotype.Component;


@Component("userService")
public class UserServiceImpl implements UserService {
    public void show1() throws Exception {
        System.out.println("show1...");
    }
}

```

通知对象MyAdvice3.java

```java
package com.tongwl.advice;


import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

/**
 * @Aspect注解表示在此方法上设置AOP织入
 */
@Component
@Aspect
public class MyAdvice3 {

    /**
     * 注解@Before指定了通知类型是Before；里面的内容指定了切点表达式，表示该通知类型会被作用到哪些类的哪些方法上
     */
    @Before("execution(void com.tongwl.service.impl.UserServiceImpl.*(..))")
    public void before() {
        System.out.println("前置的增强");
    }

    @After("execution(void com.tongwl.service.impl.UserServiceImpl.*(..))")
    public void after() {
        System.out.println("后置的增强");
    }

    @AfterReturning("execution(void com.tongwl.service.impl.UserServiceImpl.*(..))")
    public void afterReturning() {
        System.out.println("afterReturning的增强");
    }

    /**
     * @param throwable
     * @AfterThrowing的参数value指定切点表达式，throwing指定的是参数throwable，为的就是throwable可以拿到错误
     */
    @AfterThrowing(value = "execution(void com.tongwl.service.impl.UserServiceImpl.*(..))", throwing = "throwable")
    public void afterThrowing(Throwable throwable) {
        System.out.println("afterThrowing的增强");
        System.out.println(throwable.getMessage());
    }


    /**
     * proceedingJoinPoint指的是正在执行的连接点，也就是切入点
     *
     * @param proceedingJoinPoint
     * @return
     */
    @Around("execution(void com.tongwl.service.impl.UserServiceImpl.*(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕前的增强方法");
        Object result = proceedingJoinPoint.proceed(); // 执行目标方法, 并且执行返回可能有返回值
        System.out.println("环绕后的增强方法");
        return result; //返回目标方法的返回值
    }
}
```

配置类MyConfig.java

```java
package com.tongwl.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;


/**
 * @Configuration 表示这是一个配置类，相当于代替applicationContext.xml
 * @ComponentScan 表示扫描哪些包作为注解生效范围
 * @EnableAspectJAutoProxy 表示启用注解
 */
@Configuration
@ComponentScan({"com.tongwl"})
@EnableAspectJAutoProxy
public class MyConfig {
}
```

启动类Application.java

```java
package com.tongwl;

import com.tongwl.config.MyConfig;
import com.tongwl.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Application {
    public static void main(String[] args) throws Exception {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyConfig.class);
        UserService userService = applicationContext.getBean(UserService.class);
        userService.show1();
    }
}
```



**对于通知对象里面的切点表达式，我们不一定把切点表达式直接放在注解上：**

```java
@After("execution(void com.tongwl.service.impl.UserServiceImpl.*(..))")
```

我们可以使用注解`@Pointcut`把这个切点表达式提取出来：
```java
@Pointcut("execution(void com.tongwl.service.impl.UserServiceImpl.*(..))")
private void myPointcut () {}
```

例子：
```java
package com.tongwl.advice;


import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * @Aspect注解表示在此方法上设置AOP织入
 */
@Component
@Aspect
public class MyAdvice3 {

    @Pointcut("execution(void com.tongwl.service.impl.UserServiceImpl.*(..))")
    private void myPointcut () {}

    /**
     * 注解@Before指定了通知类型是Before；里面的内容指向切点表达式方法
     */
    @Before("myPointcut()")
    public void before() {
        System.out.println("前置的增强");
    }
}
```



