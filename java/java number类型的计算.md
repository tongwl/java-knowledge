# java number类型的计算和转换

1. Java默认的整数类型是`int`类型。

2. Java默认的浮点数类型是`double`类型。

3. 自动类型转换的优先级：

   ```
   低  ------------------------------------>  高
   
   byte,short,char —> int —> long —> float —> double 
   ```

4. 整数之间的运算，`char`, `byte`, `short`类型自动会转换为`int`类型。

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
   int result2 = b + c; // right
   
   short e = 1;
   short f = 2;
   short result3 = e + f; // error
   int result4 = e + f; // right
   
   byte aa = 4;
   byte bb = 2;
   byte cc = aa / bb; // error
   int dd = aa / bb; // right
   ```

5. 整数之间的除法，得到的也是向下取整的整数(并不是四舍五入)。

   ```java
   public class Test34 {
       public static void main(String[] args) {
           byte a = 3;
           byte b = 2;
           int c = a / b;
           System.out.println(c); // 1
       }
   }
   ```

   如果希望得到正确的浮点数值，需要将至少一方转换为float / double类型。例子：

   ```java
   public class Test34 {
       public static void main(String[] args) {
           byte a = 3;
           byte b = 2;
           float c = a / (float)b;
           System.out.println(c); // 1.5
       }
   }
   ```

6. 浮点数到整数的转换是通过舍弃小数得到(也就是向下取整)，而不是四舍五入方式。
   例子：

   ```java
   public class Test34 {
       public static void main(String[] args) {
           double a = 1.9;
           int b = (int)a;
           System.out.println(b); // 1
       }
   }
   ```

7. 当一个整数和一个浮点数进行计算的时候，整数类型自动会提升为浮点类型进行计算，得到的结果也是浮点类型。
   例子1：

   ```java
   public class Test34 {
       public static void main(String[] args) {
           int a = 1;
           double b = 1.0;
           double c = a + b;
           System.out.println(c); // 2.0
       }
   }
   ```

   例子2：

   ```java
   public class Test34 {
       public static void main(String[] args) {
           int a = 1;
           double b = 1.0;
           double c = b / a;
           System.out.println(c); // 1.0
       }
   }
   ```
   
8. 基础数字类型之间的转换
   类型优先级

   ```java
   低  ------------------------------------>  高
   
   byte,short,char —> int —> long —> float —> double 
   ```

   * 低优先级自动转换为高优先级类型

   * 高优先级需要使用强制转换，转换过程中可能导致溢出或损失精度
     例子：

     ```java
     public class Test35 {
         public static void main(String[] args) {
             int i = 127;
             byte i2 = (byte)i;
             System.out.println(i2); // 127
     
             int a = 128;
             byte a2 = (byte)a;
             System.out.println(a2); // -128, 因为128已经超过了byte的最大范围
         }
     }
     ```

9. Number类之间的转换(Byte, Short, Integer, Long, Float, Double)
   使用`xxxValue()`将 Number 对象转换为xxx数据类型的值并返回。

   | 类型            | 方法及描述                                        |
   | :-------------- | :------------------------------------------------ |
   | byte            | **byteValue() :**以 byte 形式返回指定的数值。     |
   | abstract double | **doubleValue() :**以 double 形式返回指定的数值。 |
   | abstract float  | **floatValue() :**以 float 形式返回指定的数值。   |
   | abstract int    | **intValue() :**以 int 形式返回指定的数值。       |
   | abstract long   | **longValue() :**以 long 形式返回指定的数值。     |
   | short           | **shortValue() :**以 short 形式返回指定的数值。   |

   例子：

   ```java
   public class Test35 {
       public static void main(String[] args) {
           Integer i = 127;
           Byte b = i.byteValue();
           System.out.println(b); // 127
   
           Integer i2 = 200;
           Byte b2 = i.byteValue();
           System.out.println(b2); // 127, 因为200已经超过了byte的最大范围
       }
   }
   ```

10. Test