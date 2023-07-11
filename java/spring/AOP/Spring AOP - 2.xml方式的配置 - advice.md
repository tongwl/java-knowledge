# Spring AOP - 2.xml方式的配置 - advice

**注意：一般我们使用aspect的配置方式。**

![image-20230531170344264](/Users/weitong/Library/Application Support/typora-user-images/image-20230531170344264.png)



实现Advice子接口的类 + `<aop:advisor>`配合使用。

例子：

MyAdvice2.java

```java
package com.tongwl.advice;

import org.springframework.aop.AfterReturningAdvice;
import org.springframework.aop.MethodBeforeAdvice;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;

/**
 * MethodBeforeAdvice 前置
 * AfterReturningAdvice 后置
 */
@Component("myAdvice2")
public class MyAdvice2 implements MethodBeforeAdvice, AfterReturningAdvice {
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println("后置通知...............");
    }

    public void before(Method method, Object[] objects, Object o) throws Throwable {
        System.out.println("前置通知............");
    }
}
```

或则

```java
package com.tongwl.advice;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;
import org.springframework.aop.AfterReturningAdvice;
import org.springframework.aop.MethodBeforeAdvice;
import org.springframework.stereotype.Component;

import java.lang.reflect.Method;

/**
 * MethodInterceptor 环绕
 */
@Component("myAdvice2")
public class MyAdvice2 implements MethodInterceptor {
    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        System.out.println("环绕前........");
        Object res = methodInvocation.getMethod().invoke(methodInvocation.getThis(), methodInvocation.getArguments());
        System.out.println("环绕后........");
        return res;
    }
}
```

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

        <!-- aop配置 -->
        <aop:config>
            <aop:pointcut id="myPointcut" expression="execution(void com.tongwl.service.impl.UserServiceImpl.show1())"/>
          
          	<!-- advice-ref指向了实现Advice接口的类 -->
            <aop:advisor advice-ref="myAdvice2" pointcut-ref="myPointcut"></aop:advisor>
        </aop:config>

</beans>
```