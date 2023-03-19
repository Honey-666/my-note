# springcloud

简介：他是管理springboot的

### 集群、分布式、微服务

*  集群:同一个业务，部署在多个服务器上，目的是实现高可用,保证节点可用！
*  分布式:一个业务分拆成多个子业务，部署在不同的服务器上,每个子业务都可以做成集群,目的是为了分摊服务器(软件服务器(tomcat 等)和硬件服务器：主机节点)的压力。
* 微服务:相比分布式服务来说,它的粒度更小,小到一个服务只对应一个单一的功能，只做一件事，使得服务之间耦合度更低，由于每个微服务都由独立的小团队负责它的开发，测试，部署，上线，负责它的整个生命周期，因此它敏捷性更高，分布式服务最后都会向微服务架构演化，这是一种趋势。

![image-20210111155527753](typora-user-images\image-20210111155527753.png)

#### 微服务概念：

微服务是一种架构风格，一个大型复杂软件应用由一个或多个微服务组成。系统中的各个微服务可被独立部署，各个微服务之间是松耦合的。每个微服务仅关注于完成一件任务并很好地完成该任务。在所有情况下，每个任务代表着一个小的业务能力

> **每个进程独占一个端口号**

### 版本命名规则

早期用英国伦敦地铁站A-Z命名，到现在还是使用年月日的方式命名的

#### springcloud和springboot的版本对应关系

参考：https://start.spring.io/actuator/info

GitHub:https://github.com/alibaba/spring-cloud-alibaba/wiki/%E7%89%88
%E6%9C%AC%E8%AF%B4%E6%98%8E

### Eureka（注册中心）

eureka可以是单节点，也可以组成集群的方式

![image-20210111164828204](typora-user-images\image-20210111164828204.png)



- 注册中心: 保存某个服务所在地址等信息，方便调用者实时获取其他服务信息,也就是Eureka Server,Eureka服务端启动后，Eureka客户端便可以注册服务了,Eureka的客户端连接到Eureka Server并维持心跳连接(默认周期30秒),这样系统的维护人员可以通过Eureka Server来监控系统中各个微服务是否正常运行!而且,如果当Eureka Server长时间接收不到客户端的心跳请求时，便会从服务注册表中把这个服务节点移出（默认90秒）。
- 服务提供者: 当服务器启动的时候，会把当前自己服务器的信息，比如：服务通讯地址等以
  别名方式注册到注册中心上。
- 服务消费者: 以该别名的方式去注册中心上获取到实际的服务通讯地址，然后,再实现本地
  RPC远程调用。
- 提示：**无论是Eureka客户端还是服务端都可以以集群的方式运行，无论是服务提供者还是消费者对于eureka来说都是客户端**。

* **yml中eureka的配置解释**

```xml
 fetch-registry: false   #表示当前eureka本身就是一个服务器端，不需要去其他eureka获取数据，搭建集群的时候设置true或这他是服务消费方和服务提供方的时候
 register-with-eureka: false #表示当前eureka本身就是一个服务器端,不需要向自己注册，集群和服务消费方和提供方是true
```

#### eureka常用的注解

@EnableEurekaServer   //开启eureka服务器端

@EnableEurekaClient  //开启eureka客户端

### Ribbon（远程调用和负载均衡）

ribbon是基于Netflix ribbon的实现的一套客户端负载均衡的工具，主要功能是提供客户端的软件负载均衡算法和服务调用

Ribbon 客户端组件提供一系列完善的配置项，如：连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer(简称LB)后面所有的机器，Ribbon 会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们很容易使用Ribbon 实现自定义的负载均衡算法。
总之: Ribbon=客户端负载均衡+RestTemplate 远程调用(用户服务调用电影服务)

#### 远程调用的使用和配置负载均衡（客户端负载均衡）：

1. 先在调用方写一个配置类

   ```java
   @SpringBootConfiguration
   public class RestTemplateConfig {
       @LoadBalanced //开启负载均衡
       @Bean
       public RestTemplate getRestTemplate() {
   
           return new RestTemplate();
       }
   }
   ```

2. 在service层注入RestTemplate，调用他的getForObject方法（两个参数的），返回一个远程调用的对象

   ```java
   @Autowired
       private RestTemplate restTemplate;
   
   此处省略其他代码
   
   //restful调用风格,注意//后面是eureka里面的注册名，在加被调用方的访问地址，第二个参数是被调用方pojo类的字节码，如果没有这个pojo类就复制过来一个
    Movie mov = restTemplate.getForObject("http://CLOUD-EUREKA-PROVIDER8001/movie/latest", Movie.class);
           result.put("movie", mov);//暂时为null
   ```

3. 测试 

**负载均衡策略：**

1. RoundRobinRule 轮询，默认策略。
2. RandomRule 随机
3. RetryRule 先按照RoundRobinRule 的策略获取服务，如果获取服务失败则在指定
   时间内会进行重试，获取可用的服务
4. WeightedResponseTimeRule 对RoundRobinRule 的扩展，响应速度越快的实例
   选择权重越大，越容易被选择
5. BestAvailableRule 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然
   后选择一个并发量最小的服务
6. AvailabilityFilteringRule 先过滤掉故障实例，再选择并发较小的实例
7. ZoneAvoidanceRule 默认规则，复合判断server 所在区域的性能和server 的可
   用性选择服务器

### OpenFeign（声明式调用远程方法，它包含注册中心和负载均衡）

**简介和描述：**Feign 是一个声明式的web 服务客户端，让编写web 服务客户端变得非常容易，只需创建一个接口并在接口上添加注解即可。说白了，Feign 就是Ribbon+RestTemplate，采用RESTful 接口的方式实现远程调用，取代了Ribbon 中通过RestTemplate 的方式进行远程通信。

SpringCloud 对Feign 进行了封装, 使其支持SpringMVC 标准注解和HttpMessageConverters。Feign 可与Eureka 和Ribbon 组合使用以支持负载均衡。

Openfeign 是在Feign 的基础上支持了SpringMVC 注解(例如@RequestMapping 等)，通过动态代理的方式实现负载均衡调用其他服务,说白了，OpenFign 是SpringCloud 对netflix 公司的Feign 组件的进一步封装，封装之后OpenFeign 就成了SpringCloud家族的一个组件了。

#### feign的使用：

1. **@EnableFeignClients**注解开启对feign的支持，放到主配置类上
2. 在service层自定义一个接口

```java
//CLOUD-EUREKA-PROVIDER8001被调用方在eureka的服务名
@FeignClient(value = "CLOUD-EUREKA-PROVIDER8001")
public interface MovieFeignService {

    //方法及@RequestMapping映射要和被调用接口保持完全一致
    @RequestMapping("/movie/latest")
    public Movie getAllMovie();
}
```

3. 在远程通信的地方注入自定义接口调用方法

```java
@Service
public class UserService {
    @Autowired
    UserDao userDao;

    //注入这个接口的代理类对象,开启调用远程服务
    @Autowired
    private MovieFeignService movieFeignService;


    public User getUserById(Integer id){
        User user = userDao.getUser(id);
        return user;
    }
    public Map<String, Object> getUserAndMovie (Integer id) {
        Map<String, Object> result = new HashMap<>();
//1、查询用户信息
        User user = getUserById(id);
//2、查到最新电影票
        result.put("user", user);
        //restful调用风格
        Movie movie = movieFeignService.getAllMovie();
        result.put("movie", movie);
        return result;
    }
}
```

#### Feign 超时设置

默认Feign 客户端只等待一秒钟，但是，服务端处理需要超过1 秒钟，导致Feign 客户端不想等待了，直接报错。为了避免这样的情况，有时候我们需要设置Feign 客户端的超时控制，也即Ribbon 的超时时间，因为Feign 集成了Ribbon 进行负载均衡

在yml中配置超时时长：

```yml
#设置Feign客户端超时时间（openfeign默认支持ribbon）
ribbon:
  ConnectTimeout: 5000   #指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ReadTimeout: 5000     #指的是建立连接后从服务器读取到可用资源所用的时间
```

### 熔断器Hystrix

服务雪崩：一个微服务不可用，影响到调用它的微服务全部不可用

#### 服务降级Fallback

就是访问如果出现异常了返回一个备份数据，就是服务降级

**哪些情况会触发降级**

* 程序运行异常
* 超时自动降级
* 服务熔断触发服务降级
* 线程池/信号量打满也会导致服务降级
* 人工降级

#### 服务熔断Breaker

服务熔断诱因:服务的降级->进而熔断->恢复调用链路

服务降级会尝试着去调用不可用的微服务

服务熔断：当尝试多次去调用微服务时，那个微服务还是没有响应，那么以后就不会在进行尝试，直接返回备份数据

熔断必会出发降级

降级超过20次会进行熔断，打开熔断的时候会有5秒会不管怎么样都会返回备份数据，5秒后会进入半开状态，进入半开状态的时候再次发送请求，会尝试请求微服务查看是否能够请求成功，如果请求成功，那么熔断关闭，如果失败，会再次进入熔断窗口阶段，5秒内发送20次请求会再次熔断，就这样循环

#### 服务限流

限制某个服务每秒的调用本服务的频率，例如秒杀高并发等操作，严禁一窝蜂的过来拥挤，
大家排队，一秒钟N 个，有序进行
防爬虫、防Ddos攻击

#### 断路器

Hystrix 是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix 能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。“断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（Fallback），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

#### 降级的步骤

**Ribbon整合降级：**

1. 在主配置类上使用@EnableCircuitBreaker注解开启降级服务

2. 在service远程通信的方法上添加@HystrixCommand(fallbackMethod = "getUserAndMovieHystrix")注解，里面参数是你备份数据的方法名，注意：

   ```
   //在方法上使用 @HystrixCommand注解设置降级时去请求那个方法
   //注意，熔断调用的方法的参数和返回值必须和没有熔断时的方法一样，也就是改方法名和内容
   HystrixCommand(fallbackMethod="getUserAndMovieHystrix")
   ```

3. 测试

4. @HystrixCommand注解参数的说明fallbackMethod =备份数据的方法名，commandProperties 设置5秒内不会进行降级，也就是请求他会等待5秒，5秒以后才会进行降级

@HystrixCommand(fallbackMethod = "payment_TimeoutHandler",commandProperties = {
            @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value="5000") //5秒钟以内就是正常的业务逻辑
    })

**Feign+Hystrix 组合**：

1. 在主配置类上使用@EnableCircuitBreaker注解开启降级服务

2. 在yml中配置

   ```xml
   feign:
     hystrix:
       enabled: true  #开启Feign对Hystrix的支持
   ```

3. 在service包下自定义一个类，实现你自定义的feign接口，并实现方法，修改里面返回的内容，当作备份数据返回
4. 在自定义feign的接口的@FeignClien注解中添加  fallback = 自定义备份数据类的字节码文件

```java
//CLOUD-EUREKA-PROVIDER8001被调用方在eureka的服务名
//fallback熔断时要走的备份方法
@FeignClient(value = "CLOUD-EUREKA-PROVIDER8001",fallback = MovieFeignServiceExceptionHandler.class)
public interface MovieFeignService {

    //方法及@RequestMapping映射要和被调用接口保持完全一致
    @RequestMapping("/movie/latest")
    public Movie getAllMovie();
}

```

自定义备份类：

```java
//如果出现熔断会走这个备份数据
@Service //将这个类放入大容器中
public class MovieFeignServiceExceptionHandler implements MovieFeignService {
    @Override
    public Movie getAllMovie() {
        Movie movie = new Movie();
        movie.setId(404);
        movie.setMovieName("访问出现异常，请联系管理人员~~~~~");
        return movie;
    }
}
```

### GateWay（网关）

**几个概念：**

- 跨域：ip、协议、端口号发生变化就会发生跨域

- 路由: 负责将外部请求转发到具体的服务实例上，是实现外部访问统一入口的基础
- 过滤:负责对请求的处理过程进行干预，是实现请求校验、服务聚合等功能的基础，以后的访问微服务都是通过网关跳转后获得。

**网关三大组件**：

1. 断言(Predicate):只有断言成功后才会匹配到微服务进行路由，路由到代理的微服务。
2. 路由(Route):分发请求
3. 过滤(Filter):对请求或者响应报文进行处理

**gateway工作流程：**

1. 先走断言
2. 再走路由
3. 路由到微服务之前会先走过滤器进行过滤

**filter的两种类型：**

过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（"pre"）或之后("post")执行业务逻辑。Filter 在"pre"类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等，在"post"类型的过滤器中可以做响应内容、响应头的修改，日志的输出，流量控制等有着非常重要的作用

#### 断言(Predicate)检查请求

**扩展：**

@EnableDiscoveryClient 表示这是一个客户端，不管是zookeeper还是 eureka他都能注册，而@EnableEurekaClient只能向eureka注册

**多个断言规则是与的关系**

**断言配置看下面：一般路由和断言都是一起使用的**

#### 路由(Route)断言成功路由给某个特定的微服务

**路由和断言配置：**

```yml
server:
  port: 9527
spring:
  application:
    name: cloud-gateway-gateway9527
  cloud:
    gateway:
      #List<RouteDefinition> routes   它是一个集合
      routes:
        #--------------------------------通过服务名进行路由---------------------
        # id表示当前route路由配置的唯一id,可以自定义，一般是拦截那个服务，然后就用那个eureka的服务名做id
        - id: CLOUD-CONSUMER-FEIGN-CONSUMER80   #通过服务名匹配路由
          uri: lb://CLOUD-CONSUMER-FEIGN-CONSUMER80   #要路由的地址，会将网关拦截的请求路径加到这里跳转的地址后面
          predicates:  #多个断言规则是与的关系
            - Path=/user/**
        # id表示当前route路由配置的唯一id,可以自定义，一般是拦截那个服务，然后就用那个eureka的服务名做id
        - id: CLOUD-EUREKA-PROVIDER8001   #通过服务名匹配路由
          uri: lb://CLOUD-EUREKA-PROVIDER8001   #要路由的地址，会将网关拦截的请求路径加到这里跳转的地址后面
          predicates:    #断言下面都是断言规则，多个断言规则是与的关系
            - Path=/movie/**
            - After=2021-01-01T08:00:00.0+08:00 # 断言，在此时间后请求才会被匹配
            - Before=2021-01-15T09:08+08:00 # 断言，在此时间前请求才会被匹配
            - Between=2021-01-01T08:00:00.0+08:00,2021-01-31T09:10+08:00 # 断言，在此时间区间内访问的请求才会被匹配
            - Cookie=username,atguigu # 断言，请求头中携带Cookie：username=atguigu才可以匹配
            - Header=X-Request-Id,\d+ # 断言，请求头中要有X-Request-Id属性并且值为整数的正则表达式
            - Method=POST # 断言，请求方式为post方式才会被匹配
            - Query=pwd,[a-z0-9_-]{6} # 断言，请求参数中包含pwd并且值长度为6才会被匹配

          filters:           #过滤器
            - AddRequestParameter=requestid,10001    #在匹配请求的请求参数中添加一对请求参数
            - AddResponseHeader=company,atguigu      #在匹配的请求的响应头中添加一对响应头
#            - MyParamFilter   #通知gateway让他对自定义局部过滤器生效

#-----------------通过ip进行匹配---------------------------

#        # id表示当前route路由配置的唯一id,可以自定义，一般是拦截那个服务，然后就用那个eureka的服务名做id
#        - id: CLOUD-CONSUMER-FEIGN-CONSUMER80
#          #不使用-开始表示继续设置对象的属性值
#          #如果当前路由断言成功，请求交由哪个微服务处理
#          #访问gateway地址:http://localhost/user/getMovie?id=1
#          uri: http://localhost:80/   #要路由的地址，会将网关拦截的请求路径加到这里跳转的地址后面
#          #表示断言多个断言规则是与的关系
#          predicates:
#            #拦截路径,/表示网关下的项目，拦截后回去找uri的地址
#            - Path=/user/**
#
#          #拦截电影的请求
#        - id: CLOUD-EUREKA-PROVIDER8001
#          uri: http://localhost:8001/   #要路由的地址，会将网关拦截的请求路径加到这里跳转的地址后面
#          predicates:  #多个断言规则是与的关系
#           - Path=/movie/**

eureka:
  client:
    fetch-registry: true
    register-with-eureka: true
    service-url:
      defaultZone: http://localhost:7001/eureka/
```

#### 过滤(Filter)找路由之前会走过滤器进行过滤

**分类:**
根据filter 的作用时机:

- 局部作用的filter:GatewayFilter(一般使用系统自带的)
  - pre 类型的在请求交给微服务之前起作用
  - post 类型的在响应回来时起作用
- 全局作用的filter:GlobalFilter(一般需要自定义)
- Gateway 自带了多个GatewayFilter ， 在yml 指定route 下配置需要使用的GatewayFilter 工厂类类名即可使用

**自定义局部过滤器**

```java
/**
 * 自定义局部过滤器
 */
@Component
public class MyParamGatewayFactory extends AbstractGatewayFilterFactory {

    @Override
    public GatewayFilter apply(Object config) {
        return new GatewayFilter() {
            @Override
            public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
                //获取参数
                MultiValueMap<String, String> queryParams = exchange.getRequest().getQueryParams();
                String name = queryParams.getFirst("name");
                if (StringUtils.isEmpty(name)){
                    System.out.println("没有携带name参数");
                }else {
                    System.out.println("name参数值："+name);
                }
                return chain.filter(exchange);
            }
        };
    }

    @Override
    public String name() {
        //这个名字可以自定义，这个名字要在yml配置文件中让给他生效
        return "MyParamFilter";
    }
}
```

**全局过滤器**

```java
/**
 * 自定义全局filter
 * 不需要在yml中配置，只要访问网关的地址就会过滤
 */

@Component
public class MyGlobalFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        //获取参数
        MultiValueMap<String, String> queryParams = exchange.getRequest().getQueryParams();
        String token = queryParams.getFirst("token");
        if (StringUtils.isEmpty(token)){
            //如果不符合条件，返回一个响应状态码
            exchange.getResponse().setStatusCode(HttpStatus.UNAUTHORIZED);
            //结束本次请求，返回响应报文
            return exchange.getResponse().setComplete();
        }
         System.out.println("获取到请求参数为：" + token);
        //放行
        return chain.filter(exchange);
    }

    //数字越小，优先级越高，可能有多个全局filter
    @Override
    public int getOrder() {
        return 0;
    }
}
```

### SpringCloud 之分布式链路请求跟踪Sleuth

**介绍：**

在分布式系统中，微服务有多个，服务之间调用关系也比较复杂，如果有的微服务网络或者服务器出现问题会导致服务提供失败，如何快速便捷的去定位出现问题的微服务，SpringCloud Sleuth 给我们提供了解决方案，它集成了Zipkin、HTrace 链路追踪工具，用服务链路追踪来快速定位问题。Zipkin 使用较多。Zipkin 主要由四部分构成：收集器、数据存储、查询以及Web 界面。Zipkin 的收集器负责将各系统报告过来的追踪数据进行接收；而数据存储默认使用Cassandra，也可以替换为MySQL；查询服务用来向其他服务提供数据查询的能力，而Web 服务是官方默认提供的一个图形用户界面。

**术语：**

TraceId对应一个请求，微服务之间调用的过程称之为span，调用的过程会产生一个spanId，调用方和被调用方的spanid相同，调用方还会有一个Client sent，想通过被调用方有一个Server Recehed，返回的时候被调用方变成了Server Sent，调用方变成了Client Recehed

![image-20210113110013566](typora-user-images\image-20210113110013566.png)