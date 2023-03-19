# Spring_day03

#### 使用Cglib代理

```java
public class CglibProxyFactory {

    public static Object createCglibProxy(Object target){
        // 增强器 它负责产生一个Cglib代理对象实例
        Enhancer enhancer = new Enhancer();
        // 指定要修改哪个目标对象的字节码程序
        enhancer.setSuperclass(target.getClass());
        // 设置方法拦截器==跟InvocationHandler接口功能一样,是代理对象调用方法时就会执行的接口(专门对目标方法进行增强)
        enhancer.setCallback(new MethodInterceptor() {
            /**
             * 只要代理对象方法调用,就会执行intercept()方法
             * @param proxy         代理对象实例 <br/>
             * @param method        调用的方法的反射对象 <br/>
             * @param args          调用方法时传递的参数 <br/>
             * @param methodProxy   方法反射对象的代理对象<br/>
             * @return  返回值是代理对象调用方法的返回值
             * @throws Throwable
             */
            @Override
            public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                LogUtils.logBefore(method.getName(), args);// 前置增强 (在目标方法前做的额外操作)
                Object result =  null;
                try {
                    /**
                     * method.invoke()通过反射调用方法<br/>
                     * 第一个参数是方法的实例对象 <br/>
                     * 第二个参数是方法调用时的参数 <br/>
                     * method.invoke() 返回值就是调用的方法的返回值<br/>
                     */
                        result = method.invoke( target , args );
                        System.out.println( " method.invoke() 方法的返回值 ===>>> " + result );
                        LogUtils.logAfterReturning(method.getName(), result); // 返回增强
                } catch (Exception e) {
                    LogUtils.logAfterThrowing(method.getName(), e);// 异常增强
                    throw  new RuntimeException(e);
                }
                return result;
            }
        });
        // 创建Cglib代理对象实例
        return enhancer.create();
    }

    public static void main(String[] args) {
        Calculator calculator = new Calculator();
        Calculator proxy = (Calculator) createCglibProxy(calculator);
        proxy.add(100, 100);
    }

}
```

优点：在没有接口的情况下，同样可以实现代理的效果。

缺点：同样需要自己编码实现代理全部过程。

但是为了更好的整合Spring框架使用。所以我们需要学习一下Spring 的AOP 功能。也就是学习Spring提供的代理功能。

面试:cglib(有无接口都可,但是目标不能是final修饰),jdk动态代理(必须有接口)

#### AOP编程的专业术语  

##### 通知(Advice)

通知就是增强的代码。比如前置增强的代码。后置增强的代码。异常增强代码。这些就叫通知



##### 切面(Aspect)

切面就是包含有通知代码的类叫切面。

 

##### 横切关注点

横切关注点，就是我们可以添加增强代码的位置。比如前置位置，后置位置，异常位置。和返回值位置。这些都叫横切关注点。



##### 目标(Target)

目标对象就是被关注的对象。或者被代理的对象。

 

##### 代理(Proxy)

为了拦截目标对象方法，而被创建出来的那个对象，就叫做代理对象。

为了给目标对象增强创建出来的对象，叫代理对象。



##### 连接点(Joinpoint)

连接点指的是横切关注点和程序代码的连接，叫连接点。



##### 切入点(pointcut)

切入点指的是用户真正处理的连接点，叫切入点。

在Spring中切入点通过org.springframework.aop.Pointcut 接口进行描述，它使用类和方法作为连接点的查询条件。

#### 图解AOP专业术语：

![image-20201207200655442](typora-user-images\image-20201207200655442.png)

#### Spring的切入点表达式

@PointCut切入点表达式语法格式是：   execution(访问权限 返回值类型 方法全限定名(参数类型列表))

```java
       execution(public int com.atguigu.pojo.Calculator.add(int , int))
```

public              是访问权限

int                 是返回值类型

com.atguigu.pojo.Calculator.add 是方法全限定名

(int , int) 参数类型列表

#### 限定符：

\* 表示任意的意思：

1)    匹配某全类名下，任意或多个方法。

```
execution(public int com.atguigu.pojo.Calculator.*(int , int))
```

以上星表示所有方法

```
execution(public int com.atguigu.pojo.Calculator.save*(int , int))
```

表示方法名以save打头

2)    在Spring中只有public权限能拦截到，访问权限可以省略（访问权限不能写*）。

```
execution(public int com.atguigu.pojo.Calculator.add(int , int))
```

以上的public可以省略写成为

```
execution(int com.atguigu.pojo.Calculator.add(int , int))
```

3)    匹配任意类型的返回值，可以使用 * 表示

```
execution(public * com.atguigu.pojo.Calculator.add(int , int))
```

以上的星，表示任意类型的返回值

4)    匹配任意子包。

```
execution(public int com.atguigu.*.Calculator.add(int , int))
以上的星，com.atguigu.任意子包
```

5)    任意类型参数

```
execution(public int com.atguigu.pojo.Calculator.add(int , *))
```

以上的星，表示第二个参数的类型是任意。

**..**：可以匹配多层路径，或任意多个任意类型参数

1) 任意层级的包

```
execution(public int com.atguigu..Calculator.add(int , int))
```

以上的点点，表示com.atguigu.任意层级，任意子包

2) 任意个任意类型的参数

```
execution(public int com.atguigu.pojo.Calculator.add(..))
```

以上的点点，表示任意个，任意类型的参数

 

模糊匹配：

// 表示任意返回值，任意方法全限定符，任意参数

execution(* *(..))

// 表示任意返回值，任意包名+任意方法名，任意参数

execution(* *.*(..)) 

 

精确匹配：

```
execution(public int com.atguigu.pojo.Calculator.add(int,int))
```

表示访问权限必须是public

返回值类型必须是int，包名必须是com.atguigu.pojo

必须是Calculator

方法名必须是add

参数必须是两个，且都是int类型



#### 使用Spring实现AOP切面编程

```java
接口代码：
public interface Calculation {
    public int add(int num1, int num2);

    public int add(int num1, int num2, int num3);

    public int div(int num1, int num2);
}

实现类代码：
    @Component
public class Calculator implements Calculation {
    @Override
    public int add(int num1, int num2) {
        System.out.println("执行目标方法！！！！！");
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

Aop切面类代码：
  /**
 * 当有多个切面类时执行顺序，
 *          先执行第一个切面类的前置，然后执行第二个切面类的全部，在执行第一个切面类的后置和异常，遵循先进后出的原则（与web中的filter过滤器相同）
 *          默认根据类名的首字母排序，如果第一个字母一样就比较第二个字母（A优先级最大Z优先级最小）
 *          可以通过@Order(1)给切面类设置执行顺序，1为先执行依次往后排
 *
 */
//@Aspect表示切面类
@Component
@Aspect
@Order(1)
public class LogUtils {
	//将切入点表达式抽取出来下面只需要调用方法即可
    @Pointcut(value = "execution(int com.atguigu.pojo.Calculator.*(int,int))")
    public static void pointcut(){}

    //表示前置通知
    //@Before(value = "execution(权限修饰符（可省略） 返回值 方法实现类全路径（或cglib方式的话就是类的全路径）.方法名(参数类型1,参数类型2))")
    //JoinPoint:切入点对象,里面封装了切入点的一些属性
    //joinPoint.getSignature().getName() 获取方法名
    //joinPoint.getArgs() 获取参数(要用集合接收因为参数个数不确定)

    @Before(value = "pointcut()")
    public static void logBefore(JoinPoint joinPoint){
        System.out.println(" L 前置通知"+"方法名是"+joinPoint.getSignature().getName()+",参数是"+ Arrays.asList(joinPoint.getArgs()));
    }


    @After(value = "pointcut()")
    public static void logAfter(JoinPoint joinPoint){
        System.out.println(" L 后置通知"+"方法名是"+joinPoint.getSignature().getName()+"，参数是"+Arrays.asList(joinPoint.getArgs()));
    }

    //获取返回值，在@AfterReturning注解中添加 returning = "返回值名"
    //logAfterReturning(JoinPoint joinPoint,Object result) 里面的返回值类型尽量用最大类型
    @AfterReturning(value = "pointcut()",returning = "result")
    public static void logAfterReturning(JoinPoint joinPoint,Object result){
        System.out.println(" L 返回通知"+"方法名是"+joinPoint.getSignature().getName()+"，参数是"+Arrays.asList(joinPoint.getArgs())+",返回值是："+result);
    }

    //获取异常信息，在@AfterThrowing注解中添加 throwing = "异常信息名"
    //logAfterThrowing(JoinPoint joinPoint,Exception e) 里面的异常类型尽量用最大类型
    @AfterThrowing(value = "pointcut()",throwing = "e")
    public static void logAfterThrowing(JoinPoint joinPoint,Exception e){
        System.out.println(" L 异常通知"+"方法名是："+joinPoint.getSignature().getName()+"，参数是"+Arrays.asList(joinPoint.getArgs())+"异常是"+e);
    }

/*    //环绕通知
    *//**
     * 环绕通知 <br/>
     *  1 使用注解@Around表示环绕通知<br/>
     *  2 环绕通知需要添加一个ProceedingJoinPoint proceedingJoinPoint参数.它可以用来执行目标方法<br/>
     *  3 环绕通知一定要把目标方法的返回值返回.否则普通的返回通知收不到结果<br/>
     *  4 环绕通知捕获到异常,一定要往外抛.否则普通的异常通知不执行<br/>
     *  5 环绕通知会比普通通知优先执行<br/>(在使用注解的情况下，在使用xml配置的情况下不是环绕先执行)
     */
    @Around(value = "execution(public int com.atguigu.pojo.Calculator.*(int,int))")
    public static Object around(ProceedingJoinPoint proceedingJoinPoint){
        Object proceed = null;
        try {
            try {
                System.out.println("环绕前置");
                Object[] args = proceedingJoinPoint.getArgs();
                proceed = proceedingJoinPoint.proceed(args);
            } finally {
                System.out.println("环绕后置");
            }
        } catch (Throwable e) {
            System.out.println("环绕异常");
            throw new RuntimeException(e);
        }
        System.out.println("环绕返回");
        return proceed;
    }
}

xml代码：
    <!--  扫描包下的注解  ->
      <contest:component-scan base-package="com"/>

<!--    切面生效,让@Aspect 生效
        proxy-target-class = true 强制使用cglib代理不管有无接口
        默认不配置的话有接口就用jdk代理
        没有接口的话就用cglib代理
-->
    <aop:aspectj-autoproxy proxy-target-class="false"/>
        
 测试代码：
        @ContextConfiguration(locations = "classpath:aopconfig.xml")
@RunWith(SpringJUnit4ClassRunner.class)
public class AopTest {

    @Autowired
    private Calculation calculation;


    @Test
    public void test() {

//        int add = calculation.add(100, 100);
        int div = calculation.div(100,2);

    }
}
```

#### Spring通知的执行顺序

Spring AOP编程中提供的常用通知有四种 , 分别是 : 前置通知 , 后置通知 , 返回通知 , 异常通知

Spring通知的执行顺序是:

正常情况:

前置=====>>>方法=====>>后置=====>>返回

异常情况:

前置=====>>>方法=====>>后置======>>异常

#### 多个切面的执行顺序

当有多个切面类时执行顺序，
 *          先执行第一个切面类的前置，然后执行第二个切面类的全部，在执行第一个切面类的后置和异常，遵循先进后出的原则（与web中的filter过滤器相同）
 *          默认根据类名的首字母排序，如果第一个字母一样就比较第二个字母（A优先级最大Z优先级最小）
 *          可以通过@Order(1)给切面类设置执行顺序，1为先执行依次往后排

#### 基于xml配置AOP

```xml
<!--============================XML的方式配置Aop======================================-->

<!--      引入目标-->
        <bean class="com.atguigu.pojo.Calculator" id="calculator"/>
<!--            引入切面类 -->
        <bean class="com.atguigu.utils.LogUtils" id="logUtils"/>

<!--    配置aop-->
    <aop:config>
<!--        引入切面类-->
        <aop:aspect ref="logUtils">
<!--                配置切入点表达式-->
            <aop:pointcut id="point" expression="execution(int com.atguigu.pojo.Calculator.*(int,int))"/>
<!--            前置通知    -->
            <aop:after method="logBefore" pointcut-ref="point"/>
<!--            后置通知-->
            <aop:after method="logAfter" pointcut-ref="point"/>
<!--            返回通知-->
            <aop:after-returning method="logAfterReturning" pointcut-ref="point" returning="result"/>
<!--            异常通知-->
            <aop:after-throwing method="logAfterThrowing" pointcut-ref="point" throwing="e"/>

<!--            环绕通知-->
<!--            <aop:around method="around" pointcut-ref="point"/>-->
        </aop:aspect>
    </aop:config>

接口、实现类、Aop切面类同上面注解的一样只是把注解去掉（因为已经在xml中配置好了）
```

#### 纯注解AOP

> 只是把xml文件替换成java配置文件了，所有都用注解来实现

```java
配置文件
/**
 * 纯注解配置Aop
 */
//(proxyTargetClass = true) 都使用cglib
//@EnableAspectJAutoProxy(proxyTargetClass = true) == <aop:auto-proxy/>
@Configuration
@ComponentScan(basePackages = "com.atguigu")
@EnableAspectJAutoProxy
public class AopConfig {
}

测试：
    @ContextConfiguration(classes = AopConfig.class)
@RunWith(SpringJUnit4ClassRunner.class)
public class AopConTest {

    @Autowired
    private Calculation calculation;


    @Test
    public void test(){
        calculation.div(100,2);
    }
}

接口、实现类、Aop切面类同上面注解的方式一样
```

#### Spring之JdbcTemplate使用

JdbcTemplate简介：

* 在Spring中提供了对jdbc的封装类叫JdbcTemplate。它可以很方便的帮我们执行sql语句，操作数据库。
* Spring 中提供的对数据库CRUD的一个工具类，和jdbc中的jdbcUtils类似（可以把它当成jdbc中的工具类使用）

创建数据库：

```sql
CREATE DATABASE IF NOT EXISTS jdbctemplate DEFAULT CHARSET utf8;

use jdbctemplate;
CREATE TABLE `employee` (
`id` int(11) primary key AUTO_INCREMENT,
`name` varchar(100) DEFAULT NULL,
`salary` decimal(11,2) DEFAULT NULL
);

insert into `employee`(`id`,`name`,`salary`) 
values (1,'李三',5000.23),(2,'李四',4234.77),(3,'王五',9034.51),
(4,'赵六',8054.33),(5,'孔七',6039.11),(6,'曹八',7714.11);

select * from employee;
```

```properties
properties配置：

user=root
password=root
url=jdbc:mysql://localhost:3306/jdbctemplate?characterEncoding=UTF-8
driverClassName=com.mysql.jdbc.Driver
initialSize=5
maxActive=10
```

```xml
xml配置：

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

<!--包扫描-->
    <context:component-scan base-package="com"/>


    <!-- 加载配置文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!-- 连接池 -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${driverClassName}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
        <property name="initialSize" value="${initialSize}"/>
        <property name="maxActive" value="${maxActive}"/>
    </bean>

    <!-- 配置Spring提供的JdbcTemplate工具类执行sql语句 -->
    <bean class="org.springframework.jdbc.core.JdbcTemplate" id="jdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <bean class="org.springframework.jdbc.core.namedparam.NamedParameterJdbcTemplate" id="namedParameterJdbcTemplate">
        <constructor-arg name="dataSource" ref="dataSource"/>
    </bean>
</beans>
```

DAO代码：

```java
@Repository
public class EmployeeDao extends JdbcDaoSupport {


    //非继承
   /* @Autowired
    private JdbcTemplate jdbcTemplate;
*/

   //继承JdbcDaoSupport
    @Autowired
    public void setJdbcTemplate(DataSource dataSource){
        setDataSource(dataSource);
    }


    public Employee selectEmp(Integer id){
        String sql ="select `id`,`name`,`salary` from employee where id = ?";
        Employee employee = getJdbcTemplate().queryForObject(sql, new BeanPropertyRowMapper<Employee>(Employee.class), id);
        return employee;
    }

}

```

测试代码：

```java
@ContextConfiguration(locations = "classpath:jdbc.xml")
@RunWith(SpringJUnit4ClassRunner.class)
public class DataSourceTest {

    @Autowired
    DataSource dataSource;

    @Autowired
    JdbcTemplate jdbcTemplate;


    @Autowired
    NamedParameterJdbcTemplate namedParameterJdbcTemplate;

    @Test
    public void test() throws SQLException {
        System.out.println(dataSource.getConnection());
        System.out.println(jdbcTemplate);
    }

    //    将id=5的记录的salary字段更新为1300.00
    @Test
    public void test2() {
        String sql = "update employee set salary = ? where id = ?";
        jdbcTemplate.update(sql, 1300.00, 5);
    }

//    批量插入

    @Test
    public void test3() {
        String sql = "insert into employee(name ,salary) values(?,?)";
        List<Object[]> list = new ArrayList<>();
        list.add(new Object[]{"aaa", "1000"});
        list.add(new Object[]{"bbb", "2000"});
        list.add(new Object[]{"ccc", "3000"});
        jdbcTemplate.batchUpdate(sql, list);
    }

    //    查询id=5的数据库记录，封装为一个Java对象返回
    @Test
    public void test4() {
        String sql = "select id,name,salary from employee where id = ?";
        Employee employee = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Employee>(Employee.class), 5);
        System.out.println("employee = " + employee);
    }


//    查询salary>4000的数据库记录，封装为List集合返回

    @Test
    public void test5() {
        String sql = "select id,name,salary from employee where salary > ?";
        List<Employee> list = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Employee>(Employee.class), 4000);
        list.forEach(listemp -> System.out.println(listemp));
    }

    //    查询最大salary
    @Test
    public void test6() {
        String sql = "select max(salary) from employee";
        BigDecimal decimal = jdbcTemplate.queryForObject(sql, BigDecimal.class);
        System.out.println("employee = " + decimal);
    }

    //    使用带有具名参数的SQL语句插入一条员工记录，并以Map形式传入参数值


    @Test
    public void test7() {
//        System.out.println(namedParameterJdbcTemplate);

        String sql = "insert into employee(name,salary) values(:name,:salary)";
        Map<String, Object> map = new HashMap<>();
        map.put("name", "催催");
        map.put("salary", "66666");
        int update = namedParameterJdbcTemplate.update(sql, map);
        System.out.println("update = " + update);
    }


//    重复实验7，以SqlParameterSource形式传入参数值

    @Test
    public void test8() {
//        System.out.println(namedParameterJdbcTemplate);

        String sql = "insert into employee(name,salary) values(:name,:salary)";

        Employee fangfang = new Employee(null, "fangfang", new BigDecimal(9898));
        namedParameterJdbcTemplate.update(sql, new BeanPropertySqlParameterSource(fangfang));
    }

    //    创建Dao，自动装配JdbcTemplate对象
    @Autowired
    private EmployeeDao employeeDao;

    @Test
    public void test9() {
        Employee employee = employeeDao.selectEmp(5);
        System.out.println("employee = " + employee);
    }


    //    通过继承JdbcDaoSupport创建JdbcTemplate的Dao
    @Test
    public void test10() {
        Employee employee = employeeDao.selectEmp(4);
        System.out.println("employee = " + employee);
    }

}
```

Bean代码：

```java
public class Employee {
    private Integer id;
    private String name;
    private BigDecimal salary;


    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public BigDecimal getSalary() {
        return salary;
    }

    public void setSalary(BigDecimal salary) {
        this.salary = salary;
    }

    public Employee() {
    }

    public Employee(Integer id, String name, BigDecimal salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", salary=" + salary +
                '}';
    }
}

```

