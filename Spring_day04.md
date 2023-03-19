# Spring_day04

### 使用Spring的注解声明事务管制

需要的jar包:

![image-20201208155538287](typora-user-images\image-20201208155538287.png)

properties配置：

```properties
user=root
password=root
url=jdbc:mysql://localhost:3306/tx?characterEncoding=UTF-8
driverClassName=com.mysql.jdbc.Driver
initialSize=5
maxActive=10
```

添加数据库：

```xml
##创建tx数据库
CREATE DATABASE IF NOT EXISTS `tx` DEFAULT CHARSET utf8;
##切换tx数据库
USE `tx`;

##创建用户表
CREATE TABLE `user` (
 `id` int primary key auto_increment,	
 `username` varchar(50) NOT NULL,
 `money` int(11) DEFAULT NULL
);

##插入数据
insert into `user`(`username`,`money`) values ('张三',1000),('李四',1000);

##创建图书表
create table `book`(
  `id` int primary key auto_increment,
  `name` varchar(500) not null,
  `stock` int
);

##插入数据
insert into book(`name`,`stock`) values('java编程思想',100),('C++编程思想',100);

##查看数据
select * from book;
select * from user;
```

Xml配置

```xml
    <!--包扫描-->
    <context:component-scan base-package="com.atguigu"/>

    <!--    引入properties文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--    从数据库连接池获取数据-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${driverClassName}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
        <property name="initialSize" value="${initialSize}"/>
        <property name="maxActive" value="${maxActive}"/>
    </bean>


<!--Spring 中提供的对数据库CRUD的一个工具类，和jdbc中的jdbcUtils类似（可以把它当成jdbc中的工具类使用）-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <constructor-arg name="dataSource" ref="dataSource"/>
    </bean>


<!--    注入事务管理器  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg name="dataSource" ref="dataSource"/>
    </bean>

    <!--    让事务注解生效
            (tx:annotation-driven表示代理 + 注解@transactional(用在类或方法中的注解))组合使用
            transaction-manager="transactionManager" 配置使用哪个事务管理器
            transaction-manager属性的默认值是: transactionManager（默认可以不写）
    -->
		
   <tx:annotation-driven transaction-manager="transactionManager"></tx:annotation-driven>
```

Src代码：

```java
---------------------------版本一不配置传播特性的情况下---------------------------------------------
BookDao：
    @Repository
public class BookDao {

    @Autowired
    JdbcTemplate jdbcTemplate;

    public void updateBook(){
        jdbcTemplate.update("update book set `name` = '图书表被修改了'");
    }
}

UserDao:
	@Repository
public class UserDao {

    @Autowired
    JdbcTemplate jdbcTemplate;

    public void updateUser(){
        jdbcTemplate.update("update user set `username` = '用户表被修改了'");
    }
}

TransactionService代码：
    @Service
public class TransactionService {

    @Autowired
    UserDao userDao;

    @Autowired
    BookDao bookDao;


    /**
     * @Transactional注解表示当前方法有事务 <br/>
     * 加上@Transactional然后在xml中配置 就代表开启事务
     * 当方法加入了@Transactional,就表明它是一个切入点
     * 底层会将DataSourceTransactionManager中 begin,commit,rollback,在程序运行的时候动态的织入进来
     *
     *---------------------设置那些异常不回滚  noRollbackFor和noRollbackForClassName-----------------
     * 默认情况下，Spring是对抛出的运行时异常,及其子异常，进行回滚
     * noRollbackFor 设置哪些异常不回滚事务
     *     @Transactional（noRollbackFor = ArithmeticException.class） 表示出现算术异常时，不回滚事务
     *    书写语法是noRollbackFor = 异常类.class(他是个Class类型)
     * noRollbackForClassName 属性设置哪些全类名的异常不回滚事务
     *    @Transactional(noRollbackForClassName = "java.lang.ArithmeticException")表示出现算术异常时，不回滚事务
     *    书写语法是noRollbackForClassName = "异常类的全路径.异常类名"
     *
     *----------------------设置那些异常回滚 rollbackFor和rollbackForClassName----------------------
     *
     * 默认情况下，Spring是对抛出的运行时异常,及其子异常，进行回滚
     * rollbackFor 属性指定哪些类型的异常也回滚事务
     *     @Transactional(rollbackFor = FileNotFoundException.class) 设置文件找不到异常也回滚（文件找不到异常是IO异常）
     *    书写语法：rollbackFor = 异常类.class
     * rollbackForClassName 属性指定哪些全类名的异常也回滚事务
     *    @Transactional(rollbackForClassName = "java.io.FileNotFoundException") 设置文件找不到异常也回滚（文件找不到异常是IO异常）
     *    书写语法：rollbackForClassName = "异常类的全路径.异常类名"
     *
     *-------------------------------------事务的只读属性---------------------------------------
     *
     * 默认情况下，事务是支持写，读操作
     * 我们也可以通过设置readOnly属性设置为只读。
     * 它的值默认是false。
     * @Transactional(readOnly = true) 表示只能执行只读操作（也就是不支持update操作）
     *    如果配置了制度操作后还对其进行写操作会报出
     *  SQLException: Connection is read-only. Queries leading to data modification are not allowed异常
     *
     * -------------------------------------事务超时属性timeout---------------------------------
     *  @Transactional(timeout = 3) 以秒为单位，设置为3秒后不允许再执行sql操作( 超时 ），默认是-1，不会超时
     *  也就是你3秒内要执行完事务方法里面的代码不然就会超时报错
     *  报出的错误：TransactionTimedOutException: Transaction timed out: deadline was Tue Dec 08 11:04:54 CST 2020
     */

  //  @Transactional
    public void multiUpdate() {

        userDao.updateUser();
        int i = 1 / 0;
        bookDao.updateBook();

    }

测试代码：
    //读取xml配置文件
@ContextConfiguration(locations = "classpath:jdbc.xml")
    //Spring增强Junit测试
@RunWith(SpringJUnit4ClassRunner.class)
public class TxTest {


    @Autowired
    TransactionService transactionService;

    @Test
    public void test(){
        transactionService.multiUpdate();

    }
}
    
---------------------------版本二配置传播特性的情况下---------------------------------------------
    在service包中多了两个service，并给他们添加了事务和传播特性
    
BookService：代码    
@Service
public class BookService {

    @Autowired
    private BookDao bookDao;

   @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void updateBooks(){
        bookDao.updateBook();
    }
}

UserService代码：    
@Service
public class UserService {

    @Autowired
    private UserDao userDao;



    @Transactional(propagation = Propagation.REQUIRED)
    public void updateUsers(){

        userDao.updateUser();
    }

}  
    
TransactionService代码：
@Service
public class TransactionService {
   @Autowired
    private BookService bookService;

    @Autowired
    private  UserService userService;


    @Transactional(propagation = Propagation.REQUIRED)
    public void mulTransaction(){
        bookService.updateBooks();
         int i = 1 / 0;
        userService.updateUsers(); 
    }
}

    xml配置、测试代码、userDao、BookDao与上面相同
```

### 事务的传播特性propagation

什么是事务的传播行为 ( 传播特性又叫传播行为 )：

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。

例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

事务的传播行为可以由传播属性指定。Spring定义了7种类传播行为,**我们平常只需要用到前两种就能够解决大部分问题后面作为了解**

**REQUIRED为默认的传播方式**

事务的传播特性，有以下几种类型：

![image-20210123084156289](typora-user-images\image-20210123084156289.png)

**通过上面代码中的UserService、BookService、TransactionService进行测试:**

测试代码：

```java
@Service
public class BookService {

    @Autowired
    private BookDao bookDao;

   // @Transactional(propagation = Propagation.REQUIRED)
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void updateBooks(){
        bookDao.updateBook();
    }
}


@Service
public class UserService {

    @Autowired
    private UserDao userDao;



   @Transactional(propagation = Propagation.REQUIRED)
    //@Transactional(propagation = Propagation.REQUIRES_NEW)
    public void updateUsers(){

        userDao.updateUser();
    }

}

TransactionService代码：
@Service
public class TransactionService {
   @Autowired
    private BookService bookService;

    @Autowired
    private  UserService userService;


    @Transactional(propagation = Propagation.REQUIRED)
    //@Transactional(propagation = Propagation.REQUIRES_NEW)
    public void mulTransaction(){
        bookService.updateBooks();
         int i = 1 / 0;
        userService.updateUsers(); 
    }
}
```

**总结REQUIRES_NEW传播属性就是不管别人自己单独执行，如果他前面有就给前面的挂起来，他先执行，如果遇到异常就回滚**

**REQUIRED要等整个方法执行完（也就是大括号走完）才会提交，中间遇到异常就会回滚**



### xml配置式事务声明

```xml
    <!--    =====================XMl配置事务=================================================-->

<!--包扫描-->
    <context:component-scan base-package="com.atguigu"/>

    <!--    引入properties文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    <!--    从数据库连接池获取数据-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${driverClassName}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${user}"/>
        <property name="password" value="${password}"/>
        <property name="initialSize" value="${initialSize}"/>
        <property name="maxActive" value="${maxActive}"/>
    </bean>


<!--Spring 中提供的对数据库CRUD的一个工具类，和jdbc中的jdbcUtils类似（可以把它当成jdbc中的工具类使用）-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <constructor-arg name="dataSource" ref="dataSource"/>
    </bean>


<!--    注入事务管理器  -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <constructor-arg name="dataSource" ref="dataSource"/>
    </bean>

<!--
         transaction-manager:引用事务管理器
         tx:attributes 事务属性
-->
    <tx:advice transaction-manager="transactionManager" id="txAdvice">

        <tx:attributes>
            <!--
              name:方法名称,可以使用通配符
              multiUpdate:表示精确方法名称
              update*:以update开头的所有方法（半模糊事务方法）
              *:剩余的方法,当做查询,只读
              匹配优先级:精确>模糊>*（只会执行其中一个）
              propagation 设置传播特性 REQUIRED是默认的
          -->
            <tx:method name="multiUpdate" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>

<!--   *代表除了上面的精确查询和半模糊查询外的查询一般都是select，所以给他设置只读-->
            <tx:method name="*" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <!--将事务属性引用到切入点上-->
    <aop:config>
        <!--切入点表达式-->
        <aop:pointcut id="Poin" expression="execution(* com.atguigu.service.TransactionService.*(..))"/>
        <!--
          将事务属性加入到切入点上
          advice-ref:事务属性的id
          pointcut-ref:切入点
      -->
       <aop:advisor advice-ref="txAdvice" pointcut-ref="Poin"/>
    </aop:config>
    
</beans>

Src中的java代码和上方的注解java代码一样，就是把注解去掉就行
Src中的java代码和上方的注解java代码一样，就是把注解去掉就行
Src中的java代码和上方的注解java代码一样，就是把注解去掉就行
```

### 事务隔离级别

**数据库有四种事务隔离级别：**

一：读未提交	read uncommitted

二：读已提交	read committed			Oracle默认

三：可重复读	repeatable read			mysql默认

四：串行事务	serializable (读写不并行)



**由事务隔离级别产生的几个常见问题：**

读未提交，可导致----->>>> 脏读

读已提交，可导致----->>>> 不可重复读

重复读，可导致  ----->>>> 幻读



#### 什么是脏读？

脏读，就是读到了别的事务中，操作，还没提交生效的数据。叫脏读。

 也就是当多个人操作同一个数据库中的同一条数据时第一个人的写操作还没提交，但是另外的人就能看到要写入的数据

#### 什么是不可重复读？

不可重复读，就是指查询相同的数据（执行一样的查询），会得到不一样的结果。

 完美的解决了脏读，但是会导致执行同一条sql得到的结果不一样

#### 什么是幻读？

幻读，就是指，多次查询相同的数据（执行一样的查询），每次得到的数据都一样。但其实，这些数据的结果已经被修改。

### 演示

1.查看当前会话隔离级别

select @@tx_isolation;

 

2.查看系统当前隔离级别

select @@global.tx_isolation;

 

3.设置当前会话隔离级别

set session transaction isolation level read uncommitted;

set session transaction isolation level read committed;

set session transaction isolation level repeatable read;

set session transaction isolation level serializable;

 

4.开启事务

start transaction; 

 

5.提交事务

commit;  

 

6.回滚事务

rollback;     

#### 读未提交，导致脏读的演示：

![img](file:///C:/Users/哦！/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg) 

#### 读已提交，导致 不可重复读。



![img](file:///C:/Users/哦！/AppData/Local/Temp/msohtmlclip1/01/clip_image004.jpg) 

#### 可重复读级别，导致幻读的演示

![img](file:///C:/Users/哦！/AppData/Local/Temp/msohtmlclip1/01/clip_image006.jpg)

#### 串行化事务的演示

 

![img](file:///C:/Users/哦！/AppData/Local/Temp/msohtmlclip1/01/clip_image008.jpg)

 

###  Spring整合Web

创建web工程，

导入jar包

![image-20201208164628741](typora-user-images\image-20201208164628741.png)

```java
Setvlet代码：
    public class SpringWebServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            doGet(request,response);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //方式一 不需要在WEb.xml中配置初始化参数和监听器
        
       /* ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("jdbc.xml");
        TransactionService transactionService = context.getBean("transactionService", TransactionService.class);
        transactionService.multiUpdate();*/

      /* //方式二(不推荐) 要配置初始化参数和监听器

        //防止框架版本升级时,ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE名称做了改变,我们还不知道
        WebApplicationContext webApplicationContext = (WebApplicationContext) this.getServletContext().getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);
        TransactionService transactionService = webApplicationContext.getBean("transactionService", TransactionService.class);
        transactionService.multiUpdate();*/

        //方式三（推荐）要配置初始化参数和监听器
        WebApplicationContext applicationContext = WebApplicationContextUtils.getWebApplicationContext(getServletContext());
        TransactionService transactionService = applicationContext.getBean("transactionService", TransactionService.class);
        transactionService.multiUpdate();

    }
}
其他的Java代码、xml代码同上面的事务制一样，只是换了一个测试环境，把咋test中测试换成了在servlet中测试
```

Web.Xml配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">


    <!-- 配置Spring容器需要的配置文件路径 -->
    <context-param>
<!--    param-name 标签内容必须是contextConfigLocation 不然会报找不到错误  -->
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:jdbc.xml</param-value>
    </context-param>


    <!--
        1 ContextLoaderListener监听器监听在web工程启动的时候创建Spring容器对象
        2 在web工程停止的时候关闭Spring容器对象。释放内存
    -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>


    <servlet>
        <servlet-name>SpringWebServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.SpringWebServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>SpringWebServlet</servlet-name>
        <url-pattern>/SpringWebServlet</url-pattern>
    </servlet-mapping>
</web-app>
```

