# java零散知识1-31



1. 一个.java文件只能有一个public类，且类名和文件名需要同名。

   例子：

   App.java

   ```java
   // 正确
   public class App
   {
   		public static void main( String[] args )
       {
           System.out.println( "Hello World!" );
       }
   }
   
   // 错误
   public class App2
   {
   		public static void main( String[] args )
       {
           System.out.println( "Hello World!" );
       }
   }
   
   // 正确
   class App3
   {
   		public static void main( String[] args )
       {
           System.out.println( "Hello World!" );
       }
   }
   ```

2. 程序都是从`main`方法开始执行的。

   ```java
   public class App2
   {
   		public static void main( String[] args )
       {
       }
   }
   ```

   

3. Java有八种基本类型。6种数字类型(4个整数型，两个浮点型)，一种字符类型，还有一种布尔型。

   * byte类型

     * byte 数据类型是8位、有符号的，以二进制补码表示的整数；
     * 最小值是 **-128（-2^7）**；
     * 最大值是 **127（2^7-1）**；
     * 默认值是 **0**；
     * byte 类型用在大型数组中节约空间，主要代替整数，因为 byte 变量占用的空间只有 int 类型的四分之一；
     * 例子：byte a = 100，byte b = -50。

   * short类型

     * short 数据类型是 16 位、有符号的以二进制补码表示的整数；
     * 最小值是 **-32768（-2^15）**；
     * 最大值是 **32767（2^15 - 1）**；
     * short 数据类型也可以像 byte 那样节省空间。一个short变量是int型变量所占空间的二分之一；
     * 默认值是 **0**；
     * 例子：short s = 1000，short r = -20000。

   * int类型

     * int 数据类型是32位、有符号的以二进制补码表示的整数；
     * 最小值是 **-2,147,483,648（-2^31）**；
     * 最大值是 **2,147,483,647（2^31 - 1）**；
     * **一般的整型变量默认为 int 类型；**
     * 默认值是 **0** ；
     * 例子：int a = 100000, int b = -200000。

   * long类型

     * long 数据类型是 64 位、有符号的以二进制补码表示的整数；
     * 最小值是 **-9,223,372,036,854,775,808（-2^63）**；
     * 最大值是 **9,223,372,036,854,775,807（2^63 -1）**；
     * 这种类型主要使用在需要比较大整数的系统上；
     * 默认值是 **0L**；
     * 例子： **long a = 100000L**，**long b = -200000L**。
       "L"理论上不分大小写，但是若写成"l"容易与数字"1"混淆，不容易分辩。所以最好大写。

   * float类型

     * float 数据类型是单精度、占 4 个字节，32位、符合IEEE 754标准的浮点数；
     * **float类型的有效位数是7位；**
     * float 在储存大型浮点数组的时候可节省内存空间；
     * 默认值是 **0.0f**；
     * 浮点数不能用来表示精确的值，如货币；
     * 例子：float f1 = 234.5f。

   * double类型

     * double 数据类型是双精度、占8个字节，64 位、符合 IEEE 754 标准的浮点数；

     * **double类型的有效位数是16位；**

     * 浮点数的默认类型为 double 类型；

     * double类型同样不能表示精确的值，如货币；

     * 默认值是 **0.0d**；

     * double精度更高，但是消耗的内存是float的两倍。

     * 例子：

       ```java
       double   d1  = 7D;
       double   d2  = 7.; 
       double   d3  =  8.0; 
       double   d4  =  8.D; 
       double   d5  =  12.9867; 
       ```

     | 类型   | 大小位数 | 描述   | 取值范围              |
     | ------ | -------- | ------ | --------------------- |
     | Float  | 32       | 单精度 | -3.40E+38~3.40E+38    |
     | Double | 64       | 双精度 | -1.79E+308~-1.79E+308 |

     > [Java 浮点类型 float 和 double 的主要区别](https://www.runoob.com/w3cnote/java-the-different-float-double.html)

   * char类型

     * char 类型是一个单一的 16 位 Unicode 字符；
     * 最小值是 **\u0000**（十进制等效值为 0）；
     * 最大值是 **\uffff**（即为 65535）；
     * char 数据类型可以储存任何字符；
     * 例子：char letter = 'A';。

   * boolean类型

     - boolean数据类型表示一位的信息；
     - 只有两个取值：true 和 false；
     - 这种类型只作为一种标志来记录 true/false 情况；
     - 默认值是 **false**；
     - 例子：boolean one = true。

4. **`byte`, `short`, `int`之间的计算结果都是int类型。**
   
   `byte` + `byte` = `int`；
   
   `byte` + `short` = `int`;
   
   `short` + `short` = `int`；
   
   `int` + `byte` = `int`;
   
   `int` + `short` = `int`;
   
   `int` + `int` = `int`;
   
   `long` + `int` = `long`;
   
   `long` + `long` = `long`；
   
   例子：
   
   ```java
   byte b = 127;
   byte c = 127;
   byte result1 = b + c; // error
   int result2 = b + c; //right
   
   short e = 1;
   short f = 2;
   short result3 = e + f; // error
   int result4 = e + f; // right
   ```
   
5. 定义一个char类型 (使用单引号)

   ```java
   char a = 'a';  //任意单个字符，加单引号。
   char b = '中'; //任意单个中文字，加单引号。
   char c = 111; //整数。0~65535。十进制、八进制、十六进制均可。输出字符编码表中对应的字符。
   ```

6. String类型 (使用双引号)

   ```java
   String str = "test";
   ```

7. char类型和String类型的区别

   * char表示字符，定义时用单引号，只能存储一个字符，如char c=’x’;
     而String表示字符串，定义时用双引号，可以存储一个或多个字符，如String name=”tom”;

   * char是基本数据类型，而String 是一个类，具有面向对象的特征，可以调用方法，如name.length()获取字符串的长度。

8. 引用类型

   - 在Java中，引用类型的变量非常类似于C/C++的指针。引用类型指向一个对象，指向对象的变量是引用变量。这些变量在声明时被指定为一个特定的类型，比如 Employee、Puppy 等。变量一旦声明后，类型就不能被改变了。

   - 对象、数组都是引用数据类型。

   - **所有引用类型的默认值都是null。**
     例子：

     ```java
     // 即使是String类型未赋值，它的默认值也是null;
     
     package org.example;
     
     public final class Test15 {
         static String username;
     
         public static void main(String[] args) {
             System.out.println(username); // null
         }
     }
     ```

   - 一个引用变量可以用来引用任何与之兼容的类型。

9. Java常量

   常量在程序运行时是不能被修改的。

   在 Java 中使用 final 关键字来修饰常量，声明方式和变量类似：

   ```java
   final double PI = 3.1415927;
   ```

   **虽然常量名也可以用小写，但为了便于识别，通常使用大写字母表示常量。**

10. 自动类型转换

    ```
    低  ------------------------------------>  高
    
    byte,short,char —> int —> long —> float —> double 
    ```

11. 数据类型转换必须满足如下规则：

    - **不能对boolean类型进行类型转换。**

    - 不能把对象类型转换成不相关类的对象。

    - 在把容量大的类型转换为容量小的类型时必须使用强制类型转换。

    - 转换过程中可能导致溢出或损失精度，例如：

      ```java
      int i =128;   
      byte b = (byte)i;
      ```

      因为 byte 类型是 8 位，最大值为127，所以当 int 强制转换为 byte 类型时，值 128 时候就会导致溢出。

    - **浮点数到整数的转换是通过舍弃小数得到，而不是四舍五入**，例如：

      ```java
      (int)23.7 == 23;  // true   
      (int)-45.89f == -45; // true
      ```

12. 自动类型转换
    必须满足转换前的数据类型的位数要低于转换后的数据类型，例如: short数据类型的位数为16位，就可以自动转换位数为32的int类型，同样float数据类型的位数为32，可以自动转换为64位的double类型。

    ```java
    public class ZiDongLeiZhuan{
            public static void main(String[] args){
                char c1='a';//定义一个char类型
                int i1 = c1;//char自动类型转换为int
                System.out.println("char自动类型转换为int后的值等于"+i1);
                char c2 = 'A';//定义一个char类型
                int i2 = c2+1;//char 类型和 int 类型计算
                System.out.println("char类型和int计算后的值等于"+i2);
            }
    }
    ```

    ```
    运行结果为:
    char自动类型转换为int后的值等于97
    char类型和int计算后的值等于66
    ```
    
    > **解析：**c2的值为字符 **A** ,查 ASCII 码表可知对应的 int 类型值为65，所以 **i2 = 65 + 1 = 66**。
    
13. 如何进行强制类型转换？

    * 条件：转换的数据类型必须是兼容的。

    *  格式：(type)value type是要强制类型转换后的数据类型 

      例子1:
      
      ```java
      public class Test{
          public static void main(String[] args){
              int i1 = 123;
              byte b = (byte)i1;//强制类型转换为byte
              System.out.println("int强制类型转换为byte后的值等于"+b);
          }
      }
      ```
      
      例子2：
      
      ```java
      // 错误
      public class Test27 {
          public static void main(String[] args) {
              String a = "8";
              int b = (int)a; // 错误，因为String和int类型并不是兼容的
          }
      }
      ```
      
      例子3：
      **String转换成int类型**
      
      ```java
      String num = "8";
      int z = Integer.parseInt(num);
      ```

14. 

    * 整数的默认类型是 int。
    * 小数默认是 double 类型浮点型，**在定义 float 类型时必须在数字后面跟上 F 或者 f。**

15. Java语言支持的变量类型有：
    
    * 实例变量：独立于方法之外的变量，不过没有static修饰。
    * 局部变量：类的方法中的变量。
    * 类变量(静态变量)：独立于方法之外的变量，用static修饰。
    
16. 实例变量：

    * 实例变量声明在一个类中，但在方法、构造方法和语句块之外；

    * 当一个对象被实例化之后，每个实例变量的值就跟着确定；

    * 实例变量可以声明在方法前或者方法后；

      ```java
      // 正确用法
      package org.example;
      
      public class App10 {
      
          public void printNum() {
              System.out.println(num);
          }
      
          private int num = 0; // 申明在方法之后也可以
      
          public static void main(String[] args) {
              App10 app = new App10();
              app.printNum();
          }
      }
      ```

      ```java
      // 错误用法
      package org.example;
      
      public class App10 {
          {
              System.out.println(num); //会在num申明前就执行，所以这里会报错的
          }
      
          private int num = 0;
      }
      ```

    * **访问修饰符可以修饰实例变量；**

    * **实例变量对于类中的方法、构造方法或者语句块是可见的。**一般情况下应该把实例变量设为私有。通过使用访问修饰符可以使实例变量对子类可见；

    * 实例变量具有默认值。**数值型变量的默认值是0，布尔型变量的默认值是false，引用类型变量的默认值是null。**变量的值可以在声明时指定，也可以在构造方法中指定；

    * **实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用对象名：ObjectReference.VariableName。**

      ```java
      // 正确例子
      package org.example;
      
      public final class Test15 {
          private String username;
      
          public static void main(String[] args) {
              Test15 t = new Test15();
              System.out.println(t.username); // null
          }
      }
      ```

      ```java
      // 错误例子
      package org.example;
      
      public final class Test15 {
          private String username;
      
          public static void main(String[] args) {
              System.out.println(username); // username是实例变量，不能像这样访问
          }
      }
      ```

      ```java
      // 正确例子
      
      package org.example;
      
      public final class Test15 {
          private static String username;
      
          public void test() {
              System.out.println(username); // 非静态方法可以直接访问静态变量
          }
      
          public static void main(String[] args) {
              System.out.println(username); // username是静态变量，在静态方法中可以直接访问
          }
      }
      ```

17. 局部变量：

    * 局部变量声明在方法、构造方法或者语句块中；

    * 局部变量在方法、构造方法、或者语句块被执行的时候创建，当它们执行完成后，变量将会被销毁；

    * **访问修饰符不能用于局部变量；**

    * 局部变量只在声明它的方法、构造方法或者语句块中可见；

    * **局部变量没有默认值，所以局部变量被声明后，必须需要赋值后，才可以使用。**

      ```java
      // 正确例子
      package org.example;
      
      public class App8 {
          int a; //实例变量可以不赋值，默认为0
      
          void sayHello() {
              System.out.println(a); // 输出0
          }
      
          public static void main(String[] args) {
              App8 app = new App8();
              app.sayHello();
          }
      }
      ```

      ```java
      // 错误例子
      package org.example;
      
      public class App8 {
          void sayHello() {
              int a;
              System.out.println(a); //错误，因为a是局部变量，必须要赋值
          }
      
          public static void main(String[] args) {
              App8 app = new App8();
              app.sayHello();
          }
      }
      ```

18. 类变量

    * **访问修饰符可以修饰类变量；**
    * **类变量也称为静态变量，在类中以 static 关键字声明，但必须在方法之外。**
    * 无论一个类创建了多少个对象，类只拥有类变量的一份拷贝。
    * 静态变量除了被声明为常量外很少使用，常量是指声明为 public/private，final 和 static 类型的变量。
    * 静态变量在第一次被访问时创建，在程序结束时销毁。
    * 与实例变量具有相似的可见性。但为了对类的使用者可见，大多数静态变量声明为 public 类型。
    * **默认值和实例变量相似。数值型变量默认值是 0，布尔型默认值是 false，引用类型默认值是 null。**变量的值可以在声明的时候指定，也可以在构造方法中指定。此外，静态变量还可以在静态语句块中初始化。
    * 静态变量可以通过：*ClassName.VariableName*的方式访问。
    * **类变量被声明为 public static final 类型时，类变量名称一般建议使用大写字母。**如果静态变量不是 public 和 final 类型，其命名方式与实例变量以及局部变量的命名方式一致。

19. 访问控制修饰符：

    * **private** : 在同一类内可见。使用对象：变量、方法。 **注意：不能修饰类（外部类）和接口**。

    * **default** (即默认，什么也不写）:**在同一包内可见，不使用任何修饰符**。使用对象：类、接口、变量、方法。

    * **protected** : 对同一包内的类和所有子类可见。使用对象：变量、方法。 **注意：不能修饰类（外部类）**。

    * **public** : 对所有类可见。使用对象：类、接口、变量、方法

      protected 需要从以下两个点来分析说明：

      - **子类与基类在同一包中**：被声明为 protected 的变量、方法和构造器能被同一个包中的任何其他类访问；

      - **子类与基类不在同一包中**：那么在子类中，子类实例可以访问其从基类继承而来的 protected 方法，而不能访问基类实例的protected方法。

        ```java
        package p2;
        class MyObject2 {
            protected Object clone() throws CloneNotSupportedException{
               return super.clone();
            }
        }
         
        package p22;
        public class Test2 extends MyObject2 {
            public static void main(String args[]) {
               MyObject2 obj = new MyObject2();
               obj.clone(); // Compile Error         ----（1）
         
               Test2 tobj = new Test2();
               tobj.clone(); // Complie OK         ----（2）
            }
        }
        ```

    * 通过以下表来说明访问权限

    * | 修饰符      | 当前类 | 同一包内 | 子孙类(同一包) | 子孙类(不同包)                                               | 其他包 |
      | :---------- | :----- | :------- | :------------- | :----------------------------------------------------------- | :----- |
      | `public`    | Y      | Y        | Y              | Y                                                            | Y      |
      | `protected` | Y      | Y        | Y              | Y/N（[说明](https://www.runoob.com/java/java-modifier-types.html#protected-desc)） | N      |
      | `default`   | Y      | Y        | Y              | N                                                            | N      |
      | `private`   | Y      | N        | N              | N                                                            | N      |

20. 访问控制和继承

    请注意以下方法继承的规则：

    - 父类中声明为 public 的方法在子类中也必须为 public。
    - 父类中声明为 protected 的方法在子类中要么声明为 protected，要么声明为 public，不能声明为 private。
    - **父类中声明为 private 的方法，不能够被子类继承。**

22. 非访问修饰符

    * static修饰符，用来修饰类方法和变量。
    * final 修饰符，用来修饰类、方法和变量。
      * **final 修饰的类不能够被继承；**
      * **final修饰的方法不能被继承类重新定义；**
      * **final修饰的变量为常量，是不可修改的；**
    * abstract 修饰符，用来创建抽象类和抽象方法。
    * synchronized 和 volatile 修饰符，主要用于线程的编程。

22. Java算术运算符

    * `+`  // 加法
    * `-`  // 减法
    * `*`  // 乘法
    * `/`  // 除法
      **在java中，整数之间的除法运算，不管你的小数有多少位，小数是几，统统舍弃，只返回整数位数字；**
      * `int(byte, short, char)` / `int(byte, short, char)` = `int`
      * `long` / `long` = `long`
    * `%`  // 取余
    * `++`  // 自增+1
    * `--` // 自减-1

    例子：

    ```java
    package org.example;
    
    public class Test17 {
        public static void main(String[] args) {
            int a = 7;
            int b = 2;
            System.out.println(a + b); // 9
            System.out.println(a - b); // 5
            System.out.println(a * b); // 14
            System.out.println(a / b); // 3，除法只返回整数部分，且不是四舍五入的
            System.out.println(++a); // 8
            System.out.println(b--); // 2
        }
    }
    ```

23. `int / int = int;`
    `long / long = long;`

    **`int`除以`int`是`int`类型，如果要得到正确的数，我们需要将其中一个值转换为`float`或则`double`类型。**
    例子：

    ```java
    package org.example;
    
    public class Test17 {
        public static void main(String[] args) {
            int a = 7;
            int b = 2;
            System.out.println(a / b); // 3
            System.out.println((double) a / b); // 3.5
        }
    }
    ```

24. **前缀自增自减法(++a,--a):** 先进行自增或者自减运算，再进行表达式运算。

    **后缀自增自减法(a++,a--):** 先进行表达式运算，再进行自增或者自减运算 

    例子：

    ```java
    package org.example;
    
    public class Test17 {
        public static void main(String[] args) {
            int a = 1;
            int b = 1;
            int c = 2 * ++a;
            int d = 2 * b++;
            System.out.println(c); // 4
            System.out.println(d); // 2
            System.out.println(a); // 2
            System.out.println(b); // 2
        }
    }
    ```

25. 关系运算符
    **关系运算符的运算结果是boolean类型。**

    * `==`

    * `!=`

    * `>`

    * `<`

    * `>=`

    * `<=`

    * 下表为Java支持的关系运算符

      表格中的实例整数变量A的值为10，变量B的值为20：

      | 运算符 | 含义             | 说明                                                         | 实例                            | 结果                 |
      | ------ | ---------------- | ------------------------------------------------------------ | ------------------------------- | -------------------- |
      | >      | 大于运算符       | 只支持左右两边操作数是数值类型(char会转换为数字类型)。如果前面变量的值大于后面变量的值， 则返回 true。 | 2>3                             | false                |
      | >=     | 大于或等于运算符 | 只支持左右两边操作数是数值类型。如果前面变量的值大于等于后面变量的值， 则返回 true。 | 4>=2                            | true                 |
      | <      | 小于运算符       | 只支持左右两边操作数是数值类型。如果前面变量的值小于后面变量的值，则返回 true。 | 2<3                             | true                 |
      | <=     | 小于或等于运算符 | 只支持左右两边操作数是数值类型。如果前面变量的值小于等于后面变量的值， 则返回 true。 | 4<=2                            | false                |
      | ==     | 相等运算符       | **如果进行比较的两个操作数都是数值类型，无论它们的数据类型是否相同，只要它们的值相等，也都将返回 true。 如果两个操作数都是引用类型，只有当两个引用变量的类型具有父子关系时才可以比较，只要两个引用指向的是同一个对象就会返回 true。 java也支持两个 boolean 类型的值进行比较。boolean 类型的变量、值不能与其他任意类型的变量、值使用 == 进行比较。** | 4==4 97=='a' 5.0==5 true==false | true true true false |
      | !=     | 不相等运算符     | 如果进行比较的两个操作数都是数值类型，无论它们的数据类型是否相同，只要它们的值不相等，也都将返回 true。 如果两个操作数都是引用类型，只有当两个引用变量的类型具有父子关系时才可以比较，只要两个引用指向的不是同一个对象就会返回 true。 | 4!=2                            | true                 |

26. `==` 和`!=`

    * **如果进行比较的两个操作数都是数值类型，无论它们的数据类型是否相同，只要它们的值相等，也都将返回 true;**
      基本类型的变量值不能和引用类型的变量值使用 == 进行比较；
      boolean 类型的变量值不能与其他任意类型的变量值使用 == 进行比较；
      如果两个引用类型之间没有父子继承关系，那么它们的变量也不能使用 == 进行比较。

    * == 和 != 可以应用于基本数据类型和引用类型。当用于引用类型比较时，比较的是两个引用是否指向同一个对象，但是实际开发过程多数情况下，只是比较对象的内容是否相当，不需要比较是否为同一个对象。

    例子1：

    ```java
    package org.example;
    
    public class Test17 {
        public static void main(String[] args) {
            int a = 97;
            long b = 97;
            float c = 97.0f;
            double d = 97.0d;
            char e = 'a';
            System.out.println(a == b); // true
            System.out.println(a == c); // true
            System.out.println(a == d); // true
            System.out.println(a == e); // true
            System.out.println(d == e); // true
        }
    }
    ```

    例子2：

    ```java
    package org.example;
    
    public class Test17 {
        public static void main(String[] args) {
            Test17_Child child = new Test17_Child();
            Test17_Child child2 = child;
            Test17_Child child3 = new Test17_Child();
            System.out.println(child == child2); // true
            System.out.println(child == child3); // false
        }
    }
    
    class Test17_Child { }
    ```

    例子3：

    ```java
    package org.example;
    
    public class Test20 {
    
    
        static class A { }
    
        static class B {}
    
        public static void main(String[] args) {
            A a = new A();
            A a2 = new A();
            B b = new B();
    
            System.out.println(a == a2); // false
            System.out.println(a == b); // 直接就编译错了，因为a和b的class并没有父子关系
        }
    }
    ```

    例子4：

    ```java
    package org.example;
    
    public class Test17 {
        public static void main(String[] args) {
            String a = "aaa";
            String b = "aaa";
            String c = new String("aaa");
            System.out.println(a == b); // true
            System.out.println(a == c); // false
        }
    }
    ```

27. 字符串的比较。

    `equals`

    **判断字符串的相等请用`equals`，千万别用 `== `。** String并不是基础类型，而是引用类型，所以String使用`==`比较是判断引用是否指向同一个对象，此种方式很有可能会出现问题，而String的`equals`比较的是String的内容是否相同，所以使用`equals`比较类型的内容相同是安全的。
    例子：

    ```java
    package org.example;
    
    public class Test19 {
        public static void main(String[] args) {
            String a = "abc";
            String b = "abc";
            String c = new String("abc");
            System.out.println(a == b); // true, a和b其实是指向同一个对象(在公共池当中)
            System.out.println(a == c); // false c是new String()出来的对象，和a并不是同一个对象
    				System.out.println(a.equals(c)); // true // 使用equals比较的是String的内容，所以相等
        }
    }
    ```

    `equals`是 java.lang.Object 类的方法，在String类中，String重写了`equals`这个方法。

    
    
    `equalsIgnoreCase()`
    equalsIgnoreCase() 方法的作用和语法与 equals() 方法完全相同，唯一不同的是 equalsIgnoreCase() 比较时不区分大小写。
    例子：
    
    ```java
    package org.example;
    
    public class Test20 {
        public static void main(String[] args) {
            String a = "Abc";
            String b = "ABc";
            System.out.println(a.equals(b)); // false
            System.out.println(a.equalsIgnoreCase(b)); // true
        }
    }
    ```
    
    
    
    `compareTo()`
    比较字符串的大小。
    
    ```
    str.compareTo(String otherstr);
    ```
    
    它会按字典顺序将 str 表示的字符序列与 otherstr 参数表示的字符序列进行比较。如果按字典顺序 str 位于 otherster 参数之前，比较结果为一个负整数；如果 str 位于 otherstr 之后，比较结果为一个正整数；如果两个字符串相等，则结果为 0。
    
    提示：如果两个字符串调用 equals() 方法返回 true，那么调用 compareTo() 方法会返回 0。
    
    例子：
    
    ```java
    package org.example;
    
    public class Test20 {
        public static void main(String[] args) {
            System.out.println((int)'a'); // 97
            System.out.println((int)'A'); // 65
            System.out.println("A".compareTo("a")); // -32
        }
    }
    ```

28. `equals`

    `equals`是 java.lang.Object 类的方法，因为String类重写了equals方法，所以我们分为两种用法。

    * 第一种：String的`equals`，**String重写了`equals`方法，它比较字符串中所包含的内容是否相同。**

      ```java
         // String.java重写的equals方法
         public boolean equals(Object anObject) {
              if (this == anObject) {
                  return true;
              }
              if (anObject instanceof String) {
                  String anotherString = (String)anObject;
                  int n = value.length;
                  if (n == anotherString.value.length) {
                      char v1[] = value;
                      char v2[] = anotherString.value;
                      int i = 0;
                      while (n-- != 0) {
                          if (v1[i] != v2[i])
                              return false;
                          i++;
                      }
                      return true;
                  }
              }
              return false;
          }
      ```
      
    * 第二种：**非字符串对象，`equals`和`==`的效果是一样的，比较的是两个引用变量是否指向同一个对象。**
    
      ```java
      // Object.java的equals方法
      
      		public boolean equals(Object obj) {
              return (this == obj);
          }
      ```
    
    * **对于基本类型，它是不能使用`equals`方法，因为equals方法是对象上的方法。**
    
      ```java
      // 错误
      package org.example;
      
      public class Test20 {
          public static void main(String[] args) {
              int a = 1;
              int b = 2;
              System.out.println(a.equals(b)); // 编译错误
          }
      }
      ```
    
29. 逻辑运算符

    * `&&` **逻辑与运算符，它叫短路与运算符，当两个都为真时，条件才为真；且第一为false时，不会执行第二个判断了；**
    * `&` **逻辑与运算符，当两个都为真时，条件才为真；但是不管第一个是否为false，都会执行第二个条件；**
      `&`也是位运算符，当`&`两边的表达式不是Boolean类型的时候，`&`表示按位操作。
    * **`||` 逻辑或运算符，它叫短路或运算符，当其中一个为真时，条件就为真；且当第一个为true时，不会执行第二个判断了；**
    * **`|` 逻辑或运算符，当其中一个为真时，条件就为真；但是不管第一个是否为true，都会执行第二个条件；**
      `|`也是位运算符，当`|`两边的表达式不是Boolean类型的时候，`|`表示按位操作。
    * `!` 逻辑非运算符，用来反转操作数的逻辑状态。如果条件为true，则逻辑非运算符将得到false。

    例子1：

    ```java
    package org.example;
    
    public class Test21 {
    
        private boolean isTrue (String ipt) {
            System.out.println("is true?");
            return ipt == "true";
        }
    
        public static void main(String[] args) {
            Test21 t = new Test21();
            System.out.println("a" == "b" && t.isTrue("haha"));
        }
    }
    
    // 输出以下内容
    // false
    ```

    例子2:

    ```java
    package org.example;
    
    public class Test21 {
    
        private boolean isTrue (String ipt) {
            System.out.println("is true?");
            return ipt == "true";
        }
    
        public static void main(String[] args) {
            Test21 t = new Test21();
            System.out.println("a" == "a" && t.isTrue("haha"));
        }
    }
    
    // 输出以下内容
    // is true?
    // false
    ```

    例子3：

    ```java
    package org.example;
    
    public class Test21 {
    
        private boolean isTrue (String ipt) {
            System.out.println("is true?");
            return ipt == "true";
        }
    
        public static void main(String[] args) {
            Test21 t = new Test21();
            System.out.println("a" == "b" & t.isTrue("haha"));
        }
    }
    
    // 输出以下内容
    // is true?
    // false
    ```

    例子4：

    ```java
    public class Test22 {
    
        private boolean isTrue(String ipt) {
            System.out.println("is true?");
            return ipt == "true";
        }
    
        public static void main(String[] args) {
            Test22 t = new Test22();
            System.out.println(1 == 1 || t.isTrue("b"));
        }
    }
    
    // 输出以下内容
    // true
    ```

    例子5：

    ```java
    public class Test22 {
    
        private boolean isTrue(String ipt) {
            System.out.println("is true?");
            return ipt.equals("true");
        }
    
        public static void main(String[] args) {
            Test22 t = new Test22();
            System.out.println(1 == 0 || t.isTrue("true"));
        }
    }
    
    // 输出以下内容
    // is true?
    // true
    ```

    例子6：

    ```java
    package org.example;
    
    public class Test22 {
    
        private boolean isTrue(String ipt) {
            System.out.println("is true?");
            return ipt.equals("true");
        }
    
        public static void main(String[] args) {
            Test22 t = new Test22();
            System.out.println(1 == 1 | t.isTrue("true"));
        }
    }
    
    // 输出以下内容
    // is true?
    // true
    ```

30. 三元运算符

    ```java
    variable x = (expression) ? value if true : value if false
    ```

31. `instanceof`运算符
    该运算符用于操作对象实例，检查该对象是否是一个特定类型（类类型或接口类型）。

    instanceof运算符使用格式如下：

    ```java
    ( Object reference variable ) instanceof  (class/interface type)
    ```

    例子：

    ```java
    public class Test23 {
    
        static class A {}
    
        public static void main(String[] args) {
            System.out.println("a" instanceof String); // true
            A a = new A();
            System.out.println(a instanceof A); // true
        }
    }
    ```

    