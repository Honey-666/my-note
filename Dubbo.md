# Dubbo

### 分布式RPC框架Apache Dubbo

#### 软件架构演变过程

软件架构的发展经历了由单体架构、垂直架构、SOA架构到微服务架构的演进过程

#### 单体架构

* 全部功能集中在一个项目内（All in one）
*  全部功能集成在一个工程中，对于大型项目不易开发、扩展和维护。
* 技术栈受限，只能使用一种语言开发。
*  系统性能扩展只能通过扩展集群节点，成本高。

图解：

![image-20201222105109375](typora-user-images\image-20201222105109375.png)

#### 垂直架构

* 按照业务进行切割，形成小的单体项目，但是会有代码冗余

* 功能集中在一个项目中，不利于开发、扩展、维护。

* 系统扩张只能通过集群的方式。

* 项目之间功能冗余、数据冗余、耦合性强。

图解：

![image-20201222105132775](typora-user-images\image-20201222105132775.png)

#### SOA架构

将重复功能或模块抽取成组件的形式对外提供服务,在项目与服务之间使用ESB（企业服务总线）的形式作为通信的桥梁

ESB

简单 来说 ESB 就是一根管道，用来连接各个服务节点。为了集 成不同系统，不同协议的服务，ESB 做了消息的转化解释和路由工作，让不同的服务互联互通；

![image-20201222105025658](typora-user-images\image-20201222105025658.png)

图解：

![image-20201222105302710](C:\Users\哦！\AppData\Roaming\Typora\typora-user-images\image-20201222105302710.png)

#### 微服务架构

其实和 SOA 架构类似,微服务是在 SOA 上做的升华，微服务架构强调的一个重点是“业务需要彻底的组件化和服务化”，原有的单个业务系统会拆分为多个可以独立开发、设计、运行的小应用。这些小应用之间通过服务完成交互和集成。

* 核心在于“微”
* 每一个服务只负责一件事
* 在微服务中将功能模块拆分的很细
* 抽取的粒度更细，遵循单一原则。

图解：

![image-20201222105657952](typora-user-images\image-20201222105657952.png)

### Apache Dubbo

简介：Apache Dubbo是一款高性能的Java RPC框架。其前身是阿里巴巴公司开源的一个高性能、轻量级的开源Java RPC框架，可以和Spring框架无缝集成

RPC：

RPC全称为remote procedure call，即**远程过程调用**。比如两台服务器A和B，A服务器上部署一个应用，B服务器上部署一个应用，A服务器上的应用想调用B服务器上的应用提供的方法，由于两个应用不在一个内存空间，不能直接调用，所以需要通过网络来表达调用的语义和传达调用的数据。

就理解成一种协议，例如http协议一样，RPC指整个网络远程调用过程

#### Dubbo架构

调用方是消费方（打成war包），被调用方是服务提供方（打成war包），注册中心，我们通常是指用zookeeper

![image-20201222112218669](typora-user-images\image-20201222112218669.png)

![image-20201222112241222](typora-user-images\image-20201222112241222.png)

### Zookeeper介绍

Zookeeper 是 Apache Hadoop 的子项目，是一个树型的目录服务，支持变更推送，适合作为 Dubbo 服务的注册中心，工业强度较高，可用于生产环境，并推荐使用 。

### Dubbo快速入门

* **包扫描**

  * <**dubbo:annotation** **package**="com.atguigu.service" /> 服务提供者和服务消费者都需要配置，表示包扫描，作用是扫描指定包(包括子包)下的类。

* **协议**

  * <**dubbo:protocol** **name**="dubbo" **port**="20880"/>       其中Dubbo支持的协议有：dubbo、rmi、hessian、http、webservice、rest、redis等。

    推荐使用的是dubbo协议

* **应用名**

  *  <dubbo:application name="一般这里是模块名"/>

* **注册中心zookeeper**

  * <dubbo:registry address="zookeeper://127.0.0.1:2181"/>   注册中心地址，端口号默认时2181

#### xml标签说明

**服务提供方：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

<!--    当前应用名，用于注册中心计算应用间依赖关系-->
    <dubbo:application name="dubbodemo_provider"/>

<!--    这里的地址一定要加zookeeper,默认端口号是2181-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>

<!--  注册  name=协议（dubbo官方建议使用dubbo）和  port=端口号 端口默认是20880  只需要在服务提供方配置这个标签即可-->
    <dubbo:protocol name="dubbo" port="20881"/>
<!--    包扫描，远程包扫描-->
    <dubbo:annotation package="com.atguigu.service.impl"/>
</beans>
```

**服务消费方:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    <!-- 当前应用名称，用于注册中心计算应用间依赖关系，注意：消费者和提供者应用名不要一样 -->
    <dubbo:application name="dubbodemo_consumer"/>
    <!-- 扫描的方式暴露接口  -->
    <dubbo:annotation package="com.atguigu.controller"/>
    <!-- 连接服务注册中心zookeeper ip为zookeeper所在服务器的ip地址-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
<!--    先启动消费服方不报错，默认时true-->
<!--    <dubbo:consumer check="false"/>-->
</beans>
```

#### 注解说明

* @Reference  远程注入，同Autowired一样，但是Autowired时本地注入
* @Service   同spring中一样注解在service类上，但是他是dubbo包下的

### 启动时检查

在消费方xml添加<**dubbo:consumer** **check**="false"/> 或在controller类中的@Reference(check = false)

### 负载均衡

负载均衡（Load Balance）：其实就是将请求分摊到多个操作单元上进行执行，从而共同完成工作任务。

方式一：

​			在消费方的controller中的@Reference注解添加(loadbalance = "random")在服务消费者一方配置负载均衡策略

方式二：

​			在每个提供者的service注解上添加(loadbalance = "random")

### 解决Dubbo无法发布被事务代理的Service问题

* dubbo不支持jdk动态代理，所以配置事务的时候要加入proxy-target-class="true"

  * ```
    <tx:annotation-driven transaction-manager="transactionManager" proxy-target-class="true"/>
    ```

* 修改实现类l类，在Service注解中加入interfaceClass属性，值为  接口名.class，作用是指定服务的接口类型

**上面两点就能解决：**

![image-20201222195220275](typora-user-images\image-20201222195220275.png)