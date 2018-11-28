# Mapper代理开发dao

程序员只需要mapper接口（相当 于dao接口）

程序员还需要编写mapper.xml映射文件

程序员编写mapper接口需要遵循一些开发规范，mybatis可以自动生成mapper接口实现类代理对象。

## 开发规范

- 在mapper.xml中namespace等于mapper接口地址

```xml
<!--
 namespace 命名空间，作用就是对sql进行分类化管理,理解为sql隔离
 注意：使用mapper代理方法开发，namespace有特殊重要的作用,namespace等于mapper接口地址
 -->
<mapper namespace="com.iot.mybatis.mapper.UserMapper">
```

- mapper.java接口中的方法名和mapper.xml中statement的id一致
- mapper.java接口中的方法输入参数类型和mapper.xml中statement的parameterType指定的类型一致。
- mapper.java接口中的方法返回值类型和mapper.xml中statement的resultType指定的类型一致。

```xml
<select id="findUserById" parameterType="int" resultType="com.iot.mybatis.po.User">
    SELECT * FROM  user  WHERE id=#{value}
</select>
```

```java
//根据id查询用户信息
public User findUserById(int id) throws Exception;
```

总结：以上开发规范主要是对下边的代码进行统一生成：

```java
User user = sqlSession.selectOne("test.findUserById", id);
sqlSession.insert("test.insertUser", user);
```

### mapper接口

```java

public interface UserMapper {
    public User findUserById(int id);

    public User findUserByName(String name);

    public void insertUser(User user);

    public void updateUser(User user);

    public void deleteById(User user);

    //查询综合信息
    public List<UserCustom> findUserList(UserQueryVo userQueryVo);

    //查询综合信息总数
    public  int findUserCount(UserQueryVo userQueryVo);
}
```

### mapper.xml文件1111

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--
 namespace 命名空间，作用就是对sql进行分类化管理,理解为sql隔离
 注意：使用mapper代理方法开发，namespace有特殊重要的作用
 -->
<mapper namespace="com.iot.mybatis.mapper.UserMapper">
 <!-- 在映射文件中配置很多sql语句 -->
    <!--需求:通过id查询用户表的记录 -->
    <!-- 通过select执行数据库查询
     id:标识映射文件中的sql，称为statement的id
     将sql语句封装到mappedStatement对象中，所以将id称为statement的id
     parameterType:指定输入参数的类型
     #{}标示一个占位符,
     #{value}其中value表示接收输入参数的名称，如果输入参数是简单类型，那么#{}中的值可以任意。

     resultType：指定sql输出结果的映射的java对象类型，select指定resultType表示将单条记录映射成java对象
     -->
    <select id="findUserById" parameterType="int" resultType="com.iot.mybatis.po.User">
        SELECT * FROM  user  WHERE id=#{value}
    </select>

    <!-- 根据用户名称模糊查询用户信息，可能返回多条
	resultType：指定就是单条记录所映射的java对象类型
	${}:表示拼接sql串，将接收到参数的内容不加任何修饰拼接在sql中。
	使用${}拼接sql，引起 sql注入
	${value}：接收输入参数的内容，如果传入类型是简单类型，${}中只能使用value
	 -->
    <select id="findUserByName" parameterType="java.lang.String" resultType="com.iot.mybatis.po.User">
        SELECT * FROM user WHERE username LIKE '%${value}%'
    </select>


    <!-- 添加用户
        parameterType：指定输入 参数类型是pojo（包括 用户信息）
        #{}中指定pojo的属性名，接收到pojo对象的属性值，mybatis通过OGNL获取对象的属性值
        -->
    <insert id="insertUser" parameterType="com.iot.mybatis.po.User">
        <!--
         将插入数据的主键返回，返回到user对象中

         SELECT LAST_INSERT_ID()：得到刚insert进去记录的主键值，只适用与自增主键

         keyProperty：将查询到主键值设置到parameterType指定的对象的哪个属性
         order：SELECT LAST_INSERT_ID()执行顺序，相对于insert语句来说它的执行顺序
         resultType：指定SELECT LAST_INSERT_ID()的结果类型
          -->
        <selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer">
          SELECT LAST_INSERT_ID()
        </selectKey>
        INSERT INTO user (username,birthday,sex,address)values (#{username},#{birthday},#{sex},#{address})
        <!--
            使用mysql的uuid（）生成主键
            执行过程：
            首先通过uuid()得到主键，将主键设置到user对象的id属性中
            其次在insert执行时，从user对象中取出id属性值
             -->
        <!--  <selectKey keyProperty="id" order="BEFORE" resultType="java.lang.String">
            SELECT uuid()
        </selectKey>
        insert into user(id,username,birthday,sex,address) value(#{id},#{username},#{birthday},#{sex},#{address}) -->

    </insert>

    <!-- 删除 用户
        根据id删除用户，需要输入 id值
         -->
    <delete id="deleteUser" parameterType="java.lang.Integer">
        delete from user where id=#{id}
    </delete>

    <!-- 根据id更新用户
    分析：
    需要传入用户的id
    需要传入用户的更新信息
    parameterType指定user对象，包括 id和更新信息，注意：id必须存在
    #{id}：从输入 user对象中获取id属性值
     -->
    <update id="updateUser" parameterType="com.iot.mybatis.po.User">
        update user set username=#{username},birthday=#{birthday},sex=#{sex},address=#{address}
        where id=#{id}
    </update>
    
</mapper>
```

### 一些问题总结

- 代理对象内部调用`selectOne`或`selectList`
  - 如果mapper方法返回单个pojo对象（非集合对象），代理对象内部通过selectOne查询数据库。
  - 如果mapper方法返回集合对象，代理对象内部通过selectList查询数据库。

- mapper接口方法参数只能有一个是否影响系统开发

mapper接口方法参数只能有一个，系统是否不利于扩展维护?系统框架中，dao层的代码是被业务层公用的。即使mapper接口只有一个参数，可以使用包装类型的pojo满足不同的业务方法的需求。

注意：持久层方法的参数可以包装类型、map...等，service方法中建议不要使用包装类型（不利于业务层的可扩展）。

