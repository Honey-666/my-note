# Set集合

Set集合的特点:
 *      1.一个不包含重复元素的 collection。
 *      2.最多只能还有一个null元素
 *      3.Set集合无法描述是有序集合还是无序集合
 *          Set集合的实现类里面含有无序集合,也含有有序集合
 *      4.Set集合是一个没有索引的集合
 *      5.Set集合没有特有的迭代器,Set集合的遍历方式有两个

Set集合的通用遍历方式
 *      迭代器
 *      增强for

# HashSet集合

**类的解释**

* 此类实现 Set 接口，由哈希表（实际上是一个 HashMap 实例）支持。 

**类的位置**

 *          java.util

**类的构造器**

 *          public HashSet()构造一个新的空 set，其底层 HashMap 实例的默认初始容量是 16，加载因子是 0.75。

**类的方法**

 *          HashSet类没有特有的方法哈希表数据结构

  **哈希表:**

 *          JDK7.0(包含)以前:数组+链表
 *          JDK8.0(包含)以后:数组 + 链表 或者 数组 + 红黑树

 *          链表里面元素越多,执行的效率越低

**HashSet集合的特点:**

 *          1.HashSet集合是一个无序的集合
             *          无序:存储的顺序和取出的顺序不一致
 *          2.HashSet集合的顺序无法保证永远不变
             *          元素是根据某种规则进行存储(其中涉及一个数据,数组的长度)
 *      3.HashSet集合是一个线程不安全的集合,执行效率高
 *      4.HashSet集合不可以存储重复元素
 *      5.最多只能还有一个null元素

**HashSet保证元素的唯一原理:**

 *      1.重写Object类的hashCode()
         *          集合泛型中的数据类型是什么,就要在哪个类中重写hashCode()
 *      2.重写Object类的equals()

哈希码值:

 *      人工模拟出来的数值,打印对象的地址值时,@后面的十六进制数就是这个哈希码值'

面试题:

 *      String类重写Object类的hashCode(),重写的方法为什么乘以31?
 *      1.这个数不能太大,如果太大的话,在方法中反复乘积,容易超出int的取值范围
 *      2.这个数要选取质数,降低HashCode值的重复率
 *      3.经过开发人员反复测试,一共有两个比较合适的数字,一个是29,一个是31
 *      4.开发人员考虑到31这个数计数方式和整数类型的取值范围格式相同,而且它二进制都是11111

**LinkedHashSet类**

 *      类的解释
         *          具有可预知迭代顺序的 Set 接口的哈希表和链接列表实现
         *          JDK7.0(包含)以前:数组 + 链表 + 链表
         *      JDK8.0(包含)以后:数组 + 链表 + 链表 / 数组 + 红黑树 + 链表
         *          多维护的一张链表结构:记录存储每个元素的顺序
         *          LinkedHashSet是一个有序的集合
         *          LinkedHashSet是一个线程不安全的集合,执行效率高
 *      类的位置
         *          java.util
 *      类的构造器
         *          public LinkedHashSet()构造一个带默认初始容量 (16) 和加载因子 (0.75) 的新空链接哈希 set。
 *      类的方法
         *      并没有自己的特有方法
 *      使用情况:
         *          需要一个容器存储元素,既要保证元素有序性,而且还有过滤掉重复元素

**TreeSet类**

 *      类的解释
         *      基于 TreeMap 的 Set 集合的实现
         *      底层数据结构是红黑树(二叉树的一种)
 *      类的位置
         *          java.util.TreeSet
 *      类的构造器
         *          public TreeSet()构造一个新的空 set，该 set 根据其元素的自然顺序进行排序
 *      类的方法
         *          public E first()返回此 set 中当前第一个（最低）元素。
         *          public E last()返回此 set 中当前最后一个（最高）元素。
 *      使用场景:
         *      给集合中的元素进行快速排序

**TreeSet的自然排序的步骤:**

 *      1.定义Comparable<T>接口的实现类,重写接口中的抽象方法compareTo
 *      2.将这个实现类作为集合的泛型的类型(集合的元素的类型)
 *      3.在集合中添加元素
 *      4.打印集合,结果会根据重写抽象方法compareTo()里面规则进行打印



1.如果应用的类是API中类,无法改动API中的类

2.如果在自定义类中实现类的Comparable<T>接口,违反了JavaBean规范

定制排序的步骤:

 *      1.自定义JavaBean
 *      2.自定义Comparator<T>接口的实现类,重写里面的抽象方法
 *      3.在测试类中创建实现类对象
 *      4.将实现类对象作为参数创建TreeSet集合对象
 *           public TreeSet(Comparator<? super E> comparator)
 *           构造一个新的空 TreeSet，它根据指定比较器进行排序。
 *      5.添加元素
 *      6.遍历输出