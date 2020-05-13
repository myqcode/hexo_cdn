---
title: AOP
date: 2020-05-13 14:37:34
tags:
typora-root-url: AOP
---

## AOP

**aop：面向切面编程。**

**面向切面编程思想指的是：在程序运行期间<u>将某段代码动态的切入到指定方法的在指定位置</u>进行运行的这种编程方式，面向编程思想。**



**比如**：在计算机运行计算方法的时候进行日志的记录，

需要在**计算之前记录**，计算之后记录，出现异常记录，正常完成记录。

1.直接卸载方法内部，显得代码冗余度高

2.希望，在**核心功能运行期间自己动态的加上日志记录**，可以使用**动态代理**。

**但是使用动态代理也有缺点：只能基于接口进行代理**。

Spring实现了AOP功能，底层就是动态代理

1.利用spring创建动态代理

​	实现简单，没有强制要求目标对象实现接口

**Aop的专用术语**

![](/01.png)



### AOP的使用：

**首先导入切面所依赖的jar包**

spring-aspects-5.2.3.RELEASE.jar

第三方增强版

com.springsource.net.sf.cglib-2.2.0.jar
com.springsource.org.aopalliance-1.0.0.jar
com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar



**目录结构如下**

![](/02.png)

**Calculator**

```java
public interface Calculator {
    int jia(int a,int b);

    int jian(int a,int b);

    int cheng(int a,int b);

    int chu(int a,int b);
}
```

**CalculatorImpl**

```java
@Component
public class CalculatorImpl implements Calculator {
    @Override
    public int jia(int a, int b) {
        return a+b;
    }
    @Override
    public int jian(int a, int b) {
        return a-b;
    }
    @Override
    public int cheng(int a, int b) {
        return a*b;
    }
    @Override
    public int chu(int a, int b) {
        return a/b;
    }
}
```

**Aspect**

```java
//@Aspect叫该类设置为切面类
//想要在目标方法执行之前以运行,需要写切入点表达式
//execution(权限修饰符 返回值类型 方法全类名 (参数类型))
@org.aspectj.lang.annotation.Aspect
@Service
public class Aspect {
    @After("execution(public int cn.myq.service.CalculatorImpl.*(int,int))")
    public void after(){
        System.out.println("方法运行之前");
    }
    @Before("execution(public int cn.myq.service.CalculatorImpl.*(int,int))")
    public void before(){
        System.out.println("方法最终之后");
    }
    @AfterThrowing("execution(public int cn.myq.service.CalculatorImpl.*(int,int))")
    public void exception(){
        System.out.println("方法出异常");
    }
    @AfterReturning("execution(public int cn.myq.service.CalculatorImpl.*(int,int))")
    public void afterReturn(){
        System.out.println("方法正常返回");
    }
}
```

**spring-config.xml：**

```xml
<!--扫面-->
<context:component-scan base-package="cn.myq"/>
<!--扫面切面-->
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
```

**测试：**

```java
public void test01(){
	//当被且面的类实现了接口，就只能通过该类的接口获取
    //如果没有接口直接使用本类也能
    Calculator calculator = aContext.getBean(Calculator.class);
    calculator.jia(1,2);
}
```

------

### **Aop细节**

#### **切入点表达式**

![](/03.png)

------



#### JoinPoint，throwing，throwing

```java
//throwing = "yichang",当有异常，异常保存在yichang中
@AfterThrowing(value = "execution(public int cn.myq.service.CalculatorImpl.*(int,int))",throwing = "yichang")
//切入点方法的信息全都保存在了joinPoint
//Exception yichang范围写大点，接收所有异常
public void exception(JoinPoint joinPoint,Exception yichang){
    //getSignature获取签名(切入点方法的详细信息)
    String name = joinPoint.getSignature().getName();
    System.out.println(name + ":方法出异常,异常信息" + yichang);
}
```

------



#### 抽取公共的切入点表达式

```java
//随便创建一个类，加上@Pointcut
//抽取公共的切入点表达式
@Pointcut("execution(public int cn.myq.service.CalculatorImpl.*(int,int))")
public void public01(){}
```

**使用：**

```java
@Before("public01()")
public void before(JoinPoint joinPoint){
    String name = joinPoint.getSignature().getName();
    System.out.println(name + ":方法最终之后");
}
```

------



**环绕通知**

**作用：快速的创建动态代理**

**核心**： **proceed = pjp.proceed(args);**等于动态代理的method.invoke

可以在此方法的上下添加一些需要的功能。

```java
//切入点
@Around(value = "public01()")
//ProceedingJoinPoint是JoinPoint的子接口
public Object myAround(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("执行环绕通知");
    Object[] args = pjp.getArgs();
    String name = pjp.getSignature().getName();
    Object proceed = null;
    try {
        System.out.println(name + "1方法执行之前参数是" + Arrays.asList(args));
        //相当于method.invoke
        //proceed返回值就是方法执行后的返回值
        proceed = pjp.proceed(args);
        System.out.println(name + "1方法执行完成返回值是" + proceed );

    } catch (Exception e) {
        System.out.println(name + "1方法出现异常" + e);
        throw new RuntimeException(e);
    } finally {
        System.out.println(name + "1正确执行完成");
    }
    return proceed;
}
```



------

