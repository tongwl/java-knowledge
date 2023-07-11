# mybatis实现例子



## 数据库准备

首先我们准备一张user表

```sql
CREATE TABLE user (
		id int(20) NOT NULL AUTO_INCREMENT,
    name varchar(30) DEFAULT NULL,
    password varchar(30) DEFAULT NULL,
    PRIMARY KEY (id)
);

INSERT INTO user (id, name, password) VALUES (1, '村雨遥', '123456'),(2, '张三', '1234567'),(3, '李四', '12345678');
```



## 使用maven引入mybatis和MySQL包

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.5</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
</dependency>
```



## 编写mybatis实例

1. 编写 MyBatis 核心配置文件，在 `main/resources` 文件下新建 `mybatis-config.xml` ，配置数据库；

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "https://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <property name="driver" value="com.mysql.jdbc.Driver"/>
                   <property name="url" value="jdbc:mysql://tidb-lab.webex.com:4000/ceshi?useSSL=true&amp;enabledTLSProtocols=TLSv1.2&amp;allowPublicKeyRetrieval=true&amp;autoReconnect=true"/>
                   <property name="username" value="udp"/>
                   <property name="password" value="K$6Uu8U$JlcJWXeT"/>
               </dataSource>
           </environment>
       </environments>
       <mappers>
           <mapper resource="UserMapper.xml"></mapper>
       </mappers>
   </configuration>
   ```

2. 创建实体类User

   ```java
   package com.william2.pojo;
   
   public class User {
       private int id;
       private String user;
       private String password;
   
       public int getId() {
           return id;
       }
   
       public void setId(int id) {
           this.id = id;
       }
   
       public String getUser() {
           return user;
       }
   
       public void setUser(String user) {
           this.user = user;
       }
   
       public String getPassword() {
           return password;
       }
   
       public void setPassword(String password) {
           this.password = password;
       }
   }
   ```

3. 编写Mapper接口

   ```java
   package com.william2.dao;
   
   import com.william2.pojo.User;
   
   import java.util.List;
   
   public interface UserDao {
       public List<User> getUser();
   }
   ```

4. 添加 `UserMapper.xml` 配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.william2.dao.UserDao">
       <select id="getUser" resultType="com.william2.pojo.User">
           select * from user
       </select>
   </mapper>
   ```

5. 添加Application.java启动运行

   ```java
   package com.william2;
   
   import com.william2.dao.UserDao;
   import com.william2.pojo.User;
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   
   import java.io.IOException;
   import java.io.InputStream;
   import java.util.List;
   
   public class Application {
       public static void main(String[] args) throws IOException {
           InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
           SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           try(SqlSession session = sqlSessionFactory.openSession()) {
               UserDao mapper = session.getMapper(UserDao.class);
               List<User> list = mapper.getUser();
               for(User user: list) {
                   System.out.println(user);
               }
           }
       }
   }
   ```

