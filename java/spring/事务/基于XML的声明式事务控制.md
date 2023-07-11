

# 基于XML的声明式事务控制



![image-20230610202928643](/Users/weitong/Library/Application Support/typora-user-images/image-20230610202928643.png)

## 例子

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"

       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd">

    <!-- 配置Spring注解生效的扫描范围 -->
    <context:component-scan base-package="com.tongwl"></context:component-scan>

    <!-- 配置配置文件的位置(使用${}读取里面的内容) -->
    <context:property-placeholder location="classpath:jdbc.properties" />

    <!-- //////// 以下数据库相关 //////// -->
    <!-- 数据库连接 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${driverClassName}"></property>
        <property name="url" value="${url}"></property>
        <property name="username" value="${username}"></property>
        <property name="password" value="${password}"></property>
    </bean>
    <!-- 将SqlSessionFactoryBean存储到spring容器 -->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 扫描指定的包，产生Mapper对象存储到Spring容器 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.tongwl.mapper" />
    </bean>
    <!-- //////// 以下数据库相关 //////// -->

    <!-- //////// 以下事务控制相关 //////// -->
    <!-- 配置平台事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

    <!-- 配置Spring提供好的Advice -->
    <!-- transaction-manager指事务管理器，mybatis这里配置DataSourceTransactionManager -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!--
                配置不同的方法的事务属性
                name: 方法的名称，*表示通配符，代表任意方法；
                      也可以直接通过方法名取指定，比如 <tx:method name="registerUser"/>;
                      也可以使用字符串+通配符的方式配置，比如 <tx:method name="add*"/>; 表示add开头的方法(addUser, addAccount, addOrder)
               isolation: 事务的隔离级别(解决)
               timeout: 超时时间，默认是-1，表示没有超时时间；单位是秒，比如timeout="3"表示3秒后数据库执行没有完成，则超时；
               read-only: 默认false，一般查询操作才可以设置read-only, update/delete等操作会报错，read-only: true可以提高性能;
               propagation: 事务的传播行为，解决业务方法调用业务方法(事务嵌套问题)
            -->
            <tx:method name="transferMoney22" isolation="DEFAULT" read-only="false" timeout="-1" propagation="REQUIRED"/>
<!--            <tx:method name="registerUser" />-->
<!--            <tx:method name="add*" />-->
        </tx:attributes>
    </tx:advice>

    <!-- 事务增强的aop -->
    <aop:config>
        <!-- 配置切点表达式 -->
        <aop:pointcut id="txPointcut" expression="execution(* com.tongwl.service.*.*(..))"/>

        <!-- 配置织入关系 advice-ref引用Spring提供好的-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut" />
    </aop:config>
    <!-- //////// 以上事务控制相关 //////// -->
</beans>
```



jdbc.properties (缺少username和password)

```properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://tidb-lab.webex.com:4000/test2?characterEncoding=utf8&useSSL=true&allowMultiQueries=true&rewriteBatchedStatements=true&autoreconnect=true&enabledTLSProtocols=TLSv1.2
username=
password=
```



Application.java

```java
package com.tongwl;

import com.tongwl.service.AccountService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Application {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        AccountService accountService = applicationContext.getBean(AccountService.class);
        accountService.transferMoney("lily", "william", 1000);
    }
}

```



AccountServiceImpl.java

```java
package com.tongwl.service.impl;

import com.tongwl.mapper.AccountMapper;
import com.tongwl.service.AccountService;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service("accountService")
public class AccountServiceImpl implements AccountService {

    @Resource
    private AccountMapper accountMapper;

    public void transferMoney(String outAccount, String inAccount, Integer money) {
        accountMapper.decrMoney(outAccount, money);
        int i = 1 / 0;
        accountMapper.incMoney(inAccount, money);
    }
}
```



AccountMapper.java

```java
package com.tongwl.mapper;

import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Update;
import org.springframework.stereotype.Repository;

public interface AccountMapper {
    // 加钱
    @Update("update tb_account set money=money+#{money} where account_name=#{accountName}")
    public void incMoney(@Param("accountName") String accountName, @Param("money") Integer money);

    //减钱
    @Update("update tb_account set money=money-#{money} where account_name=#{accountName}")
    public void decrMoney(@Param("accountName") String accountName, @Param("money") Integer money);

}
```





## 配置细节

参考文档：
https://juejin.cn/post/6844903608224333838

https://javaguide.cn/system-design/framework/spring/spring-transaction.html#%E4%BA%8B%E5%8A%A1%E5%B1%9E%E6%80%A7%E8%AF%A6%E8%A7%A3



Spring框架中，事务管理相关最重要的 3 个接口如下：

* **`PlatformTransactionManager`**： （平台）事务管理器，Spring 事务策略的核心。
* **`TransactionDefinition`**： 事务定义信息(事务隔离级别、传播行为、超时、只读、回滚规则)。
* **`TransactionStatus`**： 事务运行状态。

我们可以把 **`PlatformTransactionManager`** 接口可以被看作是事务上层的管理者，而 **`TransactionDefinition`** 和 **`TransactionStatus`** 这两个接口可以看作是事务的描述。

**`PlatformTransactionManager`** 会根据 **`TransactionDefinition`** 的定义比如事务超时时间、隔离级别、传播行为等来进行事务管理 ，而 **`TransactionStatus`** 接口则提供了一些方法来获取事务相应的状态比如是否新事务、是否可以回滚等等。



### PlatformTransactionManager:事务管理接口

**Spring 并不直接管理事务，而是提供了多种事务管理器** 。Spring 事务管理器的接口是： **`PlatformTransactionManager`** 。

通过这个接口，Spring 为各个平台如 JDBC(`DataSourceTransactionManager`)、Hibernate(`HibernateTransactionManager`)、JPA(`JpaTransactionManager`)等都提供了对应的事务管理器，但是具体的实现就是各个平台自己的事情了。





### TransactionDefinition:事务属性

事务属性包含了 5 个方面：

- 隔离级别
  * 一般用默认就可以了，即使修改，一般**不会选择**隔离级别最低的`TransactionDefinition.ISOLATION_READ_UNCOMMITTED`和隔离级别最高的`TransactionDefinition.ISOLATION_SERIALIZABLE`，**只会选择**`TransactionDefinition.ISOLATION_READ_COMMITTED`或`TransactionDefinition.ISOLATION_REPEATABLE_READ`。
  * 隔离级别越低，隔离性越差，性能越高；隔离级别越高，隔离性越好，但是性能越差。
- 事务超时
  * **其单位是秒，默认值为-1，表示没有超时限制。**
- 传播行为
  * 一般使用默认值`REQUIRED`就够了；其次可能会用`SUPPORTS`。

- 是否只读
  * **默认是false，readonly只能设置在查询数据上，如果在更新/删除等操作上设置readonly="true"则会报错。**
  * **readonly设置为true可以提高性能；**
- 回滚规则



#### 1. 隔离级别

**`TransactionDefinition.ISOLATION_DEFAULT`** :使用后端数据库默认的隔离级别，MySQL 默认采用的 `REPEATABLE_READ` 隔离级别 Oracle 默认采用的 `READ_COMMITTED` 隔离级别.

**`TransactionDefinition.ISOLATION_READ_UNCOMMITTED`** :最低的隔离级别，使用这个隔离级别很少，因为它允许读取尚未提交的数据变更，**可能会导致脏读、幻读或不可重复读**

**`TransactionDefinition.ISOLATION_READ_COMMITTED`** : 允许读取并发事务已经提交的数据，**可以阻止脏读，但是幻读或不可重复读仍有可能发生**

**`TransactionDefinition.ISOLATION_REPEATABLE_READ`** : 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，**可以阻止脏读和不可重复读，但幻读仍有可能发生。**

**`TransactionDefinition.ISOLATION_SERIALIZABLE`** : 最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，**该级别可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

![image-20230613143338420](/Users/weitong/Library/Application Support/typora-user-images/image-20230613143338420.png)



#### 2. 事务超时

 所谓事务超时，就是指一个事务所允许执行的最长时间，如果超过该时间限制但事务还没有完成，则自动回滚事务。在 `TransactionDefinition` 中以 int 的值来表示超时时间，**其单位是秒，默认值为-1，表示没有超时限制。**



#### 3. 传播行为

**事务传播行为是为了解决业务层方法之间互相调用的事务问题**。

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

![image-20230614133248053](/Users/weitong/Library/Application Support/typora-user-images/image-20230614133248053.png)



一般使用默认值`REQUIRED`就够了；其次可能会用`SUPPORTS`。



#### 4. 是否只读 (readonly)

**默认是false，readonly只能设置在查询数据上，如果在更新/删除等操作上设置readonly="true"是无效的。**

**readonly设置为true可以提高性能；**

对于只有读取数据查询的事务，可以指定事务类型为 readonly，即只读事务。只读事务不涉及数据的修改，数据库会提供一些优化手段，适合用在有多条数据库查询操作的方法中。

很多人就会疑问了，为什么我一个数据查询操作还要启用事务支持呢？

拿 MySQL 的 innodb 举例子，根据官网描述：

> MySQL 默认对每一个新建立的连接都启用了`autocommit`模式。在该模式下，每一个发送到 MySQL 服务器的`sql`语句都会在一个单独的事务中进行处理，执行结束后会自动提交事务，并开启一个新的事务。

但是，如果你给方法加上了`Transactional`注解的话，这个方法执行的所有`sql`会被放在一个事务中。如果声明了只读事务的话，数据库就会去优化它的执行，并不会带来其他的什么收益。

如果不加`Transactional`，每条`sql`会开启一个单独的事务，中间被其它事务改了数据，都会实时读取到最新值。

分享一下关于事务只读属性，其他人的解答：

- 如果你一次执行单条查询语句，则没有必要启用事务支持，数据库默认支持 SQL 执行期间的读一致性；
- 如果你一次执行多条查询语句，例如统计查询，报表查询，在这种场景下，多条查询 SQL 必须保证整体的读一致性，否则，在前条 SQL 查询之后，后条 SQL 查询之前，数据被其他用户改变，则该次整体的统计查询将会出现读数据不一致的状态，此时，应该启用事务支持



#### 5. 回滚规则