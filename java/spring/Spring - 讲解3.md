# Spring - 讲解3

![image-20230309173925424](/Users/weitong/Library/Application Support/typora-user-images/image-20230309173925424.png)

![image-20230309213429854](/Users/weitong/Library/Application Support/typora-user-images/image-20230309213429854.png)

![image-20230330112316247](/Users/weitong/Library/Application Support/typora-user-images/image-20230330112316247.png)



## BeanFactoryPostProcessor

它只会执行一次。

![image-20230310095110336](/Users/weitong/Library/Application Support/typora-user-images/image-20230310095110336.png)

注意：实现了该接口的类需要交给Spring容器管理才能成为Spring的BeanFactory postProcessor。所以要在xml中也将这个类转换为bean，也就是交由spring管理。



例子1：修改一个bean对象。
自定义一个MyBeanFactoryPostProcessor类实现BeanFactoryPostProcessor

```java
package com.william.processor;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;

public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        //修改某个BeanDefinition，将它的class设置为UserServiceImpl
        BeanDefinition beanDefinition = beanFactory.getBeanDefinition("userDao");
        beanDefinition.setBeanClassName("com.william.service.UserServiceImpl");

        System.out.println(beanFactory);
        System.out.println("beanDefinitionMap填充完成后回调该方法");
    }
}
```

application.xml

```xml
<bean id="userDao" class="com.william.dao.UserDaoImpl"></bean>
<!-- 将MyBeanFactoryPostProcessor交给spring管理 -->
<bean class="com.william.processor.MyBeanFactoryPostProcessor"></bean>
```

application.java

```java
package com.william;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class application {

    public static void main(String[] args) throws Exception {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext2.xml");
        Object userDao = applicationContext.getBean("userDao");
    }
}
```



例子2：使用BeanFactoryPostProcessor注入未被spring管理的类。

定义一个MyBeanFactoryPostProcessor实现接口BeanFactoryPostProcessor

```java
package com.william.processor;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.BeanFactoryPostProcessor;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.support.DefaultListableBeanFactory;
import org.springframework.beans.factory.support.RootBeanDefinition;

public class MyBeanFactoryPostProcessor2 implements BeanFactoryPostProcessor {
    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {
      	// 自定义一个BeanDefinition，并将它注入Spring管理
        BeanDefinition definition = new RootBeanDefinition();
        definition.setBeanClassName("com.william.dao.PersonImpl");
        DefaultListableBeanFactory defaultListableBeanFactory = (DefaultListableBeanFactory)configurableListableBeanFactory;
        defaultListableBeanFactory.registerBeanDefinition("personImpl", definition);

    }
}
```



Spring提供了一个BeanFactoryPostProcessor的子接口**BeanDefinitionRegistryPostProcessor**专门用于注册BeanDefinition操作。

例子：

```java
package com.william.processor;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.beans.factory.config.ConfigurableListableBeanFactory;
import org.springframework.beans.factory.support.BeanDefinitionRegistry;
import org.springframework.beans.factory.support.BeanDefinitionRegistryPostProcessor;
import org.springframework.beans.factory.support.RootBeanDefinition;

public class MyBeanDefinitionRegistryPostProcessor implements BeanDefinitionRegistryPostProcessor {
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry beanDefinitionRegistry) throws BeansException {
      	// 定义一个BeanDefinition对象
        BeanDefinition definition = new RootBeanDefinition();
        definition.setBeanClassName("com.william.dao.PersonImpl");
      	// 将BeanDefinition对象注入到spring中
        beanDefinitionRegistry.registerBeanDefinition("personImpl", definition);
    }

    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {

    }
}
```





## BeanPostProcessor

每个Bean实例化之后，都会执行一次。

![image-20230320185308481](/Users/weitong/Library/Application Support/typora-user-images/image-20230320185308481.png)



BeanPostProcessor接口有两个方法：`postProcessBeforeInitialization`和`postProcessAfterInitialization`，他们是`@Nullable`的，也就是类实现这个接口可以不实现这两个方法，他们的执行时机是：

```
bean对象实例化(调用构造方法) -> postProcessBeforeInitialization -> 初始化方法(init-method) -> postProcessAfterInitialization
```

例子1：

MyBeanPostProcessor.java

```java
package com.william.processor;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPostProcessor implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + ":postProcessBeforeInitialization");
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + ":postProcessAfterInitialization");
        return bean;
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
    <bean id="animal" init-method="init" class="com.william.dao.AnimalImpl"></bean>
    <bean class="com.william.processor.MyBeanPostProcessor"></bean>
</beans>
```

AnimalImpl.java

```java
package com.william.dao;

public class AnimalImpl implements Animal {
    public AnimalImpl() {
        System.out.println("animal constructor");
    }
  
  	public void init() {
        System.out.println("animal init");
    }
}
```

Application.java

```java
package com.william;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) throws Exception {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext3.xml");
    }
}

// 运行结果
animal constructor
animal:postProcessBeforeInitialization
animal init
animal:postProcessAfterInitialization
```

上述运行结果说明`postProcessBeforeInitialization`和`postProcessAfterInitialization`这两个方法是在bean实例化后执行的，

postProcessBeforeInitialization在初始化方法`int`之前执行，而`postProcessAfterInitialization`在它之后。



例子2：修改bean的内容

MyBeanPostProcessor2.java

```java
package com.william.processor;

import com.william.dao.Animal;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPostProcessor2 implements BeanPostProcessor {
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        if (bean instanceof Animal) {
            ((Animal) bean).setName("dog");
        }
        System.out.println(beanName + ":postProcessBeforeInitialization");
        return bean;
    }

    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + ":postProcessAfterInitialization");
        return bean;
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
    <bean class="com.william.processor.MyBeanPostProcessor"></bean>
</beans>
```

AnimalImpl.java

```java
package com.william.dao;

public class AnimalImpl implements Animal {

    private String name;

    public AnimalImpl() {
        System.out.println("animal constructor");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

Applicaiton.java

```java
package com.william;

import com.william.dao.Animal;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {

    public static void main(String[] args) throws Exception {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext3.xml");
        Animal animal = applicationContext.getBean(Animal.class);
        System.out.println("animal name is " + animal.getName());
    }
}

//运行结果
animal constructor
animal:postProcessBeforeInitialization
animal:postProcessAfterInitialization
animal name is dog
```
