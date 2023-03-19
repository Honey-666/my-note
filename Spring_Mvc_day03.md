# Spring_Mvc_day03

### Restful风格

**Restful 一种软件架构风格、设计风格，而不是标准**，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制。

**Restful是一种设计风格。对于我们Web开发人员来说。就是使用一个url地址表示一个唯一的资源。然后把原来的请求参数加入到请求资源地址中。然后原来请求的增，删，改，查操作。改为使用HTTP协议中请求方式GET、POST、PUT、DELETE表示。**

### restful风格格式

传统的方式是：

比如：http://ip:port/工程名/资源名?请求参数

举例：http://127.0.0.1:8080/springmvc/book?action=delete&id=1

restful风格是：

比如：http://ip:port/工程名/资源名/请求参数/请求参数

举例：http://127.0.0.1:8080/springmvc/book/delete/1

请求的动作删除由请求方式delete决定

### restful风格中请求方式GET、POST、PUT、DELETE分别表示查、增、改、删

GET请求		对应   查询

http://ip:port/工程名/book/1		HTTP请求GET		表示要查询id为1的图书

http://ip:port/工程名/book			HTTP请求GET		表示查询全部的图书

POST请求	对应	添加

http://ip:port/工程名/book			HTTP请求POST		表示要添加一个图书

PUT请求		对应	修改

http://ip:port/工程名/book/1		HTTP请求PUT		表示要修改id为1的图书信息

DELETE请求	对应	删除

http://ip:port/工程名/book/1		HTTP请求DELETE		表示要删除id为1的图书信息

### SpringMVC中如何发送GET请求、POST请求、PUT请求、DELETE请求。

我们知道发起GET请求和POST请求，只需要在表单的form标签中，设置method=”get” 就是GET请求。

设置form标签的method=”post”。就会发起POST请求。而PUT请求和DELETE请求。要如何发起呢。

1. 要有post请求的form标签
2. 在form表单中，添加一个额外的隐藏域_method=”PUT”或_method=”DELETE”
3. 在web.xml中配置一个Filter过滤器org.springframework.web.filter.HiddenHttpMethodFilter**（注意，这个Filter一定要在处理乱码的Filter后面）**

### Restful风格的Controller如何实现

JAVA代码：

```java
@Controller
public class TestController {
  /**
     * restful风格测试查询一本图书
     * @return
     */
    @RequestMapping(value = "/book/1",method = RequestMethod.GET)
    public String findBookById(){
        System.out.println("查询一本图书");
        return "redirect:/index.jsp";
    }
   /**
     * restful风格测试查询所有图书
     * @return
     */
    @RequestMapping(value = "/book",method = RequestMethod.GET)
    public String findAllBook(){
        System.out.println("查询所有图书");
        return "redirect:/index.jsp";
    }
   /**
     * restful风格测试查询所有图书
     * @return
     */
    @RequestMapping(value = "/book",method = RequestMethod.POST)
    public String insertBook(){
        System.out.println("添加一本图书");
        return "redirect:/index.jsp";
    }

   /**
     * restful风格测试修改一本图书
     * @return
     */
    @RequestMapping(value = "/book/1",method = RequestMethod.PUT)
    public String updateBook(){
        System.out.println("修改book的id为1的图书");
        return "redirect:/index.jsp";
    }

   /**
     * restful风格测试删除一本图书
     * @return
     */
    @RequestMapping(value = "/book/1",method = RequestMethod.DELETE)
    public String delBook(){
        System.out.println("删除book的id为1的图书");
        return "redirect:/index.jsp";
    }
}

```

JSP代码：

```jsp
<%--                        restful风格测试        --%>
<a href="${pageContext.request.contextPath}/book/1">查询一本图书</a><br/><br/>
<a href="${pageContext.request.contextPath}/book">查询所有图书</a><br/><br/>
<form action="${pageContext.request.contextPath}/book" method="post">
  <input type="submit" value="添加一本图书">
</form>
<br/><br/>
<!--     这里 put 和delete 都要使用隐藏域，并且隐藏域里面的那么都是必须是_method -->
<form action="${pageContext.request.contextPath}/book/1" method="post">
  <input type="hidden" name="_method" value="PUT">
  <input type="submit" value="修改一本图书">
</form>
<br/><br/>
<form action="${pageContext.request.contextPath}/book/1" method="post">
  <input type="hidden" name="_method" value="DELETE">
  <input type="submit" value="删除一本图书">
</form>
```

xml配置：

```xml
    <!--表示支持restful风格的Filter
        注意要写在处理字符集后面，因为处理字符集要放在请求参数前面，这里面有请求参数
-->
   <filter>
       <filter-name>hiddenHttpMethodFilter</filter-name>
       <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
   </filter>
    <filter-mapping>
        <filter-name>hiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

### Restful风格在高版本Tomcat中无法转发到jsp页面

在Tomcat8之后的一些高版本，使用restful风格访问然后转发到jsp页面。就会有如下的错误提示：

 ![image-20201212210157470](typora-user-images\image-20201212210157470.png)

解决方案有两种：

1 使用重定向

2 在跳转去的页面中page指令里添加 isErrorPage=true 属性

### @PathVariable 路径参数获取

如何接收restful风格请求的参数。比如前面的id值

```java
/**
 * @RequestMapping(value = "/book/{id}") <br/>
 * value = "/book/{id}" 地址中 {参数名} 我们管它叫路径参数，它是一个变量 <br/>
 * @PathVariable("id") Integer id 表示获取路径参数id的值，赋给方法参数id<br/>
 */
@RequestMapping(value = "/book/{id}", method = RequestMethod.GET)
public String queryBookById(
        @PathVariable("id") Integer id) {
    System.out.println(" 查询 id 为 " + id + " 的图书信息 ");

    return "forward:/index.jsp";
}
```

### 解决中文乱码问题

```xml
在web.xml中配置解决中文乱码问题
配置最上边
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

### 文件上传与下载

文件上传在SpringMVC中如何实现：

准备工作前端控制器 , SpringMVC两个标配

1. 准备一个文件上传的表单

2. 导入文件上传需要的jar包

- commons-fileupload-1.2.1.jar
- commons-io-1.4.jar

3. 配置文件上传解析器	**CommonsMultipartResolver**

4. 配置Controller控制器的代码

JSP代码：

```jsp
文件上传的表单和下载的超链接： 
<form action="${pageContext.request.contextPath}/upload" method="post" enctype="multipart/form-data">
     <input type="text" name="username"><br>
     <input type="file" name="photo"><br>
     <input type="submit" value="上传">
 </form>
 <a href="${pageContext.request.contextPath}/download">文件下载</a>
```

xml配置：

```xml
    <!-- 配置解析上传数据的解析器
      而且要求id值必须是: multipartResolver ( 否则不能使用 )
 -->
    <bean class="org.springframework.web.multipart.commons.CommonsMultipartResolver" id="multipartResolver">
        <property name="defaultEncoding" value="UTF-8"/>
    </bean>
```

JAVA代码：

```java
@Controller
public class FileController {
    /**
     * 文件上传
     *MultipartFile类型接收上传的文件
     * @param username
     * @param photo
     * @return
     */
    @RequestMapping("/upload")
    public String upload(
            @RequestParam("username") String username,
            @RequestParam("photo") MultipartFile photo) {

        System.out.println("文件名是：" + username);
        try {
        
            //获取文件名
            String filename = photo.getOriginalFilename();
            //获取uuid随机数
            String uid = UUID.randomUUID().toString().replace("-", "");
            //截取文件名的后缀
            String fileExt = filename.substring(filename.lastIndexOf(".") + 1);
            //将uuid和获取到的文件后缀拼接
            filename = uid+"."+fileExt;
            //上传文件
            photo.transferTo(new File("D:/Tomcat/" + filename));
        } catch (IOException e) {
            e.printStackTrace();
        }

        return "redirect:/pages/ok.jsp";
    }

    /**
     * 文件下载
     */
    @RequestMapping("/download")
    public ResponseEntity<byte[]> download(HttpSession session){
        String filename = "1.jpg";
        // 获取ServletContext对象
        ServletContext servletContext = session.getServletContext();
        InputStream inputStream = servletContext.getResourceAsStream("/files/" + filename);

         // 将输入流中的数据读取出来。转换为字节数组
        byte[] bytes = null;
        try {
            bytes = IOUtils.toByteArray(inputStream);
            inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
         // 获取指定路径文件的数据类型
        String mimeType = servletContext.getMimeType("/files/" + filename);

        MultiValueMap headers = new HttpHeaders();
         // 设置返回的数据类型
        headers.add("Content-Type",mimeType);
        // 设置响应头，告诉浏览器收到的数据统一以附件的形式用于下载
        headers.add("Content-Disposition", "attachement;filename=" +filename);

     // 创建ResponseEneity返回客户端需要的数据
    /**
     *  ResponseEntity可以分别用来设置响应行，响应头，响应体<br/>
     *  第一个参数是响应体<br/>
     *  第二个参数是响应头<br/>
     *  第三个参数是响应状态码<br/>
     */

        ResponseEntity responseEntity = new ResponseEntity<byte[]>(bytes,headers, HttpStatus.OK);
        return responseEntity;
    }
}
```

### HandlerInterceptor拦截器

SpringMVC中的拦截器，它可以对SpringMVC的资源的请求进行拦截处理（像JavaWeb三大组件之Filter过滤器）。

SpringMVC拦截器HandlerInterceptor使用步骤如下：

1 编写一个类去实现HandlerInterceptor接口

2 实现其接口方法

3 到springmvc的配置文件中去配置拦截器

#### 单个HandlerInterceptor拦截器的示例

1、编写一个类去实现HandlerInterceptor接口

2、到Spring的容器配置文件中去配置拦截器，让SpringMVC知道都拦截哪些目标方法

```java
public class FirstInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("一   目标方法前执行");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

        System.out.println("一   目标方法后执行");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("一   页面渲染完成！");
    }
}
```

xml配置

```xml
<!--     <mvc:interceptors> 配置拦截器,如果里面不配置 <mvc:interceptor>仅有一对 <mvc:interceptors>标签的话就是拦截全部
         <mvc:interceptor> 标签配置每一个拦截器
                path配置拦截的路径
                bean 拦截器是哪个类(也就是那个类实现了HandlerInterceptor接口)
-->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/hello"/>
            <bean class="com.atguigu.Interceptor.FirstInterceptor" />
        </mvc:interceptor>
```

Controller代码：

```java
@Controller
public class HelloController {

    @RequestMapping("/hello")
    public String hello(Map<String,String> map){

        System.out.println("目标方法执行");
        map.put("name","fangfang");
        return "redirect:/pages/ok.jsp";
    }
}
```

正常情况下.它拉执行顺序是:

​	preHandle()  ==>>  目标方法  ==>> postHandle()  ==>>  渲染页面  ==>> afterCompletion() 

#### 单个拦截器异常时的执行顺序

一：目标方法前返回false的情况：

1、目标方法前执行     返回false

2、这是目标方法     不执行

3、目标方法之后     不执行

4、这是渲染页面     不执行

5、页面渲染完成！     不执行 

二：目标方法前返回true的情况，目标方法异常

1、目标方法前执行     返回true

2、这是目标方法     异常

3、目标方法之后     不执行

4、这是渲染页面     渲染异常页面

5、页面渲染完成！     执行

三：目标方法前返回true的情况，目标方法后异常 

1、目标方法前执行     返回true

2、这是目标方法     执行

3、目标方法之后     异常

4、这是渲染页面     渲染异常页面

5、页面渲染完成！     执行

四：目标方法前返回true的情况，渲染页面异常

1、目标方法前执行     返回true

2、这是目标方法     执行

3、目标方法之后     执行

4、这是渲染页面     异常

5、页面渲染完成！     执行

#### 多个拦截器的执行介绍 

同上面一样在写一个实现类，xml配置如下：

```xml
<!--     <mvc:interceptors> 配置拦截器,如果里面不配置 <mvc:interceptor>仅有一对 <mvc:interceptors>标签的话就是拦截全部
         <mvc:interceptor> 标签配置每一个拦截器
                path配置拦截的路径
                bean 拦截器是哪个类(也就是那个类实现了HandlerInterceptor接口)
-->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/hello"/>
            <bean class="com.atguigu.Interceptor.FirstInterceptor" />
        </mvc:interceptor>

        <mvc:interceptor>
            <mvc:mapping path="/hello"/>
            <bean class="com.atguigu.Interceptor.SecondInterceptor" />
        </mvc:interceptor>
    </mvc:interceptors>
```

多个拦截器正常执行顺序:

一   目标方法前执行
二   目标方法前执行
目标方法执行
二   目标方法后执行
一   目标方法后执行
二   页面渲染完成
一   页面渲染完成！

多个拦截器执行出现异常时。它的执行细节有什么注意：

​			如果一个拦截器的preHandle()方法只要返回了true。那么它的afterCompletion() 就会执行。

多个拦截器异常情况下,我们只需要记住一点 : **如果preHandle() 方法返回了true.它的afterCompletion() 方法就一定会执行,由于目标方法不执行,所以postHandle()不会执行**

