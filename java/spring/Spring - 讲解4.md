# Spring - 讲解4

![image-20230330130335337](/Users/weitong/Library/Application Support/typora-user-images/image-20230330130335337.png)

![image-20230330131712319](/Users/weitong/Library/Application Support/typora-user-images/image-20230330131712319.png)

![image-20230330132857250](/Users/weitong/Library/Application Support/typora-user-images/image-20230330132857250.png)

### 循环引用的原理：

![image-20230330133854511](/Users/weitong/Library/Application Support/typora-user-images/image-20230330133854511.png)



![image-20230330134148331](/Users/weitong/Library/Application Support/typora-user-images/image-20230330134148331.png)

上述死循环了



三级缓存

![image-20230330134756864](/Users/weitong/Library/Application Support/typora-user-images/image-20230330134756864.png)





![image-20230330151231323](/Users/weitong/Library/Application Support/typora-user-images/image-20230330151231323.png)





![image-20230330151937440](/Users/weitong/Library/Application Support/typora-user-images/image-20230330151937440.png)

![image-20230330151956717](/Users/weitong/Library/Application Support/typora-user-images/image-20230330151956717.png)



![image-20230330152039755](/Users/weitong/Library/Application Support/typora-user-images/image-20230330152039755.png)

![image-20230330152134978](/Users/weitong/Library/Application Support/typora-user-images/image-20230330152134978.png)







![image-20230331144420591](/Users/weitong/Library/Application Support/typora-user-images/image-20230331144420591.png)

通过实现上述接口分别可以得到ServletContext, BeanFactory, BeanName, ApplicationContext。

例子：
Application.java

```java
package com.william;

import com.william.dao.Animal;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {

    public static void main(String[] args) throws Exception {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext3.xml");
    }
}


// 执行结果
animal constructor
animal
org.springframework.beans.factory.support.DefaultListableBeanFactory@26ba2a48: defining beans [animal]; root of factory hierarchy
org.springframework.context.support.ClassPathXmlApplicationContext@133314b, started on Fri Mar 31 15:37:20 CST 2023
```

AnimalImpl.java

```java
package com.william.dao;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.BeanFactoryAware;
import org.springframework.beans.factory.BeanNameAware;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class AnimalImpl implements Animal, BeanFactoryAware, ApplicationContextAware, BeanNameAware {

    public AnimalImpl() {
        System.out.println("animal constructor");
    }
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        System.out.println(beanFactory);
    }

    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        System.out.println(applicationContext);
    }

    public void setBeanName(String s) {
        System.out.println(s);
    }
}
```

applicationContext3.xml

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
    <bean id="animal" class="com.william.dao.AnimalImpl"></bean>
</beans>
```



![image-20230331154133455](/Users/weitong/Library/Application Support/typora-user-images/image-20230331154133455.png)

![image-20230331154244977](/Users/weitong/Library/Application Support/typora-user-images/image-20230331154244977.png)

![image-20230331154544680](/Users/weitong/Library/Application Support/typora-user-images/image-20230331154544680.png)

![image-20230331154810890](/Users/weitong/Library/Application Support/typora-user-images/image-20230331154810890.png)

