# Spring_Mvc_day02

### 数据在域中的保存

```java
//types属性，它表示把request域中value类型为Integer.class或Person.class类型的键值对，也保存到Session中
@SessionAttributes(types = {Integer.class, Person.class})
@Controller
public class RequestController {

    @RequestMapping("/test")
    public String test(){
        System.out.println("测试");
        return "test";
    }

    //Request对象中保存数据
    @RequestMapping("/request")
    public String requestScope(HttpServletRequest request){

        System.out.println("测试有没有方法调用~~~~");
        request.setAttribute("reqKey1","value1");
        request.setAttribute("reqKey2","value2");
        return "scope";
    }

    //Session域中保存数据
    @RequestMapping("/session")
    public String sessionScope(HttpSession session){
        System.out.println("测试有没有方法调用~~~~");
        session.setAttribute("sessKey1","sessValue1");
        session.setAttribute("sessKey2","sessValue2");
        return "scope";
    }

    //ServletContext域中保存数据
    //两种方式
        //方式一:通过注解@Autowired因为ServletContext这恶对象由容器创建,所以我们直接使用即可
        //方式二:通过session域的方法获得servletContext
    //方式一
 /*   @Autowired
    ServletContext servletContext;*/

    @RequestMapping("/servletContext")
    //方式二传入session通过session的方法获取servletContext
    public String servletContextScope(HttpSession session){
        System.out.println("方法执行!!");
        session.getServletContext().setAttribute("servletKey1","servletValue1");
        session.getServletContext().setAttribute("servletKey2","servletValue2");
        return "scope";
    }

    //Map形式保存数据到Reqeust域中:
    @RequestMapping("/map")
    public String mapToRequest(Map<String,Object> map){
        System.out.println("方法执行!!");
        // 这些数据同时也会同步到Reqeust域中
        map.put("mapKey1","mapValue1");
        map.put("mapKey2","mapValue2");
        return "scope";
    }

    //Model形式保存数据到Reqeust域 中:
    @RequestMapping("/model")
    public String modelToRequest(Model model){
        System.out.println("方法执行!!");
        // 这些数据同时也会同步到Reqeust域中
        model.addAttribute("modelKey1","modelValue1");
        model.addAttribute("modelKey2","modelValue2");
        return "scope";
    }
    //ModelMap形式保存数据到Request域中
    @RequestMapping("/modelAndMap")
    public String modelAndMap(ModelMap modelMap){
        System.out.println("方法执行!!");
        // 这些数据同时也会同步到Reqeust域中
       modelMap.addAttribute("modelMapKey1","modelMapValue1");
       modelMap.addAttribute("modelMapKey2","modelMapValue2");
        return "scope";
    }


    //BindingAwareModelMap隐含模型对象的介绍
    @RequestMapping("/mapAndModelAndModelMap")
    public String mapAndModelAndModelMap(Map<String,Object> map,Model model,ModelMap modelMap){
        System.out.println(map);
        System.out.println(model);
        System.out.println(modelMap);
        System.out.println("====================");

        map.put("mapKey","mapValue");
        System.out.println(map);
        System.out.println(model);
        System.out.println(modelMap);
        System.out.println("====================");

        model.addAttribute("modelKey","modelValue");
        System.out.println(map);
        System.out.println(model);
        System.out.println(modelMap);
        System.out.println("====================");

        modelMap.addAttribute("modelMapKey","modelMapValue");
        System.out.println(map);
        System.out.println(model);
        System.out.println(modelMap);
        System.out.println("====================");

        System.out.println(map.getClass());
        System.out.println(model.getClass());
        System.out.println(modelMap.getClass());

        return "scope";
    }
    /*  ------------------结果-----------------------
        {}
        {}
        {}
        ====================
        {mapKey=mapValue}
        {mapKey=mapValue}
        {mapKey=mapValue}
        ====================
        {mapKey=mapValue, modelKey=modelValue}
        {mapKey=mapValue, modelKey=modelValue}
        {mapKey=mapValue, modelKey=modelValue}
        ====================
        {mapKey=mapValue, modelKey=modelValue, modelMapKey=modelMapValue}
        {mapKey=mapValue, modelKey=modelValue, modelMapKey=modelMapValue}
        {mapKey=mapValue, modelKey=modelValue, modelMapKey=modelMapValue}
        ====================
        class org.springframework.validation.support.BindingAwareModelMap
        class org.springframework.validation.support.BindingAwareModelMap
        class org.springframework.validation.support.BindingAwareModelMap
    */
```

JSP测试代码：

```jsp
request域中key1的值是==>${requestScope.reqKey1}<br/>
request域中key2的值是==>${requestScope.reqKey2}<br/>
<hr>
session域中key1的值是==>${sessionScope.sessKey1}<br/>
session域中key2的值是==>${sessionScope.sessKey2}<br/>
<hr>
servletContext域中key1的值是==>${applicationScope.servletKey1}<br/>
servletContext域中key2的值是==>${applicationScope.servletKey2}<br/>
<hr>
map中的key1值是==>${requestScope.mapKey1}<br>
map中的key2值是==>${requestScope.mapKey2}<br>
<hr>
model中的key1值是==>${requestScope.modelKey1}<br>
model中的key2值是==>${requestScope.modelKey2}<br>
<hr>
modelMap中的key1值是==>${requestScope.modelMapKey1}<br>
modelMap中的key2值是==>${requestScope.modelMapKey1}<br>
```

### ModelAndView方式保存数据到request域中

```java
  //ModelAndView方式保存数据到request域中
    @RequestMapping("/modelAndViewRequest")
    public ModelAndView modelAndViewRequest(){
        ModelAndView modelAndView = new ModelAndView("scope");
        System.out.println(" modelAndViewToRequest () 方法调用了");
        modelAndView.addObject("modelAndViewKey1","modelAndViewValue1");
        modelAndView.addObject("modelAndViewKey2","modelAndViewValue2");

        return modelAndView;
    }

JSp测试代码：
    modelAndView中的key1值是==>${requestScope.modelAndViewKey1}<br>
	modelAndView中的key2值是==>${requestScope.modelAndViewKey2}<br>
```

### @SessionAttributes保存数据到Session域中（了解内容）

@SessionAttributes 注解可以标注在类上。它的作用是指定隐含模型中哪些数据也同步到Session域中。@SessionAttributes(value = { "key1","key2" }, types = { String.class, Book.class })

value属性，它表示把request域中key为key1，key2的键值对信息，也保存到Session中

types属性，它表示把request域中value类型为String.class\**或Book.class类型的键值对，也保存到Session中

Controller中的代码:

```java
@SessionAttributes(names = {"key1","value"})
@Controller
public class ScopeController {
   // @SessionAttributes保存数据到Session域中
   @RequestMapping("/session")
   public String SessionAttributesScope(Map<String,Object> map){

       map.put("key1","value1");
       return "ok";
   }
}

JSP测试：
    SessionAttributes:${sessionScope.key1}
（默认是被放到request，因为被@SessionAttributes注解修饰了所以就把他共享到session域中了，所以通过session域也能获取域中的value）
```

### @ModelAttribute注解

```java
/**
 * 1. 被标注了@ModelAttribute的方法都会在Controller的目标方法之前执行。
 * 2. 目标方法的参数（JavaBean对象）会先从隐含模型中获取值传入。
 * 3. 被标注在参数上，参数值会按照指定的key从隐含模型中获取值。
 * @param map
 */
@ModelAttribute
public void ModelAttributeScope(Map<String, Person> map){
    map.put("person1",new Person(1,"hh"));
}

@RequestMapping("/target")
public String testModetAttr(@ModelAttribute("person1") Person person){

    System.out.println(person);

    return "ok";
}
这里的person会从上面@ModelAttribute修饰的方法中获取
```

### 解决SpringMVC无法处理静态资源

我们给SpringMVC的前端控制器，配置的拦截路径是 ： 斜杠。

而这个斜杠跟Tomcat服务器系统中提供一个专门用来处理静态资源的Servlet程序的请求地址相同了。

就导致了Tomcat服务器中的专门用来处理静态资源的DefaultServlet程序无法再工作。

解决方法如下：

```xml
<!-- SpringMVC开发的（必备）标配 -->
<!-- 开启SpringMVC中对静态资源的处理支持 -->
<mvc:default-servlet-handler></mvc:default-servlet-handler>
<!-- 开启SpringMVC高级的注解功能 -->
<mvc:annotation-driven></mvc:annotation-driven>
```

