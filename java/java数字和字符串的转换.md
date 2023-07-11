# java数字和字符串的转换

1. 将字符串转换为数字类型(以int为例)

   * 使用`Integer.parseInt(Strings)`方法
     例子1

     ```java
     public class Test39 {
         public static void main(String[] args) {
             String s = "10";
             int i = Integer.parseInt(s);
             System.out.println(i); // 10
         }
     }
     ```

     > Integer.parseInt返回的是int类型，推荐使用。

   * 使用`Integer.valueOf(String s)`方法
     例子1：

     ```java
     public class Test35 {
         public static void main(String[] args) {
             String s = "123";
             int i = Integer.valueOf(s);
             System.out.println(i); // 123
         }
     }
     ```

     例子2：

     ```java
     public class Test35 {
         public static void main(String[] args) {
             String s = "123.12";
             double i = Double.valueOf(s);
             System.out.println(i); // 123.12
         }
     }
     ```

     > Integer.valueOf返回的是Integer对象，需要再转到int类型，所以更推荐`parseInt`方式。

2. 将数字转换为字符串类型

   * **String s = String.valueOf(i);**
   * **String s = Integer.toString(i);**
   * **String s = "" + i;**

