# Spring_day01

IOC（控制反转）:就是不需要通过自己去new对象，Spring去帮你创建（有一个对象工厂）

> 注意：如果自己new了对象那么就不能使用Spring了

DI（依赖注入）：就是给创建的对象属性赋值

#### IOC练习

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/util https://www.springframework.org/schema/util/spring-util.xsd">

<!--
        bean标签表示配置一个Bean对象实例
        id：唯一标识
        class：pojo类的全路径
        property 表示给类的属性赋值  name表示属性名，value表示属性值
-->
        <bean id="person" class="com.atguigu.pojo.Person">
            <property name="name" value="张三"/>
            <property name="age" value="18"/>
            <property name="id" value="10001"/>
            <property name="phone" value="1234567"/>
        </bean>


<!--
            通过构造器给属性赋值
            constructor-arg表示构造器
            name属性表示属性
            value 表示属性值
-->
    <bean id="person1" class="com.atguigu.pojo.Person">
       <constructor-arg name="id" value="10002"/>
       <constructor-arg name="name" value="李四"/>
       <constructor-arg name="age" value="19"/>
        <constructor-arg name="phone" value="987654321"/>
    </bean>

<!--
        通过构造器给属性赋值的顺序
        index表示顺序，从0开始
         name属性表示属性
         value 表示属性值
         type表示属性的类型，可以省略
         用途:当你书写的属性顺序跟构造器的顺序不一样的时候你可以通过索引告诉构造器你传参的顺序
-->
    <!--<bean id="person2" class="com.atguigu.pojo.Person">
    <constructor-arg name="id" value="10003" index="0" type="java.lang.Integer"/>
    <constructor-arg name="name" value="王五" index="1" type="java.lang.String"/>
        <constructor-arg name="phone" value="123123123" index="2" type="java.lang.String"/>
        <constructor-arg name="age" value="20" index="3" type="java.lang.Integer"/>
    </bean>-->

<!--	通过类型和索引给构造器中的参数赋值
        public Person(Integer id, String name, String phone, Integer age)
        value 属性值
        index 表示参数的索引
        type 数据类型
-->
  <!--  <bean id="person2" class="com.atguigu.pojo.Person">
        <constructor-arg value="10003" index="0" type="java.lang.Integer"/>
        <constructor-arg value="王五" index="1" type="java.lang.String"/>
        <constructor-arg value="123123123" index="2" type="java.lang.String"/>
        <constructor-arg value="20" index="3" type="java.lang.Integer"/>
    </bean>-->

<!--
        使用时需要alt+enter导包（ xmlns:p="http://www.springframework.org/schema/p"）
 		p就相当于别名，代表对象的意思      
 		p名称空间的使用格式如下:
        p:属性名="值"
-->
    <bean id="person3" class="com.atguigu.pojo.Person"
          p:id="10004" p:name="芳芳" p:age="18" p:phone="18023457659"/>

<!--
            给属性赋空值
            null标签表示null值
			也可以直接value的值给成null
-->
    <bean id="person4" class="com.atguigu.pojo.Person">
        <property name="name">
           <null></null>
        </property>
        <property name="phone" value="12332145"/>
        <property name="id" value="10005"/>
        <property name="age" value="13"/>
    </bean>

<!--
        之子对象的赋值测试
        ref 表示引用，用在自定义的引用数据类型中
        ref="car" 相当于value的值是#{car}
        效果：person5 = Person{id=10006, name='荣荣', phone='null', age=null, car=Car{name='托垃圾', carNo='京B66666'}}
-->
    <bean id="person5" class="com.atguigu.pojo.Person">
        <property name="id" value="10006"/>
        <property name="name" value="荣荣"/>
        <property name="car" ref="car">

        </property>
    </bean>
    <bean id="car" class="com.atguigu.pojo.Car">
        <property name="name" value="托垃圾"/>
        <property name="carNo" value="京B66666"/>
    </bean>


<!--
        内部Bean的使用
        就是将bean当成一个参数赋值给另一个bean的属性（前提是这个bean中有当成参数bean的属性）
       结果： person6 = Person{id=10007, name='张丽芳', phone='null', age=null, car=Car{name='保时捷', carNo='沪C123456'}}
       后面的car.name的属性值会覆盖上面的car作为参数的属性值
       在spring中,如果要对属性使用级联属性赋值,需要先给car属性赋值
       注意：级联属性一定要先注入对象。再注入对象的属性
-->

    <bean id="person6" class="com.atguigu.pojo.Person">
        <property name="name" value="张丽芳"/>
        <property name="id" value="10007"/>
        <property name="car">
            <bean id="car2" class="com.atguigu.pojo.Car">
                <property name="name" value="夏利"/>
                <property name="carNo" value="沪C123456"/>
            </bean>
        </property>
        <property name="car.name" value="保时捷"/>
    </bean>

<!--
           List属性的赋值
           使用list或array标签嵌套value
           list或array标签表示整个集合的数据
           value表示 集合中每一个属性的值
-->
    <bean id="person7" class="com.atguigu.pojo.Person">
        <property name="name" value="银行"/>
        <property name="list">
<!--            <list>-->
<!--                <value>值1</value>-->
<!--                <value>值2</value>-->
<!--                <value>值3</value>-->
<!--            </list>-->
            <array>
                <value>值1</value>
                <value>值2</value>
                <value>值3</value>
            </array>
        </property>
    </bean>

<!--
        Map属性的赋值
        使用map或props标签嵌套entry或prop标签
        map或props标签表示整个集合的数据
        entry或props表示 集合中每一个属性的值
        entry赋值格式： <entry key="key值" value="value值"/>
        props赋值格式： <prop key="key值">value值</prop>
-->

    <bean id="person8" class="com.atguigu.pojo.Person">
        <property name="map">
<!--            <map>-->
<!--                <entry key="key1" value="value1"/>-->
<!--                <entry key="key2" value="value2"/>-->
<!--                <entry key="key3" value="value3"/>-->
<!--            </map>-->
            <props>
                <prop key="key1">value1</prop>
                <prop key="key2">value2</prop>
                <prop key="key3">value3</prop>
            </props>
        </property>
    </bean>

<!--    Properties属性的赋值
        和map的赋值方式相同
-->
    <bean id="person9" class="com.atguigu.pojo.Person">
        <property name="props">
           <!-- <map>
                <entry key="url" value="jdbc:mysql://localhost:3306/test"/>
                <entry key="driverClassName" value="com.mysql.jdbc.Driver"/>
                <entry key="username" value="root"/>
                <entry key="password" value="root"/>
            </map>-->
            <props>
                <prop key="url">jdbc:mysql://localhost:3306/test</prop>
                <prop key="driverClassName">com.mysql.jdbc.Driver</prop>
                <prop key="username">root</prop>
                <prop key="password">root</prop>
            </props>
        </property>
    </bean>

<!--
        util 名称空间
        Alt+enter导包xmlns:util="http://www.springframework.org/schema/util"
        util名称空间，可以定义全局公共的集合信息,方便容器直接获取,或者是给属性赋值使用（就是将通用的代码提取出来用的时候直接ref引用即可）
        使用<util:list>标签嵌套 <value>标签
        <util:list>标签表示整个集合的数据
       <value>表示 集合中每一个属性的值
       格式：<util:list>
            <value>值1</value>
            <value>值2</value>
            <value>值n</value>
            </util:list>
-->
    <util:list id="list01">
        <value>值1</value>
        <value>值2</value>
        <value>值3</value>
    </util:list>
    <bean id="person10" class="com.atguigu.pojo.Person">
        <property name="list" ref="list01"/>
    </bean>

<!--
            静态工厂方法创建Bean
            class表示工厂的全类名
            factory-method属性静态方法名
-->
    <bean id="person11" class="com.atguigu.pojo.PersonFactory" factory-method="getPerson">
    </bean>

<!--    创建实例方法
        工厂实例方法创建Bean对象,需要由 bean + factory-bean + factory-method组合实现
        首先先创建工程bean，然后在通过工程bean调用方法
-->
    <bean id="PersonFactory" class="com.atguigu.pojo.PersonFactory">
    </bean>
    <bean id="person12" factory-bean="PersonFactory" factory-method="getPerson2"/>

<!--    接口方式创建对象
        实现FactoryBean<?>接口重写他里面的方法
-->
<bean id="person13" class="com.atguigu.pojo.PersonFactoryBean"/>

<!--    继承Bean配置
         abstract="true"
            表示当前bean是抽象bean.
            抽象bean不能暴露出去

        parent表示你要继承那个bean就将那个bean的id当成值传给他
-->
    <bean id="parent" class="com.atguigu.pojo.Person" abstract="true">
        <property name="id" value="100"/>
        <property name="name" value="父配置"/>
        <property name="phone" value="110"/>
        <property name="age" value="100"/>
    </bean>
    <bean id="person14" class="com.atguigu.pojo.Person" parent="parent">
        <property name="id" value="18"/>
        <property name="phone" value="1234567890"/>
    </bean>

<!--    abstract抽象Bean
        abstract="true" 表示当前配置信息,只能用于继承,不能被实例化,默认为false
        parent:表示继承
-->
    <bean id="person15" class="com.atguigu.pojo.Person" abstract="true" parent="parent">
        <property name="id" value="18"/>
        <property name="phone" value="1234567890"/>
    </bean>
<!--   Bean对象创建顺序
       1 在Spring容器中.Bean对象的创建顺序默认是他们在配置文件中,从上到下的顺序决定.
       2 可以在bean的配置上,使用属性depends-on表示前置创建
       效果：C 被创建了
            B 被创建了
            A 被创建了
-->
    <bean id="a" class="com.atguigu.pojo.A" depends-on="c,b"/>
    <bean id="b" class="com.atguigu.pojo.B"/>
    <bean id="c" class="com.atguigu.pojo.C"/>

<!--    Bean的单例和多例
        scope:指定bean的范围是单例还是多例
            singleton:单例
                默认
                会跟着Spring容器一起被创建:立即加载
                多次调用getBean()方法都会返回一个对象

            prototype:多例
                不会跟着Spring容器一起被创建:延迟加载
                每次调用getBean()会重新创建一个新的

		springMVC学：
   			request:表示一次请求内多次调用getBean都返回同一个对象
            session:表示一个会话内多次调用getBean都返回同一个对象
            globalsession: 全局session

            测试结果：
                 Person person16 = context.getBean("person16", Person.class);
                 Person person16_1 = context.getBean("person16", Person.class);
                 System.out.println(person16==person16_1);//false多例
-->
    <bean id="person16" class="com.atguigu.pojo.Person" scope="prototype">
        <property name="id" value="20"/>
    </bean>

<!--    Bean的生命周期
        init-method:初始化方法
        destroy-method:容器销毁或bean销毁调用,ClassPathXmlApplicationContext 在close()方法调用
        scope="prototype":多例不会调用close(),原因是gc（gc就是垃圾回收）调用(也就是多例的调用了colse方法也不会执行销毁方法)
-->
    <bean id="person17" class="com.atguigu.pojo.Person"
          init-method="init" destroy-method="destroy" scope="prototype">
        <property name="id" value="20"/>
    </bean>

<!--    懒加载
        lazy-init值为true表示开启懒加载
        false:立即加载,容器启动时就立即进行实例化
		多例也是懒加载
-->
    <bean id="person18" class="com.atguigu.pojo.Person" lazy-init="true">
        <property name="id" value="26"/>
        <property name="name" value="小村"/>
    </bean>

</beans>
```

#### 基于xml配置文件的自动注入

```xml
<!--        自动注入

         <constructor-arg name="car" ref="car"/> 手动 注入
        autowire:自动去容器中找相同类型的bean 进行 自动 注入
            no||default:表示不会自动注入    相当于没有写autowire属性
            constructor 表示按照构造器方法参数到Spring容器中查找并赋值
            public Person(Car car)
                1 先按构造器参数的类型到Spring容器中查找
                2 找到一个就直接赋值
                3 如果找到多个，接着按参数名做为id在Spring容器中继续查找并将赋值
                4 如果找不到，就不赋值

            byType 表示按照属性的类型到Spring容器中去查找对象，并赋值
            private Car car;  把Car做为 类型 到Spring中找
            相当于 applicationContext.getBean(Car.class);
                1 找到一个就直接赋值
                2 找不到就不赋值（或值为null）
                3 找到多个就报错（报错原因是找不到那个是你要注入的）

            byName 表示按照属性的名做为id到Spring容器中去查找对象并赋值
            private Car car;  把car做为id到Spring中找
            相当于 applicationContext.getBean("car");
                1 找到就直接赋值
                2 找不到就不赋值（或值为null）
-->

<!--    <bean id="car2" class="com.atgui.pojo.Car">-->
<!--        <property name="type" value="布加迪"/>-->
<!--        <property name="price" value="10002"/>-->
<!--    </bean>-->
    <bean id="car" class="com.atgui.pojo.Car">
        <property name="type" value="睚眦"/>
        <property name="price" value="1000212"/>
    </bean>

    <bean id="bean2" class="com.atgui.pojo.Person" autowire="byName">
    </bean>
```

#### 几个问题

问题：

1. FileSystemXmlApplicationContext怎么用?

   答：跟使用JavaSE的相对路径一样,绝不推荐

   ApplicationContext applicationContext =
    new FileSystemXmlApplicationContext("src/applicationContext.xml");

2. Bean是在什么时候被创建的?

   答：在创建ApplicatiocnContext容器对象时一起创建Bean对象（默认）,项目启动扫描配置文件就会加载bean标签,创建bean组件(立即加载) lazy-init="true":对象是多例,每次getBean的时候创建一个新的bean(延迟加载)

3. 如果调用getBean多次，会创建几个?

   答：默认创建同一个,默认单例,scope="prototype",多例

   4.Spring中的bean是线程安全的吗？

​		答：单例安全，之创建一个对象，因为后面的如果要使用要等前面的用完，只有一个线程

​				多例不安全，会创建多个对象，多线程操作所以不安全