# JSP

#### 概述

- Jsp本质是一个Servler,Servlet能做的事情jsp都能做
- jsp必须运行在服务器中，不能用浏览器直接打开

#### Jsp和Servlet分工

* jsp主要负责显示及获取数据，Jsp相当于在html中嵌入java代码，jsp=html+java
* Servlet主要负责处理业务，Servlet相当于在java中嵌入html代码，Servlet=java+html

#### Jsp工作原理

* 第一次房屋内jsp是，服务器将jsp先翻译成xxx.java文件，在编译成xxx.class文件
* 以后再访问jsp时
  * 如果修改了jsp页面：那么从新翻译和编译
  * 如果没更改：那么不会从新编译和翻译，会用上次加载好的（有暂时缓存机制）

#### Jsp本质

* 翻译后的文件继承了HttpJspBase，HttpJspBase继承了HttpServlet，所以Jsp本质也是Servlet

#### Jsp基本语法

* 指令
  * 语法**<%@ 属性=属性值  属性2=属性值2...%>**
* 模板元素：html、css、js
* 脚本片段（小脚本）
  * 语法：<%%>
  * 作用：在_jspService()方法中，书写java代码
* 表达式
  * 语法<%=%>
  * 作用：将数据显示到页面中
* 声明
  * 语法<%! %>
  * 作用：在类中书写Java代码
* 注释
  * java
    * 单行注释
    * 多行注释
  * html
    * <!-- -->
  * jsp
    * <%-- --%>

#### jsp 头部的page 指令

* 语法格式：<%@ 属性=属性值  属性2=属性值2...%>

**<%@ page contentType="text/html;charset=UTF-8" language="java" %>**

- language		属性表示jsp 翻译后是什么语言文件。暂时只支持java
- contentType      属性表示jsp 返回的数据类型是什么。也是源码中response.setContentType()参数值
- pageEncoding 属性表示当前jsp 页面文件本身的字符集（优先级高于meta标签的字符集）

========================**两个属性是给out 输出流使用**=============================

- autoFlush 属性设置当out 输出流缓冲区满了之后，是否自动刷新冲级区。默认值是true**(不建议修改**)
- buffer 属性设置out 缓冲区的大小。默认是8kb**(不建议修改)**

- errorPage 属性设置当jsp 页面运行时出错，自动跳转去的页面路径
  - errorPage 表示错误后自动跳转去的路径，这个路径一般都是以斜杠打头，它表示请求地址为http://ip:port/工程路径/
- isErrorPage 属性设置当前jsp 页面是否是错误信息页面。默认是false。如果是true 可以获取异常信息。
- session 属性设置访问当前jsp 页面，是否会创建HttpSession 对象。默认是true  **（不建议修改）**
- extends 属性设置jsp 翻译出来的java 类默认继承谁。（了解）

#### 声明脚本(极少使用)

* 声明脚本的格式是： <%! 声明java 代码%>
* 作用：可以给jsp 翻译出来的java 类定义属性和方法甚至是静态代码块。内部类等。

#### 表达式脚本（常用）

- 表达式脚本的格式是：<%=表达式%>
- 表达式脚本的作用是：的jsp 页面上输出数据。
- 输出整型
  1. 输出整型
  2. 输出浮点型
  3. 输出字符串
  4. 输出对象

- 表达式脚本的特点：
  1. 所有的表达式脚本都会被翻译到_jspService() 方法中_
  2. _表达式脚本都会被翻译成为out.print()输出到页面上
  3. _由于表达式脚本翻译的内容都在_jspService() 方法中,所以_jspService()方法中的对象都可以直接使用。
  4. 表达式脚本中的表达式不能以分号结束。

#### 代码脚本

* 代码脚本的格式是：<%    java 语句     %>
* 代码脚本的作用是：可以在jsp 页面中，编写我们自己需要的功能（写的是java 语句）。
* 可以编写的类型可以是：
  * ----if 语句
  * ----for 循环语句
  * 翻译后java 文件中_jspService 方法内的代码都可以写
* 代码脚本的特点是：
  1. 代码脚本翻译之后都在_jspService 方法中_
  2. _代码脚本由于翻译到_jspService()方法中，所以在_jspService()方法中的现有对象都可以直接使用。
  3. 还可以由多个代码脚本块组合完成一个完整的java 语句。
  4. 代码脚本还可以和表达式脚本一起组合使用，在jsp 页面上输出数据

#### include指令

- 语法格式：<%@  include file="设置被包含文件路径"%>
- 作用：静态包含指定文件
- 特点：被包含的文件，不会被翻译编译【先包含，再翻译】
- 使用场景：在jsp中的静态资源较多时使用。【少量java代码】

#### Jsp动作标签

语法格式：\<jsp:xxx>\</jsp:xxx>

#### 动态包含

- 语法：\<jsp:include page="设置被包含文件的路径">\</jsp:include>
- 作用：动态包含指定文件
- 特点：被包含的文件，同时会被翻译&编译【先翻译，再包含】
- 使用场景：在jsp中的java代码较多时使用【静态资源少】

####  转发

- 语法：\<jsp:forward page="设置请求转发路径">\</jsp:forward>

- 代码

```jsp
<jsp:forward page="index.jsp"></jsp:forward>

<jsp:forward page="index.jsp">
    <jsp:param name="stuName" value="yinhang"/>
</jsp:forward>
```

- 注意：使用jsp动作标签中的转发时，如需参数直接添加\<jsp:param>即可。如不需参数：\<jsp:forward>\</jsp:forward>的开始标签与结束标签之间不允许书写任何内容。

####  Jsp九大隐式对象【隐含对象|内置对象】- 面试题

> 隐式对象：在jsp中无需我们程序员手动创建，就可以直接使用的对象，称之为隐式对象。
>
> jsp中共有9个内置对象，但默认只有8个内置对象【exception对象需在page指令中设置isErrorPage="true",才可显示。】

- pageContext
  - 类型： javax.servlet.jsp.PageContext
  - 作用：域对象【页面域】
  - 在Servlet中获取方式：无
- request
  - 类型：javax.servlet.http.HttpServletRequest 
  - 概述：详情请查阅Servlet笔记
  - 作用：域对象【请求域】详情请查阅Servlet笔记
  - 在Servlet中获取方式：在doGet()或doPost()中直接使用
- session
  - 类型：javax.servlet.http.HttpSession
  - 作用：域对象【会话域】
  - 在Servlet中获取方式：request.getSession();
- application
  - 类型：javax.servlet.ServletContext
  - 概述：详情请查阅Servlet笔记
  - 作用：域对象【web域】详情请查阅Servlet笔记
  - 在Servlet中获取方式：在doGet()或doPost()中，调用getServletContext()方法获取。
- page
  - 类型：java.lang.Object
  - 概述&作用：this
- response
  - 类型：javax.servlet.http.HttpServletResponse
  - 概述&作用：详情请查阅Servlet笔记
- out
  - 类型：javax.servlet.jsp.JspWriter
  - 作用：与Servlet中PrintWriter作用类似，将数据显示到页面。【JspWriter与PrintWriter是兄弟关系，都继承java.io.Writer】
- config
  - 类型：javax.servlet.ServletConfig
  - 概述&作用：详情请查阅Servlet笔记
- exception
  - 类型： java.lang.Throwable
  - 作用：捕获处理异常

#### Jsp四大域对象

#### 域对象

- 生活中域对象【快递行业】：将物品从一位置，传递另一位置。

  - 同区快递【外卖】
  - 同城快递【文件】
  - 全国快递【网购】
  - 全球快递【网购】

- 程序中域：将数据共享【传递】

  - pageContext【页面域】

    - 有效范围：当前页面有效

  - request【请求域】

    - 有效范围：当前请求有效
    - 当前请求：只要路径跳转，一般就不在当前请求【如使用转发时，依然在当前请求】

  - session【会话域】

    - 有效范围：当前会话有效

    - 当前会话：与浏览器有关【与服务器无关】，只要浏览器不关闭&不更换就在当前会话

      ​		浏览器关闭或更换就不在当前会话。

  - application【web域|上下文域，他就是Servlet中的二serlvetContext】

    - 有效范围：当前web应用中有效
    - 只要web应用未卸载【未关闭】就有效。
    - 重启服务器或重新部署服务器都算

#### 域对象共性：

* 所有域对象都有一套通用方法【底层维护一个Map<String,Object>】

- 设置属性到域中：void setAttribute(String key , Object value);
- 从域中获取指定的属性：Object  getAttribute(String key);
- 移除域中指定属性：void removeAttribute(String key);