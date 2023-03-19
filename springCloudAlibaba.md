# springCloudAlibaba

由于性能关系，Eureka 停止更新，Hystrix 和Ribbon 进入维护模式，不再继续更新。2018.10.31，Spring Cloud Alibaba 正式入驻了Spring Cloud 官网孵化器，并在Maven 中央库发布了第一个版本。Spring Cloud Alibaba 致力于提供微服务开发
的一站式解决方案。此项目包含开发分布式应用微服务的必需组件，方便开发者通过Spring Cloud 编程模型轻松使用这些组件来开发分布式应用服务。依托Spring CloudAlibaba，您只需要添加一些注解和少量配置，就可以将Spring Cloud 应用接入阿里微服务解决方案，通过阿里中间件来迅速搭建分布式应用系统。

参考：https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md

**常见的注册中心：**

- Eureka（原生，2.0遇到性能瓶颈，停止维护）
- Zookeeper（支持,专业的独立产品。例如：dubbo）
- Consul（原生，GO语言开发）
- Nacos

**Nacos = Spring Cloud Eureka + Spring Cloud Config**

```txt
通过NacosServer和spring-cloud-starter-alibaba-nacos-config 实现配置的动态变更

通过NacosServer和spring-cloud-starter-alibaba-nacos-discovery实现服务注册与发现
```

### Nacos

> 注意事项：本地启动nacos要改成单节点启动否则会报错

**Nacos 主要提供以下四大功能：**

- 服务发现和服务健康监测
- 动态配置服务
- 动态DNS服务
- 服务及其元数据管理

**Nacos 就是注册中心+配置中心的组合,等价于:Nacos = Eureka+Config+Bus**

**几个注解的解释：**

- **@EnableDiscoveryClient**：开启对客户端的支持
- **@EnableCircuitBreaker**    开始熔断保护机制
- **@EnableFeignClients**      开启feign远程调用

**nacos默认支持负载均衡**

**nocos与其他注册中心的比较：**

![image-20210113143233091](typora-user-images\image-20210113143233091.png)

### nocos配置中心

nacos 配置中心：系统配置的集中管理（编辑、存储、分发）、动态更新不重启、回滚配置（变更管理、历史版本管理、变更审计）等所有与配置相关的活动。

#### dataId 的完整格式

```txt
${prefix}-${spring.profile.active}.${file-extension}

1.prefix 默认为所属工程配置spring.application.name 的值（nacos-provider-movie*）,也可以通过配置项spring.cloud.nacos.config.prefix来配置。

2.spring.profile.active 即为当前环境对应的profile，详情可以参考Spring Boot文档。注意：当spring.profile.active 为空时，对应的连接符- 也将不存在，dataId 的拼接格式变成${prefix}.${file-extension}

3.file-exetension 为配置内容的数据格式，可以通过配置项spring.cloud.nacos.config.file-extension 来配置。目前只支持properties 和yaml类型。
```

**总结：配置所属工程的spring.application.name 的值+ "." + properties/yml**

**nacos的注册中心和配置中心是可以分开的**

**bootstrap.properties**这个文件是告诉配置中心

**@RefreshScope**     让配置中心的配置也能通过@value注入，实现自动更新配置中心变量的值（就是你修改了能够自动更新不用重新部署）

#### 多个名称空间的切换

nacos配置中心可以创建多个命名空间

在bootstrap.properties文件中设置spring.cloud.nacos.config.namespace=要转换的空间key

#### 加载多配置文件

bootstrap.properties文件的配置信息：

```properties
#配置中心地址
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
#切换命名空间
spring.cloud.nacos.config.namespace=f80452ab-83d9-418c-adf4-127d8bcf3451
#加载多配置中心的配置文件，通过索引的方式，他底层是一个集合List<NacosConfigProperties.Config> extConfig
spring.cloud.nacos.config.ext-config[0].data-id=jdbc.properties
#开启动态刷新配置，否则配置文件修改,工程无法感知
spring.cloud.nacos.config.ext-config[0].refresh=true
spring.cloud.nacos.config.ext-config[1].data-id=redis.properties
#开启动态刷新配置，否则配置文件修改,工程无法感知
spring.cloud.nacos.config.ext-config[1].refresh=true
```

#### 配置分组

在实际开发中，除了不同的环境外。不同的微服务或者业务功能，可能有不同的redis 及mysql 数据库(集群中的相同微服务都连接自己的数据库或者redis)。区分不同的环境我们使用名称空间（namespace），区分不同的微服务或功能，使用分组（group）。当然，你也可以反过来使用，名称空间和分组只是为了更好的区分配置，提供的两个维度而已

1. 在配置中心创建文件的时候自定义组
2. 在bootstrap.properties配置文件中使用spring.cloud.nacos.config.ext-config[1].group=组名来指定你要去哪个组中找

**最佳实践：**命名空间区分环境，分组区分业务功能

### Nacos 集群和持久化配置(重要)

默认Nacos 使用嵌入式数据库实现数据的存储。所以，如果启动多个默认配置下的Nacos节点，数据存储是存在一致性问题多的。为了解决这个问题，Nacos 采用了集中式存储的方式来支持集群化部署，目前只支持MySQL 的存储。

**nacos默认是使用的derby数据库，如果要做集群它只支持mysql数据库**

#### nacos支持的三种部署模式

- 单机模式-用于单机和测试使用
- 集群模式-用于生产环境，确保高可用
- 多集群模式-用于多数据中心场景

修改nacos的持久化数据库为mysql数据库，mysql中的config_info表对应着nacos中的配置文件信息

**集群搭建看课件**

### sertinel实现熔断与限流

**削峰添谷的概念**：当流量多的时候先接收过来存放着，接收过来先不处理，等着微服务慢慢处理

Sentinel 以流量为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

#### Sentinel与hytrix的区别

![image-20210115090553703](typora-user-images\image-20210115090553703.png)

#### Sentinl的整合

1. 导依赖
2. 配置yml

**QPS（QUery Per Sencnd）**：每秒请求的次数

#### Sentinel-流控模式

**流控规则设置面板的解释**：

​	![image-20210115092113653](typora-user-images\image-20210115092113653.png)



**@SentinelResource设置资源名称**

**Warm up(预热)流控模式**:当系统运行时,有并发请求来访问系统时，为了避免系统崩溃，可以设置一个阈值，让项目可以处理请求的数量逐渐增加到设定的阈值。

Warmup 配置
默认coldFactor(冷却因子) 为3，即请求QPS 从单机阈值/ 3 (冷却因子)开
始，经预热时长逐渐升至设定的QPS 阈值。

#### Sentinel-降级模式

几个降级策略：

- RT(平均响应时间,秒级)
  - 平均响应时间(DEGRADE_GRADE_RT)：超过阈值且时间窗口内的请求>=5，两个条件同时满足后触发降级，窗口期过后关闭断路器
  - RT 最大4900 ms，更大的需要通过启动配置项-Dcsp.sentinel.statistic.max.rt=xxx来配置。
- 异常比例（秒级）
  - QPS>=5 且异常比例（秒级统计）超过阈值时，触发降级；时间窗口结束后，关闭降级
- 异常数（分钟级）
  - 异常数（分钟统计）超过阈值时，触发降级；时间窗口结束后，关闭降级

Sentinel 的断路器是没有半开状态的,没有异常就关闭断路器恢复使用，有异常则继续打开断路器不可用。(对比Hystrix)

#### 热点key 限流

限流、熔断降级，sentinel 默认的提示为Blocked by Sentinel(flow limiting)，我们可以采用类似hystrix 返回兜底数据的方式处理。Sentinel 提供了**@SentinelResource** 注解。

```
@SentinelResource注解：表示出现异常返回备份数据的
   value：表示资源名称，value值不一定要和@GetMapping的value值保持一致
   blockHandler：指定备份数据的方法名，备份数据的方法的参数和返回值必须和受保护的方法（当前方法）保持一致在备份方				 法的参数中需要加入一个BlockException类型的参数，放到最后即可
   blockHandlerClass:如果兜底方法和正常返回数据的方法没有在一个类中的话需要blockHandlerClass指定兜底方法所在类的字节码,并且兜底类里的方法必须要由public static修饰
```

例：

**方法一：兜底方法和受保护方法在一个类中**（在controller中）

```java
@GetMapping("/testHotKey")
@SentinelResource(value = "testHotKey",blockHandler = "deal_testHotKey")
public String testHotKey(@RequestParam(value = "p1",required = false) String p1,
						 @RequestParam(value = "p2",required = false) String p2)
			{
//int age = 10/0;
				return "------testHotKey";
	}
//兜底方法
public String deal_testHotKey (String p1, String p2, BlockException exception){
			return "------deal_testHotKey,o(╥﹏╥)o";
}
```

**方式二：兜底方法和受保护方法不在一个类中**

controller层方法：

```java
    @GetMapping("/testHotKey")
    @SentinelResource(value = "testHotKey",blockHandler = "deal_testHotKey",blockHandlerClass = ExceptionHandler.class)
    public String testHotKey(@RequestParam(value = "p1",required = false) String p1,
                             @RequestParam(value = "p2",required = false) String p2)
    {
//int age = 10/0;
        return "------testHotKey";
    }
```

兜底类中的方法：

```java
public class ExceptionHandler {

    //兜底方法
    //备份方法的参数中需要加入一个BlockException类型的参数，放到最后即可
    //如果要和返回正常数据的方法分开的话方法必须以public static修饰
    public static String deal_testHotKey (String p1, String p2, BlockException exception){
        return "-------------deal_testHotKey,o(╥﹏╥)o-------------";
    }
}
```

**注意：做流控设置的时候资源名要设置为@SentinelResource 注解的value**

#### 系统规则

![image-20210115113653611](typora-user-images\image-20210115113653611.png)

系统规则对整个微服务模块都起作用

#### 规则持久化

将限流配置规则持久化进Nacos 保存，只要刷新6600 某个rest 地址，sentinel 控制台的流控规则就能看到，只要Nacos 里面的配置不删除，则Sentinel 上配置的流控规则持续有效

1、导入依赖

```xml
<dependency>
<groupId>com.alibaba.csp</groupId>
<artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

2、在yml的sentinel下配置

```yml
sentinel: 
  datasource:   #将sentinel持久化到nacos中
        ds1:
          nacos:
            server-addr: localhost:8848 #nacons地址
            dataId: cloudalibaba-sentinel-service #nacos中的data_id
            groupId: DEFAULT_GROUP  #nacos默认分组
            data-type: json  #使用json数据类型
            rule-type: flow   #规则类型跟sentinel的设置规则一样有流控、热点、降级、权限   flow代表是流控
feign:
   sentinel:
     enabled: true #激活feign对sentinel的支持
```

3、添加Nacos 业务规则配置文件，文件中是json数据

例：

```json
[
{
"resource": "/movie/test2",
"limitApp": "default",
"grade": 1,
"count": 1,
"strategy": 0,
"controlBehavior": 0,
"clusterMode": false
}
]
```

![image-20210115115128312](typora-user-images\image-20210115115128312.png)

4、启动测试