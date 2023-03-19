# EL和JSTL

#### 什么是EL 表达式，EL 表达式的作用?

- EL 表达式的全称是：Expression Language。是JSP内置表达式语言。
- EL 表达式的什么作用：EL 表达式主要是代替jsp 页面中的表达式脚本在jsp 页面中进行数据的输出。
- 因为EL 表达式在输出数据的时候，要比jsp 的表达式脚本要简洁很多。
- EL表达式仅仅用来读取数据，而不能对数据进行修改**（数据必须在域中才能取到数据）**
- EL 表达式的格式是：${表达式}
- EL 表达式在输出null 值的时候，输出的是空串。jsp 表达式脚本输出null 值的时候，输出的是null 字符串。
- EL在得到某个数据时会进行自动类型转换

#### 表达式搜索域数据的顺序

- EL 表达式主要是在jsp 页面中输出数据。主要是输出域对象中的数据。
- 当四个域中都有相同的key 的数据的时候，EL 表达式会按照四个域的从小到大的顺序去进行搜索，找到就输出。

#### EL的基本使用

* EL获取对象和对象属性语法
  * 获取对象
    * ${域对象.标识符}：从指定域中获取数据
  * 获取对象属性
    * ${域对象.对象名.属性}
    * ${域对象.对象名["属性"]}
  * 属性：封装后的全局变量
  * ${域对象.对象名.属性}相当于调用对象中的getXXX()方法

#### empty 运算符

- 描述：empty 运算可以判断一个数据是否为空，如果为空，则输出true,不为空输出false。
  - “”
  - null
  - 数组或集合的长度为0
  - 以下几种情况为空：
         1、值为null 值的时候，为空
         2、值为空串的时候，为空
         3、值是Object 类型数组，长度为零的时候
         4、list 集合，元素个数为零
         5、map 集合，元素个数为零
  - ${empty 标识符}：判断元素是否为空，true:空
  - ${not empty 标识符}：判断元素非空，true:非空

#### “.”点运算和[] 中括号运算符

- .点运算，可以输出Bean 对象中某个属性的值。
- []中括号运算，可以输出有序集合中某个元素的值。
- 并且[]中括号运算，还可以输出map 集合中key 里含有特殊字符的key 的值。(比如加减乘除运算点等)

```jsp
例：
<%
Map<String,Object> map = new HashMap<String, Object>();
map.put("a.a.a", "aaaValue");
map.put("b+b+b", "bbbValue");
map.put("c-c-c", "cccValue");
request.setAttribute("map", map);
%>
${ map['a.a.a'] } <br>		 输出aaaValue
${ map["b+b+b"] } <br>		 输出bbbValue
${ map['c-c-c'] } <br>  	 输出cccValue
```

#### EL 表达式的11 个隐含对象

- EL 个达式中11 个隐含对象，是EL 表达式中自己定义的，可以直接使用。
- 变量                                                  类型                                     作用
- pageContext                       PageContextImpl                它可以获取jsp 中的九大内置对象
- pageScope                          Map<String,Object>           它可以获取pageContext 域中的数据
- requestScope                     Map<String,Object>           它可以获取Request 域中的数据
- sessionScope                     Map<String,Object>            它可以获取Session 域中的数据
- applicationScope              Map<String,Object>            它可以获取ServletContext 域中的数据
- param                                 Map<String,String>             它可以获取请求参数的值
- paramValues                     Map<String,String[]>           它也可以获取请求参数的值，获取多个值的时候使用。
- header                                Map<String,String>             它可以获取请求头的信息
- headerValues                    Map<String,String[]>           它可以获取请求头的信息，它可以获取多个值的情况
- cookie                                 Map<String,Cookie>            它可以获取当前请求的Cookie 信息
- initParam                           Map<String,String>              它可以获取在web.xml 中配置的<context-param>上下文参数

**扩展：pageContext 能够获取jsp中其他8个对象**

* 如在el中需要jsp中request对象的其他功能，怎么办？【获取url，转发】
  - 答：${pageContext.request.contextPath}（使用pageContext先获取到request对象在调用request的方法）

# JSTL

#### 简介

* JSTL(JSP Standard Tag Library)
* JSTL用于简化jsp中的java 代码
* JSTL相当于以html的形式去书写java 代码
* JSTL由5个不同功能的标签库组成。如下：

|       功能范围       | 前缀  |                  URI                   |
| :------------------: | :---: | :------------------------------------: |
| **核心标签库--重点** | **c** | **http://java.sun.com/jsp/jstl/core**  |
|     格式化标签库     |  fmt  |    http://java.sun.com/jsp/jstl/fmt    |
|      函数标签库      |  fn   | http://java.sun.com/jsp/jstl/functions |
|    数据库(不使用)    |  sql  |    http://java.sun.com/jsp/jstl/sql    |
|     XML(不使用)      |   x   |    http://java.sun.com/jsp/jstl/xml    |

```jsp
格式
CORE 标签库
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

FMT 标签库 
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>

FUNCTIONS 标签库
 <%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions" %>
```

####  \<c:out>标签

**作用：用于输出到页面，功能类似于JSP表达式<%= %>和EL表达式${}**

**标签内的属性**：

| 属性              | 作用                                                         | 参数类型 |
| ----------------- | ------------------------------------------------------------ | -------- |
| value             | 要输出的值                                                   | Object   |
| default           | 当value为null时显示的默认值                                  | Object   |
| escaXml(**了解)** | 是否对特殊字符进行转义（如果转义就输出文本格式不转义输出标签样式格式，ture为转义false为不转义） | boolean  |

示例：

```jsp
<c:out value = "123" default=""></c:out>
```

#### \<c:set>标签

**作用：用于 添加 或 修改 域中的属性**

**标签内的属性**：

| **属性** | **描述**                                                     | **是否必要** | **默认值** |
| :------: | :----------------------------------------------------------- | :----------- | ---------- |
|  value   | 要存储或修改的值                                             | 否           | 主体的内容 |
|  target  | 要修改的域对象的Key值（必须是JavaBean或者Map）               | 否           | 无         |
| property | 指定要修改的对象的属性名                                     | 否           | 无         |
|   var    | 表示域中存放的属性名                                         | 否           | 无         |
|  scope   | var属性的作用域 (page , request , session ,application) ，若不指定则为page | 否           | page       |

示例：

```jsp
在作用域中添加新属性
<c:set var="str" value="123" scope="page"></c:set>
//默认不写scope时是最小域page

 Student stu = new Student(18,"yinhang");
 request.setAttribute("stu",stu);
修改已存中的作用域的属性
<c:set target="${requestScope.stu}" property="name" value="admin"></c:set>
//代码翻译：将key值为stu的域对象的属性name改为admin
```

#### \<c:if>标签

**作用：判断表达式的值，如果表达式的值为 true 则执行其主体内容。**

**标签内的属性**：

| **属性** | **描述**               | **是否必要** | **默认值** |
| -------- | ---------------------- | ------------ | ---------- |
| test     | 条件                   | 是           | 无         |
| var      | 用于存储条件结果的变量 | 否           | 无         |
| scope    | var属性的作用域        | 否           | page       |

示例：

```jsp
<c:if test="${2>1}">
如果test中的结果为ture就执行这句话否则就不执行
</c:if>
```

####  \<c:choose>、\<c:when>、\<c:otherwise>标签

**作用：\<c:choose>标签与Java switch语句的功能一样，用于在众多选项中做出选择。**

**switch语句中有case，而\<c:choose>标签中对应有\<c:when>，switch语句中有default，而\<c:choose>标签中有\<c:otherwise>。\<c:when>、\<c:otherwise>这两个标签在\<c:choose>中联合起来类似于java中的if--else**

**标签内的属性：**

\<c:choose>标签没有属性。

\<c:when>标签只有一个属性test，他的test同c:if标签中的test一样为条件

\<c:otherwise>标签没有属性。

示例：

```jsp
choose 标签开始选择判断
when 标签表示每一种判断情况
test 属性表示当前这种判断情况的值
otherwise 标签表示剩下的情况

<c:set var="age" value="18" scope="request"></c:set>
<c:choose>
    <c:when test="${requestScope.age<18}">
      青少年
    </c:when>
    <c:when test="${requestScope.age>18 && requestScope.age<35}">
    	青年
    </c:when>
    <c:when test="${requestScope.age>35 && requestScope.age<50}">
    青壮年
    </c:when>
    <c:otherwise>
    老年
    </c:otherwise>
</c:choose>
```

#### \<c:forEach>标签

**作用：标签是迭代一个集合中的对象-可以是数组，也可以是list，也可以是map对象**

**标签内的属性：**

| **属性**  | **描述**                                   | **是否必要** | **默认值**   |
| --------- | ------------------------------------------ | ------------ | ------------ |
| items     | 要被循环的数据集合-可以使用el表达式(KEY)   | 否           | 无           |
| begin     | 开始的索引（0=第一个元素，1=第二个元素）   | 否           | 0            |
| end       | 最后一个索引（0=第一个元素，1=第二个元素） | 否           | Last element |
| step      | 每一次迭代的步长                           | 否           | 1            |
| var       | 代表当前条目的变量名称                     | 否           | 无           |
| varStatus | 代表循环状态的变量名称                     | 否           | 无           |

示例：

```jsp
普通for循环
<c:forEach var="i" begin="1" end="10" step="1">
		${i}   //代表从1开始，到10结束，每次增加1
</c:forEach>

遍历集合
 List<Student> list = new ArrayList<Student>();
    list.add(new Student(18,"yinhang"));
    list.add(new Student(19,"bifu"));
    list.add(new Student(20,"ziyang"));
     
    request.setAttribute("list",list);
     
<c:forEach var="stu" items="${requestScope.list}">//这里的list就是setAttribute中的key
    ${stu}
    ${stu.name}
    ${stu.age}
</c:forEach>
```

remove

ri

#### 格式化标签库

一般使用在时间上

\<fmt:formatDate>\</<fmt:formatDate>>

