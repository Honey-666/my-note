### 1、vim的三种模式以及切换方式

- 命令模式：Vi  文件名   可进入命令模式
- 输入模式：通过a、i、o命令进入到输入模式
- 末行模式：在输入模式种按esc进入末行模式，通过q！、q、wq、wq！退出到控制台

### 2、如何查看linux系统防火墙的状态以及如何关闭，是否启动？

**只答前三个即可**

* 查看状态：systemctl status firewalld
* 开启：systemctl start firewalld
* 关闭：systemctl stop firewalld



​	若遇到无法开启
​		先用:systemctl unmask firewalld.service
​		然后:systemctl start firewalld.service

### 3、git分支和工作流

#### git分支

1. 默认只有一个master分支
2. 一般开发一个新功能的时候创建一个新分支，一般也叫功能分支
3. **创建一个分支的本质是创建一个指针，切换一个分支的本质是切换指针HEAD的指向**，**HEAD默认指向master**

#### git工作流

1. 在实际开发中，master主分支用来保存正式发布版，首先从master上切换出一个开发分支dev
2. dev开发分支不做实际开发，仅用来切换合并功能分支
3. 在开发分支上切换出多个功能分支进行独立开发，开发完成后经过初步的测试后，合并到dev开发分支上形成一个新的版本
4. 由测试人员在dev切换出一个测试分支，对代码进行测试，第一个版本称为预发布版，经过测试人员测试修改bug后成为稳定版
5. 将稳定版合并到master主分支上成为正式发布版的同时，也合并到dev开发分支上一份，以便后续的使用

### 4、redis的优点是什么？为什么能支持访问量和高并发并举例解释

**优点**

1. redis性能极高，50个并发环境下，每秒可读11万次，每秒可写8万次
2. redis支持的多种类型的value，如：string、hash、list、set、zset等
3. redis的操作都是原子性的
4. redis支持数据持久化，适用于高频修改、适合的内存容量的应用

**为什么支持访问量和高并发**

1. redis是基于内存的，内存的读写速度非常快
2. redis是单核单线程的，省去了切换线程的时间和竞争（争抢锁）
3. redis使用多路IO复用技术让单个线程高效地处理并发的连接
4. 通过集群可以解决访问量和高并发的压力。
   * 假设有6个redis节点，将数据分布存储在这些节点中，3个作为主机，3个作为从机，使用主从复制。
   * 将访问请求分摊在3个主机上，可以减轻单个的压力，3个主机都可以进行写操作，能够分摊高并发的压力，
   * 集群还支持哨兵模式，主机宕机了之后从机会升级成为主机，并且集群解决了读写分离的问题，主机写，从机读，提高了高并发写的能力

### 5、如何实现redis的事务

redis事务的主要作用是串联多个命令防止其他命令插队，而实现redis的事务会经过三个阶段，事务开始、命令入队、事务执行

实现事务的三个命令：MULTI、EXEC、DISCARD

1.MULTI用来组装一个事务；
2.EXEC用来执行一个事务；
3.DISCARD用来取消一个事务；

1. multi开启事务，开启事务后，输入的命令不会立刻执行，而是暂存到一个队列中
2. 当使用EXEC命令时，会执行事务队列里的命令
3. DISCARD用来取消一个事务
4. redis的事务在发生错误时有两种情况
   - 组队错误：某个命令出错，执行的整个队列都会被取消
   - 执行错误：某个命令出错，只有报错的命令不会执行，其他命令不受影响

### 6、RDB与AOF的优缺点

RDB：持久化是以快照的方式保存，它恢复时是将快照文件直接读到内存里

AOF：是将命令保存到一个文件中，redis 启动会读取该文件重新构建数据

* RDB:
  * 优点：全量备份，占用空间小，恢复速度快
  * 缺点存储速度慢，优先级低，数据会丢失
* AOF:
  * 优点：增量备份存储速度快，有重写机制，优先级高
  * 缺点：占用磁盘空间大，恢复速度慢

**或者直接这样回答**

* RDB:全量备份存储速度慢，优先级低，占用空间小，恢复速度快，数据会丢失
* AOF:增量备份存储速度快，有重写机制，优先级高但占用磁盘空间大，恢复速度慢

### 7、nginx有哪些基本功能

1. 虚拟主机：nginx内部可以提供类似于主机的服务，自身对外提供服务
2. 反向代理：nginx代理的是服务端，帮助服务器接收客户端的请求
3. 负载均衡：将请求分发到多个服务器上，将负载分发到不同的服务器
4. 动静分离：将静态资源，如html、css、img、js存放到Nginx上，将动态资源放到tomcat上

### 8、对nginx负载均衡的理解

​	在早期，客户端发送多个请求到服务器，服务器处理请求并相应数据给客户端；但是随着信息量的不断增大以及业务逻辑的复杂程度不断提升，这种单一的系统不仅速度慢，还容易造成服务器崩溃。在排除了纵向上通过升级服务器的不可行之后，我们采用了横向处理的方式，也就是通过集群。我们增加服务器的数量，将请求分发到各个服务器上，将负载分发到不同的服务器，也就是负载均衡

**答加粗部分即可：**

**nginx通过upstream name{....}的语法实现负载均衡，将请求分发到多个个人服务器上，且可以根据不同的负载均衡策略，如轮询、weight、ip_hash、fair等更好地实现服务器的性能**

### 9、springcloud的组件有哪些

**答加粗部分即可：**

- **eureka：注册中心**
- **ribbon：负载均衡**
- **feign：声明式远程调用（包含了负载均衡）**
- **hystrix：熔断、降级、监控(能够对每秒执行多少请求进行实时监控)**
- **gateway：网关**
- sleuth：链路跟踪
- **nacos：服务注册以及配置中心**
- sentinel：熔断、降级、限流

### 10、gateway网关的执行流程

- 客户端向网关发送请求，网关进行断言，断言成功找到与请求相匹配的路由，在经过过滤器过滤将请求发送到实际的服务，然后将结果返回，在过滤器中我们还可以进行进行校验和携带参数

### 11、nacos可以实现哪些功能

**答加粗部分即可：**

1. 服务发现和服务健康监测
2. 动态配置服务
3. 动态DNS服务
4. 服务及其元数据管理

- **将微服务注册到注册中心，远程访问注册中心管理的其他微服务；微服务可以通过nacos从配置中心动态拉取配置**

### 12、springboot自动配置原理

**简单描述：**

**@EnableAutoConfiguration**自动配置注解里有一个**selectImports**方法，这个方法可以返回一个集合，这个集合中存储着spring-boot-autoconfigure.jar包下META-INF下**spring.factories**文件中org.springframework.boot.autoconfigure.**EnableAutoConfiguration**这个值对应的所有的配置类容器，在这些配置类上都有一个**@Configuration**注解和**@EnableConfigurationProperties**注解，**@EnableConfigurationProperties**这个注解里面对应一个**xxxProperties.class**类的字节码，**xxxProperties**这个类上有一个**@ConfigurationProperties**注解，它能够将当前类的配置文件中的属性注入到当前类的属性上，从而配置能够将这个字节码类里面的属性取出来赋值给当前配置类的bean对象的属性，并注入到容器中。



**精确描述(跟步骤傻瓜式操作)：**

首先**@springbootapplication**注解中包含了**@EnableAutoConfiguration**自动配置注解，这个注解上面有引入了一个类**AutoConfigurationImportSelectorta**会调用**selectImports**(AnnotationMetadata annotationMetadata)方法，

这个方法里面又调用了**getAutoConfigurationEntry**(AutoConfigurationMetadata autoConfigurationMetadata, AnnotationMetadata annotationMetadata)方法，这个方法里面有一个List\<String> configurations = this.**getCandidateConfigurations**(annotationMetadata, attributes);

**getCandidateConfigurations**这个方法中通过List\<String> configurations = **SpringFactoriesLoader.loadFactoryNames**(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());

获取候选配置，在spring-boot-autoconfigure-版本号.jar包中都有一个META-INF，他下面有一个**spring.factories**文件，这个文件中有一个key（org.springframework.boot.autoconfigure.**EnableAutoConfiguration**）和这个key对应的有很多配置类，每个配置类上都有一个**configuration**注解和**@EnableConfigurationProperties**注解，**@EnableConfigurationProperties**这个注解里面对应一个**xxxProperties.class**类的字节码，通过这个类里面的属性能够给当前bean设置值（就是把xxxProperties文件中的属性拿过来给当前的配置类的属性赋值，**xxxProperties**类上有一个**@ConfigurationProperties**，这个注解就是将当前yml文件中的属性赋值到这个类的属性上，我们在yml中修改的属性其实就是修改了底层**xxxProperties**的yml文件里的属性，那么**xxxProperties**这个类上的属性也会被影响，间接的赋值给配置类的bean对象的属性也会被影响

### 13、什么是缓存雪崩，一般怎么解决

缓存雪崩:大量缓存数据同时失效，导致用户直接发送大量请求到数据库，产生瓶颈
解决：

1. 生成随机失效的缓存时间数据
2. 生成不失效的缓存数据
3. 定时任务更新缓存数据

### 14、redis怎么实现队列

**百度回答：**

​	redis实现队列有两种方式
​		1）、生产者消费者模式
​			lpush rpop：非阻塞式
​			Redis LPush 命令将一个或多个值插入到列表头部。如果 key 不存在，一个空列表会被创建并执行 LPUSH 操作。
​			当 key 存在但不是列表类型时，返回一个错误。
​			lpush brpop：阻塞式
​			Redis BRPop 命令移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
​		2）、订阅模式
​	三个用户A,B,C同时都订阅了一个channel名字叫msg，然后发布者往msg的channel里面发布了一个数据，那么A,B,C三个用户都会收到该数据。

**自己描述：**

使用rpush + lpop 或 lpush + rpop

rpush : 往列表右侧推入数据

lpush: 往列表做侧推入数据

lpop : 从左侧弹出数据

rpop : 从右侧弹出数据

### 15、远程接口调用的实现，讲述原因

**自己描述：**

使用feign技术实现远程接口的调用

1. 创建一个springcloud模块，引入openfeign的依赖，**在配置文件中开启Feign对Hystrix 支持（加粗部分可以不写这个是开启对熔断的支持）**
2. 在主启动类上加@EnableFeignClient，开启feign的支持，实现基于接口的远程调用
3. 在service层创建一个接口，接口中方法的参数、返回值和映射要与被调用接口的方法的参数、返回值和映射完全一致，在接口上添加@FeignClient注解，并指定被调用方法在eureka的服务名
4. 在controller注入设置好的接口，进行代理，并调用接口中的方法

**选择feign的原因：**

1. Feign是面向接口编程，我们一直在强调面向接口编程
2. feign对ribbon进行了封装，采用接口的方式，只需要创建一个接口，将调用的方法定义成抽象方法即可。
3. 使得编写web应用服务相当简便