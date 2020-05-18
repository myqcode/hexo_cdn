---
**title**: mysql语法
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

## 函数

### 字符函数

**length获取参数值的直接输**

```sql
SELECT LENGTH('哈哈mmm');		'9'
```

**concat拼接字符**

```sql
SELECT CONCAT(last_name,'_',first_name) FROM `employees`  ‘Kochhar_Neena’
```

**upper转成大写 lower转成小写**

```sql
SELECT UPPER('list');	'LIST'
SELECT LOWER('lisT');	'list'
```

**substr截取从指定索引处后面所有字符**

```sql
#索引是从1开始的
SELECT SUBSTR('德玛西亚',3); '西亚'

#截取从指定索引出指定字符长度的字符
SELECT SUBSTR('德玛西亚就是我',5,3);   '就是我'
```

```sql
#查询：姓名中首字符大写，其他字符消息额然后用_拼接，显示出来
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),'_',LOWER(SUBSTR(last_name,2))) 姓名
FROM employees
```

**instr返回字串第一次出现的索引，如果找不到返回0**

```sql
SELECT INSTR('老六了老七','老七') AS out_put;   4
```

**trim去空格**

```sql
#gbk 一个字符2个字节 utf-8一个字符3个字节
SELECT LENGTH(TRIM('  德玛西亚   ')) AS out_put;  12
```

```sql
#去除头尾指定的字符
SELECT TRIM('a' FROM  'aa44a444ab') AS out_put;    44a444ab
```

**用指定的字符实现左右填充指定长度**

```sql
SELECT LPAD('德玛西亚',9,'@');	'@@@@@德玛西亚'

SELECT RPAD('卢本伟',7,'!');   '卢本伟!!!!'
```

**replace替换**

```sql
SELECT REPLACE('卢本伟nb','nb','流弊'); '卢本伟流弊'
```

### 数学函数

**round 四舍五入**

```sql
SELECT ROUND(10.96);  11
SELECT ROUND(-10.96); -11

#保留2位小数
SELECT ROUND(-10.965,2);   -10.97  
```

**ceil 向上取整**

```sql
SELECT CEIL(10.001) 11
SELECT CEIL(-9.99)  -9
```

**floor 向下取整**

```sql
SELECT FLOOR(10.1)  10
SELECT FLOOR(-10.1)  -11
```

**truncate截断**

```sql
#只能保留1位小数其他全部截断
SELECT TRUNCATE(1.6554,1)
```

**mod取余**

```sql
SELECT MOD(10,-3) 1
```

------

### 日期函数

**now：获取当前系统时间**

```sql
SELECT NOW();	‘2020-05-18 23:41:41‘
```

**获取指定的部分,年月日，小时,分钟，秒**

```sql
#获取某日期格式的年份月份
SELECT YEAR(`hiredate`) 年 FROM `employees`
SELECT MONTH(`hiredate`) 月 FROM `employees`
```

**DATEDIFF(日期一，日期二)，获取两个日期之间相差多少天**

```sql
SELECT DATEDIFF(MAX(`hiredate`),MIN(hiredate)) FROM `employees`
```

**str_to_date将日期格式的字符转换成指定格式的日期**

```sql
#应用场景有时用户输入的格式不一样，可以对应改
SELECT STR_TO_DATE('10-9 2012','%c-%d %y')    2020-10-09
```

**date_format 将日期转换成字符**

```sql
#将日期按照指定的格式转换成字符
SELECT DATE_FORMAT(CURDATE(),'%y年%c月%d日')   20年5月18日
```

### 流程控制函数

**if(判断条件,成立,不成立)**

```sql
#判断是否有奖金
SELECT `last_name`,`commission_pct` ,IF(`commission_pct` IS NULL,'没奖金','有奖金' ) 备注
FROM `employees`
```

**case使用的两中方法**

**使用一：**

```sql
#用于判断相等
#查询部门id 如过id =30 salary是1.1倍
SELECT salary,`department_id`,
CASE `department_id`
WHEN 30 THEN salary*1.1
WHEN 40 THEN salary*1.2
WHEN 50 THEN salary*1.3
ELSE salary
END 新工资
FROM `employees`

```

**使用二：**

```sql
#用于判断区间范围
#查询工资大于10000 为A
SELECT salary,last_name,
CASE 
WHEN salary>10000 THEN 'A'
WHEN salary>80000 THEN 'B'
ELSE 'c' 
END 排名
FROM `employees`
```

------

### 分组函数

**语法：**

```sql
统计使用
分类 sum求和，avg平均值，max最大值，min最小值，count计算个数
特点：
	1.sum，avg一般用于处理数值行
	2.以上所有分组函数都忽略null值
	
输出：一行一列
```

**简单使用**

```sql
SELECT SUM(`salary`)
SELECT AVG(`salary`)
SELECT MAX(`salary`)
SELECT MIN(`salary`)
SELECT COUNT(`salary`)
```

**和distinct搭配使用** (distinct去重)

```sql
SELECT SUM(DISTINCT(`salary`)),SUM(salary)
FROM `employees`  		结果不同
```

**count(*)个count(1)**区别

```sql
#count(*) 就是如果多个列的同一行内容都为null不计入计算，否则记录计算
#count(1) 就是记录行数
#实际中一般是用count(*) ，效率差不多
```

------



## 分组查询

**语法：**

```sql
语法：
	select 分组函数，列（要求出现在group by的后面）
	from 表明
	【where 赛选条件】
	group by 分组列表
	【order by子句】
	
注意：
	查询列表必须特殊，要求是分组函数和group by后出现的字段
	
	
特点：
	1.分组查询中的筛选条件分为两类
				数据源		  位置		   关键字       
	分组前帅选	原始表		   group by前	where
	分组后帅选   分组后的结果集	 group by后	 having
	
	1.分组函数作条件肯定是放在having
	2.能用分组前筛选的，就有限考虑使用分组前筛选
	
	2.group by 子句支持单个字段分组，多个字段分组(多个字段之间没有顺序要求)
	3.也可以添加排序(排序放在分组查询的最后)
```

**查询每个部门的平均工资**

```sql
SELECT AVG(salary),`job_id`
FROM `employees`
GROUP BY job_id;
```

**查询邮箱中包含a字符的，每个部门的平均工资**

```sql
SELECT AVG(salary) 平均工资,`department_id`,email
FROM `employees`
WHERE email LIKE '%a%'
GROUP BY `department_id`
```

**添加分组后的筛选，使用having，对于查询的结果集进行筛选**

**询哪个部门的员工个数>2**

```sql
SELECT COUNT(*) 员工个数,`department_id`
FROM `employees`
GROUP BY department_id
HAVING 员工个数>2
```

**查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内**

```sql
SELECT MIN(salary) 最低工资,`manager_id`
FROM `employees`
WHERE manager_id IS NOT NULL
GROUP BY `manager_id`
HAVING 最低工资>=6000
```

------

## 连接查询

**语法：**

```sql
含义：多表连接，当查询的字段来自多个表示，就会用到连接查询
注意：如果为表起了别名，则查询的字段就不能使用原来的表名去限定
```

**查询员工名和对应的部门名**

```sql
SELECT `last_name`,`department_name`
FROM `employees` e,`departments` d
WHERE e.`department_id`=d.`department_id`;
```

