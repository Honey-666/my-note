# RabbitMQ

**消息中间件MQ**：MQ 全称为Message Queue，是在消息的传输过程中保存消息的容器。多用于分布式系统，之间进行通信。**它可以实现解耦**

![image-20210115141903371](typora-user-images\image-20210115141903371.png)

**MQ 有三大特点：**

- 第一：本身是一个服务，生产者和消费者都需要连接该服务
- 第二：底层采用队列(Queue)的数据结构实现先进先出
- 第三：结构是一个Pub、Sub 模型(发布、订阅模型)生产者发布消息到中间键，消费者到中间键获取消息

**queue为什么不能做消息队列**

1. 因为Queue 是不能跨进程的，所以不能在分布式系统中使用
2. Queue 没有持久化之类的机制。

**为什么使用MQ：**

可将一些无需即时返回且耗时的操作提取出来，进行异步处理，而这种异步处理的方式大大的节省了服务器的请求响应时间，从而提高了系统的吞吐量。

> **在mq中每条消息都有一个唯一标识**

**销峰填谷**：将大量请求放到消息中间件中，避免高峰访问量大低峰访问量小的问题，说白了就是把请求存起来慢慢消费，实现平均

> **rabbitmq一般都是做异步请求**

### MQ的适用场景

* **生产者不需要从消费者处获得反馈**。引入消息队列之前的直接调用，其接口的返回值应该为空，这才让明明下层的动作还没做，上层却当成动作做完了继续往后走，即所谓异步成为了可能
* 容许短暂的不一致性。
* 确实是用了有效果。即解耦、提速、削峰这些方面的收益，超过加入MQ，管理MQ 这些成本

### AMQP和JMS

#### AMQP

AMQP 是一种协议，更准确的说是一种binary wire-level protocol（链接协议）。
这是其和JMS 的本质差别，AMQP 不从API 层进行限定，而是直接定义网络交换的数据格
式。AMQP 是一种跨语言、跨平台的协议，工作在应用层。RabbitMQ 是AMQP 协议的实现。

publisher消息的生产者

consumer消息的消费者

每个虚拟主机里面有多个交换机和消息队列

交换机只做消息的转发，消息队列做消息的存储

![image-20210115144322900](typora-user-images\image-20210115144322900.png)

#### JMS

JMS 即Java 消息服务（JavaMessage Service）应用程序接口，是一个Java 平台中关于面向消息中间件（MOM）的API，用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信，**类似于java中的Jdbc**

#### AMQP和JMS区别

* JMS 是定义了统一的接口，来对消息操作进行统一；AMQP 是通过规定协议来统一数据交互的格式

- JMS 限定了必须使用Java 语言；AMQP 只是协议，不规定实现方式，因此是跨语言的。
- JMS 规定了两种消息模式；而AMQP 的消息模式更加丰富

### 消息队列产品

市场上常见的消息队列有如下：
- ActiveMQ：基于JMS
- ZeroMQ：基于C 语言开发
- RabbitMQ：基于AMQP 协议，erlang 语言开发，稳定性好
- RocketMQ：基于JMS，阿里巴巴产品
- Kafka：类似MQ 的产品；分布式消息系统，高吞吐量

![image-20210115144749661](typora-user-images\image-20210115144749661.png)

### RabbitMQ

简介：RabbitMQ 是由erlang 语言开发，基于AMQP（Advanced Message Queue 高级消息队列协议）协议实现的消息队列，它

是一种应用程序之间的通信方法，消息队列在分布式系统开发中应用非常广泛。

RabbitMQ 官方地址：http://www.rabbitmq.com/

简略图：

![image-20210115151414953](typora-user-images\image-20210115151414953.png)

详细图：

![image-20210115151430186](typora-user-images\image-20210115151430186.png)

对上图概念的解释：

*  Broker：接收和分发消息的应用，RabbitMQ Server 就是Message Broker 
* Virtual host：出于多租户和安全因素设计的，把AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的namespace 概念。当多个不同的用户使用同一个RabbitMQ server 提供的服务时， 可以划分出多个vhost， 每个用户在自己的vhost 创建exchange／queue 等
*  Connection：publisher／consumer 和broker 之间的TCP 连接
*  Channel：如果每一次访问RabbitMQ 都建立一个Connection，在消息量大的时候建立TCP Connection 的开销将是巨大的，效率也较低。Channel 是在connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个thread 创建单独的channel 进行通讯，AMQP method 包含了channel id 帮助客户端和message broker 识别channel，所以channel 之间是完全隔离的。Channel 作为轻量级的Connection 极大减少了操作系统建立TCP connection 的开销
* Exchange:message 到达broker 的第一站，根据分发规则，匹配查询表中的routing key，分发消息到queue 中去。常用的类型有：direct (point-to-point),topic (publish-subscribe) and fanout (multicast)
* Queue：消息最终被送到这里等待consumer 取走
* Binding：exchange 和queue 之间的虚拟连接，binding 中可以包含routingkey。Binding 信息被保存到exchange 中的查询表中，用于message 的分发依据

#### RabbitMQ的启停命令

- systemctl start rabbitmq-server.service     启动服务
- systemctl status rabbitmq-server.service   查看状态
- systemctl restart rabbitmq-server.service  重启
- systemctl stop rabbitmq-server.service    停止

#### 图形化界面几个模块的解释

* overview：概览
* connections：无论生产者还是消费者，都需要与RabbitMQ 建立连接后才可以完成消息的生产和消费，在这里可以查看连接情况
* channels：通道，建立连接后，会形成通道，消息的投递获取依赖通道。
* Exchanges：交换机，用来实现消息的路由
* Queues：队列，即消息队列，消息存放在队列中，等待消费，消费后被移除队列。

#### 三个端口号

- 5672: rabbitMq 的编程语言客户端连接端口
- 15672：rabbitMq 管理界面端口
- 25672：rabbitMq 集群的端口

### 五种工作模式

> **知识补充：局部变量放到类中要用final修饰**

#### 简单工作模式

只有一个生产者、消费者、消息队列

![image-20210116090122285](typora-user-images\image-20210116090122285.png)

生产者示例：

```java
public class simpleSimpleProducer {
    public static void main(String[] args) throws IOException, TimeoutException {
        //1.创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //2.设置参数
        factory.setHost("192.168.142.200");
        factory.setPort(5672);
        factory.setVirtualHost("/ems");
        factory.setUsername("root");
        factory.setPassword("root");
        //3.创建连接
        Connection connection = factory.newConnection();
        //4.创建通道
        Channel channel = connection.createChannel();
        //5.声明（创建）队列
        /**
         * String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
         *         第一个参数：queue表示队列名称
         *         第二个参数：durable表示当前队列是否支持持久化，一般设置为true，支持持久化
         *         第三个参数: exclusive:表示是否独占,是否只有一个消费者可以连接该队列，一般设置为false
         *         第四个参数：autoDelete表示是否自动删除,当channel和queue断开连接后，是否自动删除该队列，通常设置为false
         *         第五个参数：arguments:对队列的一些设置，一般设置为null即可
         */
       channel.queueDeclare("firstRabbit", true, false, false, null);
        //6.发送消息
        //定义一个消息
        String message = "hello rabbit";
        /**
         *  String exchange, String routingKey, BasicProperties props, byte[] body
         *         第一个参数:exchange表示交换机名称，没有设置为"",
         *         第二个参数:routingKey：路由key,在简单模式中，暂时用队列名称充当路由key
         *         第三个参数：BasicProperties，可以设置消息的属性信息,一般不用设置，给null
         *         第四个参数：body:表示要发送的消息
         */
        channel.basicPublish("","firstRabbit",null,message.getBytes());
        System.out.println("已发送"+message);
        //7.关闭资源
        channel.close();
        connection.close();

    }
}
```

消费者示例：

```java
public class SimpleConsumer {
    public static void main(String[] args) throws IOException, TimeoutException {
        //1.创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //2.设置参数
        factory.setHost("192.168.142.200");
        factory.setPort(5672);
        factory.setVirtualHost("/ems");
        factory.setUsername("root");
        factory.setPassword("root");
        //3.创建连接
        Connection connection = factory.newConnection();
        //4.通过连接创建通道channel
        final Channel channel = connection.createChannel();
        //5.通过通道消费队列的消息

        /**
         *  String queue, boolean autoAck, Consumer callback
         *         第一个参数：queue:指定队列名称
         *         第二个参数：autoAck表示消费者获取到队列里面的消息之后，是否要和消息队列确认,这里设置为true，
         *         第三个参数：Consumer表示消费者
         */
        //创建消费者因为是接口，所以创建他的实现类
        Consumer consumer = new DefaultConsumer(channel){
            /**
             *
             * @param consumerTag  用户唯一标识
             * @param envelope  信封的意思，通过它可以获取交换机信息、路由key信息、消息的id
             * @param properties  获取设置消息的属性
             * @param body   消息队列的内容
             * @throws IOException
             */
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                super.handleDelivery(consumerTag, envelope, properties, body);

                int i = 10 /0;

                //里面是业务逻辑代码
                System.out.println("用户唯一标识是："+consumerTag);
                System.out.println("交换机："+envelope.getExchange());
                System.out.println("路由key信息是："+envelope.getRoutingKey());
                System.out.println("消息唯一标识是："+envelope.getDeliveryTag());
                System.out.println("内容是："+new String(body));


                //业务逻辑代码执行完进行手动确认
                /**
                 * long deliveryTag, boolean multiple:设置false
                 *     第一个参数：消息的唯一标识
                 *     第二个参数：multiple表示当有多个消费者都消费这个队列中的这个消息时是否让他们都确认，false表示只需要一个确认就行
                 */
                //这个方法的channel必须时final修饰的
                channel.basicAck(envelope.getDeliveryTag(),false);
            }
        };
        //手动确认
        channel.basicConsume("firstRabbit",false,consumer);
        //不要释放资源，一直监听这消息队列
    }
}
```

#### Work queues 工作队列模式

**一个提供者，多个消费者，同一条消息只能被一个消费者消费**

**默认情况下多个消费者是平均消费的，假如是两个那么就是一人拿一半，如果想要实现能者多劳要在一个消费者上面设置channel.basicQos(5);表示这个消费者一次之消费五条，不按默认平均分**

> **注意：能者多劳模式必须为手动提交**

![image-20210116090142664](typora-user-images\image-20210116090142664.png)

**应用场景：**对于任务过重或任务较多情况使用工作队列可以提高任务处理的速度。

工具类：

```java
public class ConnectionUtil {

    public static Connection getConnection() throws IOException, TimeoutException {

            //1.创建连接工厂
            ConnectionFactory factory = new ConnectionFactory();
            //2.设置参数
            factory.setHost("192.168.142.200");
            factory.setPort(5672);
            factory.setVirtualHost("/ems");
            factory.setUsername("root");
            factory.setPassword("root");
            //3.创建连接
            Connection connection = factory.newConnection();

            return connection;

    }
}
```

生产者代码：

```java
public class SecondProducer {
    public static void main(String[] args) throws IOException, TimeoutException {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        channel.queueDeclare("secondName", true, false, false, null);

        for (int i = 0; i < 30; i++) {
            String message = "hello honey"+i;
            channel.basicPublish("","secondName",null,message.getBytes());

        }
     channel.close();
        connection.close();
    }
}
```

消费者1：

```java
public class SecondConsumer1 {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();
        final Channel channel = connection.createChannel();

        //设置让这个消费者一次消费5条数据
        //注意：能者多劳模式必须为手动提交
        channel.basicQos(5);
        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者1"+new String(body));
                channel.basicAck(envelope.getDeliveryTag(),false);
            }
        };
        channel.basicConsume("secondName",false,consumer);
    }
}
```

消费者2：

```java
public class SecondConsumer2 {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();
        final Channel channel = connection.createChannel();

        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2"+new String(body));
                channel.basicAck(envelope.getDeliveryTag(),false);
            }
        };
        channel.basicConsume("secondName",false,consumer);
    }
}
```

#### Publish/Subscribe

通过交换机一次同时向多个消费者发送消息

Exchange：交换机，图中的X。一方面，接收生产者发送的消息。另一方面，知道如何处理消息，例如递交给某个特别队列、递

交给所有队列、或是将消息丢弃。到底如何操作，取决于Exchange 的类型。Exchange 有常见以下3 种类型：

- **Fanout**：广播，将消息交给所有绑定到交换机的队列
- **Direct**：定向，把消息交给符合指定routing key 的队列
- **Topic**：通配符，把消息交给符合routing pattern（路由模式） 的队列

E**xchange（交换机）只负责转发消息，不具备存储消息的能力**,因此如果没有任何队列与Exchange 绑定，或者没有符合路由规则

的队列，那么消息会丢失！

在广播模式下，消息发送流程是这样的:

- 可以有多个消费者
- 每个消费者有自己的queue（队列）
- 每个队列都要绑定到Exchange（交换机）
- 生产者发送的消息,只能发送到交换机,交换机来决定要发给哪个队列,生产者无法决定。
- 交换机把消息发送给绑定过的所有队列
- 队列的消费者都能拿到消息。实现一条消息被多个消费者消费

![image-20210116090202610](typora-user-images\image-20210116090202610.png)

生产者：

```java
public class PubsubProducer {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();

        //创建交换机
        /**
         * String exchange, String type
         *      参数一：交换机的名字
         *      参数二：交换机的类型，有三种类型，现在用广播类型
         */
        channel.exchangeDeclare("myexchange","fanout");
        //消息内容
        String message = "Hello everyone";
        //将消息发送给交换机，就不用直接发给队列了
        channel.basicPublish("myexchange","",null,message.getBytes());
        channel.close();
        connection.close();
    }
}
```

消费者1：

```java
public class PubsubConsumer1 {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();

        final Channel channel = connection.createChannel();
        channel.queueDeclare("pubsub_queue1",true,false,false,null);

        // 绑定队列到交换机
        /**
         * String queue, String exchange, String routingKey
         *      参数一：消息队列名
         *      参数二：交换机名
         *      参数三：路由key
         */
        channel.queueBind("pubsub_queue1","myexchange","");

        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("pubsub_queue1队列中的消息："+new String(body));
                channel.basicAck(envelope.getDeliveryTag(),false);
            }
        };

        channel.basicConsume("pubsub_queue1",false,consumer);
    }
}
```

消费者2:

```java
public class PubsubConsumer2 {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();

        final Channel channel = connection.createChannel();
        channel.queueDeclare("pubsub_queue2",true,false,false,null);

        // 绑定队列到交换机
        /**
         * String queue, String exchange, String routingKey
         *      参数一：消息队列名
         *      参数二：交换机名
         *      参数三：路由key
         */
        channel.queueBind("pubsub_queue2","myexchange","");


        Consumer consumer = new DefaultConsumer(channel){

            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("pubsub_queue2队列中的消息："+new String(body));
                channel.basicAck(envelope.getDeliveryTag(),false);
            }
        };

        channel.basicConsume("pubsub_queue2",false,consumer);
    }
}
```

#### Routing 路由模式

通过交换机识别路由key发给指定队列，只有路由key匹配才能接收队列消息

**有选择性的接收消息**

我们希望不同的消息被不同的队列消费。这时就要用到Direct 类型的Exchange。

**路由模式特点：**

- 队列与交换机的绑定，不能是任意绑定了，而是要指定一个`RoutingKey`（路由key）
- 消息的发送方在向Exchange 发送消息时，也必须指定消息的`RoutingKey`。
- Exchange 不再把消息交给每一个绑定的队列，而是根据消息的`Routing Key`进行
判断，只有队列的`Routingkey`与消息的`Routing key`完全一致，才会接收到消息

![image-20210116090241621](typora-user-images\image-20210116090241621.png)

生产者代码：

```java
public class RoutingProducer {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //创建交换机
        /**
         * String exchange, String type
         *      参数一：交换机的名字
         *      参数二：交换机的类型，有三种类型，现在用广播类型
         */
        channel.exchangeDeclare("routing_exchange", "direct");

        channel.basicPublish("routing_exchange","info",null,"路由生产者发送的消息....".getBytes());

        channel.close();
        connection.close();
    }
}
```

消费者1代码：

```java
public class RoutingConsumer1 {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();

        channel.queueDeclare("routing_queue1",true,false,false,null);

        // 绑定队列到交换机
        /**
         * String queue, String exchange, String routingKey
         *      参数一：消息队列名
         *      参数二：交换机名
         *      参数三：bindingkey,绑定key
         */
        channel.queueBind("routing_queue1","routing_exchange","error");


        Consumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消息队列1获取的消息"+new String(body));
            }
        };
        channel.basicConsume("routing_queue1",true,consumer);
    }
}
```

消费者2代码：

```java
public class RoutingConsumer2 {
    public static void main(String[] args) throws Exception {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();

        channel.queueDeclare("routing_queue2",true,false,false,null);

        // 绑定队列到交换机
        /**
         * String queue, String exchange, String routingKey
         *      参数一：消息队列名
         *      参数二：交换机名
         *      参数三：bindingkey,绑定key
         */
        channel.queueBind("routing_queue2","routing_exchange","error");
        channel.queueBind("routing_queue2","routing_exchange","info");
        channel.queueBind("routing_queue2","routing_exchange","warning");


        Consumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消息队列2获取的消息"+new String(body));
            }
        };
        channel.basicConsume("routing_queue2",true,consumer);
    }
}
```

#### Topics 通配符模式

与上面的模式相同，但是他是以通配符进行匹配的

Topic 类型与Direct 相比，都是可以根据`RoutingKey`把消息路由到不同的队列。只不过`Topic`类型`Exchange`可以让队列在绑定`Routing key` 的时候使用通配符！

通配符规则：
`#`：匹配一个或多个词
`*`：匹配不多不少恰好1 个词
举例：
`item.#`：能够匹配`item.insert.abc` 或者`item.insert`
`item.*`：只能匹配`item.insert

![image-20210116090307295](typora-user-images\image-20210116090307295.png)

生产者代码：

```java
public class TopicsProducer {
    public static void main(String[] args) throws IOException, TimeoutException {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();

        /**
         * 创建交换机
         *    参数一：给交换机起个名
         *    参数二：指定他以那种形式
         *    参数三： true表示开启持久化
         */
        channel.exchangeDeclare("topics_exchange","topic",true);

        //MessageProperties.PERSISTENT_TEXT_PLAI  表示对消息进行持久化

        channel.basicPublish("topics_exchange","aaa.bank", MessageProperties.PERSISTENT_TEXT_PLAIN,"topics生产者发送的消息队列".getBytes());
        channel.close();
        connection.close();
    }
}
```

消费者1代码：

```java
public class TopicsConsumer1 {
    public static void main(String[] args) throws IOException, TimeoutException {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        channel.queueDeclare("topics_queue1",true,false,false,null);

        channel.queueBind("topics_queue1","topics_exchange","usa.#");


        Consumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者1从消息队列拿到的数据"+new String(body));
            }
        };
        channel.basicConsume("topics_queue1",true,consumer);
    }
}
```

消费者2代码：

```java
public class TopicsConsumer2 {
    public static void main(String[] args) throws IOException, TimeoutException {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        channel.queueDeclare("topics_queue2",true,false,false,null);

        channel.queueBind("topics_queue2","topics_exchange","#.bank");


        Consumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                System.out.println("消费者2从消息队列拿到的数据"+new String(body));
            }
        };
        channel.basicConsume("topics_queue2",true,consumer);
    }
}
```

### 持久化

#### 交换机持久化

在生产者创建交换机的时候

```java
/**
 * 创建交换机
 *    参数一：给交换机起个名
 *    参数二：指定他以那种形式
 *    参数三： true表示开启持久化
 */
channel.exchangeDeclare("topics_exchange","topic",true);
```

#### 队列持久化

在消费者创建消息队列时

```java
  channel.queueDeclare("firstRabbit", true, false, false, null);//第二个参数设置持久化
```

#### 消息持久化

在生产者发送消息到队列时

```java
//MessageProperties.PERSISTENT_TEXT_PLAI  表示对消息进行持久化
channel.basicPublish("topics_exchange","aaa.bank",MessageProperties.PERSISTENT_TEXT_PLAIN,"topics生产者发送的消息队列".getBytes());
```

### Rabbitmq整合springboot

生产者、消费者、交换机三者之间时没有关联的，完全可以分离（分开创建）



yml文件(都是一样的配置)：

```yml
spring:
  rabbitmq:
    host: 192.168.142.200
    port: 5672
    username: guest
    password: guest
    virtual-host: /ems  #指定虚拟主机的名字
```

方式一在生产者创建队列和交换机：

生产者代码：

```java
@Configuration
public class RabbitMQConfig {

    //创建交换机("bootExchange")bean的类名默认时方法名
    @Bean
    public Exchange bootExchange(){
        return ExchangeBuilder.topicExchange("springboot_topic_exchange").build();
    }

    //创建消息队列("bootQueue"),bean的类名默认时方法名
    @Bean
    public Queue bootQueue(){
        //durable方法设置持久化并指定队列的名字
        return QueueBuilder.durable("springboot_topic_queue").build();
    }

    //交换机绑定消息队列
    @Bean
    public Binding bindingQUeueAndExchange(@Qualifier("bootExchange") Exchange exchange,@Qualifier("bootQueue") Queue queue){
        //with指定路由key
        //noargs表示没有其他参数了  结束
       return BindingBuilder.bind(queue).to(exchange).with("item.#").noargs();
    }
}
-----------------------------在测试类中测试------------------------------------------------
    
    @SpringBootTest
class ProducerApplicationTests {

    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    @Test
    void contextLoads() {

rabbitTemplate.convertAndSend("springboot_topic_exchange","item.insert","springboot整合mq生产者发送的消息.......");

    }

}
```

消费者代码

> **rabbitMq监听的时候会自动创建队列**

```java
@Component
public class TopicsConsumer {

    //监听消息队列里面将消息队列名传过来
    @RabbitListener(queues = "springboot_topic_queue")
    public void ListenerQueue(String message){

        System.out.println("从消息队列中获取的信息是："+message);
    }
}
```

方式二在消费者创建交换机和消息队列：

生产者代码：

```java
只有一个测试类：
@SpringBootTest
class Producer02ApplicationTests {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    void contextLoads() {
        rabbitTemplate.convertAndSend("springboot_topic_exchange02","usa.jvm","交换机和队列在消费者创建");
    }

}
```

消费者代码：

```java
@Component
public class TopicsConsumer {

    /**
     * @RabbitListener：监听某个队列
     *          bindings:数组类型的数组，它里面配置交换机和消息队列
     *          @QueueBinding: 时bindings里面存储的类型，
     *              exchange表示创建交换机，
     *                  value ：交换机名称
     *                  ignoreDeclarationExceptions 表示忽略声明式异常，也可以不加
     *                  type 类型
     *              value表示创建队列
     *                  durable表示当前队列是否支持持久化
     *                  name：消息队列名称
     *                  exclusive:表示是否独占
     *                  autoDelete表示是否自动删除
     *              key: 表示指定队列绑定到路由器上的绑定key，他是数组类型的
     *
     *   如果创建多个的话，创建多个方法即可
     *
     *
     *
     *
     */
    @RabbitListener(bindings ={
            @QueueBinding(
                    exchange = @Exchange(
                            value = "springboot_topic_exchange02",
                            type = "topic",
                            ignoreDeclarationExceptions = "true"
                    ),
                    value = @Queue(
                            name = "springboot_topic_queue02",
                            durable = "true",
                            exclusive = "false",
                            autoDelete = "false"
                    ),
                    key = {"usa.#"}
            )

    } )
    public void consumer02(String message){
        System.out.println(message+"1");
    }


    @RabbitListener(bindings ={
            @QueueBinding(
                    exchange = @Exchange(
                            value = "springboot_topic_exchange02",
                            type = "topic",
                            ignoreDeclarationExceptions = "true"
                    ),
                    value = @Queue(
                            name = "springboot_topic_queue02",
                            durable = "true",
                            exclusive = "false",
                            autoDelete = "false"
                    ),
                    key = {"*.jvm"}
            )

    } )
    public void consumer(String message){
        System.out.println(message+"2");
    }
}
```

### 扩展(一个项目使用两个MQ)

配置文件

```properties
#第一个mq
spring.rabbitmq.first.host=47.98.234.186
spring.rabbitmq.first.port=5672
spring.rabbitmq.first.username=test
spring.rabbitmq.first.password=qwe123
spring.rabbitmq.first.virtualHost=/neptune.test
spring.rabbitmq.first.connection-timeout=20000

#第二个mq
spring.rabbitmq.second.host=121.196.209.183
spring.rabbitmq.second.port=5672
spring.rabbitmq.second.username=neptune
spring.rabbitmq.second.password=neptune123.jhk
spring.rabbitmq.second.virtualHost=/neptune
spring.rabbitmq.second.connection-timeout=20000
```

配置类

```java
@Configuration
public class FirstMqConfig {
    /**
     * 第一个链接工厂
     *
     * @param host
     * @param port
     * @param username
     * @param password
     * @param virtualHost
     * @param timeout
     * @return
     */
    //Primary可以理解为默认优先选择,同时不可以同时设置多个
    //使用了这个注解，消费者默认会用这个，当然也可以进行配置指定，详细配置百度
    @Primary
    @Bean("firstFactory")
    public ConnectionFactory firstMqConnectionFactory(
            @Value("${spring.rabbitmq.first.host}") String host,
            @Value("${spring.rabbitmq.first.port}") int port,
            @Value("${spring.rabbitmq.first.username}") String username,
            @Value("${spring.rabbitmq.first.password}") String password,
            @Value("${spring.rabbitmq.first.virtualHost}") String virtualHost,
            @Value("${spring.rabbitmq.first.connection-timeout}") int timeout) {

        CachingConnectionFactory factory = new CachingConnectionFactory();
        factory.setHost(host);
        factory.setPort(port);
        factory.setUsername(username);
        factory.setPassword(password);
        factory.setVirtualHost(virtualHost);
        factory.setConnectionTimeout(timeout);
        return factory;
    }

    @Bean(name="firstRabbitTemplate")
    public RabbitTemplate mqaRabbitTemplate(@Qualifier("firstFactory") ConnectionFactory connectionFactory){
        RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
        return rabbitTemplate;
    }

}
```

```java
@Configuration
public class SecondMqConfig {
    /**
     * 第二个连接工厂
     * @param host
     * @param port
     * @param username
     * @param password
     * @param virtualHost
     * @param timeout
     * @return
     */
    @Bean("secondFactory")
    //@Primary
    public ConnectionFactory secondMqConnectionFactory(
            @Value("${spring.rabbitmq.second.host}") String host,
            @Value("${spring.rabbitmq.second.port}") int port,
            @Value("${spring.rabbitmq.second.username}") String username,
            @Value("${spring.rabbitmq.second.password}") String password,
            @Value("${spring.rabbitmq.second.virtualHost}") String virtualHost,
            @Value("${spring.rabbitmq.second.connection-timeout}") int timeout) {

        CachingConnectionFactory factory = new CachingConnectionFactory();
        factory.setHost(host);
        factory.setPort(port);
        factory.setUsername(username);
        factory.setPassword(password);
        factory.setVirtualHost(virtualHost);
        factory.setConnectionTimeout(timeout);
        return factory;
    }

    @Bean(name = "secondRabbitTemplate")
    public RabbitTemplate secondRabbitTemplate(@Qualifier("secondFactory") ConnectionFactory connectionFactory) {
        RabbitTemplate secondRabbitTemplate = new RabbitTemplate(connectionFactory);
        return secondRabbitTemplate;
    }


}
```

测试发送消息

```java
@SpringBootTest
public class MqConfigTest {
    private static final String EXCHANGE1 = "direct.first.test.exchange";
    private static final String QUEUE_NAME1 = "direct.first.test.queue";
    private static final String ROUTING_KEY1 = "direct.first.test.key";

    protected static final String EXCHANGE2 = "direct.second.test.exchange";
    private static final String QUEUE_NAME2 = "direct.second.test.queue";
    private static final String ROUTING_KEY2 = "direct.second.test.key";

    private static final String EXCHANGE3 = "direct.producer.test.exchange";
    private static final String QUEUE_NAME3 = "direct.producer.test.queue";
    private static final String ROUTING_KEY3 = "direct.producer.test.key";

    @Autowired
    @Qualifier("firstRabbitTemplate")
    private RabbitTemplate firstRabbitTemplate;

    @Autowired
    @Qualifier("secondRabbitTemplate")
    private RabbitTemplate secondRabbitTemplate;

    //往第一个mq发消息
    @Test
    public void producerTest() {
        firstRabbitTemplate.convertAndSend(EXCHANGE1, ROUTING_KEY1, "first test2");
    }

    //往第二个mq发消息
    @Test
    public void producerTest2(){
        secondRabbitTemplate.convertAndSend(EXCHANGE2,ROUTING_KEY2,"second test2");
    }

    //两个不同mq 往相同名称的交换机队列发消息
    //会各自发各自的相互 互不影响
    @Test
    public void producerTest3(){
        firstRabbitTemplate.convertAndSend(EXCHANGE3,ROUTING_KEY3,"producer test");
        secondRabbitTemplate.convertAndSend(EXCHANGE3,ROUTING_KEY3,"producer test");
    }
}
```

测试消费消息

```java
@Component
//@Profile("prod")
public class ConsumerTest {
    private static final String EXCHANGE3 = "direct.producer.test.exchange";
    private static final String QUEUE_NAME3 = "direct.producer.test.queue";
    private static final String ROUTING_KEY3 = "direct.producer.test.key";

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(name = QUEUE_NAME3,durable = "true"),
            exchange = @Exchange(name = EXCHANGE3,type = ExchangeTypes.DIRECT,durable = "true"),
            key = ROUTING_KEY3))
    public void testConsumer(String msg){
        System.err.println(msg);
    }
}
```