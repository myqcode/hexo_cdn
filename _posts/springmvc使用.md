---
title: springmvc使用
date: 2020-04-30 14:36:29
tags:
typora-root-url: springmvc使用
---

## 1.创建springmvc_hello

### 1.首先在web.xml配置DispatcherServlet

DispatcherServlet主要用作职责调度工作，本身主要用于控制流程。

```xml
<servlet>
    <servlet-name>hra</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--配置，容器坐标-->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:ioc.xml</param-value>
    </init-param>
    <!--优先级，优先级越低越快被创建-->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>hra</servlet-name>
    
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

**在web容器启动时，DispatcherServlet会初始化，然后它会做上面这些事情**：
DispatcherServlet是前台控制器，配置在web.xml中，拦截匹配的请求，Servlet拦截匹配规则要自己定义，把拦截下来的请求，依据某某规则分发到目标Controller，这个过程需要



**ioc容器：**

```
<!--配置扫描-->
<context:component-scan base-package="cn.myq"/>
```

**类：**

```java
@Controller
public class Hello {
    //设置请求
    @RequestMapping("/hello")
    public String test01(){
        System.out.println("收到了请求");
        //设置目标
        return "/WEB-INF/pages/ok.jsp";
    }
}
```

**目录结构**：

<img src="/01.png" style="zoom:67%;" />



使用**InternalResourceViewResolver** 对返回目标的优化

**ioc.xml配置如下：**

```xml
<!--配置扫描-->
<context:component-scan base-package="cn.myq"/>
<!--视图解析器，可以简化方法的返回值，返回值就是作为目标页面地址
    只不过视图解析器可以帮助我们拼串-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <!--前缀-->
    <property name="prefix" value="/WEB-INF/pages/"></property>
    <!--后缀-->
    <property name="suffix" value=".jsp"></property>
</bean>
```

hello类：

```java
@Controller
public class Hello {
    //设置请求
    @RequestMapping("/hello")
    public String test01(){
        System.out.println("收到了请求");
        //设置目标
        //对模型视图名称的解析，即在模型视图名称添加前后缀
        return "test1";
    }
}
```

**DispatcherServlet细节：**

<img src="/04.png" style="zoom:80%;" />

## 2.请求参数：

### 1.@RequuestMapping的用法

**1.将RequestMapping放在类名上**

```java
@RequestMapping("/six")
//示例http://localhost:8888/mvc_test02/six/six1	访问才有效
@Controller
public class Test01 {
    @RequestMapping("/six1")
    public String test01(){
        System.out.println("接收请求");
        return "test1";
    }
}
```

**index.jsp改成如下:**

```
<html>
<head>
  <title>$Title$</title>
</head>
<body>
<a href="six/six1">点我</a>
</body>
</html>
```

**结论：当将RequestMapping放在类上时表示所有的请求的方法都需要以该地址作为父路径**

**2.RequuestMapping的六个属性**

**1.value（默认）**：指定请求的实际地址，指定的地址可以是具体地址

**2.method**

```java
@Controller
public class Test02 {
	//method限制页面什么请求（才可以运行）
    @RequestMapping(value = "/six2",method = RequestMethod.POST)
    public String test02(){
        return "test2";
    }
}
```

**3.params**

```java
/**
 * params = {"username"}  需要请求有username属性
 * params = {"username=123"} 需要请求有username属性，并且值为123才能访问
 * params = {"username=!123"} 需要请求有username属性,并且值不为123才能访问
 * params = {"!password"} 需要请求没有password属性
 * 例如：http://localhost:8888/mvc_test/hello1?username=123
 * @return
 */
@RequestMapping(value = "/six3",params = {"!password"})
public String test03(){
    return "test3";
}
```

**结论：request中必须遵守指定参数的限定，才能给予访问**

**4.headers**

```java
/**
 * 谷歌：User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.122 Safari/537.36
 * 火狐：Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:76.0) Gecko/20100101 Firefox/76.0
 * 例如如下：只能火狐浏览器才可以访问
 * @return
 */
@RequestMapping(value = "/six500",headers = {"User-Agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:76.0) Gecko/20100101 Firefox/76.0"})
public String test500(){
    System.out.println("test500.....");
    return "test500";
}
```

**结论：指定request中必须包含某些指定的header值，才能让该方法处理请求**

####  consumes，produces

------



### 2.模糊查询：

**优先匹配范围小的**

<u>?的用法</u>

```java
/**
 * ？：表示匹配一个字符，例如：http://localhost:8888/mvc_test01/six5
 * @return
 */
@RequestMapping(value = "/six?")
public String test04(){
    System.out.println("test4.....");
    return "test4";
}
```

<u>*的用法</u>

```java
/**
 * 可以匹配一个或多个字符，例如：http://localhost:8888/mvc_test01/six123
 * 只能可以匹配一层目录，例如：http://localhost:8888/mvc_test01/121/six123
 * @return
 */
@RequestMapping(value = "/*/six*")
public String test05(){
    System.out.println("test5.....");
    return "test4";
}
```

<u>**的用法</u>

```java
/**
 * 可以匹配一个或多个字符
 * 可以匹配多层目录，例如：http://localhost:8888/mvc_test01/121/1/six123
 * @return
 */
@RequestMapping(value = "/**/six*")
public String test06(){
    System.out.println("test6.....");
    return "test4";
}
```

### 3.@PathVariable的使用

```java
/**
 * @PathVariable
 * 路径上可以有占位符，占位符语法就是在指定位置上写上{}
 * 例如：http://localhost:8888/mvc_test01/username=myq
 *  占位符的路径只有一层
 * @param id
 * @return
 */
@RequestMapping(value = "username={id}")
public String test07(@PathVariable("id") String id){
    //获取需要的参数值
    System.out.println(id);
    return "test6";
}
```

### 4.REST

**Rest:系统希望以非常简洁的URL地址来发请求**

怎么样表示对一个资源的增删改查用请求方式来区分

/getBook？id=1			  ：查询book

/deleteBook？id=1		：删除book

/updateBook？id=1	  ：更新book

/addBook？	    			：添加book



**Rest推荐：**

​	**url地址这么起名：/资源名/资源标识符**

/book/1					:GET----查询一号图书

/book/1					:PUT----更新一号图书

/book/1					:DELETE----删除图书

/book						:POST----添加图书



**如何从页面发起PUT，DELETE形式的请求?spring提供了对 Rest风格的支持**

1.springmvc中有一个Filter，它可以把普通的请求转化为规定形式的请求：配置filter

```xml
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

**2.如何发其他形式的请求？**

​       按照以下要求，

**1.创建一个post类型的表单** 

**2.表单项中携带一个_method的参数** 

**3.这个_method的值就是DELETE，PUT**

```
//创建一个post类型的表单 
<form method="post" action="book/1">
//表单项中携带一个_method的参数 
//这个_method的值就是DELETE，PUT
  <input name="_method" value="delete">
  <input type="submit" value="删除图书">
</form><br>
```

**class：**

```java
/**
 * 增加
 * @return
 */
@RequestMapping(value = "/add",method ={RequestMethod.GET})
public String test01(){
    System.out.println("添加图书");
    return "ok";
}

/**
 * 查询
 * @return
 */
@RequestMapping(value = "/select",method ={RequestMethod.POST})
public String test02(){
    System.out.println("查询图书");
    return "ok";
}
```

**当tomcat是8.0及以上版本时会报错如下图**

![](/02.png)

只需要将目标文件添加如下：(**isErrorPage="true"**)

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %>
```

------



#### @RequestParam的使用

**作用：获取请求时的参数：相当于request.getParameter(xxxx);**

**方式一：**

```java
@RequestMapping("/hello6")
//默认在形参中写上参数就代表获取？后面的参数
public String test06(String username){
    System.out.println(username);
    System.out.println("hello6收到了信息");
    return "ok";
}
```

**方式二：**

```java
/**
 * 可以获取请求带的参数
 * @RequestParam有三个参数用来限定
 * required : 当请求没有这个参数时不做响应(不报错，继续访问跳转网页)
 * defaultValue: 当请求没有这个参数时默认的值
 * value :user = request.parameter("username");
 *
 * @param username
 * @return
 */
@RequestMapping(value = "hello?")
public String test02(@RequestParam(value = "user",required = false,defaultValue = "666") String username){
    System.out.println(username);
    return "ok";
}
```



#### @RequestHeader的使用

**作用：获取请求头的参数信息**

```java
/**
 * @RequestHeader ：可以获取请求头的参数
 * value :获取哪个请求头的参数
 * required :当请求没有这个参数时不做响应(不报错，继续访问跳转网页)
 * defaultValue :当请求没有这个参数时默认的值
 * @param UserAgent
 * @return
 */
@RequestMapping(value = "/hello4")
public String test03(@RequestHeader(value = "User-Agent") String UserAgent){
    System.out.println(UserAgent);
    return "ok";
}
```



#### @CookieValue的使用

**作用：获取Cookie的值**

```java
/**
*内置三个参数和上面相同
*/
@RequestMapping("/hello7")
public String test07(@CookieValue("JSESSIONID") String JSESSIONID){
    System.out.println("您的CookieValue值为" + JSESSIONID);
    return "ok";
}
```



**将请求的数据注入到javabean中**

```java
@RequestMapping("/hello8")
public String test08(Book book){
    System.out.println(book);
    return "ok";
}
```

**jsp：**

```
<body>
    <form action="hello8">
        <input type="text" name="bookName">
        <input type="text" name="author">
        <input type="submit">
    </form>
</body>
```

解决：

**可能遇到中文乱码问题请转到如下链接**

https://blog.csdn.net/weixin_44066293/article/details/89194643

https://blog.csdn.net/u013014636/article/details/72357415

------



## 数据输出

**作用：将servlet数据保存在域中发送给jsp页面**

**使用Map，或者Model或者ModelMap向域中存储数据**

```java
/**
 * SpringMVC除了在方法上传入原生的request和session外还能怎么样把数据带给页面
 *  1.可以在方法处传入Map，或者Model或者ModelMap。
 *          给这些参数里面保存的所有数据都会放在request域中
 *       关系：
 *          Map，Model，ModelMap，最终都是BindingAwareModelMap在工作
 *          相当于给BindingAwareModelMap中保存的东西都会被放在请求域中
 */
/**
 * MAP
 * @param map
 * @return
 */
@RequestMapping("/hello02")
public String test01(Map<Object,String> map){
    map.put("username","test01");
    return "ok";
}

/**
 * model
 * @param model
 * @return
 */
@RequestMapping("/hello03")
public String test02(Model model){
    model.addAttribute("username","test02");
    return "ok";
}

/**
 * ModelMap
 * @param modelMap
 * @return
 */
@RequestMapping("/hello04")
public String test03(ModelMap modelMap){
    modelMap.addAttribute("username","test03");
    return "ok";
}
```



**使用ModelAndView来向域中保存数据**

```java
/**
 * 使用ModelAndView来向域中保存数据
 * @return
 */
@RequestMapping("hello05")
public ModelAndView test04(){
    ModelAndView mv = new ModelAndView("ok");
    mv.addObject("username","test04");
    return mv;
}
```

**当需要将数据传入session中时，应使用原生API解决Httpsession**

------



### 视图转发：

**方式一：**

**使用绝对路径转发**

```java
/**
 * 视图跳转
 * @return
 */
@RequestMapping("/hello06")
public String test05(){
    System.out.println("视图跳转成功hello06");
    return "../../index01";
}
```

**方式二**：

**使用forward**

```java
@RequestMapping("/hello07")
public String test06(){
    System.out.println("视图跳转成功hello07");
    return "forward:/index01.jsp";
}
```

**两个方法的执行结果都是一样的**

------



### 视图重定向：

**使用redirect**

```java
/**
 * 请求重定向的方式
 * 使用redirect
 * @return
 */
@RequestMapping("/hello08")
public String test07(){
    System.out.println("视图跳转成功hello07");
    return "redirect:/index01.jsp";
}
```



#### **解决一些Controller方法只是为了跳转页面，如果多了可能会写很多这样的空方法**

使用如下方法，写在mvc。xml中

```xml
<!--发送一个请求(“hello09”):直接来到web-inf下的jsp页面，无需在controller里写
     空方法
    path：指定的是哪个请求
    view-name：跳转的视图名
    走了Springmvc的整个流程；视图解析。
    会导致其他注解不好使用
 -->
<mvc:view-controller path="/hello09" view-name="ok"></mvc:view-controller>
<!--开启MVC注解驱动模式，开启mvc高级模式，解决其他注解不好使-->
<mvc:annotation-driven></mvc:annotation-driven>
```

#### **当引入jquery时，使用页面发现jquery404**

**引入下面配置：**

```xml
<mvc:default-servlet-handler></mvc:default-servlet-handler>
<mvc:annotation-driven></mvc:annotation-driven>
```

#### 日期格式化

在需要添加日期格式化的javabean对象上添加@DateTimeFormat(日期格式)注解

**spring.xml配置如下**

```xml
<bean class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
        
</bean>
```



### 参考文献：

https://blog.csdn.net/weixin_44066293/article/details/89194643

https://blog.csdn.net/u013014636/article/details/72357415

