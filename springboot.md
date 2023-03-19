# springboot

简介：SpringBoot 是Spring 家族的一个开源框架，SpringBoot 底层实际上是基于Spring 框架的，也即SpringBoot是用来简化基于Spring 框架的开发的，尤其是简化了Spring 的配置文件。简化Spring 的开发当然也就是简化SpringMVC 开发，毕SpringMVC实际上是属于Spring 框架的一部。总而言之，言而总之，SpringBoot能让我们更加便捷的构建Spring 应用；SpringBoot 对于Spring 程序的简化超乎你的想象

SpringBoot 的官方网站：https://spring.io/projects/spring-boot
SpringBoot 的中文网站：http://springboot.fun/

springboot开发的服务叫做微服务

### 注解

- @EnableAutoConfiguration       开启自动配置（不用配扫描包了，也不用配置web.xml文件了等）
- @ComponentScan(value = "com.xxx.controller")      扫描controller层的类
  - **如果要运行的类不在配置类的子包下或通个包下需要自已在配置扫描包注解ComponentScan，一旦配置扫描包注解那么@SpringBootApplication注解中的扫描包注解将不再起作用,也就是如果子包下有接口也会不可用，必须配置扫描包，一般我们都使用默认的直接在配置类的子包下写接口（这里的接口也就是controller）**
- **@SpringBootApplication**相当于三个注解的总和
  - @SpringBootConfiguration：相当于Configuration注解Configuration注解：加了Configuration注解的类是一个配置类，配置类相当于applicationcontext.xml文件（相当于一个容器）
   - @EnableAutoConfiguration：开启自动配置
   - @ComponentScan：配置扫描包，默认扫描当前主配置类所在的包及子包中的组件（加了注解的类）
 - @Configuration 注解:使用@Configuration 注解标记一个类后,这个类成为配置类,加载这个类中的配置可以
   取代以前的XML 配置文件
 - @Bean 注解:相当于XML 配置文件中的bean 标签。用于把一个类的对象加入到IOC 容器
 - @Import 注解 导入的作用，引入其他配置类
 - @ConditionalOn 开头的注解:一个类在满足特定条件时才加入IOC 容器（他有多个条件）
 - @MapperScan，在主配置类上使用就避免在dao层的xxxMapper的接口上方声明@Mapper注解了，进行管理比较方便，@MapperScan("mapper接口所在包的全路径")

其他注解：

```
@RestController:

@GetMapping:@RequestMapping（method=RequestMethod.GET）

@PostMapping:@RequestMapping（method=RequestMethod.POST）

@PutMapping

@DeleteMapping

@SpringBootApplication：@ComponentScan+@EnableAutoConfiguration

@Controller标注当前类是一个处理器类

@Service标注当前类是一个业务逻辑层类

@Component标注当前类是一个普通组件

@Repository标注当前类是一个数据访问层类

@RequestMapping映射请求url

@PathVariable绑定路径变量到目标方法的参数中

@RequestParam:获取请求参数信息

@ControllerAdvice:标注当前类是一个全局异常处理类

@ExceptionHandler标注当前方法可以处理某些指定的异常信

@RequestHeader:获取请求你头信息
```



### 三种运行springboot的方式

1. 直接执行主配置类
2. 首先打成jar包（必须是官网的那种依赖方式），然后在项目的target 目录下执行： java -jar jar 包名称
3. pom.xml 文件所在目录执行mvn 命令： mvn spring-boot:run

```xml
将可执行文件打成jar包的方式，上方第二种情况需要

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

#### springboot访问静态资源默认去static下找，不需要加static

#### 场景启动器

```
<!--        springboot的starter分为两种：
                    1、官方提供的（我们遇到的大部分都是官方提供的）
                        groupId都是org.springframework.boot
                        artifactId都是spring-boot-starter-所需要的组件
                    2、非官方提供的
                        其他不同上方的都是非官方提供的
 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```

> **日志级别：debug>info>warning>error，这个顺序是输出日志量最多到最少**
>
> **springboot默认输出的是info级别以上的**
>
> **在配置文件中配置logging.level.com.xxx.boot=error可以调整输出的日志级别**

### springboot配置管理

#### 命令行方式（了解）

1. 打成可执行jar包后，使用可执行jar包的方式运行时 可以通过--后面跟要配置的命令
2. 在上方bulid小锤子旁边的运行框中配置

![image-20210109105929296](typora-user-images\image-20210109105929296.png)

#### 配置文件（重点）

有两种配置文件：

​	application.properties

​	application.yml(**推荐**)

> **名字必须是application，必须在resource目录下**
>
> 不同配置项在两个配置文件中配置会都生效，相同配置项以yml为准（老版本以properties为准）

- properties结尾的键和值之间没有空格

- yml文件键和值之间用冒号间隔，值和冒号之间必须有一个空格

- yml级别不能用tap键缩进，只能用空格缩进，相同级别缩进相同，级别越低缩进越多（写出来类似于楼梯一样）

- #号开头表示注释
- 在yml文件中如果是0开头，他会当成八进制进行解析，用引号引起来就是正常的0数字

* **@configurationProperties(prefix = "配置文件中的属性或者类")**注解会去配置文件中找以xxx的开头的属性，将配置文件中的属性和类中的属性相对应的进行赋值，必须保证配置文件中的属性和类中的属性名一致才能附上值
* 在yml文件中事件类型默认时1997/11/02，如果写成1997-11-02这种类型，要在给属性赋值的上方加上**@DateTimeFormat(pattern = "yyyy-MM-dd")**指定你使用的时间格式

### profile三种生产环境

- 三种生产环境：
  - 开发环境
  - 测试环境
  - 线上环境

#### yml环境下配置

在yml中使用三个杠  --- （称之为连字符）表示一个生产环境，最上面的段是公共配置段，其他下面的是专有配置段

- 开发环境段
  - spring：
    - profiles: dev
- 测试环境段
  - spring：
    - profiles: test
- 线上环境段：
  - spring：
    - profiles：prod

- **使用方式**
  1. 然后在公共配置段使用spring.profiles.active:指定要用的环境段
  2. 还有一种在bulid和运行中间的小窗口进行指定

![image-20210109142013007](typora-user-images\image-20210109142013007.png)

#### properties环境下配置

建立多个properties文件

公共文件为：application.properties

开发环境配置文件：application-dev.properties

测试环境配置文件：application-test.properties

线上环境配置文件：application-prod.properties

在主配置文件中使用spring.properties.active=dev/test/prod进行激活，或者在bulid和run中间的小窗口激活

#### yml文件中解决mapper.xml文件映射问题

```
mybatis:
	config-location: classpath:/mybatis/mybatis-config.xml #指定主配置文件
	mapper-locations: classpath:/mybatis/mapper/*.xml # 指定xml映射文件路径
	type-aliases-package: com.atguigu.domain # 指定mybatis别名包
```

### springboot自动配置原理

**@SpringBootApplication**相当于三个注解的总和

- @SpringBootConfiguration：相当于Configuration注解Configuration注解：加了Configuration注解的类是一个配置类，配置类相当于applicationcontext.xml文件（相当于一个容器）
 - @EnableAutoConfiguration：开启自动配置
 - @ComponentScan：配置扫描包，默认扫描当前主配置类所在的包及子包中的组件（加了注解的类）

**自动配置原理描述：**

首先**@springbootapplication**注解中包含了**@EnableAutoConfiguration**自动配置注解，这个注解上面有引入了一个类**AutoConfigurationImportSelectorta**会调用**selectImports**(AnnotationMetadata annotationMetadata)方法，

这个方法里面又调用了**getAutoConfigurationEntry**(AutoConfigurationMetadata autoConfigurationMetadata, AnnotationMetadata annotationMetadata)方法，这个方法里面有一个List\<String> configurations = this.**getCandidateConfigurations**(annotationMetadata, attributes);

**getCandidateConfigurations**这个方法中通过List\<String> configurations = **SpringFactoriesLoader.loadFactoryNames**(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());

获取候选配置，在spring-boot-autoconfigure-版本号.jar包中都有一个META-INF，他下面有一个**spring.factories**文件，这个文件中有一个key（org.springframework.boot.autoconfigure.**EnableAutoConfiguration**）和这个key对应的有很多配置类，每个配置类上都有一个**configuration**注解和**@EnableConfigurationProperties**注解，**@EnableConfigurationProperties**这个注解里面对应一个**xxxProperties.class**文件，通过这个配置类里面的属性给能够给当前bean设置值（就是把xxxProperties文件中的属性拿过来给当前的配置类的属性赋值，**xxxProperties**类上有一个**@ConfigurationProperties**，这个注解就是将当前yml文件中的属性赋值到这个类的属性上，我们在yml中修改的属性其实就是修改了底层**xxxProperties**的yml文件里的属性，那么**xxxProperties**这个类上的属性也会被影响，间接的赋值给配置类的bean对象的属性也会被影响

---------------------------------------------------------------

图解:

![image-20210112112147152](typora-user-images\image-20210112112147152.png)

![image-20210112112308727](typora-user-images\image-20210112112308727.png)

![image-20210112112349668](typora-user-images\image-20210112112349668.png)

![image-20210112112435502](typora-user-images\image-20210112112435502.png)

![image-20210112122115744](typora-user-images\image-20210112122115744.png)

![image-20210112122154164](typora-user-images\image-20210112122154164.png)

![image-20210112122327616](typora-user-images\image-20210112122327616.png)

![image-20210112122412798](typora-user-images\image-20210112122412798.png)

![image-20210112122537037](typora-user-images\image-20210112122537037.png)

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------

**拓展：**@EnableConfigurationProperties（配置类.class）他就是加载配置类的，然后将属性赋值给有@Bean声明的方法上的属性中，相当于给bean对象的属性赋值

我们修改了yml中的属性会影响xxxProperties.class类，从而影响配置类例面bean对象的值，又因为bean对象在容器里面，所以我们修改后的数据会受影响

### junit在springboot中版本的区别

![image-20210109165028147](typora-user-images\image-20210109165028147.png)

junit4版本如果要使用，要在测试类上加上@RunWith(SpringRunner.class)

### springboot整个redis

几个注意事项：

* service层中的RedisTemplate在注入时给的泛型是两个obj或两个string类型
* 缓存穿透问题（如果100000个人同时查，并且同时查到缓存中没数据那么就会出现缓存穿透问题）
  * 解决：因为service层默认是单例的，使用双重检查的单例模式加锁的方式，这里变量必须声明为全局变量

```java
@Service
@Transactional
public class StudentService {

    @Autowired
    private StudentMapper studentMapper;

    //声明全局变量，不然会多次出现去数据库中查，我们的目的是第一次去数据库中查其他的都是缓存中拿
    private List<Student> studentList;

    @Autowired
    private RedisTemplate<Object, Object> redisTemplate;

    public List<Student> getAllStudent() {
        //这里声明局部变量是不行的要提取为全局变量
        studentList = (List<Student>) redisTemplate.opsForValue().get("list");
        if (studentList == null) {
            //防止缓存穿透问题 使用加锁的方式
            synchronized (this) {
                //判断缓存中有没有
                if (studentList == null) {
                    studentList = studentMapper.getStudents();
                    redisTemplate.opsForValue().set("list", studentList);
                }
            }
            
        }
        return studentList;
    }
}

```

**拓展：**

![image-20210113211820531](typora-user-images\image-20210113211820531.png)

### springboot整合定时任务

//开启定时任务
@EnableScheduling

在类中的配置

```java
@Component
public class AppJobTest {

    /**
     * 定时任务3步：
     *      1、导入spring-context-support依赖
     *      2、在著配置类上加@EnableScheduling注解
     *      3、在job类的方法上加 @Scheduled注解
     *
     * @Scheduled注解的三个常用属性
     *      1、fixedDelay: 表示定时任务之间执行的间隔是xxx毫秒(ms),他和它自身的上一次结束时间做对比，也就是从上一次结束时间开始算
     *      2、fixedRate:执行频率:取决于设置定时任务fixedRate值和业务逻辑本身消耗的时间值谁大就听谁的
     *          业务逻辑本身消耗的时间>定时任务fixedRate值:以业务逻辑本身消耗的时间为准
     *          定时任务fixedRate值>业务逻辑本身消耗的时间:以定时任务fixedRate值为准
     *          他计算的是两个开始时间中间的间隔，跟第一种方式算法不同，上面是以结束开始算
     *      3、cron: 以空格隔开的6位或者7位字符组成
     *        类似于第一个，是指的结束到开始
     *
     */
   // cron = "*/3 * * * * ?"  表示每隔两秒执行一次，和mvc中学的cron有所不同（mvc中这样写时3秒）

    //设置定时时长  以毫秒为单位
    @Scheduled(cron = "*/3 * * ? * *")
    public void printHelloWorld() throws InterruptedException {
        System.out.println("开始时间："+new SimpleDateFormat("yyyy-MM-dd-HH:mm:ss").format(new Date()));
        Thread.sleep(4000);
        System.out.println("结束时间："+new SimpleDateFormat("yyyy-MM-dd-HH:mm:ss").format(new Date()));
    }

}
```