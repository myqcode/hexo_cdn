---
ztitle: IDEA操作
date: 2020-04-21 22:59:34
tags: [JAVA,IDEA]
typora-root-url: ./
categories: 软件设置
---

## IDEA基本配置操作：

------



### 1.设置IDEA鼠标悬停(提示注释)

![](/IDEA操作/01.png)

### 2.设置自动导包

![](/IDEA操作/02.png)

### 3.设置方法之间的分隔符

![](/IDEA操作/03.png)

### 4.当数据源多时(多把数据源输出,多行效率高)

![](/IDEA操作/04.png)

### **5.修改类头的文档注释信息**

![](/IDEA操作/05.png)

```java
${PACKAGE_NAME} - the name of the target package where the new class or interface will be created. 
${PROJECT_NAME} - the name of the current project. 
${FILE_NAME} - the name of the PHP file that will be created. 
${NAME} - the name of the new file which you specify in the New File dialog box during the file creation. 
${USER} - the login name of the current user. 
${DATE} - the current system date. 
${TIME} - the current system time. 
${YEAR} - the current year. 
${MONTH} - the current month. 
${DAY} - the current day of the month. 
${HOUR} - the current hour. 
${MINUTE} - the current minute. 
${PRODUCT_NAME} - the name of the IDE in which the file will be created. 
${MONTH_NAME_SHORT} - the first 3 letters of the month name. Example: Jan, Feb, etc. 
${MONTH_NAME_FULL} - full name of a month. Example: January, February, etc.
```

### 6.更改字符集(必须)

![](/IDEA操作/06.png)

### 7.设置自动编译

![](/IDEA操作/07.png)



### 8.模板(代码更加便捷)

![](/IDEA操作/09.png)

------



## IDEA快捷键：	

### 1.设置快捷键与XXX一样![](/IDEA操作/08.png)

### 2.基本快捷键

| **快捷键**       | **介绍**                                                     |
| ---------------- | ------------------------------------------------------------ |
| Ctrl + F         | 在当前文件进行文本查找 `（必备）`                            |
| Ctrl + R         | 在当前文件进行文本替换 `（必备）`                            |
| Ctrl + Y         | 删除光标所在行 或 删除选中的行 `（必备）`                    |
| Ctrl + D         | 复制光标所在行 或 复制选择内容，并把复制内容插入光标位置下面 `（必备）` |
| Ctrl + N         | 根据输入的 **类名** 查找类文件                               |
| Ctrl + W         | 选中光标所在的单词或段落，连续按会在原有选中的基础上再扩展选中范围 |
| Ctrl + /         | 注释光标所在行代码，会根据当前不同文件类型使用不同的注释符号 `（必备）` |
| Ctrl + Shift + F | 根据输入内容查找整个项目 或 指定目录内文件 `（必备）`        |
| Ctrl + Shift + R | 根据输入内容替换对应内容，范围为整个项目 或 指定目录内文件 `（必备）` |
| Ctrl + Shift + J | 自动将下一行合并到当前行末尾 `（必备）`                      |
| Ctrl + Shift + Z | 取消撤销 `（必备）`                                          |
| Ctrl + Shift + T | 对当前类生成单元测试类，如果已经存在的单元测试类则可以进行选择 |
| F2               | 跳转到下一个高亮错误 或 警告位置 `（必备）`                  |
| ctrl + x         | 删除当前列                                                   |
| ctrl + p         | NEW实例时查看构造器的参数                                    |
| ctrl + q         | 查看方法的详细信息                                           |
| shfit + t        | 更改文件名                                                   |
| Ctrl＋Alt＋T     | 可以将代码包在一块内，例如try/catch                          |
| Ctrl + Alt + L   | 格式化代码                                                   |