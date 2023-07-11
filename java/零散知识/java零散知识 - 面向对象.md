# java零散知识 - 面向对象



## 继承

继承就是子类继承父类的特征和行为，使得子类对象(实例)具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。

**Java不支持多继承，但是支持多重继承。**

![img](https://www.runoob.com/wp-content/uploads/2013/12/java-extends-2020-12-08.png)



#### 继承特性：

* 子类拥有父类非private的属性和方法(包括静态变量)。
* 子类可以拥有自己的属性和方法，即子类可以对父类进行扩展。
* 子类可以重写父类方法。
* 子类必须(通过`super()`)调用父类构造器(对于含有无参构造器的父类，子类可以省略定义构造器。)
* `super()`的显示调用必须放在构造方法的第一行。
* Java 的继承是单继承，但是可以多重继承，单继承就是一个子类只能继承一个父类，多重继承就是，例如 B 类继承 A 类，C 类继承 B 类，所以按照关系就是 B 类是 C 类的父类，A 类是 B 类的父类，这是 Java 继承区别于 C++ 继承的一个特性。
* 提高了类之间的耦合性（继承的缺点，耦合度高就会造成代码之间的联系越紧密，代码独立性越差）。



#### 继承语法：

```java
class 父类 {
}
 
class 子类 extends 父类 {
}
```



#### 简单例子：
Father.java

```java
package Test2;

public class Father {
    private String name;
    private int id;

    public static final String XX = "haha";

    public Father(String name, int id) {
        this.name = name;
        this.id = id;
    }

    public void eat() {
        System.out.println(name + "正在吃");
    }

    public void sleep() {
        System.out.println(name + "正在睡");
    }

    public void introduce() {
        System.out.println("大家好！我是" + id + "号" + name);
    }

    public static void test() {
        System.out.println(XX);
    }
}
```

Child1.java

```java
package Test2;

public class Child1 extends Father{
    public Child1(String username, int id) {
        super(username, id);
    }
}
```

Child2.java

```java
package Test2;

public class Child2 extends Father {
    public Child2 (String username, int id) {
        super(username, id);
    }
}
```

MainTest2.java

```java
package Test2;

public class MainTest2 {
    public static void main(String[] args) {
        Child1 child1 = new Child1("tongwl", 18);
        child1.eat();
        child1.introduce();
        child1.sleep();
        System.out.println(child1.XX);
        child1.test();

        Child2 child2 = new Child2("lilysong", 20);
        child2.eat();
        child2.introduce();
        child2.sleep();
    }
}


//输出
tongwl正在吃
大家好！我是18号tongwl
tongwl正在睡
haha
haha
lilysong正在吃
大家好！我是20号lilysong
lilysong正在睡
```



#### super与this关键字

`super`关键字：我们可以通过super关键字来实现对父类成员的访问，用来引用当前对象的父类。

`this`关键字：指向自己的引用。

例子：
Animal.java

```java
package Test3;

public class Animal {
    void eat () {
        System.out.println("Animal eat");
    }
}
```

Dog.java

```java
package Test3;

public class Dog extends Animal {
    void eat() {
        System.out.println("Dog eat");
    }

    void eatTest() {
        super.eat(); // super指向父类
        this.eat(); // this指向自己
    }
}
```

Test3Main.java

```java
package Test3;

public class Test3Main {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.eat();
        dog.eatTest();
    }
}


// 输出
Dog eat
Animal eat
Dog eat
```



#### `final`修饰

* `final`修饰的类不能被继承；
* `final`修饰的方法不能被子类重写；



#### 构造器

子类是不继承父类的构造器(构造方法)的，它只是调用(隐式或则显式)。如果父类的构造器带有参数，则必须在子类的构造器中显式的通过super关键字调用父类的构造器并配以适当的参数列表。

如果父类构造器没有参数，则在子类中不需要显式的定义构造函数或则显式的使用`super`关键字调用父类构造器，系统会自动调用父类的无参构造器。

例子1：

Father.java

```java
package Test6;

public class Father {
    public Father () {
        System.out.println("调用Father构造器");
    }
}
```

Child.java

```java
package Test6;

public class Child extends Father {
    public static void main(String[] args) {
        Child c = new Child();
    }
}

// 输出
调用Father构造器
```

即使Child.java没有显式的定义构造器，但是编译器自动会给我们加上构造器。

```java
public Child() {
	super();
}
```

例子2：

Father.java

```java
package Test7;

public class Father {
    public Father() {
        System.out.println("调用Father构造器");
    }
}
```

Child.java

```java
package Test7;

public class Child extends Father {
    public Child() {
        //在这相当于省略了super();
        System.out.println("调用Child构造器");
    }

    public static void main(String[] args) {
        Child child = new Child();
    }
}


//输出
调用Father构造器
调用Child构造器
```

例子3：

对于有参构造的父类，子类必须在构造器中使用`super()`手动调用父类构造器。

Father.java

```java
package Test8;

public class Father {
    private String user;

    public Father (String user) {
        this.user = user;
        System.out.println("调用父类构造器：" + this.user);
    }
}
```

Child.java

```java
package Test8;

public class Child extends Father {
    public Child () {
        super("tongwl");
    }

    public static void main(String[] args) {
        Child child = new Child();
    }
}

//输出
调用父类构造器：tongwl
```

例子4：

对于有多个构造器的父类，子类(显式/隐式)调用一个父类构造器即可。

Father.java

```java
package Test9;

public class Father {

    private String username;
    private int age;

    public Father() {
        System.out.println("Father构造器1");
    }

    public Father(String username) {
        this.username = username;
        System.out.println("Father构造器2");
    }

    public Father(String username, int age) {
        this.username = username;
        this.age = age;
        System.out.println("Father构造器3");
    }
}
```

Child.java

```java
package Test9;

public class Child extends Father {
    private String username;
    public Child(String username) {
        super(username, 18);
        this.username = username;
    }a

    public static void main(String[] args) {
        Child child = new Child("tongwl");
    }
}

//输出
Father构造器3
```

例子5：

子类调用父类构造方法必须放在第一行

```java
// right

package Test9;

public class Child extends Father {
    private String username;
    public Child(String username) {
        super(username, 18); // 只能放在这第一行
        this.username = username;
    }a

    public static void main(String[] args) {
        Child child = new Child("tongwl");
    }
}
```

```java
// error

package Test9;

public class Child extends Father {
    private String username;
    public Child(String username) {
        this.username = username;
        super(username, 18); // 因为没有在第一行，所以它报错了
    }a

    public static void main(String[] args) {
        Child child = new Child("tongwl");
    }
}
```

