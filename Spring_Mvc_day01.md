# Spring_Mvc_day01

### SpringMvc概述

* **Spring MVC**框架是一个开源的Java平台，为开发强大的基于JavaWeb应用程序提供全面的基础架构支持，并且使用起来非常简单容易。
* **Spring web MVC**框架提供了MVC(**模型 - 视图 - 控制器**)架构，用于开发灵活和松散耦合的Web应用程序的组件。 **MVC**模式使应用程序的不同组件(输入逻辑，业务逻辑和UI逻辑)合理有效的分离，同时又有效的将各组件组合一起完成功能。
*  **模型**(Model)           封装了应用程序数据，通常它们将由`POJO`类组成。
*  **视图**(View)             负责渲染模型数据，一般来说它负责生成客户端浏览器可以解释HTML输出。
* **控制器**(Controller)     负责处理用户请求并构建适当的模型，并将其传递给视图进行渲染。

### SpringMVC的核心DispatcherServlet程序

* DispatcherServlet 前端控制器，它主要用于接收SpringMVC程序中所有客户端发送过来的请求。然后再根据请求的路径（请求地址）再分发给不同的Controller控制器中的业务方法。将Controller中方法返回的结果，以及数据交给视图去渲染执行输出。

### Hello Word

需要的jar包

spring-aop-5.2.5.RELEASE.jar

spring-aspects-5.2.5.RELEASE.jar

spring-beans-5.2.5.RELEASE.jar

spring-context-5.2.5.RELEASE.jar

spring-core-5.2.5.RELEASE.jar

spring-expression-5.2.5.RELEASE.jar

spring-jcl-5.2.5.RELEASE.jar

spring-web-5.2.5.RELEASE.jar

spring-webmvc-5.2.5.RELEASE.jar

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--
        DispatcherServlet 是SpringMVC的前端控制器（SpringMVC也有自己的一个SpringIOC容器，它也需要Spring的配置文件）
        在DispatcherServlet 前端控制器里，会创建一个SpringIOC容器对象
    -->
    <servlet>
<!--        如果web-Inf下配置了xml文件,并且配置xml文件的命名格式是
            <servlet-name>标签里面的名字加上-servlet.xml那么下面初始化参数的配置可以省略
			配置好的文件名为：dispatcherServlet-servlet.xml
-->
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!-- <init-param>
&lt;!&ndash;            初始化参数 如果上面在web-Inf下配置了那么可以省略初始化操作  &ndash;&gt;
            contextConfigLocation这个值是固定的
			<param-name>contextConfigLocation</param-name>
            <param-value>classpath:mvc.xml</param-value>
        </init-param>-->
<!--        配置加载因子  优先加载-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <!--  / 表示除只放行jsp
  			  /* 表示都不放行
		-->
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

```xml
dispatcherServlet-servlet.xml配置文件：

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.atguigu"/>
<!--    配置视图解析器，这样在java的controller中的返回值只需要写文件名即可-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="viewResolver">
<!--        前缀  会把这个拼接的controller返回值的前面-->
        <property name="prefix" value="/page/"/>

        <!--        后缀  会把这个拼接的controller返回值的后面-->
        <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

```java
	//@RequestMapping  注解是给当前业务方法配置一个唯一的访问地址
	//value = "/hello   url访问地址，相当于servlet中的url一样
@RequestMapping(value = "/hello)
    public String hello(){
        System.out.println("SpringMvc Hello,你好中国!!");
        return "ok";
    }

```

### 视图解析器

```xml
<!--    配置视图解析器，这样在java的controller中的返回值只需要写文件名即可-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="viewResolver">
<!--        前缀  会把这个拼接的controller返回值的前面-->
        <property name="prefix" value="/page/"/>

        <!--        后缀  会把这个拼接的controller返回值的后面-->
        <property name="suffix" value=".jsp"/>
    </bean>
```

视图解析器的好处: 

1. 方法我们返回值里只需要写上简短的内容即可

2. 视图解析器会把Controller方法的返回值,做前缀和后缀的拼接操作,然后再跳转

3. 方便我们移动资源的目录

### @RequestMapping

@RequestMapping是给个方法配置一个访问地址。就比如web学习的Servlet程序，在web.xml中配置了访问地址之后，它们之间就有一个访问映射关系。

#### value属性

* value属性是给方法配置访问地址使用.

* 它可以配置一个或者多个请求路径.

* 注意: 一个方法可以有多个请求路径 , 一个路径只能分配给一个方法
  * 例：@RequestMapping(value = {"/hello","/manger"})

#### params属性

* params = "username"表示访问路径必须携带参数username(在地址栏中的访问路径加?username)
* @RequestMapping(value = "/hello",params = "username=admin")表示必须有username并且还要等于于admin
* @RequestMapping(value = "/hello",params = "username=admin")表示必须有username并且还要不等于admin
* @RequestMapping(value = "/hello",params = {"username!=admin","password=qwerdf666"})
* 表示必须要有username=admin并且还要有password=qwerdf666
* 再有多个参数时多个参数的链接符时&&的关系
  * 例：@RequestMapping(value = "/hello",params = {"username!=admin","password=qwerdf666"})

#### headers属性

headers 是 要求请求头匹配。在使用规则上，跟params一样。

```java
 // @RequestMapping(value = "/hello",headers = "User-Agent") 表示要求请求头中必须有User-Agent请求头
@RequestMapping(value = "/hello",headers = "User-Agent") 
public String hello(){
        System.out.println("SpringMvc Hello,你好中国!!");
        return "ok";
    }
```

#### method属性

method属性要求请求的方式必须匹配.也就是GET请求,或POST请求( 其他请求也一样 ).

如果没有标识method属性值,表示任意的请求方式都可以

```java
//@RequestMapping(value = "/hello",method = RequestMethod.POST) 表示设置请求方式
   @RequestMapping(value = "/hello",method = RequestMethod.POST)
    public String hello(){
        System.out.println("SpringMvc Hello,你好中国!!");
        return "ok";
    }
GET请求和其他请求相同改革属性就行了
```

### @RequestMapping标注在Controller类上

@RequestMapping不仅可以标识在方法上。还可以标识在Controller类上。这样可以给每个方法的请求都都添加一个前缀（又叫模块路径）

例：类上没有注解时的代码：http://localhost:8080/day01/hello

​		类上加上注解后的代码：类上没有注解时的代码：http://localhost:8080/day01/类上加上的那层路径名/hello

### ant模式地址通配符

**规则：精确匹配====>?匹配========>*匹配**

```java
 	//?代表任意一个字符/fu?
    //*代表任意多个字符/fu*
    //    /?/fun 代表任意一层单个字符的目录
    //    /*/fun 代表任意一层多个字符的目录
    //    /**/fun 代表任意多层多个字符的目录	

    @RequestMapping(value = "/?/fun")
    public String fun(){
        System.out.println("SpringMvc Hello,你好中国!!");
        return "ok";
    }
```

### Controller中如何接收请求参数

```java
@Controller
public class ParameterController {

    //原生API参数类型
    //HttpServletRequest类、HttpSession类、HttpServletResponse类
    @RequestMapping(value = "/param1")
    public String param1(HttpServletRequest request, HttpServletResponse response, HttpSession session){
        System.out.println(request.getContextPath());
        System.out.println(response.getHeaderNames());
        System.out.println(session.getId());
        return "hello";
    }
    //普通参数
    //方法中的参数名必须要和前端页面的参数名一致不然为null
    //当传入的参数不一致的时候可以用注解@RequestParam()
    //@RequestParam(value = "与前端传入的参数名一致",required = false（可以不传递和spring中的required一样）,defaultValue = "设置默认值")
    @RequestMapping(value = "/param2")
    public String param2(@RequestParam(value = "username",defaultValue = "hello boy！",required = false)
                                     String user, String password){
        System.out.println("username = " + user);
        System.out.println("password = " + password);
        return "hello";
    }

    //普通类型数组的参数
    @RequestMapping(value = "/param3")
    public String param3(String[] hobby){
        System.out.println(hobby[0]);
        System.out.println(hobby[1]);
        System.out.println(hobby[2]);
        return "hello";
    }

    //@RequestHeader获取请求头入参
    @RequestMapping(value = "/param4")
    public String param4(@RequestHeader("User-Agent") String UserAgent){
        System.out.println("UserAgent = " +UserAgent);
        return "hello";
    }

    //@CookieValue获取Cookie值入参
    @RequestMapping(value = "/param5")
    public String param5(@CookieValue("JSESSIONID") String JSESSIONID){
        System.out.println("JSESSIONID = " +JSESSIONID);
        return "hello";
    }

    //一个Pojo对象类型的参数
    @RequestMapping(value = "/param6")
    public String param6(Person person){
        System.out.println("person = " +person);
        return "hello";
    }

    //对象中套对象（级联属性）
    @RequestMapping(value = "/param7")
    public String param7(Person person){
        System.out.println("person = " +person);
        return "hello";
    }
}
xml同上面的hello word一样 xml同上面的hello word一样  xml同上面的hello word一样  xml同上面的hello word一样
```

```jsp
  <form action="${pageContext.request.contextPath}/param6" method="get">
<%--    这里的name必须与pojo中的属性一样 不然赋不上值--%>
<%--      级联属性的赋值，name必须为类中的属性（这个属性是个对象），然后在.这个属性里面自己对象的属性名--%>
    ID: <input type="text" name="id"><br/>
    姓名: <input type="text" name="name"><br/>
    年龄: <input type="text" name="age"><br/>
    车类型: <input type="text" name="car.type"><br/>
    车牌: <input type="text" name="car.cardNo"><br/>
    <input type="submit" value="提交">
  </form>
```

### 返回值的设置	

```java
@Controller
public class ReturnController {

    /**
     * 返回String默认情况
     * 默认使用的时请求转发，地址栏不变
     */
    @RequestMapping(value = "/return1")
    public String return1() {
        System.out.println("哈哈  呵呵  喜喜");
        return "hello";
    }

    /**
     * 显示转发
     * 使用forward显示转发，当使用显示转发的时候不会走视图解析器，所以要把路径名写全
     * 所有路径都用绝对路径以  /打头
     */
    @RequestMapping(value = "/return2")
    public String return2() {
        System.out.println("哈哈  呵呵  喜喜");
        return "forward:/page/hello.jsp";
    }
    /**
     * 显示重定向
     * 使用redirect显示重定向，当使用显示重定向的时候不会走视图解析器，所以要把路径名写全
     * 所有路径都用绝对路径以  /打头
     */
    @RequestMapping(value = "return3")
    public String return3() {
        System.out.println("哈哈  呵呵  喜喜");
        return "redirect:/page/hello.jsp";
    }

    /**
     * 返回modelAndView的默认情况
     * 方式一创建modelAndView对象调用set方法
     * 方式二创建modelAndView对象直接在构造器中赋值
     */
    @RequestMapping(value = "return4")
    public ModelAndView return4() {
        System.out.println("modelAndView test");
        ModelAndView modelAndView = new ModelAndView("hello");
        //方式一创建modelAndView对象调用set方法
//        modelAndView.setViewName("hello");
        return modelAndView;
    }


    /**
     * 返回modelAndView的显示请求转发
     * 方式一创建modelAndView对象调用set方法
     * 方式二创建modelAndView对象直接在构造器中赋值
     * 使用显示转发不会使用视图解析器，所以要把路径写全
     */
    @RequestMapping(value = "return5")
    public ModelAndView return5() {
        System.out.println("modelAndView test5");
        ModelAndView modelAndView = new ModelAndView();

        modelAndView.setViewName("forward:/page/hello.jsp");
        return modelAndView;
    }

    /**
     * 返回modelAndView的显示请求转发
     * 方式一创建modelAndView对象调用set方法
     * 方式二创建modelAndView对象直接在构造器中赋值
     * 使用显示重定向不会使用视图解析器，所以要把路径写全
     */
    @RequestMapping(value = "return6")
    public ModelAndView return6() {
        System.out.println("modelAndView test6666");
        ModelAndView modelAndView = new ModelAndView("redirect:/page/hello.jsp");

//        modelAndView.setViewName();
        return modelAndView;
    }
}
xml同上面的hello word一样 xml同上面的hello word一样  xml同上面的hello word一样  xml同上面的hello word一样
```

### springMVC执行流程

![image-20210329104703321](typora-user-images\image-20210329104703321.png)

![image-20210330211530734](typora-user-images\image-20210330211530734.png)