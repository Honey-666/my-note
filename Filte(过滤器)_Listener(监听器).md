# Filte(过滤器)

#### 概述

* 是web三大组件之一
  * web组件共性
    1. 都需要实现某个接口
    2. 都需要注册web.xml

#### filter作用

* Servlet：发出请求，做出响应
* filter：过滤请求和响应

#### Filter常用API

* filterConfig
* doFilter
* dostroy

#### Filter的配置

* xml配置文件中\<url-patterm>标签内一般是要过滤的url（一般都是servlet中xml的url，多个Filter中的url是一致的）
* doFilter中一定要执行chain.doFilter(request,response);放行请求
* chain.doFilter(request,response);方法前是放行前代码，chain.doFilter(request,response);后的代码是放行后的代码

#### Filter的工作原理（单个）

* 浏览器向服务器发送请求(Filter映射servleturl)
* 执行doFilter（）方法中放行请求前的代码
* 在执行chain.doFilter放行请求
* 执行serlvet的代码
* 最后执行doFilter（）方法中放行请求后的代码

#### Filter的工作原理（多个）

* 浏览器向服务器发送请求(Filter映射servleturl)

* 执行**过滤器1**doFilter（）方法中放行请求前的代码

* 执行**过滤器2**doFilter（）方法中放行请求前的代码

* 在执行chain.doFilter放行请求

* 执行serlvet的代码

* 执行**过滤器2**doFilter（）方法中放行请求后的代码

* 最后执行**过滤器1**oFilter（）方法中放行请求后的代码

  > 多个Filter执行doFilter遵循先进后出
  >
  > Filter的执行顺序与xml中Filter-mapping中的url有关
  >
  > 有xml配置和注解的时候先执行xml中的最后执行注解的Filter中的url
  >
  > 如果都是注解跟filtername有关名称越靠前越先执行（0-9，a-z,A-Z）

#### Filter生命周期

> 同serrvlet的生命周期大体相同

* 构造器
  * 执行时机：启动服务器成功后，执行
  * 执行次数：在整个生命周期中，执行一次
* init()
  * 执行时机：启动服务器成功且执行构造器后，执行
  * 执行次数：在整个生命周期中，执行一次
* doFilter()
  * 执行时机：每次接收匹配请求时，执行
  * 执行次数：在整个生命周期中，执行n次[n>=1]
* destroy()
  * 执行时机：关闭或重启服务器时，执行
  * 执行次数：在整个生命周期中，执行一次

#### URl的配置规则

* 精确匹配

  * 方式一：直接在xml中给定URL名称
    * 示例

  ```xml
   <filter-mapping>
          <filter-name>Filter2</filter-name>
          <url-pattern>/HelloServlet</url-pattern>
      </filter-mapping>
  ```

* 方式二：将xml中的url-pattern标签替换成servlet-name标签

  * 示例

  ```xml
   <filter-mapping>
          <servlet-name>Filter2</servlet-name>
          <servlet-name>/HelloServlet</servlet-name>
      </filter-mapping>
  ```

* 模糊匹配

  * 前置模糊（后罪名）匹配

    * 将（*）放到最前面，如：\*.jsp

      * 代表所有.jsp结尾的（注意访问jsp文件就会执行，即使没有这个文件，访问Filter也会执行）

      > \*不能写在中间

* 后置模糊（目录）匹配

  * 将（\*）放到最后面，如：/目录/目录2/*
    * 代表该目录下的文件（注意访问jsp文件就会执行，即使没有这个文件，访问Filter也会执行）

  > 扩展：
  >
  > 【/*】:当前项目下的所有路径
  >
  > 【/】：【除了jsp之外的】当前项目下的所有路径

# listener(监听器)

#### 简介

- 监听者：程序员创建
- 监听对象：HttpServlerRequest、HttpSession、ServletContext等
- 监听事件：创建、属性变化、销毁
- 监听结果：根据项目需要进行处理

**监听器注册时只需要\<listener-class>标签**

服务器创建三大组建创建的顺序：监听器-------》过滤器---------》Servlet

三大组件销毁的顺序遵循先进后出