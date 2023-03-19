# Servlet

#### 为什么使用servlet

* 因为传统的方式都不能将页面中的数据直接传递到java文件中，再通过java中的JDBC技术与数据库交互

* 想要实现html和jsp的数据提交到java文件中要使用servlet

#### Servlet简介和描述

* Servlet是Sun公司提供的一套技术标准
* Servlet是在javax.servlet包下的一个接口，并且它还有子接口（javax包实在tomcat服务器中的API）
* Servlet容器就是Servlet服务器（也就是tomcat）
* Servlet的方法也是容器在特定情况下调用的
* Servlet其实就是具有URL特性的java代码

#### Servlet操作步骤

* 搭建web动态环境
* 创建动态工程
* 创建javax.servlet.Servlet接口的实现类：（自己创建的类）

#### Servlet工作原理【执行流程】

* 浏览器发送请求【例如：超链接中的href属性值，form中action的属性值等能够触发请求事件】
* 请求web.xml中\<servlet-mapping>中的\<url-pattern>/URL（一般与别名一致）\</url-pattern>
  * 通过\<servlet-mapping>映射\<servlet>，从而找到相应全类名
* 执行java类中的相应方法【service()】

#### Servlet的生命周期【重点】

* 为什么要讨论servlet的生命周期
  * 因为他不是我们创建和调用的是由容器（tomcat）创建的

* 构造器
  * 执行时机：第一次发送请求被创建
  * 执行次数：在整个生命周期中，只执行一次
* init（）
  * 执行时机：第一次接受请求时，执行构造器之后执行初始化Servlet
  * 执行次数:在整个生命周期中，只执行一次
* service（）
  * 执行时机：每次接收请求时，都会执行，接收请求做出响应
  * 执行次数：在整个生命周期中，只执行n次（n>=1次）
* destroy（）
  * 执行时机：关闭服务器或重启服务器时执行destroy（）方法销毁Servlet对象
  * 执行次数：在整个生命周期中，只执行一次

#### 口述版：

**Servle声明周期描述：**首先启动服务器，当第一次发送请求时构造器创建对象，并调用init方法对Servlet对象进行初始化，然后调用service方法处理请求，最后当关闭服务器时调用destroy方法对Servlet对象进行销毁；这里创建对象、初始化对象、销毁对象在整个周期中都只会执行一次，，而service处理请求方法会随着我们发送请求次数改变。

#### 扩展：

* Servlet是单例的
* 如果在xml文件中添加\<load-on-startup>index[加载因子（正整数）]   数值越小优先级越高\</load-on-startup>   标签那么启动服务器成功后，立即创建Servlet对象并执行init（）方法初始化
* 示例

```xml
<servlet>
<!--        设置servlet别名-->
   <servlet-name>MyFirstServlet</servlet-name>
<!--        设置Servlet全类名-->
   <servlet-class>com.atguigu.servlet.MyFirstServlet</servlet-class>
	<!-- index[加载因子]
           正整数
           数值越小，优先级越高。
   -->	
   <load-on-startup>1</load-on-startup>
 </servlet>
```

* 运行页面有优先级index.html>index.jsp

* 如果运行时不想要首次访问的页面时index页面，那么在xml中添加

  ```html
  <welcome-file-list>
      <welcome-file>要访问的页面路径</welcome-file>
  </welcome-file-list>
  ```

#### Servlet获取get请求和post请求的方式

* 方式一
  * 在service方法中获取ServletRequest的子接口HttpServletRequest，然后使用HttpServletRequest对象调用getMethod方法查看是get还是post请求（servletRequest没有getMethod方法，他的子接口有getmethod方法）
* 方式二
  * java类继承Servlet的子接口HttpServlet，然后重写他的doGet和doPost方法
* 方式三
  * 直接在Java类所在的包中右击New下面有Create New Servlet（**开发中常用的方式**）（**注意有一个Create Java EE的东西取消勾选*****他是会使用3.0以上的注解功能****）

#### Servlet类的继承关系

![image-20201115125217812](typora-user-images\image-20201115125217812.png)

#### ServletConfig类

* 类的解释

  * ServlerConfig是Servlet程序的配置信息类（也就是web.xml文件）

* 类的作用

  * 可以获取Servlet程序的别名，也就是Servlet-name的值

    * servlerConfig.getServletName()方法获取别名

  * 获取初始化参数init-param

    * 在xml文件中添加初始化参数

    * ```html
      <servlet>
              <servlet-name>MyFirstServlet</servlet-name>
              <servlet-class>servlet222.MyFirstServlet</servlet-class>
      
              <init-param>
                  <!--参数名  相当于key-->
                  <param-name>username</param-name>
                  <!--参数值   相当于value-->
                  <param-value>root</param-value>
              </init-param>
              <init-param>
                  <!--参数名-->
                  <param-name>URL</param-name>
                  <!--参数值-->
                  <param-value>jdbc:mysql://localhost:3306/db6</param-value>
              </init-param>
          </servlet>
      ```

      * 使用getInitParameter（"key值"）方法获取初始化值（**获取初始化值参数是key获取的是value值**）

  * 获取ServletContext对象

    * 使用getServletContext()方法
      * org.apache.catalina.core.ApplicationContextFacade@32ef98a3（对象）

* Servlet和ServletConfig是由容器负责创建我们负责使用的

* Servlet程序默认是第一次访问的时候创建，ServletConfig是每个Servlet程序创建时，就创建一个对应的ServletConfig对象

* ServletConfig也可以在其他地方使用，但是每个对象里面使用ServleConfig只能获取当前对象的初始化值

* 如果重写了init方法那个init方法首行要加Super.init(config);   不然回报空指针异常（报500异常）

#### ServletContext类

* 概述：ServletContext是Servlet上下文对象，代表当前web应用
* ServletContext是一个接口，他表示Servlet上下文（）
* 一个web工程只有一个ServletContext对象实例
* ServletContext是一个域对象
* ServletContext是在web工程部署启动的时候创建，在web工程停止的时候销毁
  * **域对象是可以向Map一样存取数据的对象**
  * 这里的域指的是存取数据的操作范围是，整个web工程

|         |      存数据       |      取数据       |       删除数据       |
| :-----: | :---------------: | :---------------: | :------------------: |
| Map对象 |      put（）      |      get（）      |      remove（）      |
| 域对象  | setArrtribute（） | getArrtribute（） | removeArrtribute（） |

* 作用
  * 获取web.xml 中配置的上下文参数context-param
  
    * 使用getInitParameter("相对的name")
  
    * ```xml
      获取全局初始化参数
      context-param标签是声明全局参数的标签
      <context-param>
          <param-name>jdbc</param-name>
          <param-value>jdbc:mysql://localhost:3306/db1</param-value>
      </context-param>
      ```
  
  * 获取当前的工程路径，格式: /工程路径
  
    * getContextPath()；
  
  * 获取工程部署后在服务器硬盘上的绝对路径
  
    * getRealPath("/")
    * 编译期的web相当于项目中的路径
  
  * 像Map 一样存取数据

#### 最终创建Servlet方式

* 自定义类---------》继承HttpServlet--------》继承GenericServlet---------》实现Servlet
  * GenericServlet作用
    * 提供两个对象的获取方法
      * getServletConfig()
      * getServletContext()
    * 将service()方法，抽象化【在子类中只关注【重写】service()即可】
  * HttpServlet作用
    * override service()【重写service()】

#### 重新部署和刷新的技巧

* 当修改html代码时刷新即可
* 当修改java文件和xml文件时要重新部署

#### HttpServletRequest类（请求）

* 定义：相当于浏览器向服务器发送的请求报文，封装到request对象中，该对象由web容器创建，一参数的形式传入到service（）方法中，最终传入doPost（）或doGet（）中

* 作用：

  * 获取请求参数

    > 什么是参数
    >
    > ​	目前常见的参数
    >
    > ​	1、<a href="xxxx?name=val1&name2=val2..."></a>a>
    >
    > ​	2、<form action="xxx" method="get/post">
    >
    > <input type="text" name="username">
    >
    > </form>>

* 类的解释

  * 每次只要有请求进入Tomcat服务器，Tomcat服务器就会把请求过来的HTTP协议信息解析好封装到Request对象中，然后传递到service（）方法（doGet和doPost）中给我们使用。我们可以通过HttpServletRequest对象获取到所有的请求信息。
* 类的方法
  * i. getRequestURI()      获取请求的资源路径

    * 效果     /day06_Servlet/servletDemo2

  * ii. getRequestURL()      获取请求的统一资源定位符（绝对路径）

    * 效果：http://localhost:8080/day06_Servlet/servletDemo2

  * 获取URL各个部分的信息

    - request.getScheme()：获取协议名
    - request.getServerName()：获取服务器名称
    - request.getServerPort()：获取服务器端口号
    - request.getContextPath()：获取上下文路径【带/项目名】

  * iii. getRemoteHost()      获取客户端的ip 地址

    * 效果 ：  127.0.0.1

  * iv. getHeader()        获取请求头

    * request.getHeaders()：获取多个请求头数值（了解）

  * v. getParameter()              获取请求的参数

  * vi. getParameterValues()      获取请求的参数（多个值的时候使用   一般在多选框用到返回数组）

  * vii. getMethod()          获取请求的方式GET 或POST

  * viii. setAttribute(key, value);          设置域数据

  * ix. getAttribute(key);         获取域数据

  * x. getRequestDispatcher()      获取请求转发对象

  * ```java
    //1.获取转发器
    RequestDispatcher requestDispatcher = request.getRequestDispatcher("login_success.html");//括号里面的参数是要跳转的地址
    
    //2.调用forward方法执行转发
    requestDispatcher.forward(request,response);//这里要传入一个请求对象和响应对象
    ```

#### HttpServletResponse类（响应）

* 类的解释
  * 和HttpServletRequest一样，每次请求进来Tomcat服务器就会创建一个Response对象传递给Servlet程序使用。HttpServletRequest表示请求过来的信息，HttpServletResponse表示所有响应的信息，我么如果需要设置返回给客户端信息都可以通过HttpServletResponse对象来进行设置
* 两个流的使用说明
  * 字节流getOutputStream(); 常用于下载（传递二进制数据）
  * 字符流getWriter(); 常用于回传字符串（常用）

**注意事项：两个流同时只能使用一个。使用了字节流，就不能再使用字符流，反之亦然，否则就会报错。**

* 类的方法

  * response.getWriter()   获取打印流对象

  * write("响应的恢复语句（写中文要改字符集）")

  * setHeader("Content-Type","text/html;charset=UTF-8")  设置响应头读取格式

  * response.sendRedirect("要跳转的路径");

    > 注意这里sendRedirect和write都是给浏览器响应，但是sendRedirect的优先级大于write当二者同时出现时执行sendRedirect（）跳转

* 作用：

  * 获取响应流，响应数据		

    ```java
    //获取响应流，响应数据
    PrintWriter writer = response.getWriter();
    writer.write("login success!登录成功！");
    ```

  * 设置响应头

    ```java
    //设置响应头
    response.setHeader("Content-Type","text/html;charset=UTF-8");
    ```

  * 重定向【路径跳转】

    ```java
    //重定向【路径跳转】
    response.sendRedirect("login_success.html");
    ```

#### 请求转发过程图解

![image-20201116133501270](typora-user-images\image-20201116133501270.png)

#### 请求重定向过程图解

![image-20201115203609308](typora-user-images\image-20201115203609308.png)

#### 转发和重定向的区别（面试题）

* 转发地址栏不变，重定向地址栏改变
* 转发可以携带request对象，重定向不能携带request对象
* 转发浏览器只发送一次请求，重定向浏览器发送两次请求
* 转发可以访问WEB-INF下资源，重定向不能访问
  - WEB-INF属于web应用的私密目录，只有服务器内部可以访问，浏览器不能直接访问。

#### JavaWeb中乱码问题

**基本概念：**

- 字符集：就是各种字符的集合，包括汉字，英文，标点符号等等。各国都有不同的文字、符号。这些文字符号的集合就叫字符集。
- 编码：将字符转换为二进制数，过程称之为编码过程
- 解码：将二进制数转换为字符，过程称之为解码过程
- 乱码：编码与解码不一致时，出现乱码现象

**乱码种类**：

- 请求乱码：浏览器向服务器发送请求时，出现乱码
  - 浏览器编码，服务器解码不一致导致的。
- 响应乱码：服务器向浏览器做出响应时，出现乱码
  - 服务器编码，浏览器解码不一致导致的。

**浏览器与服务器默认字符集**：

- 服务器编码与解码默认一致为：ISO-8859-1【不支持中文】
- 浏览器
  - 浏览器编码默认：UTF-8【支持大量中文，大陆推荐使用的字符集】
  - 浏览器默认解码：GBK【GB2312：支持少量中文】

#### 解决乱码【三行代码解决请求与响应乱码】

解决请求乱码

- GET请求

  - 思路：设置URL字符集【修改8080端口号位置(tomcat->conf->server.xml)，进行设置】

  - 代码：

    ```java
    <Connector URIEncoding="UTF-8" port="8080" protocol="HTTP/1.1"
                   connectionTimeout="20000"
                   redirectPort="8443" />
    ```

  - POST请求

    - 思路：将服务器解码设置为：UTF-8【浏览器编码：UTF-8，与服务器解码：ISO-8859-1,不一致】
    - 代码：request.setCharacterEncoding("UTF-8");

- 解决响应乱码

  - 思路&代码：【服务器编码：ISO-8859-1与浏览器解码：GBK不一致】

    - 思路：将服务器编码设置为：GBK

      - 代码：response.setCharacterEncoding("GBK");

    - 思路：将服务器编码与浏览器解码均设置为UTF-8【推荐使用】

      - 代码：

        ```java
        response.setHeader("Content-Type","text/html;charset=UTF-8");
        response.setContentType("text/html;charset=UTF-8");//【推荐使用】
        ```

#### JavaWeb中路径问题

> 问题：使用转发跳转路径时，由于转发特点【地址栏不变】，导致路径中使用[../]可能出现404，不使用[../]也可能出现404。
>
> 所以：使用相对路径不可靠，建议使用【绝对路径】

##### 什么是绝对路径？

- 以/开头的路径，称之为绝对路径。

##### 【/】代表的语义

- 服务器解析【/】，【/】代表当前web应用路径：http://localhost:8080/day06_servlet
  - 以下两种情况，由服务器解析【/】
    1. 书写在web.xml中的【/】，由服务器解析
    2. 转发
- 浏览器解析【/】，【/】代表当前服务器路径：http://localhost:8080
  - 以下两种情况，由浏览器解析【/】
    1. 书写在html中的【/】，由浏览器解析
    2. 重定向

##### 代码

* html[jsp]

```java
<base href="/day06_servlet/">  //就是将共同的路径提取出来，然后下次再写就不用加那个路径了，注意谢了这个之后前面就不要再加绝对路径了
<form action="LoginServlet" method="get"></form>
<a href="index.html">回首页</a>
```

- servlet

```java
response.sendRedirect(request.getContextPath()+"/pages/regist.html");
request.getRequestDispatcher("/pages/regist_success.html").forward(request,response);
```



#### 三层结构

- 表示层
- 业务逻辑层
- 数据访问层

**为什么使用三层结构，为了解耦**

解耦可以提高代码的扩展性等

#### java中的两个bean

一个数据bean一个业务bean

数据bean对应数据库

业务bean主要处理数据库中的业务（实现功能如分页等）