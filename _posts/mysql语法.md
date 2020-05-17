---
title: mysql语法
date: 2020-04-24 12:15:03
tags: mysql
---

## 基础查询

**打开指定的数据库**

```sql
USE `myemployees`;
```

**显示表结构**

```sql
DESC 表名
DESC `employees`;
```

**查询表中单个字段**

```sql
语法：select 查询列表 from 表名；
SELECT `first_name` FROM `employees`;
```

**查询表中多个字段**

```sql
语法：select 查询列表1,查询列表2 from 表名；
SELECT `first_name`,`last_name` FROM `employees`;
```

**查询表中所有字段**

```sql
语法：SELECT * FROM 表名;
SELECT * FROM `employees`;
```

**查询常量值**

```sql
SELECT 500
SELECT '卢本伟'
```

**查询表达式**

```sql
SELECT 100*200
SELECT 200%5
```

**起别名**

```sql
好处便于理解
如查询的字段有重名，去以区分
方式一：
SELECT `first_name` AS 姓名 FROM `employees`;
方式二：
SELECT `first_name` 姓名 FROM `employees`;
```

**去重**DISTINCT

```sql
示例：查询员工中涉及的所有部门种类
SELECT DISTINCT`job_id` FROM `employees`;
```

ifnull

```sql
#语法IFNULL(字段名,设置的值)
#将查询薪水为null的改为0
SELECT IFNULL(`commission_pct`,0),`commission_pct` FROM `employees`
```

isnull

```sql
#判断某字段的值是否为null，为null返回1，否则返回0
SELECT ISNULL(commission_pct),commission_pct FROM `employees`
```

------

## 条件查询

**语法格式如下：**

```sql
select 查询列表 from 表名
where 筛选条件
```

**条件表达式筛选：**

```sql
查询salary大于20000的员工信息
SELECT * FROM `employees` WHERE salary > 20000; 

查询salary不等于10000的员工信息
SELECT * FROM employeesWHERE salary <> 10000;
```



**按逻辑表达式筛选：**

**作用：用于连接条件表达式**

```sql
查询id>109 并且 salary >500的员工信息
SELECT * FROM `employees` WHERE `employee_id`>109 AND salary>500;

查询salary不在5000和10000之间的员工
SELECT * FROM `employees` WHERE NOT(salary>5000 AND salary<10000);
```

**模糊查询：**

内容：**like** ，**between**  **and**， **in**， **is not null**

**like使用**

```sql
#查询员工名中包含字符a的员工信息
通配符：%表示任意字符
	_任一单个字符
			
大小写不区分
SELECT * FROM `employees` WHERE `last_name` LIKE '%a%';


#查询姓名的第二个字符为a第4个字符为b的员工信息。
SELECT * FROM `employees` WHERE last_name LIKE '_a_b%';

#查询姓名第二个字符为_的员工信息
SELECT * FROM `employees` WHERE last_name LIKE '_$_%' ESCAPE '$';
```

**between and使用**

```sql
特点：包含两个取值
      取值不能置换
#查询工资在5000到10000范围之内的员工信息
SELECT * FROM `employees` WHERE salary BETWEEN 5000 AND 10000;
```

**in使用**

```sql
特点：查询X的值为Y或z(Y和z需要时字段)
查询job_id为AD_VP,IT_PROG的员工信息
SELECT * FROM `employees` WHERE job_id IN('AD_VP','IT_PROG');
```

**is null/is not null使用**

```sql
 #查询奖金为NULL的员工信息
 SELECT * FROM `employees` WHERE `commission_pct` IS NULL
 
 查询奖金不为NULL的员工信息
 SELECT * FROM `employees` WHERE `commission_pct` IS NOT NULL
 
```

------

## 排序查询

**语法格式**

```sql
语法：
	select 查询列表
	from 表
	【where 筛选条件】
	order by 排序列表【asc|desc】	
#asc升序 desc降序
默认是升序
```

**按年薪的高低显示员工的信息和年薪【按别名查询】**

```sql
SELECT *,salary*12*(1+IFNULL(`commission_pct`,0)) 年薪
FROM `employees` 
ORDER BY 年薪 DESC;
```

**按姓名的长度显示员工的姓名和工资【按函数排序】**

```sql
SELECT LENGTH(`last_name`) 字段长度,`last_name`,`salary` 
FROM `employees` 
ORDER BY 字段长度 ASC;
```

**查询员工信息，要求先按工资升序，员工编号降序【按多个字段排序】**

```sql
SELECT *
FROM employees
ORDER BY `salary` ASC,`manager_id` DESC;
#先按asc排完序，如过salary字段值相同在按照manager_id降序
```

