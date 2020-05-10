---
title: Druid数据库连接池使用
date: 2020-04-25 17:04:20
tags:
---

**1.为什么要使用数据库连接池**

**为了避免重复的获取数据库连接，和是释放数据库连接而导致大量性能开销，提高响应速度。**

**在需要连接数据库时，数据库中已经有一些数据库连接在池中备用，当我们需要连接直接冲连接池中取就行，释放时归还连接，一般将最大同时连接个数设置最大，以防范用户一次连接上限，导致其他用用户等待。**



**需要的jar包**：

mysql-connector-java-5.1.7-bin.jar	mysql连接驱动
druid-1.1.10.jar	连接池

**通过读取配置文件获得连接**

```properties
username=root
password=11111111
url=jdbc:mysql://localhost:3306/book
driverClassName=com.mysql.jdbc.Driver
initialSize=5
maxActive=10
```

```java
private static DruidDataSource dataSource;
static {
    try {
        //读取配置文件
        Properties properties = new Properties();
        //类加载时获取配置
        InputStream is = JdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
        //加载
        properties.load(is);
        //createDataSource是读取数据源的配置信息。
        dataSource = (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);

    } catch (Exception e) {
        e.printStackTrace();
    }
}
//获取链接
public static Connection getConnection(){
        Connection connection = thread.get();
        try {
            connection = dataSource.getConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    
    	return connection;
}
```



**Spring配置获取连接**

优点：因为spring创建bean默认时单实例

```java
        <!--读取外部配置信息需要 依赖context名称空间-->
        <context:property-placeholder location="config/JDBC.properties"/>

        <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" >
            <property name="username" value="${jdbc.username}"></property>
            <property name="password" value="${jdbc.password}"></property>
            <property name="url" value="${jdbc.url}"></property>
            <property name="driverClassName" value="${jdbc.driverClassName}"></property>
        </bean>
        
        ----------------------------------------------------------------------------
        
		ApplicationContext a2 = new ClassPathXmlApplicationContext("config/ioc3.xml");
        DataSource bean = a2.getBean(DataSource.class);
        Connection connection = bean.getConnection();
        try {
            System.out.println(bean.getConnection());
        } catch (SQLException e) {
            e.printStackTrace();
        }
        
        
```

```properties
jdbc.username=root
jdbc.password=13758284693
jdbc.url=jdbc:mysql://localhost:3306/test
jdbc.driverClassName=com.mysql.jdbc.Driver
```