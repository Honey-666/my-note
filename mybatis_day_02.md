# Mybatis_day02

#### 使用注解的方式进行CRUD

1. 不需要写接口xml文件，直接在接口中上面加一个@select（sql语句），其中select根据实际情况写是select、update、delete、insert
2. 后面的测试中与第一天一样
3. 其实就是省略了xml直接将sql语句写在了接口上方的注解中

示例：

```java
    /**
     * 注解查询全部
     */
    @Select("select * from users")
    public List<User> getAllUser();
/**
 * 查询单个使用注解
 */

    @Select("select * from users where id = #{id}")
    public User getUserById(Integer id);

/**
 * 修改使用注解
 */

    @Update("update users set last_name = #{lastName},sex = #{sex} where id = #{id}")
    public void updateById(User user);


/**
 * 删除使用注解
 */

    @Delete("delete from users where id = #{id}")
    public void deleteById(Integer id);


    /**
     * 获取主键
     * @param user
     */
    @SelectKey(statement = {"select last_insert_id()"},keyColumn = "id"
            ,keyProperty = "id",before = false,resultType = Integer.class)

    @Insert("insert into users(last_name,sex) values(#{lastName},#{sex})")
    public void insertUser(User user);
```

#### @selectKey注解

* 表示添加一条数据返回主键
* statement：sql函数
* keyProperty：pojo中的主键属性
* keyColumn：数据库中的主键名称
* before：之前之后执行，false为之后执行
* resultType:bean中的属性类型.class

#### 多个参数的传递

* xml的sql语句中当只有一个参数时可以任意类型
* 当具有多个参数时要用arg0、arg1或param1、param2，这时paramType参数类型就可以省略，因为不知道时那个类型
* 可以使用注解的方式使用属性名作为参数
  * 在接口的方法中参数使用@param注解功能

示例：

```java
   /**
     * 查询使用多个参数（注解）
     */
接口中的代码
    public User getUserByIdToParam(@Param("lastName") String lastName,@Param("sex") Integer sex);
xml中的代码
    <mapper namespace="com.atguigu.mapper.UserMapper">

    <select id="getUserByIdToParam" resultType="com.atguigu.pojo.User">
     select `id`,`last_name`,`sex` from users where last_name=#{lastName} and sex = #{sex}
	</select>
	</mapper>
Test测试中的代码
        @Test
    public void getUserByIdToParam(){
        User user = mapper.getUserByIdToParam("芳芳", 1);
        System.out.println("user = " + user);
    }
```

> 底层源码
>
> ​		当使用注解的时候map就存储的是注解的参数和param1、param2
>
> ​		当使用arg0，arg1时，底层源码ap中存储的时arg0，arg1和param1、param2



![image-20200819134246824](D:/尚硅谷/尚硅谷_200923班级_JavaSE上课物料包/01-课件/SSM/MyBaits/day02/笔记/mybatis02.assets/image-20200819134246824.png)

![image-20200819134348810](D:/尚硅谷/尚硅谷_200923班级_JavaSE上课物料包/01-课件/SSM/MyBaits/day02/笔记/mybatis02.assets/image-20200819134348810.png)![image-20200819134246824](D:/尚硅谷/尚硅谷_200923班级_JavaSE上课物料包/01-课件/SSM/MyBaits/day02/笔记/mybatis02.assets/image-20200819134246824.png)

![image-20200819134403497](D:/尚硅谷/尚硅谷_200923班级_JavaSE上课物料包/01-课件/SSM/MyBaits/day02/笔记/mybatis02.assets/image-20200819134403497.png)

* 当传入的参数时Map时，可以直接传入，因为底层就是map
  * 当我们的参数为map对象的时候。我们可以使用 map对象的key来做为占位符，输出数据。
  * **注意:map的key必须与#{}的名称一致**

* 传递自定义类型
  * 传递的参数（也就是#{}中的内容）必须与bean中的属性一致
* 传递多个自定义类型时
  * 方式一使用注解
  * 方式二arg0、arg1
  * 方式三param1、param2

#### 模糊查询

* 一般查询的都是多条，返回的基本都是集合
* %的书写位置
  * 方式一在测试类中添加参数的时候参数前后或前、后加%
    * **List\<User> users = mapper.findUserLikelastName("%" + "芳" + "%");**
  * 方式2在xml的sql语句中参数不使用#{}了使用  **'%${参数}%'** 
    *  **$ last_name like '%${value}%'  如果使用 $,值必须是value**
    *  如果只有一个参数并且是简单类型，`${value}`中只能是value，不能是其它名称
  * 方式三：sql函数    在xml的sql语句中使用**concat（'%',参数,'%'**）代替${}
    * **where last_name like concat('%',#{lastName},'%')**

#### #{}和${}解释

* #{}表示预编译sql，防止sql注入
  * #{}相当于prepared
* ${}是字符串拼接，但是不安全

#### 自定义结果集（resultMap）

**resultMap常用的三个功能**

* 解决数据库中的字段名与javabean中的属性名不对应问题
* 多表联查 left join
* 延迟加载

示例代码：

```xml
一对一查询：
	xml代码
<mapper namespace="com.atguigu.mapper.StudentMapper">

<!--
            id唯一标识和下面select的resultMap对应
            type返回的javabean中的类型的全类名
            第一行id标签是主键，后面每行都是result
            column表示数据库中的字段名
            property表示要映射的属性名
-->
<resultMap id="selectStudentAndTeacher" type="com.atguigu.pojo.Student">
    <id column="id" property="id"></id>
    <result column="name" property="name"></result>
    <result column="tname" property="teacher.name"></result>
</resultMap>

<select id="selectOneToOne" resultMap="selectStudentAndTeacher">
    select
          stu.id,
            stu.name,
            tea.name tname,
            tea.id tid
        from t_student stu
        left join t_teacher tea
        on stu.teacher_id = tea.id
        where stu.id = #{id}
</select>
</mapper>
接口代码：
	 /**
     * 一对一查询
     */
    public Student selectOneToOne(Integer id);
测试类代码：
	   @Test
    public void test1() {
        Student student = mapper.selectOneToOne(1);
        System.out.println("student = " + student);
    }
```

#### 延迟加载

```xml
<!-- 配置全局mybatis的配置 -->
<settings>
    <!-- 启用驼峰标识 -->
    <setting name="mapUnderscoreToCamelCase" value="true" />
    <!-- 打开延迟加载的开关 -->
    <setting name="lazyLoadingEnabled" value="true" />
    <!-- 将积极加载改为消息加载即按需加载 -->
    <setting name="aggressiveLazyLoading" value="false" />
</settings>
```

#### 一对一表查询代码示例

```xml
studentmapper中的xml代码：
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace是名称空间,它的取值必须是对应的接口的全类名 -->
<mapper namespace="com.atguigu.mapper.StudentMapper">
<!--    向上提取，将teacher提取成全局的

        id唯一标识
        type：提取出来的类型的全类名路径
-->
<resultMap id="terMap" type="com.atguigu.pojo.Teacher">
        <result column="id" property="id"></result>
        <result column="name" property="name"></result>
</resultMap>

<!--
            id唯一标识和下面select的resultMap对应
            type返回的javabean中的类型的全类名
            第一行id标签是主键，后面每行都是result
            column表示数据库中的字段名
            property表示要映射的属性名
-->
<resultMap id="selectOneToOneMap" type="com.atguigu.pojo.Student">
    <id column="id" property="id"></id>
    <result column="name" property="name"></result>
<!--    引入外部的resultMap
            association:一对一的关联使用
            resultMap:引用成员的映射
            property:必须使用,子对象的属性名也就是javabean中的Student 的属性名

的类型-->
    <association property="teacher" resultMap="terMap"/>
<!--    <result column="tid" property="teacher.id"></result>-->
<!--    <result column="tname" property="teacher.name"></result>-->
</resultMap>

<select id="selectOneToOne" resultMap="selectOneToOneMap">
    select
          stu.id,
            stu.name,
            tea.name tname,
            tea.id tid
        from t_student stu
        left join t_teacher tea
        on stu.teacher_id = tea.id
        where stu.id = #{id}
</select>
<!--    懒查询       懒查询          懒查询          懒查询           懒查询            懒查询         懒查询

            association:关联Teacher中的查询方法
            property ：表示javabean中的Student 的属性名，也就是她下面select的调用查询的方法返回回来的值
            select:将关联的方法全路径加上
            column:将关联的方法参数加上  public Teacher getTerById(Integer id);
            （解释也就是第一次通过查询学生能够获取到教师的id，
            在第二次查询教师的时候将这个查出来的id作为参数传递到查询教师的方法中）
-->
    <resultMap id="selectStuAndTeaTwoMap" type="com.atguigu.pojo.Student">
        <id column="id" property="id"></id>
        <result column="name" property="name"></result>
        <association property="teacher"
                     select="com.atguigu.mapper.TeacherMapper.getTerById"
                     column="teacher_id">
        </association>
    </resultMap>


    <select id="getStuById" resultMap="selectStuAndTeaTwoMap">
    select id,name,teacher_id from t_student where id = #{id}
</select>
</mapper>

student接口代码：
		 /**
     * 一对一查询
     */
    public Student selectOneToOne(Integer id);

    /**
     * 懒查询
     */

    public Student getStuById(Integer id);
teacher接口中的代码：
	 /**
     * 懒查询
     * @param id
     * @return
     */
    public Teacher getTerById(Integer id);
teachermapperxml中的代码：
	<mapper namespace="com.atguigu.mapper.TeacherMapper">

	<select id="getTerById" resultType="com.atguigu.pojo.Teacher">
    select `id`,name from t_teacher where id = #{id}
	</select>
	</mapper>
```

#### 一对多查询示例代码和双向查询

```xml
BUSxml代码：
<!--    普通一对多     普通一对多     普通一对多     普通一对多     普通一对多     普通一对多     普通一对多 -->
<!--
        List<Passenger> passengers;
        collection:用于一对多关联映射
        property:写多的一方的集合变量名称
        ofType:集合的泛型的类型 全类名路径
-->
<resultMap id="BusMap" type="com.atguigu.pojo.Bus">
    <id column="bid" property="id"></id>
    <result column="bname" property="name"></result>
    <collection property="passengers" ofType="com.atguigu.pojo.Passenger" >
        <result column="pid" property="id"></result>
        <result column="pname" property="name"></result>
    </collection>
</resultMap>

<select id="selectBusAndPassById" resultMap="BusMap">
        SELECT
            pass.id as pid,
            pass.NAME as pname,
            bus.NAME as bname,
            bus.id as bid
        FROM
            t_passenger pass
            LEFT JOIN t_bus bus ON pass.bus_id = bus.id
        where bus.id = #{id};
    </select>

<!--    懒查询    懒查询    懒查询    懒查询    懒查询    懒查询    懒查询    懒查询    懒查询    懒查询-->
<resultMap id="BusSelectPasByIdMap" type="com.atguigu.pojo.Bus">
    <id column="id" property="id"></id>
    <result column="name" property="name"></result>
    <!--
           一对多的关联延迟加载
           property:多的一方的集合变量名称
           select:多的一方的方法全路径
           column:传递过去的参数字段 （也就是通过公交车的id来查询乘客）

           ofType:集合泛型的类型 类的全路径
       -->
    <collection property="passengers" select="com.atguigu.mapper.PassengerMapper.selectById"
                column="id" ofType="com.atguigu.pojo.Passenger"/>

</resultMap>

    <select id="selectBusAndPasToId" resultMap="BusSelectPasByIdMap">
        select `id`,name from t_bus where id = #{id}
 <!--         双向关联         双向关联         双向关联         双向关联         双向关联         双向关联-->
    <select id="selectBusBiId" resultType="com.atguigu.pojo.Bus">
        select `id`,name from t_bus where id = #{id}
    </select>
        
        

Passengerxml代码： 
</select>
  <select id="selectById" resultType="com.atguigu.pojo.Passenger">
        select `id`,name,bus_id from t_passenger where bus_id = #{id}
    </select>


Passengerxml代码：
<!--    双向关联
        这里乘客对公交就是一对一了所以用association
       association里的属性
        column 表示bus的id
        property  表示javabean中的属性名
        select 表示调用bus的查询方法
-->
    
    <resultMap id="PasMap" type="com.atguigu.pojo.Passenger">
        <id column="id" property="id"></id>
        <result column="name" property="name"></result>
        <association property="bus" select="com.atguigu.mapper.BusMapper.selectBusBiId"
                     column="bus_id"/>
    </resultMap>
    <select id="selectPasById" resultMap="PasMap">
        select `id`,name,bus_id from t_passenger where id = #{id}
    </select>


BusMapper代码：
  /**
     * 一对多一次性查询
     * @param id
     * @return
     */
    public Bus selectBusAndPassById(Integer id);
    /**
     * 一对多 慢查询
     */
    public Bus selectBusAndPasToId(Integer id);
    /**
     * 双向关联
     */
    public Bus selectBusBiId(Integer bid);

PassengerMapper代码：
 /**
     * 一对多慢查询
     * @param id
     * @return
     */
    List<Passenger> selectById(Integer id);
    /**
     * 双向关联使用乘客能查询到公交
     */
    public Passenger selectPasById(Integer id);
```

#### 多值传参

```xml
BUSxml代码：
<!--    多值传参    多值传参    多值传参    多值传参    多值传参    多值传参    多值传参    多值传参    多值传参-->
    <!--
       (@Param("id") Integer bid, @Param("name") String name);
        column="{id=id,name=name}"
        当传递多个值的时候,column中的变量名称必须与被关联方法中的@Param中的值一致
        column{id=id,name=name},第一个id是注解后的名，第二个是bus的id也就是下方select语句中的id，name同id一样的解释
   -->
   <resultMap id="getBusMap" type="com.atguigu.pojo.Bus">
        <id column="id" property="id"></id>
       <result column="name" property="name"></result>
       <collection property="passengers" ofType="com.atguigu.pojo.Passenger"
                   select="com.atguigu.mapper.PassengerMapper.getPasByIdAndByName"
                   column="{id=id,name=name}"/>
   </resultMap>
    
    <select id="getBusById" resultMap="getBusMap">
         select `id`,name from t_bus where id = #{id}
    </select>


Passengerxml代码：
<!--    模糊查询多个参数    模糊查询多个参数    模糊查询多个参数    模糊查询多个参数    模糊查询多个参数-->
    <select id="getPasByIdAndByName" resultType="com.atguigu.pojo.Passenger">
        select `id`,name,bus_id from t_passenger where id = #{id} or name like concat('%',#{name},'%')
    </select>


BusMapper代码： 
	/**
     * 多值传参
     */
    public  Bus getBusById(Integer id);



PassengerMapper代码：

```

