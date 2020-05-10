---
title: Filter
date: 2020-04-21 12:53:36
tags:
typora-root-url: ./
---

## 1.filter有什么作用？



- Filter也称之为过滤器，它是Servlet技术中最实用的技术，Web开发人员通过Filter技术，对web服务器管理的所有web资源。

- 例如实现URL级别的权限访问控制、过滤敏感词汇、压缩响应信息等一些高级功能。例如对Jsp, Servlet, 静态图片文件或静态 html 文件等进行拦截，从而实现一些特殊的功能。

- 它主要用于对用户请求进行预处理，也可以对HttpServletResponse进行后处理。使用Filter的完整流程：Filter对用户请求进行预处理，接着将请求交给Servlet进行处理并生成响应，最后Filter再对服务器响应进行后处理。

![](/Filter/01.png)

## 2.Filter的生命周期

- 和我们编写的Servlet程序一样，Filter的创建和销毁由WEB服务器负责。 web 应用程序启动时，web 服务器将创建Filter 的实例对象，并调用其init方法，读取web.xml配置，完成对象的初始化功能，从而为后续的用户请求作好拦截的准备工作

- filter对象只会创建一次，init方法也只会执行一次。开发人员通过init方法的参数，可获得代表当前filter配置信息的FilterConfig对象。

- 这个方法完成实际的过滤操作。当客户请求访问与过滤器关联的URL的时候，Servlet过滤器将先执行doFilter方法。FilterChain参数用于访问后续过滤器。

- Filter对象创建后会驻留在内存e799bee5baa6e79fa5e98193e78988e69d8331333431346339，当web应用移除或服务器停止时才销毁。在Web容器卸载 Filter 对象之前被调用。该方法在Filter的生命周期中仅执行一次。在这个方法中，可以释放过滤器使用的资源。

模拟用户登录

```bash
当用户需要访问filter配置过的路径时，需要判断是否成立，成立条件就是用servlet判断用户名是否正确
filter通过查看用户名是否正确来判断权限的开放。
```

## 3.Filter的特点

​	多个filter过滤器的特点：

​	1.所有的filter都执行在同一个线程中（默认）。

​	2.共享一个request对象



​	filterchain.dofilter()方法的作用

​		1.执行下一个filter过滤器（如果有的情况下）

​		2.执行目标资源

​	在多个filter指向同一个目标源时，他们的优先顺序时在xml里的配置顺序

![](/Filter/03.png)



## 4.Fileter的配置如下

```
 <filter>
        <filter-name>helloFilter</filter-name>
        <filter-class>cn.myq.filter.HelloFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>helloFilter</filter-name>
       	 <!--
            当访问该目录下的任何一个文件时调用，HelloFilter程序
            判断权限是否开放
      	  -->
        <url-pattern>/admin/*</url-pattern>
    </filter-mapping>
```



## 参考文献：

https://zhidao.baidu.com/question/471427755.html

http://www.atguigu.com/