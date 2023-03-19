# mybatis_day_03

#### 一对一(使用注解)

*  **@Results**表示使用注解的方式进行多表查询，它里面是以一种和数组相似的赋值方式，由{ @Result}里的内容构成的
  * **results**相当去xml中的resultMap标签
*  **@Result**里的内容
  * id为true表示主键，默认id为false
  *  column表示数据库字段名 
  * property表示JavaBean中的属性名（将column字段名付给的属性名是那个）
*  one = @One（里面是select=调用方法的全类名和开启懒加载）表示一对一懒加载使用
  *  @One中的select=要调用哪个方法再次进行查询的方法全类名（也就是从包开始到方法名）
  * fetchType = FetchType.LAZY表示开启懒加载

示例代码

```java
StudentMapper代码：
public interface StudentMapper {
    /**
     * 一对一全部加载
     */
    @Results({
            @Result(id = true,column = "sid",property = "id"),
            @Result(id = false,column = "sname",property = "name"),
            @Result(id = true,column = "tid",property = "teacher.id"),
            @Result(id = false,column = "tname",property = "name")
    })
    @Select(" select\n" +
            "          stu.id as sid,\n" +
            "            stu.name sname,\n" +
            "            tea.name tname,\n" +
            "            tea.id tid\n" +
            "        from t_student stu\n" +
            "        left join t_teacher tea\n" +
            "        on stu.teacher_id = tea.id\n" +
            "        where stu.id = #{id}")
    public Student getStuById(Integer id);
    
    /**
     * 一对一懒加载
     */
    @Results({
            @Result(id = true,column = "id",property = "id"),
            @Result(id = false,column = "name",property = "name"),
            @Result(column = "id",property = "teacher",
                    one = @One(select = "com.atguigu.mapper.TeacherMapper.getTeaById",
                            fetchType = FetchType.LAZY))
    })
    @Select("select `id`,name from t_student where id = #{id}")
    public Student getStuByIdLazyLoading(Integer id);
}

TeacherMapper代码：
public interface TeacherMapper {
    /**
     * 一对一
     */
    @Select("select `id`,name from t_teacher where id = #{id}")
    public Teacher getTeaById(Integer id);
}
```

#### 一对多(使用注解)

*  **@Results**表示使用注解的方式进行多表查询，它里面是以一种和数组相似的赋值方式，由{ @Result}里的内容构成的
  * **results**相当去xml中的resultMap标签
*  **@Result**里的内容
  * id为true表示主键，默认id为false
  *  column表示数据库字段名 
  * property表示JavaBean中的属性名（将column字段名付给的属性名是那个）
*  many= @Many（里面是select=调用方法的全类名、参数、返回集合的名称和开启懒加载）表示一对多懒加载使用
  *  @Many中的select=要调用哪个方法再次进行查询的方法全类名（也就是从包开始到方法名）
  * fetchType = FetchType.LAZY表示开启懒加载
  * property Javabean中返回集合的名称（也就是从select中查询出来的数据返回到那个集合黎明property 的属性就是给他指定集合名）
  * column：表示参数（就是执行select中的方法所需要的参数，因为可以从当前的@Select("select `id`,name from t_bus where id =#{id}")中查询出来所以直接传当前sql返回对象的id即可）

示例代码

```java
BusMapper中的代码：
public interface BusMapper {
    /**
     * 一对多懒加载
     */
    @Results({
            @Result(id = true,column = "id",property = "id"),
            @Result(column = "name",property = "name"),
            @Result(property = "passengers",column = "id",
                    many = @Many(select = "com.atguigu.mapper.PassengerMapper.getPassById",
                            fetchType = FetchType.LAZY))
    })
    @Select("select `id`,name from t_bus where id =#{id}")
    public Bus getBusById(Integer id);
}

PassengerMapper中的代码：
public interface PassengerMapper {
    /**
     * 一对多查询
     */
    @Select("select `id`,name from t_passenger where bus_id = #{bid}")
    public Passenger getPassById(Integer bid);
}
```

#### 一对多，多传参(使用注解)

```java
BusMapper中的代码：
	/**
     * 多值多传参
     */
    @Results({
            @Result(id = true,column = "id",property = "id"),
            @Result(column = "name",property = "name"),
 //{id=id,name=name},第一个数注解中的参数，第二个是要给select跳转的方法中出入的参数，从下方 @Select中获取（也就是跟下方select中一样就行）
            @Result(column = "{id=id,name=name}",property = "passengers",
            many = @Many(select = "com.atguigu.mapper.PassengerMapper.getPasArgsTwo",
                    fetchType = FetchType.LAZY))
    })
    @Select("select `id`,name from t_bus where id =#{id}")
    public Bus selectArgsTwo(Integer id);

PassengerMapper中的代码：
	/**
     * 多值多传参
     */
    @Select("select `id`,name from t_passenger where bus_id = #{id} or name like concat ('%',#{name},'%')")
    public List<Passenger> getPasArgsTwo(@Param("name")String name,@Param("id")Integer id);

Test测试代码：
      @Test
    public void testselectArgsTwo() {
        Bus bus = mapper.selectArgsTwo(2);
        System.out.println(bus.getName());
        System.err.println("===================");
        System.out.println(bus.getPassengers().get(0).getName());
    }
```

#### 动态SQL

* **if** 判断xml中的sql语句

* **where** 去掉开头多余的AND和OR 也就是第一个条件不成立会有多余的and或or它能够去掉他们

*   **trim** 可以动态在包含的语句前面和后面添加内容。也可以删除前面或者后面给定的内容

  ​        prefix 前面添加内容

  ​        suffix 后面添加内容

  ​        suffixOverrides 删除的后面内容

  ​        prefixOverrides 删除的前面内容

* **choose when otherwise** 可以执行多路选择判断，但是只会有一个分支会被执行。类似switch case 语句和JSTL中的choose when otherwise

* **set**语句具有sql中set的功能，还能去掉set字段前后的逗号

* **foreach**语句：能够批量查询和批量添加

  * collection：默认只能使用list和collection，除非使用注解才能使用自定义
  *  item当前迭代的对象，相当于for循环中的 i
  * open代表前面的（不懂看下面代码）
  * close代表后面的（不懂看下面代码）
  *  separator代表用什么字符分割一般都是逗号（","）

* **sql**：将通用的sql语句提取出来

  * id唯一标识
  * 使用\<include refid="上面呢id值">引入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.atguigu.mapper.UserMapper">
<!--    sql片段（就是将通用的sql语句提取出来）-->
    <sql id="selectUser">
         select `id`,last_name lastName,sex from users
    </sql>


 <!--
     * 根据用户lastName和sex来查询用户信息 <br/>
     *  1 性别的有效值是0和1 , 1表示男,0表示女.,如果性别值无效,就不加入到查询条件中 <br/>
     *  2 lastName的值如果是null,也不加入到查询条件中 <br/>
-->
<!--    where的练习  -->
  <!--  <select id="getUserByNameAndSex" resultType="User">
        select `id`,last_name lastName,sex from users
        <where>
        <if test="lastName != null">
            last_name like concat('%',#{lastName},'%')
        </if>
        <if test="sex == 0 || sex == 1">
            and sex = #{sex}
        </if>
        </where>
    </select>-->
<!--
        trim练习
-->
    <select id="getUserByNameAndSex" resultType="User">
        <include refid="selectUser"></include>
        /*select `id`,last_name lastName,sex from users*/
       <trim prefix="where" prefixOverrides="and" suffixOverrides="and">
            <if test="lastName != null">
                last_name like concat('%',#{lastName},'%') and
            </if>
            <if test="sex == 0 || sex == 1">
               sex = #{sex}
            </if>
       </trim>
    </select>

<!--    choose 练习-->

    <select id="getUserChoose" resultType="User">
        <include refid="selectUser"></include> where
        <choose>
            <when test="lastName != null">
                last_name like concat('%',#{lastName},'%')
            </when>
            <when test="sex == 0 || sex == 1">
                sex = #{sex}
            </when>
        </choose>
    </select>

<!--    set练习-->
    <update id="updateUser" parameterType="User">
        update users
        <set>
            <if test="lastName != null">
                last_name = #{lastName},
            </if>
            <if test="sex == 0 || sex == 1">
                sex = #{sex}
            </if>
        </set>
        where id = #{id}
    </update>

<!--    foreach练习 批量查询-->
    <select id="selectForeach" resultType="User">
        <include refid="selectUser"></include> where id

        <foreach collection="list"
                 item="uid"
                 open="in("
                 close=")"
                 separator=",">
             #{uid}
        </foreach>

    </select>
    
<!--   foreach 批量插入-->
    <insert id="insertUsers" parameterType="User">
        insert into users(last_name,sex) values
        <foreach collection="list" item="user" separator=",">
            (#{user.lastName},#{user.sex})
        </foreach>
    </insert>
    
</mapper>
```

#### 一级缓存

* 缓存失效的四种情况：
  * 不在同一个SqlSession对象中
  * 执行语句的参数不同。缓存中也不存在数据。
  * 执行增，删，改，语句，会清空掉缓存
  * 手动清空缓存数据

#### 二级缓存

![image-20201202200430417](typora-user-images\image-20201202200430417.png)

* 二级缓存执行流程

  1. 当我们执行一个查询语句的时候。mybatis会先去二级缓存中查询数据。
2. 二级缓存中没有。就到一级缓存中查找。
  3. 如果二级缓存和一级缓存都没有。就发sql语句到数据库中去查询。
4. 查询出来之后马上把数据保存到一级缓存中。
  5. 当SqlSession关闭或commit的时候，会把一级缓存中的数据保存到二级缓存中。

* 二级缓存的使用：

  1 到mybatis-config.xml配置文件中通过settings标签启动二级缓存

  2 到Mapper.xml配置文件中使用cache标签，表示使用二级缓存

  3 被缓存的数据对象。一定要实现序列化接口。

* 二级缓存，存储的是序列化，用的时候在反序列化

#### useCache="false"的演示和说明

* useCache属性是select标签中存在，作用是设置查询完之后是否将结果保存到二级缓存中。
  * useCahce值默认是true。表示使用二级缓存。

  ```xml
  //useCache="false" 查询结果不保存到二级缓存中
  <select id="queryUserById" resultType="com.atguigu.pojo.User" useCache="false">
          select `id`,`last_name` lastName,`sex` from t_user where id = #{id}
      </select>
  ```

  #### flushCache="false"的演示和说明

* flushCache属性存在于insert,delete,update标签中，作用是是执行insert,update,delete语句后是否清空缓存。

  默认值是true。表示清空缓存，也可以设置为false，让执行insert,delete,update语句，执行完后，不清空缓存。

```xml
// flushCache="false"不清空缓存 
<update id="updateUser" parameterType="com.atguigu.pojo.User" flushCache="false">
        update
            t_user
        set
            `last_name` = #{lastName},
            `sex` = #{sex}
        where
            id = #{id}
    </update>

```

#### mybatis 逆向工程
