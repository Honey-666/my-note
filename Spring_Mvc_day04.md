# Spring_Mvc_day04

### 处理异常的三种方式

#### 方式一使用@ExceptionHandler注解处理异常

```java
@Controller
public class ExceptionController {

    /**
     *  @ExceptionHandler注解的方法会在Controller抛出异常时,自动调用<br/> 
     *   1 处理异常 <br/> 
     *   2 跳转到更友好的错误提示页面 (目标方法的跳转无效,使用异常方法的跳转) <br/> 
     */
    @ExceptionHandler
    public String exceptionHandler1(Exception exc){
        System.out.println("exceptionHandler1 异常信息是: " + exc);
        return "error1";
    }

    /**
     *  @ExceptionHandler注解的方法会在Controller抛出异常时,自动调用<br/> 
     *   1 处理异常 <br/> 
     *   2 跳转到更友好的错误提示页面 (目标方法的跳转无效,使用异常方法的跳转) <br/> 
     */
    @ExceptionHandler
    public String exceptionHandler2(RuntimeException exc){
        System.out.println("exceptionHandler2 异常信息是: " + exc);
        return "error2";
    }

    /**
     *  @ExceptionHandler注解的方法会在Controller抛出异常时,自动调用<br/> 
     *   1 处理异常 <br/> 
     *   2 跳转到更友好的错误提示页面 (目标方法的跳转无效,使用异常方法的跳转) <br/> 
     */
    @ExceptionHandler
    public String exceptionHandler3(ArithmeticException exc){
        System.out.println("exceptionHandler3 异常信息是: " + exc);
        return "error3";
    }


    @RequestMapping("/hello")
    public String testException(){
        System.out.println(" 这是hello方法() ");
        int i = 12 / 0;
        return "redirect:/index.jsp";
    }
}
```

#### 方式二使用@ControllerAdvice注解处理异常(推荐)

@ControllerAdvice注解标识在放在bean上.

它可以让当前Controller中所有的异常处理方法改为全局异常处理方法

当有局部异常处理方法和全局异常处理方法同时存在时.他们的优先顺序是 :  精确优先 >局部优先 

```java
//在controller之后执行,拦截异常,在全局异常处理器中,也有多个异常处理器
//也会根据异常类的关系进行匹配,越精确的越先执行
@ControllerAdvice
@Component
public class ExceptionControllerAdvice {

    /**
     *  @ExceptionHandler注解的方法会在Controller抛出异常时,自动调用<br/> 
     *   1 处理异常 <br/> 
     *   2 跳转到更友好的错误提示页面 (目标方法的跳转无效,使用异常方法的跳转) <br/> 
     */
    @ExceptionHandler
    public String exceptionHandler1(Exception exc){
        System.out.println("exceptionHandler1 异常信息是: " + exc);
        return "error1";
    }

    /**
     *  @ExceptionHandler注解的方法会在Controller抛出异常时,自动调用<br/> 
     *   1 处理异常 <br/> 
     *   2 跳转到更友好的错误提示页面 (目标方法的跳转无效,使用异常方法的跳转) <br/> 
     */
    @ExceptionHandler
    public String exceptionHandler2(RuntimeException exc){
        System.out.println("exceptionHandler2 异常信息是: " + exc);
        return "error2";
    }

    /**
     *  @ExceptionHandler注解的方法会在Controller抛出异常时,自动调用<br/> 
     *   1 处理异常 <br/> 
     *   2 跳转到更友好的错误提示页面 (目标方法的跳转无效,使用异常方法的跳转) <br/> 
     */
    @ExceptionHandler
    public String exceptionHandler3(ArithmeticException exc){
        System.out.println("exceptionHandler3 异常信息是: " + exc);
        return "error3";
    }
}
```

#### 方式三使用SimpleMappingExceptionResolver类映射异常跳转

```xml
<!--     简单的映射异常解析器SimpleMappingExceptionResolver-->
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver" id="exceptionResolver">
    <property name="exceptionMappings">
         <!-- 每一个prop标签表示一种类型的异常.和对应的一个错误跳转路径
                     key是具体的异常全类名
                     error1 , error2 , error3 是视图名( 跳转的路径 )
                -->
        <props>
            <prop key="java.lang.Exception">error1</prop>
            <prop key="java.lang.RuntimeException">error2</prop>
            <prop key="java.lang.ArithmeticException">error3</prop>
        </props>
    </property>
</bean>
```

**异常处理顺序：越精确越优先执行**

### 使用@ResponseBody将返回的数据转成json ( 重点 )

json的使用无非就两种情况,

1. 返回json数据
2. 接收json数据,转换为Java对象( )而我们在使用json的时候,一般大多是和Ajax请求一起使用.)

使用的步骤如下：

1、导入json相关的包到web工程中

- jackson-annotations-2.1.5.jar
- jackson-core-2.1.5.jar
- jackson-databind-2.1.5.jar

2、编写一个请求的方法接收请求，并返回数据对象

3、在方法上添加注解@ResponseBody自动将返回值json化

#### 响应JSON数据

```java
@Controller
public class JsonController {
//=============================将响应的数据转换为json=====================

    /**
     * @param
     * @return
     * @ResponseBody 注解告诉SpringMVC将返回值。转换为json字符串返回给客户端
     */
    @ResponseBody
    @RequestMapping("/json1")
    public Person jsonController1() {
        Person person1 = new Person(1, "ff");
        return person1;
    }

    /**
     * 返回list转换为json
     *
     * @return
     */
    @ResponseBody
    @RequestMapping("/json2")
    public List<Person> jsonController2() {
        List<Person> list = new ArrayList<>();
        list.add(new Person(1, "ff"));
        list.add(new Person(2, "cc"));
        list.add(new Person(3, "nn"));
        return list;
    }

    /**
     * 返回map转换为json
     *
     * @return
     */
    @ResponseBody
    @RequestMapping("/json3")
    public Map<String, Person> jsonController3() {
        Map<String, Person> map = new HashMap<>();
        map.put("1", new Person(1, "ff"));
        map.put("2", new Person(2, "cc"));
        map.put("3", new Person(3, "nn"));
        return map;
    }
```

#### 使用@RestController简化返回JSON数据

```java
//@RestController== @Controller + @ResponseBody
@RestController
public class JsonController {
//=============================将响应的数据转换为json=====================

    /**
     * @param
     * @return
     * @ResponseBody 注解告诉SpringMVC将返回值。转换为json字符串返回给客户端
     */
  
    @RequestMapping("/json1")
    public Person jsonController1() {
        Person person1 = new Person(1, "ff");
        return person1;
    }

    /**
     * 返回list转换为json
     *
     * @return
     */
    @RequestMapping("/json2")
    public List<Person> jsonController2() {
        List<Person> list = new ArrayList<>();
        list.add(new Person(1, "ff"));
        list.add(new Person(2, "cc"));
        list.add(new Person(3, "nn"));
        return list;
    }

    /**
     * 返回map转换为json
     *
     * @return
     */
    @RequestMapping("/json3")
    public Map<String, Person> jsonController3() {
        Map<String, Person> map = new HashMap<>();
        map.put("1", new Person(1, "ff"));
        map.put("2", new Person(2, "cc"));
        map.put("3", new Person(3, "nn"));
        return map;
    }
```

#### 将请求数据转换为JSON

```java
//@RestController== @Controller + @ResponseBody
@RestController
public class JsonController {
    //=================================将请求的数据转换为json===================

    /**
     * 单个对象
     * @RequestBody 将数据转换为json数据
     * @param person
     * @return
     */
    @RequestMapping(value = "/save1",method = RequestMethod.POST)
    public Person savePerson1(@RequestBody Person person) {
        System.out.println(person);//打印json数据
        person.setId(10);
        return person;//响应回去
    }

    /**
     * @RequestBody 将请求的数据转换为json数据
     * List集合
     * @param person
     * @return
     */
  //  @ResponseBody
    @RequestMapping(value = "/save2",method = RequestMethod.POST)
    public List<Person> savePerson2(@RequestBody List<Person> person) {
        System.out.println(person);//打印json数据
        return person;//响应回去
    }

}
```

前端JSP页面：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %>
<html>
  <head>
    <title>$Title$</title>

    <script type="text/javascript" src="${pageContext.request.contextPath}/script/jquery-1.7.2.js"></script>

    <script type="text/javascript">
      $(function () {
		//-------------将请求的单个对象转换为json数据---------------------
        // 绑定单击事件
        $("button:eq(0)").click(function () {
                /**
                   * 1:url
                   * 2:数据
                   * 6:请求方式
                   * 3:回调
                   * 4:响应类型
                   * 5:响应数据类型
                   * contentType : application/json  表示发送给服务器的数据格式是: json字符串<br/>
                   * contentType : "application/json" 和 服务器注解 @RequestBody 是组合在一起使用的
                    */
                var person = {"id":1,"name":'mm'};
          $.ajax({
                url:"${pageContext.request.contextPath}/save1",
                data:JSON.stringify(person),
                //请求方式必须为post
                type:"post",
                dataType:"json",
                contentType:"application/json",
                success:function (obj) {
                    console.log(obj);
                }

          })

        })

        //-------------将请求的list集合转换为json数据---------------------

        $("button:eq(1)").click(function () {
          /**
           * 1:url
           * 2:数据
           * 6:请求方式
           * 3:回调
           * 4:响应类型
           * 5:响应数据类型
           * contentType : application/json  表示发送给服务器的数据格式是: json字符串<br/>
           * contentType : "application/json" 和 服务器注解 @RequestBody 是组合在一起使用的
           */
          var person = [{"id":1,"name":'mm'},{"id":2,"name":'ff'},{"id":1,"name":'cc'}];
          $.ajax({
            url:"${pageContext.request.contextPath}/save2",
            data:JSON.stringify(person),
            //请求方式必须为post
            type:"post",
            dataType:"json",
            contentType:"application/json",
            success:function (obj) {
              console.log(obj);
            }

          })

        })

      })


    </script>

  </head>
  <body>
<%--  将响应的数据转换为json数据在页面显示--%>
  <h1><a href="${pageContext.request.contextPath}/hello">hello</a></h1>
  <br/>
  <br/>
  <br/>
  <br/>
<%--  =====================将请求的数据转换为json数据=====================--%>

  <button>Person</button>
  <br>
  <br>
    <button>PersonList</button>

  </body>
</html>
```

### **mybatis+spring+springMvc见ideaspringMvc下的day04_ssm     model**

