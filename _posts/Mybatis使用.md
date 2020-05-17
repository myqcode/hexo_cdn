---
title: Mybatis使用
date: 2020-05-17 09:33:19
tags:
typora-root-url: Mybatis使用

---

## 1.Mybatis的介绍

**什么是Mybatis？**

1.Mybatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，用户只需要**关注SQL语句**，不需要花精力关心其他。

2.MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。					

## 2.Mybatis的HelloWord

**mybatis的官方文档：**

https://mybatis.org/mybatis-3/zh/getting-started.html



1.创建数据库

2.创建对应的javabean

3.创建该javabean的dao

**4.创建mybatis-config.xml文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <!--property value 写连接的信息-->
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
            <property name="username" value="root"/>
            <property name="password" value="13758284693"/>
        </dataSource>
    </environment>
</environments>

    <!--引入我们自己编写的每一个接口的实现文件-->
<mappers>
    <!--resource：表示从该类路径下寻找文件-->
    <mapper resource="UserDao.xml"/>
</mappers>
</configuration>
```

**UserDao.xml:**

**作用：**相当于创建了一个UserDao的一个实现类，实现里面的方法。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace：告诉mybatis这个配置文件是实现了哪个类的接口-->
<mapper namespace="cn.myq.dao.UserDao">
   <!--
     select:用来定义查询操作
     id：该类的哪个方法
     resultType：该方法的返回值类型(写全类名)
     select标签内部写上需要执行的语句
  -->
    <select id="getUser" resultType="cn.myq.pojo.User">
        select * from user_hello where id=#{id}
    </select>
</mapper>
```

**test；**

```java
String resource = "mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//从 SqlSessionFactory 中获取 SqlSession,执行dao定义的方法
SqlSession sqlSession = null;
try {
    //sqlSession，相当于获取一个链接
    sqlSession = sqlSessionFactory.openSession();
}finally {
    inputStream.close();
}
UserDao mapper = sqlSession.getMapper(UserDao.class);
User user = mapper.getUser(1);
System.out.println(user);
```

**详细信息查看mybatis官网**

------



## 3.xml配置

xml的配置是由顺序的进入**configuration**内查看即可

**MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：**

- configuration（配置）
  - [properties（属性）](https://mybatis.org/mybatis-3/zh/configuration.html#properties)
  - [settings（设置）](https://mybatis.org/mybatis-3/zh/configuration.html#settings)
  - [typeAliases（类型别名）](https://mybatis.org/mybatis-3/zh/configuration.html#typeAliases)
  - [typeHandlers（类型处理器）](https://mybatis.org/mybatis-3/zh/configuration.html#typeHandlers)
  - [objectFactory（对象工厂）](https://mybatis.org/mybatis-3/zh/configuration.html#objectFactory)
  - [plugins（插件）](https://mybatis.org/mybatis-3/zh/configuration.html#plugins)
  - environments（环境配置）
    - environment（环境变量）
      - transactionManager（事务管理器）
      - dataSource（数据源）
  - [databaseIdProvider（数据库厂商标识）](https://mybatis.org/mybatis-3/zh/configuration.html#databaseIdProvider)
  - [mappers（映射器）](https://mybatis.org/mybatis-3/zh/configuration.html#mappers)

### **属性1.properties**

**properties:**
      **作用： 这些属性可以在外部进行配置，并可以进行动态替换**
      一般**不推荐使用**可以直接用spring获取，**spring容器能作更多的事情**



**外部properties：**

```properties
driverClassName=com.mysql.jdbc.Driver
username=root
password=13758284693
url=jdbc:mysql://localhost:3306/mybatis
```

**mybatis-config.xml:**

```xml
<!--properties:
            resource：引入当前类下的配置文件
            这些属性可以在外部进行配置，并可以进行动态替换
            一般不推荐使用可以直接用spring获取，spring容器能作更多的事情
    -->
<properties resource="JDBC.properties"></properties>
<environments default="development">
    <environment id="development">
        <transactionManager type="JDBC"/>
        <!--property value 写连接的信息-->
        <dataSource type="POOLED">
            <property name="driver" value="${driverClassName}"/>
            <property name="url" value="${url}"/>
            <property name="username" value="${username}"/>
            <property name="password" value="${password}"/>
        </dataSource>
    </environment>
</environments>
```



### 设置（settings,重点）

**setting**

​	作用：这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为

​	以mapUnderscoreToCamelCase举例**详细参数看官方文档**

​	mapUnderscoreToCamelCase： 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。



**例如数据库字段名：a_book,对应javabean属性的abook，无需在sql语句写重命名只需要将mapUnderscoreToCamelCase属性设置为ture**

```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```



### mappers（映射器，重点）

**mappers**

​	**作用：引入我们自己编写的每一个接口的实现文件**

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

```xml
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```

```xml
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

注意：当时用**类名**时有两种方法

​	**其一：**将xml放在和dao接口同**目录下**，**而且文件名和接口名一致**

​	**其二：**直接在dao接口每个方法上面标注解@Select

```java
public interface UserDao {
    @Select("select * from user_hello where id=#{id}")
    User getUser(Integer id);
}
```

**这时候也不用写UserDao.xml了**

**直接引用该接口类名即可**

```xml
<mappers>
    <mapper class="cn.myq.dao.UserDao"></mapper>
</mappers>
```



***如果当有很多接口的配置文件时应该如何快捷的使用呢？***

![](/01.png)

**out目录时是idea整合文件**

**bin目录是eclipse整合文件夹**

![](/02.png)

**注意**：如下图将CartDao.xml和UserDao.xml移动到cn.myq.dao目录下时，配置文件中的resultType和namespace等一些其他的**属性值可能会自动发生改变**，引起错误，**需改回之前的配置**。

![](/04.png)

**其他几个属性查看官方文档**

------

## 4.XML 映射器

### insert, update 和 delete

获取自增长主键的值：可以使用**useGeneratedKeys**和**keyProperty**配合

**useGeneratedKeys**（仅适用于 insert 和 update）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系型数据库管理系统的自动递增字段），默认值：false。

**keyProperty**仅适用于 insert 和 update）指定能够唯一识别对象的属性，MyBatis 会使用 getGeneratedKeys 的返回值或 insert 语句的 selectKey 子元素设置它的值，默认值：未设置（`unset`）。如果生成列不止一个，可以用逗号分隔多个属性名称。

**通俗点讲**： <u>useGeneratedKeys="true"获取insert 和 update的id值</u>

<u>keyProperty将刚刚自增的id封装给哪个属性</u>

```xml
<insert id="aUser" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO `user_hello`(id,`name`,`age`,`email`,`u_book`) VALUES(#{id},#{name},#{age},#{email},#{ubook})
</insert>
```

------



### **sql参数注入**

当**注入**的**sql参数**只有**一个时**，可以使用**#{方法参数名(任意都是可以的)}来注入**，但是当传入的参数有多个值时，这个方法就不行了.

**会提示**：Parameter 'id' not found. Available parameters are [0, 1, param1, param2]

**解决方法一：**

​	**根据提示可以如下写法：**

```
<!--User getUser2(Integer id,String name);-->
<select id="getUser2" resultType="cn.myq.pojo.User">
    select * from user_hello where id = #{0} and name =#{1}
</select>
```

```xml
<!--User getUser2(Integer id,String name);-->
<select id="getUser2" resultType="cn.myq.pojo.User">
    select * from user_hello where id = #{param1} and name =#{param2}
    <!--按照map里的key拿到对应的值-->
</select>
```

**内部时创建了一个map将方法参数写入了该参数**

**该map是按照方法参数的顺寻一次封装**



**解决方法二：**也可以自己在方法参数类型写成map

```java
public User getUser3(LinkedHashMap<String,Object> map);
```

​	

```java
SqlSession sqlSession = sqlSessionFactory.openSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
LinkedHashMap<String,Object> map = new LinkedHashMap<String,Object>();
 //该添加的key就是，动态去除的#{设置的key}
map.put("id",1);
map.put("name","myq");
User myq = mapper.getUser3(map);
System.out.println(myq);
```

```xml
<select id="getUser3" resultType="cn.myq.pojo.User">
    select * from user_hello where id = #{id} and name =#{name}
</select>
```

解决方法三：**

可以在要执行的方法参数上**加上@Param("指定的参数名")**，这样就可以在配置文件中使用**#{设置的参数名}查找**

```java
public User getUser2(@Param("id") Integer id,@Param("name")String name);
```

```xml
<!--可以将上面设置的对应动态值用#{}获取到-->
<!--User getUser2(Integer id,String name);-->
<select id="getUser2" resultType="cn.myq.pojo.User">
    select * from user_hello where id = #{id} and name =#{name}
</select>
```



扩展

------



### mybatis中的两种取值方式（重点）：

1.#{属性名}：是参数预编译的方式，参数的位置都是用？代替，参数后来都是预编译设置进去的，安全，不会有sql注入问题

2.${属性名}：不是参数预编译，而是直接和sql语句进行拼串

```
一般都是使用#{}，安全，在支持参数预编译的位置进行取值就是用${};
${}可以用在不需要预编译的时候用比如，动态选择库就可以使用${}
```

 

------



### 将查询出的数据封装成list集合

**接口方法**

```java
public List<User> getUser4(Integer id);
```

**实现接口的xml**

```xml
<!--public List<User> getUser4(Integer id);-->
<!--resultType:就是list里存储的对象-->
<select id="getUser4" resultType="cn.myq.pojo.User">
    select * from user_hello where id = #{id}
</select>
```



### 将查询出单条记录封装成map集合

**接口方法**

```java
public Map<Integer,User> getUser5(Integer id);
```

**实现接口的xml**

```xml
<!--resultType="map" 是因为mybatis已经将Java 类型内建的类型别名-->
<!--public Map<Integer,User> getUser5(Integer id);-->
<select id="getUser5" resultType="map">
    select * from user_hello where id = #{id}
</select>
```



### 将查询出多条条记录封装成map集合 

**接口方法**

```java
//@MapKey("id"):将查询出来的哪个属性值设置为map的key
@MapKey("id")
public Map<Integer,User> getUser6();
```

**实现接口的xml**

```xml
<!--    public Map<Integer,User> getUser6();-->
<!--切记resultType类型是map值的类型-->
<select id="getUser6" resultType="cn.myq.pojo.User">
    select * from user_hello
</select>
```

------



### resultMap自定义封装规则

**作用：当数据库的字段和javabean的定义不同可以使用resultMap进行更改**(当然也可以使用**起别名**)，以下情况，**javabean和数据库字段的映射关系**。

****![](/03.png)

```java
Cart getCart(Integer id);
```

**实现接口的xml**

```xml
<!--
    作用：javabean和数据库字段的映射关系
    id:唯一标识符
    type：为哪个javabean封装对象
-->
<resultMap id="myCart" type="cn.myq.pojo.Cart">
    <!--
         id:为主键的属性
         property:指定为cart的哪个属性封装id
         column:指定哪一列为主键列-->
    <id property="id" column="id"></id>
    <!--为普通列指定-->
    <result property="name" column="c_name"></result>
    <result property="age" column="c_age"></result>
    <result property="gender" column="c_gender"></result>
</resultMap>
```