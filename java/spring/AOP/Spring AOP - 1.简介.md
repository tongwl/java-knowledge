# Spring AOP - 简介

![image-20230523171427774](/Users/weitong/Library/Application Support/typora-user-images/image-20230523171427774.png)



![image-20230523171736517](/Users/weitong/Library/Application Support/typora-user-images/image-20230523171736517.png)







## 模拟实现AOP

AOP的实现借助于动态代理技术，下面我们简单模拟来实现下AOP。

我们要实现的功能是在包`com.tongwl.service.impl`下的所有方法执行前和执行后都插入日志。

步骤：

1. 在包`com.tongwl.service.impl`创建目标对象UserService.java
2. 创建增强对象MyAdvice.java
3. 创建实现接口BeanPostProcessor的实现类MockAopBeanPostProcessor，在此实现中使用动态代理对目标对象的方法进行日志记录



UserService.java

```java
package com.tongwl.service;

public interface UserService {
    void show1();
    void show2();
}
```



UserServiceImpl.java

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



MyAdvice.java

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



MockAopBeanPostProcessor.java

```java
package com.tongwl;

import com.tongwl.advice.MyAdvice;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

@Component
public class MockAopBeanPostProcessor implements BeanPostProcessor, ApplicationContextAware {
    private ApplicationContext applicationContext;

    public Object postProcessAfterInitialization(final Object bean, String beanName) throws BeansException {
        // 目标是对UserServiceImpl的show1和show2方法进行增强，增强的方法在MyAdvice中
        // 问题1：筛选service.impl包下的所有类的所有方法都可以进行增强
        // 问题2：如何获取MyAdvice 解决方案：可以将MyAdvice将给Spring管理，然后从Spring容器中获取MyAdvice
        if (bean.getClass().getPackage().getName().equals("com.tongwl.service.impl")) {
            System.out.println("进来了");
            // 生成当前Bean的Proxy对象
            Object beanProxy = Proxy.newProxyInstance(bean.getClass().getClassLoader(), bean.getClass().getInterfaces(), new InvocationHandler() {
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    // 执行增强对象的before方法
                    MyAdvice myAdvice = applicationContext.getBean(MyAdvice.class);
                    myAdvice.beforeAdvice();

                    // 执行目标对象的目标方法
                    Object result = method.invoke(bean, args);

                    // 执行增强对象的after方法
                    myAdvice.afterAdvice();
                    return result;
                }
            });
            return beanProxy;
        }

        return null;
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
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
    <context:component-scan base-package="com.tongwl"></context:component-scan>
</beans>
```



application.java

```java
package com.tongwl;

import com.tongwl.service.UserService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserService userService = applicationContext.getBean(UserService.class);
        userService.show1();
        userService.show2();
    }
}
```
