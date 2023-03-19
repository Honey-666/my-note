# Spring_day02

#### 三种数据库链接方式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

<!--      方式一   -->
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/person"/>
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </bean>
    <!--      方式二  引入外部properties文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource2">
        <property name="driverClassName" value="${driverClassName}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
    </bean>


    <!--      方式三  引入外部properties文件 -->
   <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
       <property name="location" value="classpath:jdbc.properties"/>
   </bean>
    <bean class="com.alibaba.druid.pool.DruidDataSource" id="dataSource3">
        <property name="driverClassName" value="${driverClassName}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
    </bean>
</beans>
```

#### Spring配置bean的常用注解

@Component		  配置Web层,Servcie层,DAO层之外的Bean对象.使用@Component

@Controller	 		配置Web层的组件.

@Service				  配置Service层的组件

@Repository			配置DAO组件

@Scope					 配置Bean的作用域 (单例.多例),用于类上,也可以用于方法上,当前方法上必须有@Bean

@PostConstruct      init-method

@PreDestory	       destory-method

@Value("abc")		  给基本类型注入    

@Value("${user}")   读取配置文件的key得到value给基本类型注入

@Value("${user:root}") 读取配置文件的key,如果没有次key,将root进行注入

@Configuraction      表明当前类是一个配置文件类

@ComponentScan(basePackages={})   扫描其它包下的注解

@Import  					导入其它配置文件类

@Bean						将当前方法返回值放入容器内,bean的Id是方法名



@Component示例

```java
//@Component == <bean id="person" class="com.atguigu.pojo.Person">
@Component
public class Person{
}
测试： 
   ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
	//使用注解后默认的id就是类名的小写
   Person person = context.getBean("person", Person.class);
   System.out.println("person = " + person);
XML代码：
    //扫描包下的所有注解
    <context:component-scan base-package="com.atguigu"/>
```

@Controller示例

```java
//@Controller == <bean id="personController" class="com.atguigu.pojo.PersonController">
@Controller
public class PersonController {
}
测试： 
   ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
	//使用注解后默认的id就是类名的小写
 PersonController personController = context.getBean("personController", PersonController.class);
   System.out.println("personController = " + personController);
```

@Service示例

```java
//@Service == <bean id="personService" class="com.atguigu.pojo.PersonService">
@Service
public class PersonService {
}
测试： 
   ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
	//使用注解后默认的id就是类名的小写
 PersonService personService = context.getBean("personService", PersonService.class);
   System.out.println("personService = " + personService);
```

@Repository

```java
//@Repository == <bean id="personDao" class="com.atguigu.pojo.PersonDao">
@Repository
public class PersonDao {
}
测试： 
   ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
	//使用注解后默认的id就是类名的小写
 PersonDao personDao = context.getBean("personDao", PersonDao.class);
   System.out.println("personDao = " + personDao);
```

#### 指定扫描包时的过滤内容

```xml
<!--扫描包下的所有注解-->
<!--    <context:component-scan base-package="com.atguigu"/>-->

    <context:component-scan base-package="com.atguigu" use-default-filters="false">
<!--        根据注解排除（包含子注解）
            如果注解排除的是Component 则@Controller、@Service、@Repository这三个都会失效
-->
<!--        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Component"/>-->



<!--        根据类名排除
            注意是类的全路径
-->
<!--        <context:exclude-filter type="assignable" expression="com.atguigu.controller.PersonController"/>-->


<!--        包含        包含        包含        包含        包含        包含        包含        包含-->
<!--         
	<context:include-filter />	设置包含的内容
	注意：通常需要与use-default-filters属性配合使用才能够达到“仅包含某些组件”这样的效果。
	即：通过将use-default-filters属性设置为false，
	如果包含的是Component 则@Controller、@Service、@Repository这三个都会被包含
-->

    <context:include-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
<!--        根据类包含，包括子类-->
    <context:include-filter type="assignable" expression="com.atguigu.pojo.Person"/>

    </context:component-scan>
</beans>
```

#### 使用注解@Autowired自动装配

```java
单个对象：
@Service
public class PersonService {

    /**
     * @Autowried:将容器中bean给变量赋值
     * id 就是类型名首字母小写
     */
    @Autowired
    private PersonDao personDao;//这里是PersonDao类

    @Override
    public String toString() {
        return "PersonService{" +
            "PersonDao=" + personDao +
            '}';
    }
}
测试代码：
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
       PersonDao personDao = context.getBean("personDao", PersonDao.class);
		 System.out.println("personDao = " + personDao);
 ----------------------------------------------------------------------------------------  
多个，当有多个实现类的时候（这里的示例是接口）：
@Service
public class PersonService {

  //这里的属性名是那个实现类的类名首字母小写就打印那个实现类，如果两个都不是那么就会报错
    //@Qualifier("要注入的bean的Id")如果 private PersonDao personDao的属性名都不是那么就使用Qualifier注解指定你要注入的对象，这里的指定优先级大于默认 private PersonDao personDao
    //@Autowired（required = false）当容器中找不到次类型的bean的时候,可以为null
    
    @Autowired
    @Qualifier("personImpl")
    private PersonDao personDaoqw;//这里的PersonDao是一个接口他有两个实现类，一个PersonDaoImpl，一个	PersonDaoImplExt

    @Override
    public String toString() {
        return "PersonService{" +
            "PersonDao=" + personDao +
            '}';
    }
}
测试代码：
    	注意：//当一个类有接口的时候,一定用接口类型（也就是getBean("personService", PersonService.class);这里前面是id，后面是接口名.class）
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
       PersonService personService = context.getBean("personService", PersonService.class);
		 System.out.println("personService = " + personService);
---------------------------------------------------------------------------------------
	@Autowired 注入方法，与注入属性一样    
 /**
     * @Autowired 修饰在方法上会在当前bean初始化后就执行
     * 被@Autowired修饰的方法的参数 会 自动 注入,(参数会自动去容器中找当前类型相同的bean进行注入)
     *      1:当有多个参数时,匹配变量名称按照id（默认时类型首字母小写）去匹配，如果都不匹配还可以自定义
     *      2:@Qualifier可以写在方法的参数中,去指定bean的id给参数注入
     *		3::@Autowired(required = false) 当找不到bean时,则注入null	
     */
    @Autowired
    public void show(@Qualifier("personDaoImpl") PersonDao personDao) {
        System.err.println("show:" + personDao);
    }
-----------------------------------------------------------------------------------------
    扩展：
    // @Resource在javax包下，它等于@Autowired+@Qualifier的功能组合，可以直接指定id去注入
     @Resource(name="personImpl")
    private PersonDao personImplExt;
```

#### Spring的专有测试

```java
//@ContextConfiguration引入xml文件相当于  ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

@ContextConfiguration(locations = "classpath:bean1.xml")

// @RunWith表示使用Spring扩展的Junit测试类来测试代码
@RunWith(SpringJUnit4ClassRunner.class)
public class SpringTest {

    @Autowired
   private PersonService personService;

    @Autowired
    @Qualifier("personImpl")
    private PersonDao personDao;

    @Autowired
    private Person person;

    @Test
    public void test(){
        System.out.println(person);
        System.out.println(personService);
        System.out.println(personDao);
    }

}

```

#### 纯注解

```java
首先创建配置文件：
 /**
 * @Configuration == 当前类就是一个Spring的配置文件去使用
 * @ComponentScan == <context:comp-scan></context:comp-scan>
 * @Import == <import resource =""/> 导入其它配置文件类（多个配置文件的时候用到）
 * @PropertySource:加载外置properties配置文件 <context:property-placeholder></context:property-placeholder>
 *
 */
    
    //表示这个类就是配置文件（也就是他就是xml）
@Configuration
@ComponentScan(basePackages = {"com.atguigu"})
@Import(SpringConfig01.class)
@PropertySource(value = "classpath:jdbc.properties")
public class SpringConfig {

    /*
    *  @Value("${读取配置文件的key}") 给变量注入
    *  写字符串给变量注入
     */
    @Value("${jdbc.username}")
    private String name;

    /**
     * jdbc.password 配置文件的key,当读不到的时候,将root给变量注入
     */
    @Value("${jdbc.password:root}")
    private String password;

    /**
     * <bean id ="" class=""/>
     * @Bean:将当前方法的返回值放入容器内,bean的id是 当前方法名
     *  @Bean("ppppp"):当前bean id  是 ppppp
     *  @Scope:还可以指定bean的作用域
     *
     * @Bean修饰的方法参数也会 进行 自动注入
     * 如果使用了 @Bean("自定义id名")那么就他的优先级大于类型名首字母小写的方式
     */
    @Bean
    public Person person(@Qualifier("personDaoImplExt") PersonDao personDao){
        System.out.println(name);
        System.out.println(password);
        System.err.println(personDao);
        return new Person();
    }
}
-------------------------------------------------------------------------------------
    测试代码：
     @Test
    public void test(){
        AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfig.class);//这里是配置文件类名.class
        Person person = applicationContext.getBean("person", Person.class);
        applicationContext.close();
    }

```

#### AOP切面编程

AOP是面向切面编程。全称：Aspect Oriented Programming

面向切面编程指的是：程序是运行期间，动态地将某段代码插入到原来方法代码的某些位置中。这就叫面向切面编程。

```java
接口代码：
    public interface Calculation {
    public int add(int num1,int num2);

    public int add(int num1,int num2,int num3);

    public int div(int num1,int num2);
}
实现类代码：
    public class Calculator implements Calculation {
    @Override
    public int add(int num1, int num2) {
        int result = 0;
        result = num1 + num2;
        return result;

    }

    @Override
    public int add(int num1, int num2, int num3) {
        int result = 0;
        result = num1 + num2 + num3;
        return result;
    }

    @Override
    public int div(int num1, int num2) {

        int result = 0;
        result = num1 / num2;
        return result;
    }
}
方法封装代码：
    public class LogUtils {
    public static void logBefore(String method,Object...args){
        System.out.println("当前运算是："+method+"，参数是："+ Arrays.asList(args));
    }

    public static void logAfterReturning(String method,Object result){
        System.out.println("当前操作是："+method+"，结果是："+ result);
    }

    public static void logAfterThrowing(String method,Exception exception){
        System.out.println("当前操作是："+method+"，异常是："+ exception);
    }
}

Jdk代理代码:
	public class JdkProxyFactory {
    public static Object creatJdkProxy(Object object) {


     return Proxy.newProxyInstance(
                object.getClass().getClassLoader(),
                object.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                        Object o = null;
                        try {
                            LogUtils.logBefore(method.getName(), args);
                            o = method.invoke(object, args);
                            LogUtils.logAfterReturning(method.getName(), args);
                        } catch (Exception e) {
                            LogUtils.logAfterThrowing(method.getName(), e);
                            e.printStackTrace();
                        }
                        return o;
                    }
                });

    }
}

测试代码：
 	public class AopTest {
    public static void main(String[] args) {
        Calculation calculation = new Calculator();

        Calculation proxyCalculation = (Calculation) JdkProxyFactory.creatJdkProxy(calculation);
        proxyCalculation.add(100,100);
//        proxyCalculation.div(100,0);

    }
}   
```

