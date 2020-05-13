---
title: IOC容器
date: 2020-04-24 10:06:02
tags:
typora-root-url: spring初体验
---



## **IOC容器**

### **ioc介绍：**

IOC容器：Ioc—Inversion of Control，即“控制反转”，就是具有依赖注入功能的容器，是可以创建对象的容器，IOC容器负责实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。

IOC是对对象进行控制反转，也成为依赖注入。依赖控制反转(把对象的控制权从业务的业务对象手中转交给平台或者框架)的模式有很多种，在spring中IoC容器是实现这个模式的载体。

依赖注入的方式：接口注入；setter注入；构造器注入。IoC设计中。setter和构造器注入是主要的注入方式。

​																				-------来自[百度百科]()

### Spring的helloword

**首先配置ioc容器**

```xml
<!--注册一个user对象，spring会自动创建这个Person对象-->
<!--一个bean标签可以注册一个组件（对象，类）
    class是需要注册的全类名 id：表示唯一表示可以用来获取该对象

-->
<bean id="user" class="cn.myq.pojo.User">
    <!--使用property可以为User的属性赋值-->
    <property name="username" value="myq"></property>
    <property name="gender" value="男"></property>
    <property name="age" value="15"></property>
</bean>

<bean id="user02" class="cn.myq.pojo.User">
    <property name="username" value="lihai"></property>
</bean>
```



**测试：**

**在User空参构造器中输出了一句话用于测试ioc什么时候创建该对象**

```
public User() {
    System.out.println("创建了user对象");
}
```

```java
    public void test01(){
        //从容器中拿到这个组件
        //ApplicationContext代表ioc容器
        ApplicationContext aContext = new ClassPathXmlApplicationContext("ioc.xml");
        System.out.println("ioc容器初始化完成");
        //根据ID拿到组测的对象
        User user = (User) aContext.getBean("user");
        //User user02 = (User) aContext.getBean("user02");
        System.out.println(user);
        //System.out.println(user02);
    }
```

**文件目录如下：**

<img src="/01.png" style="zoom: 67%;" />



**结果：**

User{username='myq', age=15, gender='男'}
User{username='lihai', age=0, gender='null'}



**关于helloword的一些注意事项**

- bin目录就是类路径的开始

- src下的源码包都会存放在bin目录下，所有源码包里的文件都会被合并到bin目录中。

- java：/bin/                           javaweb:/web-inf/classes/



**几个细节：**

- 给容器中注册一个组件：我们也冲容器中按照id拿到了这个组件的对象？

  ​	组件的创建工作，是容器完成：

  ​	person对象是什么时候创建好了呢？

  ​	**容器中对象的创建在容器创建完成的之前就已经创建好了**

- **同一个组件在ioc容器中是单实例的，容器启动完成都已经创建准备好的**

- 容器中如果没有这个组件，获取该容器会报异常。

- javabean的属性是有什么决定的？ 答是由getter/setter方法决定的

------



### 根据bean的类型从IOC容器中获取bean的实例

**如果使用如下方法创建该类的实例，ioc容器中只允许有一个该类**

```java
@Test
public void test02(){
    User bean = aContext.getBean(User.class);
    System.out.println(bean);
}
```

**使用该方法可以解决上述的缺点**

```java
@Test
public void test02(){
    User bean = aContext.getBean("user02",User.class);
    System.out.println(bean);
}
```

**使用类型创建bean实例的作用就是，省去了强制类型转换。**

------



### 通过构造器为bean的属性赋值

```java
        <!--使用构造器赋值一定要将参数顺序写对
                内置也有index可以改变参数的顺序
        -->
        <bean id="user02" class="cn.myq.pojo.User">
                <constructor-arg name="username" value="myq" ></constructor-arg>
                <constructor-arg name="age" value="20"></constructor-arg>
                <constructor-arg name="gender" value="男"></constructor-arg>
        </bean>
```

------



### 通过p名称空间为bean赋值

**作用：在xml中名称空间是用来防止标签重复的**

```java
<bean id="user04" class="cn.myq.pojo.User" 			p:username="小鱼人" p:age="10">

</bean>
```

------

### 正确的为各种属性赋值

**User类：**

```java
public class User {
    private String username;
    private int age;
    private String gender;
    private Cart cart;
    private List<Book> list;
    private Map<String,Object> map;
```

**Cart类**

```java
public class Cart {
    private String name;
    private String color;
    private Integer price;
```

**Book类**

```java
public class Book {
    private String name;
    private Integer price;
```



**首先为：private Cart cart 中的cart赋值**

**方式一：引用**

```xml
<bean id="cart01" class="cn.myq.pojo.Cart">
    <property name="name" value="宝马"></property>
    <property name="color" value="红色"></property>
    <property name="price" value="10000000"></property>
</bean>

<bean id="user01" class="cn.myq.pojo.User">
      <!--标签property：为该类的属性赋值-->
      <!--ref：引用其他的bean对象，上面创建的cart01-->
    <property name="cart" ref="cart01"></property>
</bean>
```

**使用引用外部的bean对象时他们的地址时相同的。**



**方式二：内部创建该类的bean对象**

```xml
<bean id="user02" class="cn.myq.pojo.User">
    <property name="cart">
        <bean id="cart02" class="cn.myq.pojo.Cart">
            <property name="name" value="奥迪"></property>
            <property name="price" value="1000000"></property>
            <property name="color" value="blue"></property>
        </bean>
    </property>
</bean>
```

**内部创建的和外部不搭嘎**，**内部类只能被内部使用，不能使用id获取该bean**。



**为List属性进行赋值List<Book> list**

```xml
<!--为list赋值-->
<bean id="book01" class="cn.myq.pojo.Book">
    <property name="name" value="马咏全太强了"></property>
</bean>

<bean id="user03" class="cn.myq.pojo.User">
    <property name="list">
        <!--相当于List list = new ArrayList<>();-->
        <list>
            <!--为每个list赋上Book对象-->
            <bean class="cn.myq.pojo.Book">
                <property name="name" value="德玛西亚"></property>
            </bean>
            <!--指向外部数据-->
            <ref bean="book01"></ref>
        </list>
    </property>
</bean>
```



**为Map属性进行赋值Map<String,Object> map**

```xml
<!--为map赋值-->
<bean id="user04" class="cn.myq.pojo.User">
    <property name="map">
        <!--相当于创建Map<String,Object> map = new LinkHashMap<String,Object>();-->
        <map>
            <!--每一个键值对-->
            <entry key="username" value="myq"></entry>
            <!--使用外部创建的bean-->
            <entry key="cart" value-ref="cart02"></entry>
            <!--内部创建bean-->
            <entry key="cart">
                <bean class="cn.myq.pojo.Cart">
                    <property name="name" value="寒冰战车"></property>
                </bean>
            </entry>
        </map>
    </property>
</bean>
```



### **util名称空间创建集合类型的bean**

https://blog.csdn.net/tuxq5721/article/details/12853965

------

### **级联属性赋值（给属性的属性赋值）**

```xml
<!--级联属性（给属性的属性赋值）-->
<bean id="user06" class="cn.myq.pojo.User">
    <property name="cart" ref="cart01"></property>
    <property name="cart.name" value="刘辟楼"></property>
</bean>
```

------

### **通过继承实现bean配置信息的重用**

```xml
<bean id="cart01" class="cn.myq.pojo.Cart">
    <property name="name" value="宝马"></property>
    <property name="color" value="红色"></property>
    <property name="price" value="10000000"></property>
</bean>

<!--parent 继承cart01的配置信息，直接拿过来，如果想要更改其配置直接重写-->
<bean id="cart04" class="cn.myq.pojo.Cart" parent="cart01">
     <property name="name" value="凤凰战车"></property>
 </bean>
```

------

### 通过abstract属性创建一个模板bean

**作用：可以让ioc容器中一个对象作为抽象的只能被继承使用**

```xml
<!--只能被继承不能创建对象
    如果创建该id的对象会出现异常
-->
<bean id="user07" class="cn.myq.pojo.User" abstract="true">
    <property name="age" value="17"></property>
</bean>
<!--引用上面的-->
<bean id="user08" class="cn.myq.pojo.User" parent="user07"></bean>
```

```java
@Test
public void test03(){
    User user08 = aContext.getBean("user08", User.class);
    System.out.println(user08);
}
```

User{username='null', age=17, gender='null', cart=null, list=null, map=null, properties=null}



------

### 测试bean的作用域，分别创建单实例和多实例的bean

**讨论ioc容器加载对象**

```xml
<bean  id="user01" class="cn.myq.pojo.User"></bean>

<bean id="cart01" class="cn.myq.pojo.Cart"></bean>

<bean id="book01" class="cn.myq.pojo.Book"></bean>
```

```java
    public void test01(){
        System.out.println("ioc容器加载完成");
        User user01 = aContext.getBean("user01", User.class);
        User user02 = aContext.getBean("user01", User.class);
        System.out.println(user01==user02);
    }
```

**结果：**

创建了user对象
创建了cart对象
创建了book对象
ioc容器加载完成

**在ioc加载完成之前，会调用该方法的构造器使之创建该对象**

**ioc容器中默认创建的对象是单实例**



**<u>可以使用如下方法可以创建多个地址 不同的对象</u>**

```xml
<!--将该对象的作用域设置为多实例 scope="prototype"-->
<bean  id="user01" class="cn.myq.pojo.User" scope="prototype"></bean>
```

**结果：**

创建了cart对象
创建了book对象
ioc容器加载完成
创建了user对象
创建了user对象

**结论：**<u>当使用scope="prototype"可以创建多实例对象是，该对象不会在ioc容器加载之前创建。</u>

**多实例bean在获取时创建对象**



------

### FactoryBean创建工厂模式

1.创建一个类实现FactoryBean

2.实现里面的方法

```java
public class BeanFactoryImpl implements BeanFactory {
    //在此方法中可以设置更改方法，然后将对象返回
    @Override
    public Object getBean(String s) throws BeansException {
        User user = new User();
        user.setAge(19);
        return user;
    }
    //设置是单实例吗？
    @Override
    public boolean isSingleton(String s) throws NoSuchBeanDefinitionException {
        return false;
    }

    //给自动调用使用
    @Override
    public Class<?> getType(String s) throws NoSuchBeanDefinitionException {
        return User.class;
    }
```

------

### spring连接数据库

**目录结构如下**

![](/02.png)

**JDBC.properties**

```properties
JDBC.username=root
JDBC.password=13758284693
JDBC.url=jdbc:mysql://localhost:3306/book
JDBC.driverClassName=com.mysql.jdbc.Driver
JDBC.initialSize=5
JDBC.maxActive=10
```

**ico.xml**

```xml
    <!--引入外部配置文件JDBC.properties-->
<context:component-scan base-package="cn.myq"/>
<context:property-placeholder location="JDBC.properties"/>

    <!--获取数据库连接的信息-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="username" value="${JDBC.username}"></property>
        <property name="password" value="${JDBC.password}"></property>
        <property name="url" value="${JDBC.url}"></property>
        <property name="driverClassName" value="${JDBC.driverClassName}"></property>
        <property name="initialSize" value="${JDBC.initialSize}"></property>
        <property name="maxActive" value="${JDBC.maxActive}"></property>
    </bean>
```

**class**

```java
public void Connection(){
    //加载ioc容器
    ApplicationContext appContext = new ClassPathXmlApplicationContext("ioc.xml");
    DataSource dataSource = appContext.getBean("dataSource", DataSource.class);
    try {
        System.out.println(dataSource.getConnection());
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```



------

### **Spring注解**

**Spring有四个注解：**

**@Service：**业务逻辑 service层

**@Controller:** 控制层  servlet层

**@Repository:** 持久化dao层

**@Component:**给不属于以上几层的组件添加这个注解(WebUtil)



**使用注解将组件快速的加入到容器中要如下几步：**

1.给要添加组件上面加上注解

2.告诉Spring，自动扫面加了注解的组件，依赖context名称空间

3.一定要导入aop包

```java
//添加注解等于在ioc容器中创建了一个id以该类名称首字母小写的组件
//<bean id="bookServlet" class="cn.myq.controller.BookServlet"></bean>
@Controller
public class BookServlet {
    //会自动的从ioc容器中查找对应id的组件bean
    //省去了自己去创建对象
    @Autowired
    BookService bookService;
    public void addBook(){
        System.out.println("执行了BookServlet");
        bookService.addBook();
    }
}
```

**注意：**

**使用注解加入到容器中的主键，和使用配置加入到容器中的组件行为都是默认一样的**

**1.组件的id，默认就是组件的类名首字母小写**

​		@Controller(value = "bookServlet02")更改组件id

**2.组件的作用域，默认就是单例的**

**3.当在一个类上添加上上面4个注解时会在ioc容器中创建该对象，配置的组件id默认就是组件的类名首字母小写，当在类上添加<u>@Autowired</u>时会自动在ioc容器中寻找对应的bean组件。**

------

**使用context:exclude-filter指定扫描包时不包含的类** 

```xml
<!-- exclude-filter:不包含
		type:annotation 按照注解去除
			assignable 按照全类名去除
		expression:要过滤的内容
		-->
<context:component-scan base-package="cn.myq">
	<context:exclude-filter type="assignable" 	expression="cn.myq.servlet.BookServlet"/>
</context:component-scan>
```

**使用context:include-filter指定扫描包时要包含的类**

```xml
<!--use-default-filters="false" 表示扫描注解的时候是否全部扫描进来
    false表示不全部扫描进来，配合include-filter，将那些扫描进来使用
-->
<context:component-scan base-package="cn.myq" use-default-filters="false">
		<context:include-filter type="assignable" 						   															expression="cn.myq.servlet.BookServlet"/>
	</context:component-scan>
```

------

**注解@Autowired**

**作用：会自动的从ioc容器中查找对应id的组件bean**

**查找流程**，首先会在ioc中容器中按类型查找，查看有没有该类型的

​	如果没有报异常

​	有过有注入

​	如有有多个

​			按照id来查

​				查不到报异常	

​				查到注入

​				可以使用**@Qualifier**注解将该类的id更改

```java
//将ioc容器中的bookDao的 id更改为booDao03
@Qualifier("booDao03")
BookDao bookDao;
```

​						如果还找不到正常会报异常

​						可以使用@**Autowired(required = false)**注解，当在容器中没有找到对应的组件时注入null；



### **本章注解分类：**

**@Service：**业务逻辑 service层

**@Controller:** 控制层  servlet层

**@Repository:** 持久化dao层

**@Component:**给不属于以上几层的组件添加这个注解(WebUtil)

**@Autowired：**自动装配

​		**参数**：required = false/true

​		**作用**：当在容器中没有找到对应的组件时注入null；

​		

**@Qualifier**：**更改ioc组件的id值**

​		**参数**：value

​		**作用**：**更改ioc组件的id值**



当使用id查找组件时找不到可以指定

### 参考文献：

https://blog.csdn.net/tuxq5721/article/details/12853965

https://zhidao.baidu.com/question/421848564.html

