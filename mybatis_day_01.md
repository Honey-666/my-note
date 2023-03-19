# 	Mybatis_day01

**框架的理解:是一个可重用组件，软件，jar包。里面封装以前的一些规范**

**使用框架的目的：使用xml或注解可以完成给某些功能，将重复的代码进行了封装，效率高，而且对整个功能进行了优化**

### mybatis简介

1. mybatis是支持定制化sql、存储过程以及高级映射的优秀持久层框架
2. mybatis是一个半自动化的持久化层orm框架
3. ORM：o代表对象，r代表关系，m代表映射
4. 将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录.

### mybatis和hibernate的使用场景

* **级联查询和复杂查询用mybatis，其他的用hibernate**

### 在源码目录下创建mybatis的核心配置文件 mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--environments 表示配置数据库环境
		default:框架运行时加载的唯一环境
-->
    <environments default="development">
        <environment id="development">
            //表示事务
            <transactionManager type="JDBC"></transactionManager>
            <!-- 数据库驱动 
				dataSource:数据源,连接池
                POOLED:就表示使用mybatis内置的连接池
                UNPOOLED:不适用连接池,每次创建一个Connection
-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    <!--mybatis 是把sql配置到xml配置文件中,下面的配置是告诉Mybatis到哪里加载sql的配置文件 -->
    <mappers>
        <mapper resource="com/atguigu/mapper/UserMapper.xml"/>
    </mappers>
    
</configuration>
```

### XML中的配置和各个标签的用途

#### properties标签

* 可以从外部properties文件中引入配置

示例代码：

```xml
// resource属性设置读取指定的xxx.properties键值对信息
    <properties resource="jdbc.properties">  
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
        <property name="password" value="root"/>
        <property name="user" value="wzg168"/>
    </properties>
resource指定的键值对，会覆盖 property子标签的键值对（也就是如果外部properties文件中已配置用户名和密码那么property标签中的值就会被覆盖）
----------------------------------------------------------------------------
//获取properties文件中的配置信息${中都是properties里面的key值}
 <properties resource="jdbc.properties"></properties>
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

#### 事务管理器（transactionManager）

```xml
<!--       transactionManager     事务标签
  type的两个属性
			MANAGED:不用事务
            JDBC:使用数据源默认的事务
-->
            <transactionManager type="JDBC"></transactionManager>
```

#### dataSource标签

```xml
<!--     POOLED使用数据库连接池
         UNPOOLED不使用数据库连接池
         dataSource  表示数据源
       -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
```

#### 核心配置settings

* 改变 MyBatis 的运行时行为

| 设置参数                         | 描述                                                         | 有效值                                                       | 默认值                                                       |
| -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| cacheEnabled                     | 该配置影响的所有映射器中配置的缓存的全局开关。               | true \| false                                                | true                                                         |
| lazyLoadingEnabled               | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置fetchType属性来覆盖该项的开关状态。 | true \| false                                                | false                                                        |
| aggressiveLazyLoading            | 当启用时，对任意延迟属性的调用会使带有延迟加载属性的对象完整加载；反之，每种属性将会按需加载。 | true \| false                                                | true                                                         |
| multipleResultSetsEnabled        | 是否允许单一语句返回多结果集（需要兼容驱动）。               | true \| false                                                | true                                                         |
| useColumnLabel                   | 使用列标签代替列名。不同的驱动在这方面会有不同的表现， 具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。 | true \| false                                                | true                                                         |
| useGeneratedKeys                 | 允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby）。 | true \| false                                                | False                                                        |
| autoMappingBehavior              | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。 | NONE, PARTIAL, FULL                                          | PARTIAL                                                      |
| autoMappingUnknownColumnBehavior | Specify the behavior when detects an unknown column (or unknown property type) of automatic mapping target.· NONE: Do nothing· WARNING: Output warning log (The log level of'org.apache.ibatis.session.AutoMappingUnknownColumnBehavior'must be set to WARN)· FAILING: Fail mapping (Throw SqlSessionException) | NONE, WARNING, FAILING                                       | NONE                                                         |
| defaultExecutorType              | 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。 | SIMPLE REUSE BATCH                                           | SIMPLE                                                       |
| defaultStatementTimeout          | 设置超时时间，它决定驱动等待数据库响应的秒数。               | Any positive integer                                         | Not Set (null)                                               |
| defaultFetchSize                 | Sets the driver a hint as to control fetching size for return results. This parameter value can be override by a query setting. | Any positive integer                                         | Not Set (null)                                               |
| safeRowBoundsEnabled             | 允许在嵌套语句中使用分页（RowBounds）。 If allow, set the false. | true \| false                                                | False                                                        |
| safeResultHandlerEnabled         | 允许在嵌套语句中使用分页（ResultHandler）。 If allow, set the false. | true \| false                                                | True                                                         |
| mapUnderscoreToCamelCase         | 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。 | true \| false                                                | False                                                        |
| localCacheScope                  | MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速重复嵌套查询。 默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession 的不同调用将不会共享数据。 | SESSION \| STATEMENT                                         | SESSION                                                      |
| jdbcTypeForNull                  | 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 | JdbcType enumeration. Most common are: NULL, VARCHAR and OTHER | OTHER                                                        |
| lazyLoadTriggerMethods           | 指定哪个对象的方法触发一次延迟加载。                         | A method name list separated by commas                       | equals,clone,hashCode,toString                               |
| defaultScriptingLanguage         | 指定动态 SQL 生成的默认语言。                                | A type alias or fully qualified class name.                  | org.apache.ibatis.scripting.xmltags.XMLDynamicLanguageDriver |
| callSettersOnNulls               | 指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法，这对于有 Map.keySet() 依赖或 null 值初始化的时候是有用的。注意基本类型（int、boolean等）是不能设置成 null 的。 | true \| false                                                | false                                                        |
| logPrefix                        | 指定 MyBatis 增加到日志名称的前缀。                          | Any String                                                   | Not set                                                      |
| logImpl                          | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | Not set                                                      |
| proxyFactory                     | 指定 Mybatis 创建具有延迟加载能力的对象所用到的代理工具。    | CGLIB \| JAVASSIST                                           | JAVASSIST (MyBatis 3.3 or above)                             |
| vfsImpl                          | Specifies VFS implementations                                | Fully qualified class names of custom VFS implementation separated by commas. | Not set                                                      |
| useActualParamName               | Allow referencing statement parameters by their actual names declared in the method signature. To use this feature, your project must be compiled in Java 8 with -parameters option. (Since: 3.4.1) | true \| false                                                | true                                                         |

**mapUnderscoreToCamelCase开启驼峰命名**

```xml
 <settings>
        <!--
            开启驼峰命名
            mapUnderscoreToCamelCase : 相当于将db last_name 改成 java : lastName
        -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
```

#### typeAliases标签（起别名）

> **注意：当一个包下的两个子包中具有同名的类文件时需要给一个类名上面加@Alias("自定义别名")**

```xml
 <typeAliases>
     //方式一
        <!--
            type:类型  alias:别名,使用时忽略大小写
        -->
        <!--<typeAlias type="com.atguigu.pojo.User" alias="user"/>-->
     //方式二
        <!--
            直接把包放入,会默认给包下的类起别名,别名是类名首字母小写
        -->
        <package name="com.atguigu"/>
    </typeAliases>
```

#### databaseIdProvider标签（数据库厂商标识，也就是用的那个数据库oracle、mysql。。。。）

> **//通过value找到相应的name，知道用的是哪个厂商的数据库**

```xml
 <!--数据库厂商标识-->
    <databaseIdProvider type="DB_VENDOR">
        <property name="SQL Server" value="sqlserver"/>
        <property name="DB2" value="db2"/>
        <property name="Oracle" value="oracle" />
        <property name="MySQL" value="mysql" />
    </databaseIdProvider>
```

#### mappers（映射，根据映射找到响应接口的xml）

```xml
  <mappers>
      //方式一
        <!--resource:找xml-->
        <!--<mapper resource="com/atguigu/mapper/UserMapper.xml"/>-->
      //方式二
        <!--使用文件协议加载配置文件-->
        <!--<mapper url="file:///C:/workspace/0923/MyBatis/MyBatis-			   Hello/src/com/atguigu/mapper/UserMapper.xml"/>-->
      //方式三
        <!--加载 mapper interface-->
        <!--<mapper class="com.atguigu.mapper.UserMapper"/>-->
      //方式四（推荐）
        <!--
            package:会加载当前包下所有的xml和interface
        -->
        <package name="com.atguigu.mapper"/>
    </mappers>
```

### Mapper.xml中配置说明

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace是名称空间,它的取值必须是对应的接口的全类名 -->
<mapper namespace="com.atgui.mapper.UserMapper">
    <!--
        select 标签用来配置select查询语句
        id 属性配置一个唯一的标识
        resultType 是查询后每一行记录封装的对象类型
        #{id} 它是占位符相当于 ?
    -->
    <select id="getAllUsers" resultType="com.atgui.pojo.User">
     select `id`,`last_name` lastName,`sex` from users
</select>

<!--       id属性是一个唯一的标识，必须是方法名
            parameterType属性设置方法的参数类型（可选的，参数类型为JavaBean的时候推荐写,也就是基本数据类型可以不写，基本数据类型可以直接写类型，因为官方已经给他起好了别名，JavaBean对象的化要写全路径或给他起别名）
            resultType属性（只有查询才有）设置查询回来的每一行记录转换为什么具体的类型对象
			databaseId:指定数据库厂商
           		 MyBatis 会加载带有匹配当前数据库 databaseId 属性和所有不带 databaseId 属性的语句。
           		 如果同时找到带有 databaseId 和不带 databaseId 的相同语句，则后者会被舍弃,如果都没找到那么就使用没有databaseId属性的
如
<select id="selectUserAll" resultType="USER">
        select `id`,`last_name` lastName,sex from t_user where 1=1
    </select>
-->
    
    
 <select id="getUserById" resultType="com.atgui.pojo.User" databaseId="oracle">
     select `id`,`last_name` lastName,`sex` from users where id = #{id}
</select>

<!--    <insert id="insertUser" parameterType="com.atgui.pojo.User">-->
<!--        insert into users(last_name,sex) values(#{lastName},#{sex})-->
<!--    </insert>-->


    <!--
     selectKey标签可以配置一个用于查询主键的sql
         order=""属性设置 当前selectKey的执行顺序
             AFTER       后执行
             BEFORE      先执行
         keyProperty="id" 表示将生成的主键值赋给id属性
  -->

<!--=======================================================================================-->

    <!--    Mysql中查询主键的方式-->

    <!--    返回主键自增
    useGeneratedKeys="true" 表示返回主键自增
     keyProperty="id" 表示将生成的主键值赋给id属性
     keyColumn="id"  表示字段名称
    -->

<!--    <insert id="insertUser" parameterType="com.atgui.pojo.User"-->
<!--    useGeneratedKeys="true" keyColumn="id" keyProperty="id"-->
<!--    >-->
<!--        insert into users(last_name,sex) values(#{lastName},#{sex})-->

<!--    </insert>-->



<!--    selectKey标签的方式   查询主键的方式-->
<!--
          order的两个属性值
                       BEFORE  selectKey先执行
                       AFTER   selectKey后执行
            last_insert_id() 是一个函数.它会返回最后一次生成的主键值
             keyProperty="id" 表示将生成的主键值赋给id属性
             resultType 设置返回的主键的类型 Integer
 -->
    <insert id="insertUser" parameterType="com.atgui.pojo.User">
    <selectKey order="AFTER" resultType="Integer" keyProperty="id">
        select last_insert_id()
    </selectKey>

        insert into users(last_name,sex) values(#{lastName},#{sex})

    </insert>

    <update id="updateUser" parameterType="com.atgui.pojo.User">
        update users set last_name = #{lastName},sex = #{sex} where id = #{id}
    </update>

    <delete id="delUser">
        delete from users where id = #{id}
    </delete>

</mapper>
```

#### 注解@MapKey的使用。

* @MapKey可以将查询回来的JavaBean以注解给定的属性做为key,封装为一个Map对象返回. 

示例：

```java
接口中的代码
	 @MapKey("id")
    public Map<Integer,User> getAllUsersReturnMap();
XML中的代码
     <select id="getAllUsersReturnMap" resultType="com.atgui.pojo.User">
     select `id`,`last_name` lastName,`sex` from users
</select>
    
测试类中的代码
    @Test
public void returnMap(){
    Map<Integer, User> map = mapper.getAllUsersReturnMap();
    map.forEach((k,v)->{
        System.err.println(k+"="+v);
    });
}

    这里就是将id作为key，返回回来的值作为value
结果：
1=User{id=1, lastName='admin', sex=0}
3=User{id=3, lastName='fangfang', sex=0}
4=User{id=4, lastName='芳芳', sex=1}
5=User{id=5, lastName='张三', sex=0}
6=User{id=6, lastName='李四', sex=0}
```

### 两个注解

* **Before可以理解为事先，After理解为事后，而@Test标注的测试方法就是这件事情**

```java
	InputStream inputStream = null;
    SqlSessionFactory build = null;
    SqlSession sqlSession = null;
    UserMapper mapper = null;


在test方法前加载
@Before
public void init(){//放通用的代码
 		//1:连接数据库,加载配置文件
        inputStream = Resources.getResourceAsStream("mybatis-config.xml");
        //2:构建连接
        build = new SqlSessionFactoryBuilder().build(inputStream);
        //3:得到sqlSession,负责执行sql
        //true表示自动提交事务
        sqlSession = build.openSession(true);
        //4:加载SQL
        mapper = sqlSession.getMapper(UserMapper.class);

}

在test方法后加载
 @After
    public void destroy(){
        //关闭连接
        sqlSession.close();
    }
```

### 两种返回主键的方式

```xml
方式一：
<!--    selectKey标签的方式   查询主键的方式-->
<!--
          order的两个属性值
                       BEFORE  selectKey先执行
                       AFTER   selectKey后执行
            last_insert_id() 是一个函数.它会返回最后一次生成的主键值
             keyProperty="id" 表示将生成的主键值赋给id属性
             resultType 设置返回的主键的类型 Integer
 -->
    <insert id="insertUser" parameterType="com.atgui.pojo.User">
    <selectKey order="AFTER" resultType="Integer" keyProperty="id">
        select last_insert_id()
    </selectKey>
    insert into users(last_name,sex) values(#{lastName},#{sex})
    </insert>
        
方式二:
           <!--    返回主键自增
    useGeneratedKeys="true" 表示返回主键自增
     keyProperty="id" 表示将生成的主键值赋给id属性
     keyColumn="id"  表示字段名称
    -->

   <insert id="insertUser" parameterType="com.atgui.pojo.User"
    useGeneratedKeys="true" keyColumn="id" keyProperty="id">
   insert into users(last_name,sex) values(#{lastName},#{sex})
</insert>
```

> **BeanFactory:SpringIoc最顶层的接口**
>
> **FactoryBean:用于Spring整合第三方类库暴露出去的接口**
