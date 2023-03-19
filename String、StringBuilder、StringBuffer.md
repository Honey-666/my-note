# String

### **String类**

 *      类的解释
         *          String 类代表字符串
 *      类的位置
         *          java.lang.String
 *      类的构造器
         *          public String()初始化一个新创建的 String 对象，使其表示一个空字符序列。
         *          public String(String original)初始化一个新创建的 String 对象，使其表示一个与参数相同的字符序列；
         *          public String(char[] value)分配一个新的 String，使其表示字符数组参数中当前包含的字符序列。
         *          String(byte[] bytes)通过使用平台的默认字符集解码指定的 byte 数组，构造一个新的 String。
 *      类的方法
         *          判断功能的方法
         *          获取功能的方法
         *          转换功能的方法
         *          分割功能的方法

**String类的特点:**

 *      1.所有的String字面值常量(例如:"abc")都是String类的对象
         *         字符串字面值常量可以调用String类的所有的实例方法
 *      2.字符串是常量；它们的值在创建之后不能更改。
         *          因为底层是一个final修饰的数组
 *      3.String类的底层数据结构是什么?
         *          在JDK8.0(包含)以前,底层数据结构是char[]
         *          在JDK9.0(包含)以后,底层数据结构是byte[]
 *      4.为什么在JDK9.0时将char[]变成byte[]
         *          a.理论上将char[]转换成byte[]内存空间节约一半
         *          b.byte类型可以和二进制位直接进行转换,从而调高效率
 *      5.为什么在字符串中可以支持中文?
         *          String 表示一个 UTF-16(比UTF-8扩容了可以看成他的升级版) 格式的字符串
 *      6.字符串字面值常量有一个特殊的"",""也是一个字符串对象,所以可以调用String实例方法
         *      null也是字面值常量(空常量),作为引用数据类型的默认值,通过null无法直接调用方法,如果间接调用方法的话,发生空指针异常
*      7.当字符串用常量池方式进行赋值的时候,如果这个字符串被final关键字修饰,且赋值的方式是直接赋值的话(不是先定义后初始化),JVM自动将这个字符串当成字面值常量

```java
例：
    public static final String s1 = "HelloWorld";
    public static final String s2 = "HelloWorld";
    public static final String s3 = "Hello";
	public static final String s3 = "Hello";
	
	System.out.println(s1 == s3 + s4);//true
```

```java
通过String类进行初始化时,内存中共有几个对象?并内存中的哪块区域?
       1.String s1 = "abc";
           一共有一个对象,在内存中的常量池
       2.String s2 = "a" + "b" + "c";
           一共有一个对象,在内存中的常量池,因为式子在编译时自动运算完毕,运算符号两边都是常量
       3.String s3 = new String("abc");
           一共有两个对象,先在常量池中创建	一个abc的对象,再堆内存中new一个对象
       4.String s4 = str1 + str2 + str3;
           一共有五个对象,先在常量池中创建三个对象,分别为a,b,c;
           str1 + str2 在堆内存中创建第四个对象  ab
           ab + str3  在堆内存中创建第五个对象 abc
	练习：
    public class StringDemo09 {
    public static void main(String[] args) {
        String s1 = "abc";

        String s2 = "a" + "b" + "c";

        String s3 = new String("abc");

        String str1 = "a";
        String str2 = "b";
        String str3 = "c";

        String s4 = str1 + str2 + str3;
    }
}


```

**String类的常用方法:判断**

 *      public boolean equals (Object anObject)
 *      将此字符串与指定对象进行比较。
 *      public boolean equalsIgnoreCase (String anotherString)
 *      将此字符串与指定对象进行比较，忽略大小写。
 *      public boolean contains(CharSequence s)
 *      contains()判断字符串中是否有传入的字符或字符串 s(代表传入的字符串或字符)，有返回true无返回false
 *      public boolean endsWith(String suffix)
 *      判断此字符串是否以suffix（传入的字符或字符串）的后缀结束。
 *      public boolean startsWith(String prefix)
 *      判断此字符串是否以prefix（传入的字符或字符串）指定的前缀开始。
 *      public boolean isEmpty()
 *      当且仅当 length() 为 0 时返回 true。
 *      public boolean startsWith(String prefix,int toffset)
 *      判断此字符串从指定索引开始是否以prefix（传入的字符或字符串）开始的。
 *      String的equals()在实际开发中的注意事项:
         *      字面值字符串或者一个固定的字符串.equals(不断变化的字符串)避免空指针异常（就是把已知的字符串去.equals(未知的字符串)）

**String类的常用方法:获取**

 *      public int length ()
 *      返回此字符串的长度。
 *      public String concat (String str)
 *      将指定的字符串连接到该字符串的末尾。
 *      public char charAt (int index)
 *      返回指定索引处的 char值（char类型的字符）。
 *      public int indexOf (String str)
 *      返回str(传入的字符或字符串)第一次出现在该字符串内的索引。
 *      public String substring (int beginIndex)
 *      传入一个索引，截取从这个索引开始到索引结束的一个字符串(这里遵循左闭右开的原则)。
 *      public String substring (int beginIndex, int endIndex)
 *      传入两个索引，表示截取从第一个索引开始，到第二个索引结束的一个字符串，然后进行返回(这里遵循左闭右开的原则)。

 **String类的常用方法:转换**
 *      public char[] toCharArray ()
 *      将此字符串转换为新的字符数组（char[]类型的数组）。
 *      public byte[] getBytes ()
 *      使用平台的默认字符集将该 String编码转换为新的字节数组（idea默认使用UTF-8的方式将字符转换为三组数字，JBK转换成两组对应的数字（可以指定想使用的格式））。
 *      public String replace (CharSequence target, CharSequence replacement)
 *      将与target匹配的字符串使用replacement字符串替换。
 *      获取字符串中每个字符方式有两种:
         *      1.toCharArray ()
         *      2.遍历String,通过charAt(索引)

**String的常用方法:分割（正则表达式所用的方法）**

* public String[] split(String regex)
* 将原有的字符串按照传入的字符或字符串进行切割（将传入的字符或字符串变换成空格进行切割）并返回为String[]数组类型。

* public boolean matches(String regex)
* 告知此字符串是否匹配给定的正则表达式
* public String replace(CharSequence  target   ,  CharSequence  replacement)
* 使用指定的字面值替换序列替换此字符串所有匹配字面值目标序列的子字符串。该替换从字符串的开头朝末尾执行，例如，用 "b" 替换字符串 "aaa" 中的 "aa" 将生成 "ba" 而不是 "ab"。 

### **StringBuilder**类

* 类的解释
   * 一个可变的字符序列。
 * 类的位置
    * java.lang.StringBuilder
 * 类的构造器
    * public StringBuilder()构造一个不带任何字符的字符串生成器，其初始容量为 16 个字符。
    * public StringBuilder(int capacity)构造一个不带任何字符的字符串生成器，其初始容量由 capacity 参数指定。
    * public StringBuilder(String str)构造一个字符串生成器，并初始化为指定的字符串内容。
 * 类的方法
    * public StringBuilder append(String str)
    * 将传入的字符串添加到原字符串的末尾。
    * public StringBuilder insert(int offset,String str)
    * 将str（传入的字符或字符串）从offset（指定的索引处）插入字符串。
    * public StringBuilder reverse()
    * 将此字符序列用其反转形式取代。

 **StringBuilder类的特点:**
 *      1.一个可变的字符序列,为什么是一个可变的字符序列?
         *          底层的数组没有被final关键字修饰
 *      2.StringBuilder类的底层数据结构是什么?
         *          在JDK8.0(包含)以前,底层数据结构是char[]
         *          在JDK9.0(包含)以后,底层数据结构是byte[]
 *      3.为什么在JDK9.0时将char[]变成byte[]
         *          a.理论上将char[]转换成byte[]内存空间节约一半
         *          b.byte类型可以和二进制位直接进行转换,从而调高效率
 *      4.StringBuilder类底层数据结构(数组)初始容量是多少?
         *      初始容量取决于所使用的构造器
                 *          StringBuilder(int capacity):用多少长度就是多少位
                 *          StringBuilder(String str):所用的长度+16
                 *          StringBuilder():初始容量16
 *      5.StringBuilder类扩容原理
         *          JDK8.0(包含)以后:(value.length << 1) + 2;
         *          JDK7.0:value.length * 2 + 2;
         *          JDK6.0(包含)以前:(value.length + 1) * 2;
 *      6.StringBuilder类是一个线程不安全的类,执行效率高

### 三个String区别:

 *      1.StringBuilder和StringBuffer除了线程安全问题,两个类一样
 *      2.创建对象的方式:
         *          String类:可以通过new创建对象,也可以直接赋值的方式
         *          StringBuilder和StringBuffer:只能使用new的方式
 *      3.底层数组的可变性:
         *          String类的数组被final修饰
         *          StringBuilder和StringBuffer没有被final关键字修饰
 *      4.底层数组的初始值
         *          String类的数组没有初始值
         *          StringBuilder和StringBuffer:根据使用不同的构造器初始一个默认长度的数组
 *      5.线程安全问题:
         *          String类和StringBuilder类是线程不安全,执行效率高
         *          StringBuffer类线程安全的,执行效率低

### 常量池

 *      专门用来存储Java中的常量
 *      常量池的好处:
         *      当常量池中有使用的常量,不在创建新的对象,直接拿已经有的常量供其使用因为常量池的存在,再加上字符串类型是程序中最常用类型,降低内存空间的使用率
 *      常量池的优化机制:
         *      当进行运算操作时,如果运算的符号两边都为字面值常量的话,在JVM进行文件编译已经运算完毕

**常量池的分类**

* 字符串常量池
  *  "abc"  字面值常量

* 整数常量池
  * -128 ~ 127

* 字符常量池
  * 0 ~ 127 ASCII

* 布尔常量池
  * true,false