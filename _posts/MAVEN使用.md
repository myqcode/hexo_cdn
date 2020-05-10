---
title: MAVEN使用
date: 2020-05-01 14:48:19
tags:
typora-root-url: MAVEN使用
---

## maven环境配置

![](/01.png)

**配置path**

![](/02.png)

maven运行需要依赖java_home,要确保环境中有JAVA_HOME.



**查看是否配置成功**

![](/03.png)



**指定jar包目录**

当设置了之后，启动maven工程会从设置的目录中寻找jar包

![](/04.png)





## IDEA集成本地maven

![](/05.png)



**解决为了在不联网的情况下创建maven工程**

![](/06.png)

**内容：**-DarchetypeCatalog=internal



## 创建maven工程：

### 普通java工程



<img src="/07.png" style="zoom: 33%;" />

<img src="/08.png" style="zoom:33%;" />

​		

maven自定义配置

<img src="/09.png" style="zoom:50%;" />

<img src="/10.png" style="zoom:50%;" />

**配置阿里云镜像**

![](/11.png)

```xml
<mirror>
          <id>nexus-aliyun</id>
          <mirrorOf>central</mirrorOf>
          <name>Nexus aliyun</name>
          <url>http://maven.aliyun.com/nexus/content/groups/public</url> 
    </mirror>
```

当有包需要直接去https://maven.aliyun.com/mvn/search下载。

------

如果需要使用maven的tomcat，请看一下转载

https://blog.csdn.net/jinhaijing/article/details/81134784



------

参考文献：https://blog.csdn.net/jinhaijing/article/details/81134784