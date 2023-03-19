# 集合

### List集合接口

 *      类的解释
         *      有序的 collection（也称为序列）。
                 *          有序:存储元素顺序和取出元素顺序一致
                 *          无序:存储元素顺序和取出元素顺序不一致
 *      类的位置
         *          java.util.List
 *      类的构造器
         *          因为是接口,没有构造器
 *      类的方法
         *          void add(int index,E element)在列表的指定位置插入指定元素（可选操作）。
         *          E get(int index)返回列表中指定位置的元素。
         *          E remove(int index)移除列表中指定位置的元素（可选操作）。
         *          E set(int index,E element)用指定元素替换列表中指定位置的元素（可选操作）。

### List集合的特点:

 *      1.List集合是一个有序的集合
 *      2.List集合是一个带有索引的集合
         *          备注:这个索引并不是单一指向数组的索引,有的List集合的实现类的索引是数组索引,还有的集合是通过某种数据结构模拟出来的编号，索引从0开始,最大的索引集合的长度-1
 *      3.List集合可以存储重复元素
 *      4.List集合可以存储多个null元素
 *      5.List集合针对里面的元素有4种遍历方式
 *      6.List 接口提供了特殊的迭代器，称为 ListIterator
         *          使用步骤和Iterator一样

### List集合的四种遍历方式:

 *      1.迭代器
 *      2.增强for(推荐)
 *      3.List集合特有的迭代器
 *      4.普通for(推荐)

```
List<String> list = new ArrayList<>();
	list.add("123")
	list.add("456")
	list.add("789")
	list.add("123456")
System.out.println("=====第一种遍历方式:迭代器=====");	
Iterator<String> it = list.iterator();
	while(it.hasNext()){
		System.out.println(it.next());
	}
System.out.println("=====第二种遍历方式:增强for=====");
	for(Object obj:list){
		System.out.println(obj);
	}
System.out.println("=====第三种遍历方式:List集合特有的迭代器=====");
	ListIterator<String> lit = new list.listIterator();
	while(lit.hasNext()){
		sout(lit.next());
	}
 System.out.println("=====第四种遍历方式:普通for=====");
 	for(int i = 0;i<list.size();i++){
 		sout(list.get(i));
 	}
```

### 集合在删除时注意事项:

 *      1.在删除List集合中的元素的时候,因为size()和索引不断变化在每次成功删除元素时,将索引往前移动一位

### ArrayList类

 *      类的解释
         *          List 接口的大小可变数组的实现。在程序中,数组的长度不可以改变,根据元素的个数创建新的数组
 *      类的位置
         *          java.util
 *      类的构造器
         *      public ArrayList()
                 *          构造一个初始容量为 10 的空列表。
         *      public ArrayList(Collection<? extends E> c)
                 *          构造一个包含指定 collection 的元素的列表，这些元素是按照该 collection 的迭代器返回它们的顺序排列的。
         *      public ArrayList(int initialCapacity)
                 *          构造一个具有指定初始容量的空列表。
 *      类的方法
         *          不需要学习新的方法,具体的方法在Collection和List集合中已经学习完了

### ArrayList集合的特点(源码分析)

 *      1.具备List集合的相关特点
 *      2.底层数据结构:数组
         *      数组结构的特点(相对LinkedList的链表结构):
                 *              更新(增删改)效率比较慢:因为new要开辟内存空间,比较耗时
                 *              查询(查)效率比较快:索引
 *      3.初始容量:根据所选择的构造器进行初始容量的设定
         *      public ArrayList(int initialCapacity)
                 *          自定义初始容量
         *      public ArrayList(Collection<? extends E> c)
                 *          根据参数的单列集合初始一个等长的数组	
         *      public ArrayList()
                 *              JDK7.0(包含)以后:先初始化一个长度为0的空数组,在第一次添加元素时,初始化一个长度为10的数组
                 *              JDK6.0(包含)以前:直接初始化一个长度为10的数组
 *      4.扩容原理:
         *          第一次添加元素时:初始化一个长度为10的数组
         *          JDK7.0(包含)以后:原来数组的长度 + (原来数组的长度 >> 1);
         *          JDK6.0(包含)以前:(原来数组的长度 * 3)/2 + 1;
 *      5.ArrayList集合不是一个线程安全的集合,执行效率高

### LinkedList集合的概述

* 类的解释
   * List 接口的链接列表实现
 * 类的位置
    * java.util
 * 类的构造器
    * public LinkedList()构造一个空列表。
    * public LinkedList(Collection<? extends E> c)构造一个包含指定 collection 中的元素的列表，这些元素按其 collection 的迭代器返回的顺序排列。
 * 类的方法
    * public void addFirst(E e)将指定元素插入此列表的开头。
    * public void addLast(E e)将指定元素添加到此列表的结尾。
    * public E removeFirst()移除并返回此列表的第一个元素。
    * public E removeLast()移除并返回此列表的最后一个元素。
    * public E getFirst()返回此列表的第一个元素。
    * public E getLast()返回此列表的最后一个元素。

### LinkedList集合的特点:

 *      1.List集合相关特点
 *      2.List接口的链接列表实现(双向链表)
         *      链接列表:一个环形的数据结构
                 *              单向链表:每个元素由两部分组成:当前元素,下一个元素
                 *      双向链表:每个元素由三部分组成:当前元素,下一个元素,上一个元素
         *      LinkedList更新比较快(增删改),查询(查)比较慢
                 *              链表结构越长,效率越低
 *      3.因为链表结构存在一个隐形结点,所以在LinkedList里面存在大量操作首尾元素的方法
 *      4.LinkedList不是线程安全的集合,效率高
 *      LinkedList集合底层数据结构是链表,并不是数组,按理说无法使用含有索引的方法,实际上可以使用含有索引的方法,为什么?
         *      LinkedList里面并不存在数组的索引,它的索引是通过链表的数据结构模拟出现Node内部类对象和modCount这两个元素模拟出来的

### Vector集合的概述和特点:(了解)

 *      类的解释
         *          Vector 类可以实现可增长的对象数组
 *      类的位置
         *          java.util
 *      类的构造器
         *          public Vector()构造一个空向量，使其内部数据数组的大小为 10，其标准容量增量为零。
 *      类的方法
         *      和ArrayList方法一样

### Vector类的特点:

 *      1.Vector类在JDK1.0出现了,不在List接口下,1.2版本之后为ArrayList集合进行取代,在JDK1.2之后源码没有任何改动
 *      2.Vector类是一个线程安全的集合,执行效率低(ArrayList)
 *      3.底层数据结构:数组
         *          数组结构的特点(相对LinkedList的链表结构):
         *              更新(增删改)效率比较慢:因为new要开辟内存空间,比较耗时
         *              查询(查)效率比较快:索引
 *      4.初始容量:根据所选择的构造器进行初始容量的设定
         *          public Vector(int initialCapacity)
         *          自定义初始容量
         *          public Vector(Collection<? extends E> c)
         *          根据参数的单列集合初始一个等长的数组
         *          public Vector()
         *          直接初始化一个长度为10的数组
 *      5.扩容原理:
         *          原来数组的长度 + 原来数组的长度