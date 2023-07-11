# java零散知识47 - 

47. Math常用方法

    * `Math.random()`

      random() 方法用于返回一个随机数，随机数范围为 0.0 =< Math.random < 1.0。

    * `Math.floor()`

      下取整，但是返回的是double类型。

      例子：

      ```java
      double d1 = 1.1;
      double d2 = 1.9;
      System.out.println(Math.floor(d1)); // 1.0
      System.out.println(Math.floor(d2)); // 1.0
      ```

    * `Math.ceil()`

      上取整，但是返回的是double类型。

      例子：

      ```java
      double d1 = 1.1;
      double d2 = 1.9;
      System.out.println(Math.ceil(d1)); // 2.0
      System.out.println(Math.ceil(d2)); // 2.0
      ```

    * `Math.round`

      四舍五入取整，有两种语法格式：

      ```java
      long round(double d)
      int round(float f)
      ```

      例子：

      ```java
      double d1 = 1.1;
      double d2 = 1.9;
      System.out.println(Math.round(d1)); // 1
      System.out.println(Math.round(d2)); // 2
      ```

    * `Math.min`

      返回**两个参数**中的最小值。

      例子：

      ```java
      System.out.println(Math.min(1.1, 1.2)); // 1.1
      System.out.println(Math.min(1, 2)); // 1
      ```

    * `Math.max`

      返回两个参数中的最大值。

      例子：

      ```java
      System.out.println(Math.max(1.1, 1.2)); // 1.2
      System.out.println(Math.max(1, 2)); // 2
      ```

    * `Math.abs`

      返回参数的绝对值。

      例子：

      ```java
      System.out.println(Math.abs(1.1)); // 1.1
      System.out.println(Math.abs(-1)); // 1
      ```

    * `Math.pow()`

      返回第一个参数的第二个参数次方。

      例子：

      ```java
      System.out.println(Math.pow(2,3)); // 8.0
      ```

48. String

    **在java中，String是一个对象。**使用`String str = "abc";`这种方式创建的字符串会放在公共池中，而使用`String str = new String("abc")`创建的对象是在堆上。例子：

    ```java
    // str == str1为true的原因是当两个对象(string)比较时，使用==比较的是是否指向同一个对象，str和str1指向的是同一个存储于公共池的对象，所以他们相等。
    String str = "abc";
    String str1 = "abc1";
    System.out.println(str == str1); // true
    ```

    ```java
    // str == str1为false的原因是当两个对象(string)比较时，使用==比较的是是否指向同一个对象，str和str1使用new String创建，他们是两个不同的对象。
    String str = new String("abc");
    String str2 = new String("abc");
    System.out.println(str == str2); // false
    ```

    对于string的比较，实际运用中，一般比较的是他们的内容是否相等，此时我们应该使用`equals`方法进行比较，千万不要使用`==`。

    ```java
    String str = new String("abc");
    String str2 = "abc";
    System.out.println(str == str2); // false
    System.out.println(str.equals(str2));  // true
    ```

    String 类是不可改变的，所以你一旦创建了 String 对象，那它的值就无法改变了。通过查看String类的源码，可以发现String的内容是使用char的数组存储，且是final定义了，所以一旦创建，它的值就无法改变了。

    ```java
    private final char value[];
    ```

    String类有11种构造方法，例子：

    ```java
    String s = new String(); // ""
    String s2 = new String("ab"); // "ab"
    char[] arr = {'a', 'b'};
    String s3 = new String(arr); // "ab"
    // ...
    ```

    对于未赋值的String，它的默认值是null，对于`new String()`，它的值是`""`空字符串。

    ```java
    public class Test46 {
        private static String s;
        private static String s2 = new String();
        public static void main(String[] args) {
            System.out.println(s); // null
            System.out.println(s2); // ""空字符串
        }
    }
    ```

    String的常用方法总结

    * 获取String的长度: `str.length()`

      ```java
      String s = "abc";
      System.out.println(s.length()); // 3
      ```

    * 连接字符串
      如果需要对字符串做很多修改，请使用`StringBuilder`类或`StringBuffer`类。

      * 使用`+`号

        ```java
        String s = "ab";
        String s2 = "cd";
        String result = s + s2;
        System.out.println(result); // abcd
        ```

      * 使用`concat`

        ```java
        String s = "ab";
        String s2 = "cd";
        String result = s.concat(s2);
        System.out.println(result); // abcd
        ```

    * 字符串的比较

      * `equals` : 比较字符串内容是否相同

        ```java
        String s = "ab";
        String s2 = "ab";
        System.out.println(s.equals(s2)); // true
        String s3 = "ab";
        String s4 = "Ab";
        System.out.println(s3.equals(s4)); // false
        ```

      * `equalsIgnoreCase` : 不区分大小写比较字符串内容是否相同

        ```java
        String s = "ab";
        String s2 = "ab";
        System.out.println(s.equalsIgnoreCase(s2)); // true
        String s3 = "ab";
        String s4 = "Ab";
        System.out.println(s3.equalsIgnoreCase(s4)); // true
        ```

      * `compareTo`: 比较字符串大小

        0表示相等；

        负数表示小于；

        正数表示大于；

        ```java
        String s = "a";
        String s2 = "t";
        String s3 = "z";
        String s4 = "a";
        System.out.println(s.compareTo(s4)); // 0
        System.out.println(s.compareTo(s2)); // -19
        System.out.println(s3.compareTo(s2)); // 6
        ```

      * `compareToIgnoreCase`: 不区分大小写的比较字符串大小

        ```java
        String s = "A";
        String s2 = "t";
        String s3 = "Z";
        String s4 = "a";
        System.out.println(s.compareToIgnoreCase(s4)); // 0
        System.out.println(s.compareToIgnoreCase(s2)); // -19
        System.out.println(s3.compareToIgnoreCase(s2)); // 6
        ```

    * 字符串查找

      * `contains(CharSequence s)` : 判断是否存在某个字符串

        例子：

        ```java
        String str = "abc";
        System.out.println(str.contains("ab")); // true
        ```

      * `indexOf(String s)`

        取得某一个字符串的索引位置，找不到返回-1
        例子：

        ```java
        String str = "abc";
        System.out.println(str.indexOf("a")); // 0
        System.out.println(str.indexOf("d")); // -1
        ```

      * `indexOf(String str,int fromIndex)`

        从指定索引位置开始检索子字符串位置，找不到就返回-1

        例子：

        ```java
        String str = "abc";
        System.out.println(str.indexOf("b", 2)); // -1
        ```

      * `lastIndexOf(String s)`

        从后向前查找指定字符串，找不到返回-1

        例子：

        ```java
        String str = "abcac";
        System.out.println(str.lastIndexOf("a")); // 3
        ```

      * `lastIndexOf(String s, int fromIndex)`

        从指定位置，由后向前查找指定字符串，找不到返回-1。注意：这里的fromIndex指的是从指定的索引开始反向搜索。

        例子：

        ```java
        String str = "abc";
        System.out.println(str.lastIndexOf("b", 0)); // -1，因为是从第0位开始反向搜索，所以其实就是只有一个"b"，所以是-1
        System.out.println(str.lastIndexOf("b", 1)); // 1, 因为是从第index为1位开始反向搜索，所以其实是字符串"ab"，得到的index是1
        ```
        
      * `boolean startsWith(String prefix)`
        `boolean startsWith(String prefix, int fromIndex)`

        判断此字符串是否以指定的前缀开始。
        例子：

        ```java
        String str = "www.baidu.com";
        System.out.println(str.startsWith("www")); // true
        System.out.println(str.startsWith("baidu")); // false
        System.out.println(str.startsWith("baidu", 4)); // true
        ```

      * `boolean endsWith(String suffix)`
        判断字符串是否以指定的后缀结束。
        例子：

        ```java
        String str = "www.baidu.com";
        System.out.println(str.endsWith("com")); // true
        ```

    * 字符串的转换

      * `String replace(CharSequence target, CharSequence replacement)`
        返回一个新的字符串，它是通过用 replacement 替换此字符串中出现的**所有** target 得到的。
        例子:

        ```java
        String str = "abcabc";
        String str2 = str.replace("a", "1");
        System.out.println(str); // "abcabc", str并不会变
        System.out.println(str2); // "1bc1bc"，使用"1"替换所有的"a"
        ```

      * `String replaceAll(String regex, String replacement)`
        返回一个新的字符串，它是通过用 replacement 替换此字符串中出现的**所有**regex得到的，**regex可以是一个正则。**
        例子1：

        ```java
        String str = "abcabc";
        String str2 = str.replaceAll("a", "1");
        System.out.println(str); // "abcabc", str并不会变
        System.out.println(str2); // "1bc1bc"，使用"1"替换所有的"a"
        ```

        例子2：

        ```java
        String str = "abcadc";
        String str2 = str.replaceAll("a[b|d]c", "1"); //a[b|d]c是一个正则字符串
        System.out.println(str); // "abcabc"
        System.out.println(str2); // "11"
        ```

      * `String replaceFirst(String regex, String replacement)`
        返回一个新的字符串，它是通过用 replacement 替换此字符串中出现的**第一个**regex得到的，**regex可以是一个正则。**
        例子1：

        ```java
        String str = "abcabc";
        String str2 = str.replaceFirst("a", "1");
        System.out.println(str); // "abcabc", str并不会变
        System.out.println(str2); // "1bcabc"，使用"1"替换第一个的"a"得到
        ```

        例子2：

        ```java
        String str = "abcadc";
        String str2 = str.replaceFirst("a[b|d]c", "1"); //a[b|d]c是一个正则字符串
        System.out.println(str); // "abcabc"
        System.out.println(str2); // "1abc"
        ```

      * `String substring(int beginIndex)`
        返回一个新的字符串，它是从beginIndex截取至尾的子字符串。
        例子：

        ```java
        String str = "abcd";
        String str2 = str.substring(1);
        System.out.println(str); // "abcd"
        System.out.println(str2); // "bcd"
        ```

      * `String substring(int beginIndex, int endIndex)`

        返回一个新的字符串，它是从beginIndex截取至的endIndex子字符串。(含头不含尾)
        例子：

        ```java
        String str = "abcd";
        String str2 = str.substring(1, 2);
        System.out.println(str); // "abcd"
        System.out.println(str2); // "b"
        ```

      * `String toLowerCase()`
        返回一个新的字符串，它是返回一个转换为全小写的字符串。
        例子：

        ```java
        String str = "ABc";
        String str2 = str.toLowerCase();
        System.out.println(str); // "ABc"
        System.out.println(str2); // "abc"
        ```

      * `String toUpperCase()`
        返回一个新的字符串，它是返回一个转换为全大写的字符串。
        例子：

        ```java
        String str = "ABc";
        String str2 = str.toUpperCase();
        System.out.println(str); // "ABc"
        System.out.println(str2); // "ABC"
        ```

      * `String trim()`
        返回一个新的字符串，它是去除前后空格的字符串。
        例子：

        ```java
        String str = " A B c ";
        String str2 = str.trim();
        System.out.println(str); // " A B c "
        System.out.println(str2); // "A B c"，只会去除前后空格，而不会去除中间的空格
        ```

    * `boolean isEmpty()`

      判断是否是空字符串
      例子：

      ```java
      String str = " ";
      String str2 = "";
      System.out.println(str.isEmpty()); // false, 有空格也不是空字符串
      System.out.println(str2.isEmpty()); // true
      ```

    * `String[] split(String regex)`
      返回一个字符串数组，根据匹配给定的regex正则表达式来拆分字符串。
      例子：

      ```java
      String str = "a,b,c,d";
      String[] arr = str.split(",");
      System.out.println(arr); // 得到一个{"a","b","c","d"}的字符串数组
      ```

    * `String[] split(String regex, int limit)`

      返回一个字符串数组，根据匹配给定的regex正则表达式来拆分字符串，但是限制数组的长度
      例子：

      ```java
      String str = "a,b,c,d";
      String[] arr = str.split(",", 2);
      System.out.println(arr); // 得到一个{"a","b,c,d"}的长度为2的字符串数组
      ```

    * 将char[]转换为String

      ```java
      char[] arr = {'a', 'b'};
      String s3 = new String(arr); // "ab"
      ```

    * 将String转换为char[]

      ```java
      String s = "abc";
      char[] c = s.toCharArray();
      ```

49. 使用`StringUtils`工具类
    StringUtils是JDK提供的String类型操作方法的补充，并且是 null 安全的(即如果输入参数 String 为 null 则不会抛出 NullPointerException ，而是做了相应处理。

    在使用StringUtils工具类之前，先在pom.xml中引入该包。

    ```xml
    <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-lang3</artifactId>
      <version>3.0</version>
    </dependency>
    ```

    StringUtils类基本都是静态方法，常用的方法有以下：

    * `static boolean isEmpty(CharSequence cs)`
      判断字符串是否是空字符串，实现源码：

      ```java
      public static boolean isEmpty(CharSequence cs) {
          return cs == null || cs.length() == 0;
      }
      ```

    * `static boolean isNotEmpty(CharSequence cs)`

      判断字符串不是null也不是非空字符串，实现源码：

      ```java
      public static boolean isNotEmpty(CharSequence cs) {
          return !isEmpty(cs);
      }
      ```

      例子：

      ```java
      String str = " ";
      String str2 = null;
      String str3 = "";
      System.out.println(StringUtils.isNotEmpty(str)); // true
      System.out.println(StringUtils.isNotEmpty(str2)); // false
      System.out.println(StringUtils.isNotEmpty(str3)); // false
      ```

    * `static boolean isBlank(CharSequence cs)`

      判断某字符串是否为空或长度为0或由空白符(whitespace) 构成

      例子：

      ```java
      String str = "    ";
      String str2 = null;
      String str3 = "";
      System.out.println(StringUtils.isBlank(str)); // true
      System.out.println(StringUtils.isBlank(str2)); // true
      System.out.println(StringUtils.isBlank(str3)); // true
      ```

    * `static boolean isNotBlank(CharSequence cs)`
      非isBlank()
      例子：

      ```java
      String str = "    ";
      String str2 = null;
      String str3 = "";
      System.out.println(StringUtils.isNotBlank(str)); // false
      System.out.println(StringUtils.isNotBlank(str2)); // false
      System.out.println(StringUtils.isNotBlank(str3)); // false
      ```

    * `static String trim(String str)`

      去除首尾空格
      例子：

      ```java
      String str = " a b c ";
      String str2 = null;
      System.out.println(StringUtils.trim(str)); // null
      System.out.println(StringUtils.trim(str2)); // "a b c"
      ```

50. StringBuilder类
    String对象一旦被创建就不能修改，对于字符串的`+`操作都会产生一个新的对象，**所以在大量字符串拼接或频繁对某一字符串进行操作时最好使用 StringBuilder。和 String 类不同的是，StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。**

    StringBuilder常用方法：

    * ` public StringBuffer append(String s)`
      将指定的字符串追加到此字符序列。
      例子:

      ```java
      StringBuilder sb = new StringBuilder("a");
      sb.append("b");
      sb.append("c");
      String s = sb.toString();
      System.out.println(s); // "abc"
      ```

    * `public  insert(int offset, String str)`
      将 `str` 参数的字符串插入此序列中。
      例子：

      ```java
      StringBuilder sb = new StringBuilder("a");
      sb.append("b");
      sb.append("c");
      sb.insert(1, "ddd");
      String s = sb.toString();
      System.out.println(s); // "adddbc"
      ```

    * ` public replace(int start, int end, String str)`
      使用给定 `String` 中的字符替换此序列的子字符串中的字符。(start, end含头不含尾)
      例子：

      ```java
      StringBuilder sb = new StringBuilder("a");
      sb.append("b");
      sb.append("c");
      sb.replace(0, 1, "dd");
      String s = sb.toString();
      System.out.println(s); // "ddbc"
      ```

    * `public delete(int start, int end)`
      移除此序列的子字符串中的字符。(start, end含头不含尾)
      例子：

      ```java
      StringBuilder sb = new StringBuilder("a");
      sb.append("b");
      sb.append("c");
      sb.delete(0, 1);
      String s = sb.toString();
      System.out.println(s); // "bc"
      ```

    * ` public StringBuffer reverse()`
       将此字符序列用其反转形式取代。

      ```java
      StringBuilder sb = new StringBuilder("a");
      sb.append("b");
      sb.append("c");
      sb.reverse();
      String s = sb.toString();
      System.out.println(s); // "cba"
      ```

    * `int length()`
       返回长度（字符数）。

    * `String toString()`
      返回此序列中数据的字符串表示形式。

    * `char charAt(int index)
      返回此序列中指定索引处的 `char` 值。`

    * `int indexOf(String str)`
      返回第一次出现的指定子字符串在该字符串中的索引。

    * `int indexOf(String str, int fromIndex)`
      从指定的索引处开始，返回第一次出现的指定子字符串在该字符串中的索引。

    * ` int lastIndexOf(String str) `

      返回最右边出现的指定子字符串在此字符串中的索引。

    *  `int lastIndexOf(String str, int fromIndex) `

      返回 String 对象中子字符串最后出现的位置。

    *  `String substring(int start) `

      返回一个新的 `String`它包含此字符序列当前所包含的字符子序列。

    * ` String substring(int start, int end) `

      返回一个新的 `String`，它包含此序列当前所包含的字符子序列。

51. StringBuffer类
    StringBuffer类和StringBuilder的区别就在于StringBuilder不是线程安全的(不能同步访问)，但是StringBuilder的执行效率更高，所以当没有线程安全的前提条件下，我们使用StringBuilder，否则选择StringBuffer。

52. 

    * 方法一般使用第一个单词应以小写字母作为开头，后面的单词则用大写字母开头写，不使用连接符。例如：**addPerson**。

    * 方法重载：
      简而言之：方法的重载是有参数的不同决定的。

      重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。

      **每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。**

      最常用的地方就是构造器的重载。

      **重载规则:**

      - 被重载的方法必须改变参数列表(参数个数或类型不一样)；
      - 被重载的方法可以改变返回类型；
      - 被重载的方法可以改变访问修饰符；
      - 被重载的方法可以声明新的或更广的检查异常；
      - 方法能够在同一个类中或者在一个子类中被重载。
      - 无法以返回值类型作为重载函数的区分标准。

      例子1：

      ```java
      public class Test1 {
          public static void main(String[] args) {
              Test1_child  test = new Test1_child();
              Test1_child test2 = new Test1_child("tongwl");
              Test1_child test3 = new Test1_child("tongwl", 18);
          }
      }
      
      class Test1_child {
          private String username;
          private int age;
          
          // 构造方法的重载
          public Test1_child() {}
      
      		// 构造方法的重载
          public Test1_child(String username) {
              this.username = username;
          }
      
      		// 构造方法的重载
          public Test1_child(String username, int age) {
              this.username = username;
              this.age = age;
          }
      }
      
      
      ```

      例子2：

      ```java
      public class Test1 {
          public static void main(String[] args) {
              Test1_child  test = new Test1_child();
              test.sayHello();
              test.sayHello("tongwl");
              System.out.println(test.sayHello("tongwl", 18));
          }
      }
      
      class Test1_child { 
          // sayHello的重载
          public void sayHello() {
              System.out.println("hello");
          }
      
        	// sayHello的重载
          public void sayHello(String name) {
              System.out.println(String.format("%s hello", name));
          }
      
        	// sayHello的重载
          public String sayHello (String name, int age) {
              return name + age + " hello";
          }
      }
      ```

53. **在内部类是不能定义静态变量和静态方法**(是可以定义静态常量的，也就是使用final定义的常量)，内部类不能使用定义静态变量/方法的原因分析：
    **内部类的实例化是由外部类实例化之后才加载的，如果外部类还没有实例化，这时候调用内部类的静态成员，此时内部类还没有被加载，却要开始创建静态成员,显示是不合理的。**
    例子1：

    ```java
    public class Test1 {
    
        public class Child {
            public String username = "tongwl"; // 正确，实例变量
    
            public static final String param1 = "18"; // 正确，静态常量
    
            public void test2 () {} // 正确，实例方法
    
            public static int param2 = 18; // 错误，内部类不能存在静态变量
    
            public static final String param3 = new String("haha"); // 错误，new String("haha")不能算是一个常量
    
            public static void test() {} // 错误，静态方法
        }
    }
    ```

    例子2：

    ```java
    public class Test1 {
        public class Child {
            public String username = "tongwl"; // 正确，实例变量
    
            public static final String param1 = "18"; // 正确，静态常量
    
            public void test2 () {
                System.out.println("调用");
            } // 正确，实例方法
        }
        public static void main(String[] args) {
            Test1 test1 = new Test1();
            Test1.Child child = test1.new Child(); // 创建了一个child对象
            System.out.println(child.username); // "tongwl"
            System.out.println(child.param1); // "18"
            System.out.println(Test1.Child.param1); // "18"
            child.test2(); // 调用test2方法
        }
    }
    ```

54. 对象和类都可以直接访问静态变量/方法。

    ```java
    public class Test1 {
        public static void main(String[] args) {
            Test1_child test = new Test1_child();
            System.out.println(test.username); // "tongwl"
            System.out.println(Test1_child.username); // "tongwl"
            test.sayHello();
            Test1_child.sayHello();
        }
    }
    
    class Test1_child {
        public static String username = "tongwl";
    
        public static void sayHello() {
            System.out.println("hello");
        }
    }
    ```

55. Test