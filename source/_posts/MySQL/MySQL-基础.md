---
title: MySQL基础
date: 2021-06-20 00:00:00
author: 神奇的荣荣
summary: ""
tags: MySQL
categories: MySQL
---

Mysql数据库

# MySql基础

## 数据库的好处

可以持久化到本地  
结构化查询

***

## 主流的数据库介绍（了解）

Sql server 数据库  
是微软，.net程序员最爱，中型和大型项目，性能高

Oracle数据库  
是甲骨文的，java程序员（必学），大型项目，特点是适合处理复杂业务逻辑。

Mysql数据库  
是sun公司，属于甲骨文公司，中型大型项目，特点：并发性好。对简单的sql处理效率高。

db2数据库  
是ibm公司，处理海量数据，大型项目。很强悍。

Informix数据库  
是ibm公司。在银行系统，安全性高

Sybase数据库

***

## mysql的优点

开源，免费成本低  
性能高，移植性也好  
体积小，便于安装

## mysql数据库的安装和配置

看MySQL-5.7.17安装与配置.docx

## mysql的基本使用

（1）连接到mysql
Cmd>mysql -h 主机名 -u 用户名 -p 密码 回车
举例：

说明：
如果你没有写-h localhost 默认是连接本地

如果你需要远程登录到另一个mysql,则需要修改配置。
一般情况下部让远程登录。

（2）sql服务的退出
exit或ctrl+c

（3）关闭和启动mysql服务
界面操作：

命令模式：
启动：net stop mysql
停止：net start mysql
举例：

说明：这里mysql不是固定的，是安装时取的服务名称。

## mysql数据库三层架构介绍

专业术语

Db：  
数据库（database）存储数据的”仓库”。它保存了一系列有组织的数据。

Dbms：  
数据库管理系统（database management system）。数据库是通过dbms创建和操作的容器。

Sql:  
结构化查询语言（Structure Query Langeuage）：专门用来与数据库通信的语言。

数据库服务器，数据库和表的关系如图所示：


示意图：

## mysql常见命令

```
1.查看当前所有的数据库
show databases;

2.打开指定的库
use 库名;

3.查看所在的数据库名
select database();

4.查看当前库的所有表
show tables;

5.查看其它库的所有表
show tables from 库名;

6.创建表
create table user(id int, name varchar(20));

7.查看表结构
desc 表名;		

8.查看服务器的版本
方式一：登录到mysql服务端
Select version();
方式二：没有登录到mysql服务端
Mysql --version或mysql --V

9.查看当前时区
SHOW VARIABLES LIKE 'time_zone'
SET time_zone='+9:00';
```

## Mysql的语法规范

1.不区分大小写，单建议关键字大写，表名，列名小写。  
2.每条语句最好用分号结尾。  
3.每条命令根据需要，可以进行缩进或换行  
4.注释  
单行注释：两种  
第一种：#注释文字  
第二种：-- 注释文字  
多行注释：/* 注释文字 */



# 常用数据类型

## 数值型

2.1.1.整型



特点：
（1）如果不设置无符号还是有符号，默认是有符号，如果想设置无符号，需要添加unsigned关键字
（2）如果插入的数值超出了整型范围，会报out of range异常，并且插入临界值
（4）如果不设置长度，会有默认的长度，长度代表了显示的最大宽度，如果不够用0在左边填充，但必须搭配zerofill使用！


2.1.2.小数


浮点型：
Float(m, d)，double(m, d)
4			8

定点型：
Dec(m, d)，decimal(m, d)
M+2		m+2

特点：
（1）
M：整数部位+小数部位（总长度）
D：小数部位
如果超出范围插入临界值

（2）
M和D都可以省略
如果是decimal，则m默认为10，d默认为0
如果是double和float，则会随着插入的数值的精度来决定精度

（3）定点型的精确度较高，如果要求插入数值的精度较高，如：货币运算等则考虑使用


2.2.字符型

较短的文本：
Char
Varchar

特点：

写法		m的意思			特点			空间的耗费		效率
Char	char(m)		最大的字符数	固定长度		比较耗费		高
Varchar	varchar(m)	最大的字符数	可变长度的字符	比较节省		低


较长的文本：
Text
Blob（较大的二进制）


Enum类型：
说明:又称为枚举类型哦，要求插入的值必须属于列表中指定的值之一。
如果列表成员为1~255，则需要1个字节存储
如果列表成员为255~65535，则需要2个字节存储
最多需要65535个成员


Set类型：
说明：和Enum类型类似，里面可以保存0~64个成员。和Enum类型最大的区
别是：SET类型一次可以选取多个成员，而Enum只能选一个
根据成员个数不同，存储所占的字节也不同


2.3.日期型


总结：
Date：只保存日期，并没有保存到时分秒
Time：只保存时间（时分秒）
Year：只保存年

Datetime：保存日期+时间
Timestamp：保存日期+时间


Datetime VS Timestamp

字节	范围		时区等的影响
Datetime 	8		1000-9999		不受
Timestamp	4		1970-2038		受

# DQL语言（select）

## 基础查询

### 语法

select 查询列表 from 表名

### 特点
查询列表开源是字段，常量表达式，函数，也可以有多个。  
查询结构是一个虚拟表。

### 示例

1.查询单个字段  
Select 字段名 from 表名

2.查询多个字段  
Select 字段名,字段名 from 表名

3.查询所有的字段  
Select * from 表名

4.查询常量  
Select 常量值  
注意：字符型和日期型常量值必须用单引号用起来，数值类不需要

5.查询函数 
Select 函数名（实例参数）;

6.查询表达式  
Select 100*10;

7.取别名  
两种方式  
As  
Select last_name as 姓名 from 表名  

空格  
Select last_name 姓名 from 表名

8.去重  
Select distinct 字段名 from 表名

9.+  
作用：加法运算  
Select 数值+数值;直接运算  
Select 字符+数值，先将字符转成数值，如果转换成功，则继续运算，否则转换成0，	再做运算。

10.补充Concat函数  
功能：拼接字符  
Select concat(字符1,字符2,字符3,....)

11.补充ifnull函数  
Select ifnull(name, 0) from user

12.补充isnull函数  
Select isnull(name) from user  
功能：拍的某个字段是否为null，如果是返回1，如果不是返回0


## 条件查询

### 语法

Select 查询列表  
From 表名  
Where 筛选条件

### 筛选条件的分类

（1）简单条件运算符  

```> < = <> != >= <= <=>```

Select * from user where age>15;

Select * from user where age<15;

Select * from user where age<>15;

<=>:安全等于，可以判断普通数值，也可以判断是否为null  
Select * from user where age<=> null;

（2）逻辑运算符

```and or not```

And:  
SELECT last_name FROM employees WHERE salary>=10000 AND salary<=20000;

Or:  
SELECT * FROM employees WHERE department_id<90 OR department_id>110 OR salary>15000;

Not:  
SELECT * FROM employees WHERE NOT(department_id>=90 AND department_id<=110) OR salary>15000;

（3）模糊查询

```Like, between and, in, is null, is not null ```

like：  
Select * from user where name like’%a%’;  
Select * from user where name like’_a%’;


between and:  
作用：在哪两个数之间  
Select * from user where age between 10 and 20;

in:  
Select * from user where id in(1,2,3);

is null:  
作用：判断是否为null  
Select * from user where age is null;

is not null:  
作用：判断是否不为空  
Select * from user where age is not null;

## 排序查询

### 语法

Select * from 表名  
[Where 筛选条件]  
Order by 排序列表[asc desc]

### 特点
（1）asc代表升序，desc代表降序，如果不写是升序。  
（2）order by子句可以支持单个字段，多个字段，表达式，函数，别名  
（3）order by子句一般是放查询语句的最后面，limit子句除外

### 案例

#案例：查询员工信息，要求工资冲高到底排序  
```sql
SELECT * FROM employees ORDER BY salary DESC;
```

#案例：按年薪的高低显示员工的信息和年薪【按表达式排序】  
```sql
SELECT *, salary*12*(1+IFNULL(commission_pct, 0)) AS 年薪 
FROM employees 
ORDER BY 年薪 DESC;
```

#案例：查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号升序 
``` sql
SELECT * FROM employees 
WHERE email LIKE '%e%' 
ORDER BY LENGTH(email) DESC, department_id ASC;
```

## 常见函数

功能：类似java中的方法  
好处：提高重用性和隐藏实现细节  
调用：select 函数名(参数列表)  

### 字符函数

LENGTH：  
作用：获取参数的字节个数  
SELECT LENGTH('oyr');  
SELECT LENGTH('欧阳荣');


CONCAT：  
作用：拼接字符串  
SELECT CONCAT('abc', 'jkl');  
SELECT CONCAT(first_name, last_name) out_put FROM employees;


UPPER：  
作用：字符串变大写  
SELECT UPPER("asc");


Lower：  
作用：字符串变小写
SELECT LOWER("ASC");


Substr:  
作用：截取字符串，两种使用方式
``` sql
#第一种：截取从指定索引处后面所有字符  
SELECT SUBSTR("欧阳荣真的帅哦", 4);  
结果：真的帅哦

#第二种：截取从指定索引处指定字符串的字符  
SELECT SUBSTR("欧阳荣不是一般帅哦", 1, 3);  
结果：欧阳荣

# 案例1：姓名首字母大写，其他字符小写然后用_拼接，显示出来
SELECT CONCAT(UPPER(SUBSTR(last_name, 1, 1)), '_', LOWER(SUBSTR(last_name, 2))) FROM employees;
```

Trim:
作用：去除两边空格或去除两边指定字符
``` sql
#去除两边空格：
SELECT LENGTH(TRIM('   欧阳荣 '));
结果：欧阳荣

#去除两边指定字符：
SELECT TRIM('a' FROM 'aaaaaaaa欧阳aaa荣aaaaaa');
结果：欧阳aaa荣
```

Lpad：  
作用：lpad 用指定的字符实现左填充指定长度  
SELECT LPAD('欧阳荣', 10, 'a') out_put;

Rpad：  
作用：用指定的字符实现右填充指定长度  
SELECT LPAD('欧阳荣', 12, 'ab') out_put;

Replace：  
作用：替换字符串  
SELECT REPLACE('赵吊彬是zz赵吊彬赵吊彬赵吊彬', '赵吊彬', '李执志');

Instr:  
作用：获取子串第一次出现的索引
SELECT INSTR('欧阳荣多对多', '欧阳');
结果为：1

### 数学函数

Round:  
作用：四舍五入
```
第一种使用：
SELECT ROUND(1.65);
结果：2

第二种使用：
SELECT ROUND(1.657, 2);
结果：1.66
```

Ceil：  
作用：向上取整  
SELECT CEIL(1.52);  
结果：2


Floor：  
作用：向下取整  
SELECT FLOOR(9.99);  
结果：9

Truncate：  
作用：截断  
SELECT TRUNCATE(10.19, 1);  
结果：10.1


Mod：  
作用：取余  
SELECT MOD(10, 3);  
结果：1

Rand：  
作用：获取随机数，返回0-1之间的小数  
SELECT RAND();

### 日期函数

NOW：  
作用：返回当前系统日期+时间  
SELECT NOW();

Curdate：  
作用：返回当前系统日期，不包含时间  
SELECT CURDATE();

Curtime：  
作用：返回当前时间，不包含日期  
SELECT CURTIME();

Year：  
作用：获取年  
Select year(now());  
SELECT YEAR('1999-1-1') 年;

Month:  
作用：获取月  
SELECT MONTH(NOW()) 月;

Day:  
作用：获取日  
Select DAY(now()) 日;


日期格式符：





Str_to_date:  
作用：将日期格式的字符转换成指定格式的日期  
SELECT STR_TO_DATE('1999-6-2', '%Y-%c-%d') out_put;  
SELECT * FROM employees WHERE hiredate=STR_TO_DATE('4-3 1992', '%c-%d %Y');


Date_format：  
作用：将日期转换成字符  
SELECT DATE_FORMAT(NOW(), '%y年%m月%d日') AS out_put;

案例：查询有奖金的员工名和入职日期（xx月/xx日 xx年）
SELECT last_name, DATE_FORMAT(hiredate, '%M月/%d日 %y年') 
FROM employees 
WHERE commission_pct IS NOT NULL;

Datediff：  
作用：返回两个日期相差的天数  
SELECT DATEDIFF(NOW(), '1999-06-24');


### 其他函数

VERSION：  
作用：查看当前mysql版本号  
SELECT VERSION();

DATABASE：  
作用：查看当前打开的数据库  
Select DATABASE();

USER：  
作用：查看当前登录的用户  
Select USER();

### 流程控制函数

If：  
作用：判断真假  
SELECT IF(10<5, '小', '大');

Case:  
作用：判断  
语法：  
case 要带的字段或表达式  
when 常量1 then 要显示的值1或语句1;  
when 常量1 then 要显示的值1或语句1;  
.....  
else 要显示的值n或语句n  
End

``` sql
案例：查询员工的工资，要求
部门号=30,显示的工资为1.1倍
部门号=40，显示的工资为1.2倍
部门号=50，显示的工资为1.3倍
其他部门，显示的工资为原工资

SELECT last_name, salary 原工资, 
CASE department_id
WHEN 30 THEN salary*1.1
WHEN 40 THEN salary*1.2
WHEN 50 THEN salary*1.3
ELSE salary
END AS 新工资
FROM employees;

案例：查询员工的工资的情况
如果工资>20000，显示A级别
如果工资>15000，显示B级别
如果工资>10000,显示C级别
否则，显示D级别

SELECT last_name,salary,
CASE
WHEN salary>20000 THEN 'A'
WHEN salary>15000 THEN 'B'
WHEN salary>10000 THEN 'C'
ELSE 'D'
END AS 级别
FROM employees;
```

## 分组

### 分组函数

sum:
作用：求和
SELECT SUM(salary) FROM employees;


max:
作用：最大值
Select max(salary) from employees;


min：
作用：最小值
SELECT MIN(salary) FROM employees;


avg：
作用：平均数
SELECT AVG(salary) FROM employees;


count：
作用：计算个数
Select count(*) from user;


注意事项：
sum，avg一般用于数值型
max，min，count可以处理任何类型
以上分组函数都忽略null值

### 分组查询

语法：
select 分组函数，列（要求出现在group by的后面）
from 表
[where 筛选条件]
group by 分组的列表
[having 分组后的筛选]
[order by 子句]

注意：
查询列表必须特殊，要求是分组函数或group by后出现的字段
分组后筛选在group by子句的后面使用 having 关键字
分组函数做条件肯定是放在having子句中
能用分组前筛选，就优先使用分组前筛选
group by 子句支持单个字段分组，多个字段分组（用逗号隔开）

简单分组：
```sql
#案例1：查询每个部门的平均工资
SELECT department_id,AVG(salary) 平均工资 FROM employees GROUP BY department_id; 

#案例2：查询每个工种的最高工资
SELECT job_id, MAX(salary) FROM employees GROUP BY job_id;

#案例3：查询每个位置上的部门个数
SELECT location_id, COUNT(*) FROM departments GROUP BY location_id;
```

添加筛选条件：
```sql
#案例1：查询邮箱中包含a字符的，每个部门的平均工资
SELECT department_id, AVG(salary) 
FROM employees 
WHERE email LIKE '%a%' 
GROUP BY department_id;

#案例2：查询有奖金的每个领导手下的最高工资
SELECT manager_id, MAX(salary)
FROM employees 
WHERE commission_pct IS NOT NULL 
GROUP BY manager_id;

#案例3：查询哪个部门的员工个数>2
Select department_id, count(*) 
from employees
group by department_id having count(*)>2;

#案例4：查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
SELECT job_id, MAX(salary) 
FROM employees
GROUP BY job_id HAVING MAX(salary)>12000;

#案例5：查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个
SELECT manager_id, MIN(salary) 
FROM employees 
WHERE manager_id>102 
GROUP BY manager_id HAVING MIN(salary)>5000;

#案例6：按员工姓名长度分组，查询每一组的员工个数，筛选员工个数>5的有哪些
SELECT last_name, LENGTH(last_name), COUNT(*) 员工个数 
FROM employees 
GROUP BY LENGTH(last_name) HAVING COUNT(*)>5;
```

## 连接查询

什么是连接查询：  
又称多表查询，当查询的字段来自多个表时，就会用到连接查询。

笛卡尔积:  
笛卡尔积乘积现象：表1 有m行，表2有n行，结果=m*n行


### 分类
	
按年代分类：  
sql92标准：仅仅支持内连接  
sql99标准【推荐】：支持内连接+外连接（左外和右外）+交叉连接

按功能分类：
- 内连接：  
    - 等值连接  
    - 非等值连接  
    - 自连接  
- 外连接：  
    - 左外连接  
    - 右外连接  
    - 全外连接（mysql不支持）  
- 交叉连接：  

### 语法

Select 查询列表
From 表1 别名 【连接类型】
Join 表2 别名
On 连接条件
【where 筛选条件】
【group by 分组】
【having 分组后筛选】
【order by 排序列表】

### 内连接

Sql192语法：
```
（1）等值连接
案例：
查询女神名和对应男生名
SELECT b.name,bs.boyName 
FROM beauty b, boys bs 
WHERE b.boyfriend_id = bs.id;

查询员工名和对应的部门名
SELECT e.last_name, e.department_id, d.department_name 
FROM employees e, departments d 
WHERE e.department_id=d.department_id;

查询有奖金的员工名，部门名
SELECT e.`last_name`, d.`department_id`, e.`commission_pct` 
FROM employees e,departments d 
WHERE e.`department_id`=d.`department_id` AND commission_pct IS NOT NULL;

查询每个工种的工种名和员工的个数，并且按员工个数降序
SELECT j.job_title, COUNT(*) 
FROM employees e, jobs j 
WHERE e.`job_id`=j.`job_id` 
GROUP BY j.`job_title` ORDER BY COUNT(*) DESC;

（2）非等值连接
案例：
查询员工的工资和工资级别
SELECT e.`salary`, j.`grade_level` 
FROM employees e, job_grades j
WHERE e.`salary` BETWEEN j.`lowest_sal` AND j.`highest_sal` AND j.`grade_level`='E';

（3）自连接
查询员工名和上级的名称
SELECT e1.last_name, e2.`last_name` 
FROM employees e1,  employees e2
WHERE e1.`manager_id`=e2.`employee_id`;

三表连接：查询员工名，部门名和所在的城市
SELECT e.last_name, d.`department_name`, l.`city` 
FROM employees e, departments d, locations l
WHERE e.`department_id`=d.`department_id` AND d.`location_id`=l.`location_id`;
```

sql99语法：Inner 
```sql
查询员工名，部门名
SELECT e.last_name, d.`department_name` FROM employees e 
INNER JOIN departments d ON e.`department_id`=d.`department_id`;

查询名字中只能包含e的员工名和工种名
SELECT e.last_name, j.`job_title` FROM employees e 
INNER JOIN jobs j ON e.`job_id`=j.`job_id`
WHERE e.`last_name` LIKE '%e%';

查询部门个数>3的城市名和部门个数
SELECT l.`city`, COUNT(*) FROM locations l 
INNER JOIN departments d ON l.`location_id` = d.`location_id`
GROUP BY l.`city` HAVING COUNT(*)>3;

查询工资级别的个数>2的个数，并按工资级别降序
SELECT j.`grade_level`, COUNT(*) 个数 FROM job_grades j
INNER JOIN employees e ON e.`salary` BETWEEN j.`lowest_sal` AND j.`highest_sal`
GROUP BY j.`grade_level` HAVING 个数>2 
ORDER BY 个数 DESC;
```

### 外连接

应用场景：用于查询一个表中有，另一个表没有

1.外连接的查询结构为主表中的所有记录  
如果从表中有和它匹配的，则显示匹配值  
如果从表中没有和它匹配的，则显示为null  
外连接查询结果=内连接结果+主表中有而从表没有的数据
	
2.左外连接，left join左边的是主表  
右外连接，right join右边的是主表  

3.左外和右外交换两个表的顺序，可以实现同样的效果

（1）左连接：  
语法：Left 【outer】
```sql
查询没有男朋友的女神名
SELECT * FROM beauty b 
LEFT JOIN boys bo ON b.`boyfriend_id`=bo.`id`
WHERE bo.`id` IS NULL;

查询哪个部门没有员工
SELECT d.`department_id`, d.`department_name` FROM departments d
LEFT JOIN employees e ON d.`department_id`=e.`department_id`
WHERE e.`employee_id` IS NULL
GROUP BY d.`department_id`;
```
（2）右连接  
语法：Right 【outer】
```sql
#查询没有男朋友的女神名
SELECT * FROM boys bo 
RIGHT JOIN beauty b ON b.`boyfriend_id`=bo.`id`
WHERE bo.`id` IS NULL;

查询哪个部门没有员工
SELECT d.`department_id`, d.`department_name` FROM employees e 
RIGHT JOIN departments d ON e.`department_id`=d.`department_id`
WHERE e.`employee_id` IS NULL 
GROUP BY d.`department_id`;
```

（3）全外（mysql不支持）  
语法：Full 【outer】

### 交叉连接

语法：Cross
```sql
女神男神表交叉连接
SELECT b.*, bo.* FROM beauty b
CROSS JOIN boys bo;
```

## 子查询

### 含义

出现在其他语句的select语句，称为子查询或内出现  
内部嵌套其他select语句的查询，称为外查询或主查询

### 分类：

按子查询出现的位置：  
1）select 后面  
2）from 后面  
3）wehre 或 having后面  
4）exists后面（相关子查询）  

按结果集的行列数不同：  
1）标量子查询（结果集只有一行一列）  
2）列子查询（结果集只有一列多行）  
3）行子查询（结果集有多行多列）  
4）表子查询（结果集一般为多行多列）

### 用法

```> < <> = ```

```sql

谁的工资比 Abel 高？
SELECT last_name, salary
FROM employees WHERE salary>(
	SELECT salary FROM employees WHERE last_name='Abel'
);

返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id和工资
SELECT last_name,job_id,salary FROM employees 
WHERE job_id =(
	SELECT job_id FROM employees WHERE employee_id=141
) AND salary>(
	SELECT salary FROM employees WHERE employee_id=141
);

案例3：返回公司工资最少的员工的last_name,job_id和salary
SELECT last_name, job_id, salary FROM employees 
WHERE salary = (
	SELECT MIN(salary) FROM employees
);

In（常用）：
作用：可以是子查询中的任一一个
返回location_id是1400或1700的部们中的所有员工姓名
SELECT last_name
FROM employees
WHERE department_id IN (
	SELECT department_id FROM departments WHERE location_id IN(1400, 1700)
);

Any（不常用）：
作用：任一一个

返回其他部门中比job_id为‘IT_PROG’部门任一工资低的员工的员工号，姓名，job_id,以及salasy
SELECT employee_id, last_name, job_id,salary FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary FROM employees WHERE job_id='IT_PROG'
) AND department_id<>'IT_PROG';

All（不常用）：
作用：所有的

返回其他部门中比job_id为‘IT_PROG’部门所有工资低的员工的员工号，姓名，job_id,以及salasy
SELECT employee_id, last_name, job_id,salary FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary FROM employees WHERE job_id='IT_PROG'
) AND department_id<>'IT_PROG';
```

## 分页查询（常用）

应用场景：当要显示的数据，一页显示不全，需要分页提交sql请求。

### 语法：

select 参数列表  
from 表  
left join 表名 on 连接条件  
where 筛选条件  
group by 分组字段 having 分组后筛选  
order by 排序字段  
limit 【offset,】 size；

offset 要显示条目的起始索引（起始索引从0开始）  
size 要显示的条目个数
特点：limit语句放在查询语句的最后

### 案例：

查询第二页，一页10条记录  
Select * from user limit 10, 10;

## Union联合查询

union 联合 合并：将多条查询语句的结果合并成一个结果

应用场景：要查询的结果来自于多个表，且多个表没有证据的连接关系，单查询的信息一致时

### 语法

查询语句1
union
查询语句2
union
....

特点：  
1.要求多条查询语句的查询列数是一致的  
2.要求多条查询语句的查询的每一列的类型和顺序最好一致  
3.union关键字默认去重，如果使用union all 可以包含重复项

### 案例

```sql
--Union
Select id, name from user
Union
Select id,name from emp;

--Union all
Select id, name from user
Union all
Select id,name from emp;
```

# DML语言

数据库操作语言：  
插入：insert  
修改：update  
删除：delete

## 插入语句

### 方式1

语法：  
Inser into 表名(列名, ...) values(值1, ...);

使用：  
INSERT INTO users VALUES(1, '欧阳荣', '男', 166842.5);  
INSERT INTO users(id, NAME, sex, gz) VALUES(2, '赵云彬', '欧阳荣', NULL);

### 方式2

语法：  
insert into 表名 set 列名=值,列名=值,....;

使用：  
INSERT INTO boys SET id=6, boyName='侄子', userCp=147258;

### 两种开发方式pk

insert方式一次可以加多条，第二种方式不行  
INSERT INTO boys VALUES(7, '欧阳荣', 10000000), (8, '欧阳荣', 10000000);

insert方式可以用子查询  
INSERT INTO boys SELECT 10, 'ii', 150044;

## 修改语句

### 修改单表的记录（常用）

语法：
Update 表名 set 列=新值, 列=新值.... where 筛选条件;

案例：
修改beauty表中姓唐的女神电话为1389988889
UPDATE beauty SET phone='1389988889' WHERE NAME LIKE '唐%';

修改boys表中id号为2的名称为张飞，魅力值10
UPDATE boys SET boyName='张飞', userCP=10 WHERE id=2;


4.2.2.修改多表的记录（补充）
192语法：
UPDATE 表名 别名,表名 别名 
SET 列=值,列=值 ... 
WHERE 连接添加 AND 筛选条件



199语法:
UPDATE 表名 别名
INNER|LEFT|RIGHT| JOIN 表名 别名 ON 连接条件
SET 列=值，列=值 ...
WHERE 筛选条件


案例（以下实现都为199语法）：

修改张无忌的女朋友的手机号为114
UPDATE boys bo 
INNER JOIN beauty b ON bo.id=b.boyfriend_id 
SET phone='114' 
WHERE bo.boyName='张无忌';



修改没有男朋友的女神的男朋友的编号都为2号
UPDATE boys bo 
RIGHT JOIN beauty b ON bo.id=b.boyfriend_id 
SET b.boyfriend_id=2 
WHERE bo.id IS NULL;



4.3.删除语句

4.3.1.方式1：delete
语法：
（1）单表删除（重要）：
Delete from 表名 where 筛选条件;

（2）多表删除（补充）
192：
Delete 表1别名, 表2别名 
from 表一 别名, 表二别名
Where 连接条件 and 筛选条件

199：
Delete 表1别名, 表2别名
From 表一 别名
inner|Left|right| join 表2 别名 on 连接条件
Where 筛选条件


案例：

删除手机号以9结尾的女神信息
DELETE FROM beauty WHERE phone LIKE '%9';


删除张无忌的女朋友的信息
DELETE b FROM beauty b INNER JOIN boys bo ON bo.id=b.boyfriend_id
WHERE bo.`boyName`='张无忌';

删除黄晓明的信息以及他女朋友的信息
DELETE b, bo FROM boys bo 
INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id` 
WHERE bo.`boyName`='黄晓明';

4.3.2.方式2：truncate
语法：
Truncate table 表名;

特点：
不能加筛选条件，也叫做清空。

4.3.3.delete pk truncate（面试题）

1.delete可以加where条件，truncate不可以
2.truncate删除，效率高一丢丢
3.假如删除的表中有自增长，使用delete删除后，再插入数据，自增长列的值从断点开始。
如果使用truncate删除后，再插入值，自增长列的值从1开始。
4.truncate删除没有返回值，delete删除有返回值
5.truncate删除不能回滚，delete删除可以回滚。


5.DDL语言
数据库定义语言，也就是库和表的管理

5.1.库的管理

5.1.1.库的创建

语法：
create database [if not exists] 库名 [character set 字符集]

案例：

创建一个db_book库
CREATE DATABASE IF NOT EXISTS db_oyr CHARACTER SET utf8;


5.1.2.库的修改

修改库名（现在不能用了）：
语法：
rename database 库名 to 新库名;

案例
把db_oyr库名改成my_oyr
RENAME DATABASE db_oyr TO my_oyr;


修改字符集：
语法：
alert database 库名 character set 字符集;

案例：

修改db_oyr的字符集为gbk
ALTER DATABASE db_oyr CHARACTER SET gbk;

5.1.3.库的删除

语法：
DROP DATABASE [IF EXISTS] 库名;

案例:
删除db_oyr
DROP DATABASE db_oyr; 

5.2.表的管理

5.2.1.表的创建

语法：
create table 表名(
	列名 列类型【（类型长度） 约束】,
	列名 列类型【（类型长度） 约束】,
	列名 列类型【（类型长度） 约束】,
	...
	列名 列类型【（类型长度） 约束】
);

案例：
创建book表
CREATE TABLE book(
	id INT,#编号
	bName VARCHAR(20),#图书名
	price DOUBLE,#价格
	authorId INT,#作者编号
	publishDate DATETIME#出版日期
);


5.2.2.表的修改

语法：
Alter table 表名 add|drop|modify|change column 列名 【列类型 约束】

（1）修改列名

ALTER TABLE book CHANGE COLUMN bName myName VARCHAR(25);

（2）修改列的类型或约束

ALTER TABLE my_book MODIFY COLUMN myName INT;

（3）添加新列

ALTER TABLE book ADD COLUMN oo VARCHAR(20);

（4）删除列

ALTER TABLE book DROP COLUMN oo;

（5）修改表名

ALTER TABLE book RENAME TO my_book;


5.2.3.表的删除

语法：
DROP TABLE 【IF EXISTS】 表名;

案例：

删除my_book表
DROP TABLE IF EXISTS my_book;


5.2.4.表的复制
1)复制表的结构
语法：
Create table 新表名 like 被复制的表名;

复制boys的结构
CREATE TABLE copy LIKE boys;

2)复制表的结构和数据
语法：
Create table 新表名
Select * from 被复制表的表名

复制boys表的结构和数据
CREATE TABLE copy2 
SELECT * FROM boys;

复制boys部门数据和部分列
CREATE TABLE copy3
SELECT id,boyName FROM boys WHERE id<5; 
SELECT * FROM copy3;

复制几个字段的结构
CREATE TABLE copy4
SELECT id, boyName FROM boys
WHERE 1=2;


5.3.常见的约束

5.3.1.约束介绍

六大约束：
NOT NULL：非空约束，用于保证该字段的值不能为空
UNIQUE：唯一约束，用于保证该字段的值有唯一性，可以为空
PRIMARY KEY：主键约束，用于保证该字段的值有唯一性，并且不能为空
CHEACK：检查约束【MYSQL中不支持，没有效果】
FOREIGN KEY：外检约束，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值，用于引用主表中某列的值
DEFAULT：默认值，用于保证字段有默认值


添加约束的时机：
创建表时
修改表时


约束的添加分类：
列级约束：
六大约束语法上都支持，单外检约束没有效果
表级约束：
除了非空，默认，其他都支持

列级约束 VS 表级约束
位置			支持的约束类型					是否可以起别名
列级：	列的后面		都支持，但外键没有效果			不可以
表级：	所有列的下面	默认和非空不支持，其他都支持	可以主键（没有效果）




5.3.2.添加列级约束

新建一个库students，在里面测试添加列级约束



# 新建主修表
create table major(
	id bigint(20) primary key, #主键约束
	major_name varchar(25) NOT NULL #非空约束
)

# 新建学生表
create table student(
	id bigint(20) primary key, #主键约束
	stu_name varchar(25) NOT NULL, #非空约束
	sex char(2) CHECK(sex = '男' or sex = '女'), #检查约束
	age int default 20, #默认约束
	card varchar(25) unique,#唯一约束
	major_id bigint(20) REFERENCES major(id) #外键约束
)

经过测试后发现，检查约束是无效的，而且外检约束也是无效的。
其他都是ok的。


5.3.3.添加表级约束

语法：
在表字段的最下面
[constraint 约束名] 约束类型(字段名)
表级约束添加外键是有效的。

指定约束名创建约束：
CREATE TABLE student (
  id bigint(20) NOT NULL,
  stu_name varchar(25) NOT NULL,
  sex char(2) DEFAULT NULL,
  age int(11) DEFAULT '20',
  card varchar(25) DEFAULT NULL,
  major_id bigint(20) DEFAULT NULL,
  CONSTRAINT pk PRIMARY KEY(id), #主键，约束名不生效
  CONSTRAINT un_card UNIQUE KEY(card), #唯一约束
CONSTRAINT ch_sex CHECK(sex = '女' or sex ='男'),
  CONSTRAINT `pk_student_major` FOREIGN KEY (major_id) REFERENCES major(id)
);




不指定约束名创建约束：
CREATE TABLE student (
  id bigint(20) NOT NULL,
  stu_name varchar(25) NOT NULL,
  sex char(2) DEFAULT NULL,
  age int(11) DEFAULT '20',
  card varchar(25) DEFAULT NULL,
  major_id bigint(20) DEFAULT NULL,
  PRIMARY KEY(id), #主键，约束名不生效
  UNIQUE KEY(card), #唯一约束
	CHECK(sex = '女' or sex ='男'),
  FOREIGN KEY (major_id) REFERENCES major(id)
);



5.3.4.主键约束 VS 唯一约束

保证唯一性	是否允许为空	一个表中有几个	是否允许出现组合
主键		√			×				最多一个		√，但不推荐
唯一		√			×				可以多个		√，但不推荐

唯一约束可以允许为空，但null只能出现一次。
组合就是组合主键或组合唯一约束。


5.3.5.外键的特点

从表：当前表
主表：被关联的表

1.要求在从表中设置外键关系
2.从表的外键列的类型和主表的关联列的类型要求一致或兼容，名称无要求
3.主表的关联列必须是一个key（主键和唯一，不然会抛出异常）
4.插入数据时，先插入主表，再插入从表，删除数据时，先删除从表，再删除主表


5.3.6.修改表时添加约束


语法：
1.添加列级约束
alter table 表名 modify column 字段名 字段类型 新约束

2.添加表级约束
alter table 表名 add [constraint 约束名] 约束类型(字段名)  [外键的引用]


添加非空约束：
列级：
alter table student modify column stu_name varchar(25) not null;


添加默认约束：
列级：
alter table student modify column age int default 20


添加主键约束：
列级：
alter table student modify column id bigint(20) primary key
表级：
alter table student add primary key(id)


添加唯一约束：
列级：
alter table student modify column card varchar(25) unique
表级：
alter table student add constraint un_card unique(card)


添加外键约束：
表级：
alter table student add constraint fk_student_major foreign key(id) references major(id)


5.3.7.修改表时删除约束

1.删除非空约束（直接不写就是删除）
alter table student modify column stu_name varchar(25)

2.删除默认约束（不写即是删除）
alter table student modify column age int default 20

3.删除主键约束
aler table 表名 drop primary key

4.删除唯一约束
alter table 表名 drop index 约束名

5.删除外键约束
alter table 表名 drop foreign key 约束名


5.4.标识列

什么是标识列？
标识列是自增长列
含义：可以不手动的插入值，系统提供默认的序列值

特点：
1.标识列必须和key值搭配（也就是说当前列必须是主键或者唯一）
2.一个表只能有一个标识列
3.标识类的类型只能是数值型
4.标识列可以通过 set auto_increment_increment=3;设置步长。可以手动插入值来设置起始值


创建表时设置标识列
create table tb_user(
	id int primary key auto_increment, #主键并且设置标识列
	name varchar(25) not null
)


修改表时设置标识列
alter table tb_user modify column id int primary key auto_increment

修改表时删除标识列
alter table tb_user modify column id int primary key


6.TCL 语言
TCL：Transaction Control Language 事物控制语言

事务：事务由单独单元的一个或多个SQL语句组成，在这个单元中，每个MySQL语句是相互依赖的。而整个单独单元作为一个不可分割的整体，如果单元中某条SQL语句一旦执行失败或产生错误，整个单元将会回滚。所有受到影响的数据将返回到事物开始以前的状态；如果单元中的所有SQL语句均执行成功，则事物被顺利执行。


6.1.MySQL 中的存储引擎

1、概念：在mysql中的数据用各种不同的技术存储在文件（或内存）中。
2、通过show engines；来查看mysql支持的存储引擎。
3、 在mysql中用的最多的存储引擎有：innodb，myisam ,memory 等。其中innodb支持事务，而myisam、memory等不支持事务


6.2.事务的ACID属性

1. 原子性（Atomicity）
原子性是指事务是一个不可分割的工作单位，事务中的操作要么执行成功，要么都执行失败。

2. 一致性（Consistency）
事务必须使数据库从一个一致性状态变换到另外一个一致性状态。拿转账为例，A有500元，B有300元，如果在一个事务里A成功转给B50元，那么不管并发多少，不管发生什么，只要事务执行成功了，那么最后A账户一定是450元，B账户一定是350元。不管转账多少次两个人的钱加起来都是800元。

3. 隔离性（Isolation）
并发执行的各个事务之间不能互相干扰。事务的隔离性是指一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的。

4. 持久性（Durability）
持久性是指一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来的其他操作和数据库故障不应该对其有任何影响。


6.3.事物的使用步骤

事物又分两种：隐式事物和显式事物
隐式事物：事物没有明显的开启和结束的标记
列如insert，update，delete

显式事物：事物具有明显的开启和结束的标记
前提：必须设置自动提交功能为禁用

查看当前自动提交功能开关
show variables like 'autocommit'

关闭自动提交功能，并不是永久的，只是当前回话被关闭
set autocommit=0;


开启事物的语法：
#步骤1：开启事物
set autocommit=0;
start transaction;#开启事物，可选的
#步骤2：编写事物中的sql语句（select insert update delete）
语句1;
语句2;
.......
#步骤3：结束事物
commit;#提交事物
rollback;回滚事物


实战操作：
初始化
create table account(
	id bigint(20) primary key auto_increment,
	acc_name varchar(25),
	money double
)
INSERT INTO account (id, acc_name, money) VALUES (1, '欧阳荣', 1500);
INSERT INTO account (id, acc_name, money) VALUES (2, '罗总', 500);





转账成功提交：执行后可以看到数据库数据已经改变了
set autocommit=0;
start transaction;
update account set money = money-500 where acc_name='欧阳荣';
update account set money = money+500 where acc_name='罗总';
commit;


转账失败回滚：执行后会发现数据并没有改变
set autocommit=0;
start transaction;
update account set money = money-500 where acc_name='欧阳荣';
update account set money = money+500 where acc_name='罗总';
rollback;



6.4.数据库的隔离级别

6.4.1.并发问题

对于同时运行的多个事务, 当这些事务访问数据库中相同的数据时, 如果没有采取必要的隔离机制, 就会导致各种并发问题:

脏读: 对于两个事务 T1, T2, T1 读取了已经被 T2 更新但还没有被提交的字段. 
之后, 若 T2 回滚, T1读取的内容就是临时且无效的.

不可重复读: 对于两个事务T1, T2, T1 读取了一个字段, 然后 T2 更新了该字段. 
之后, T1再次读取同一个字段, 值就不同了.

幻读: 对于两个事务T1, T2, T1 从一个表中读取了一个字段, 然后 T2 在该表中插
入了一些新的行. 之后, 如果 T1 再次读取同一个表, 就会多出几行。

数据库事务的隔离性: 数据库系统必须具有隔离并发运行各个事务的能力, 使它们不会相互影响, 避免各种并发问题。

一个事务与其他事务隔离的程度称为隔离级别. 数据库规定了多种事务隔离级别, 不同隔离级别对应不同的干扰程度, 隔离级别越高, 数据一致性就越好, 但并发性越弱。



6.4.2.四种事务隔离级别



Oracle支持2种事务隔离级别：READ COMMITED, SERIALIZABLE。Oracle 默认的事务隔离级别为: READ COMMITED。

Mysql支持4种事务隔离级别。Mysql 默认的事务隔离级别为: REPEATABLE READ。

6.5.回滚点使用（savepotion）

set autocommit=0;#设置不自动提交
start transaction;#开启事物
delete form account where id=1;
savepoint a;#保存点
delete from account where id=2;
rollback to a;#回滚到保存点
这上面的sql意思是只回滚到保存点，其实就是删除了id=1的数据，id=2的数据并没有被删除。


7.视图
7.1.什么是视图

含义：虚拟表，和普通表一样使用
MySQL从5.0.1版本开始提供视图功能。一种虚拟存在的表，行和列的数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的，只保存了sql逻辑，不保存查询结果。


应用场景：
1.多个地方用到同样的查询结果
2.该查询结果使用的sql语句较复杂


7.2.视图的创建

语法：
create  [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
view 视图名 as
查询语句
[WITH [CASCADED | LOCAL] CHECK OPTION]

OR REPLACE：表示替换已有视图

ALGORITHM：表示视图选择算法，默认算法是UNDEFINED(未定义的)：MySQL自动选择要使用的算法 ；merge合并；temptable临时表

[WITH [CASCADED | LOCAL] CHECK OPTION]：
表示视图在更新时保证在视图的权限范围之内
cascade是默认值，表示更新视图的时候，要满足视图和表的相关条件
local表示更新视图的时候，要满足该视图定义的一个条件即可
推荐使用WHIT [CASCADED|LOCAL] CHECK OPTION选项，可以保证数据的安全性 


实际操作：

1.查询邮箱中包含a字符的员工名，部门名，工种信息
-- 创建视图
create view view_test1
AS
SELECT
	CONCAT(e.first_name, e.last_name) 员工名,
	dept.department_name 部门名,
	j.job_title 工种信息
FROM
	employees e
LEFT JOIN departments dept ON dept.department_id = e.department_id
LEFT JOIN jobs j ON j.job_id = e.job_id
where email LIKE '%a%'
-- 查询视图
select * from view_test1


2.查询各部门的平均工资级别
create view view_test2
as 
SELECT
	dept.department_name dept_name,
	AVG(e.salary) ag
FROM
	departments dept
LEFT JOIN employees e ON dept.department_id = e.department_id
GROUP BY dept.department_name

SELECT
	vt.dept_name,
	jb.grade_level
FROM
	view_test2 vt
LEFT JOIN job_grades jb ON vt.ag BETWEEN jb.lowest_sal
AND jb.highest_sal


3.查询平均工资最低的部门
create view view_test3
as
SELECT
	dept.department_name dept_name,
	AVG(e.salary) ag
FROM
	departments dept
LEFT JOIN employees e ON dept.department_id = e.department_id
GROUP BY dept.department_name having ag is not null 
order by ag asc limit 0, 1


4.查询平均工资最低的部门名和工资
create view view_test4
as
SELECT
	dept.department_name dept_name,
	AVG(e.salary) ag
FROM
	departments dept
LEFT JOIN employees e ON dept.department_id = e.department_id
GROUP BY dept.department_name having ag is not null 
order by ag asc limit 0, 1


视图的好处：
重用sql语句
简化复杂的sql操作，不必知道它的查询细节
保护数据，提高安全性


7.3.视图的修改

方式一：如果存在，则覆盖
create  [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
view 视图名 as
查询语句
[WITH [CASCADED | LOCAL] CHECK OPTION]


方式二：指定修改视图
alter
[ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]
view 视图名
as
查询语句
[WITH [CASCADED | LOCAL] CHECK OPTION];


方式一实际操作：
create or replace view view_test4
as 
select * from employees where employee_id > 105


方式二实际操作：
alter view view_test4
as
select * from employees where employee_id > 120



7.4.视图的删除

语法：
drop view 视图名,视图名,视图名........

实际操作：
drop view view_test4, view_test3


7.5.查看视图
方式1：desc 视图名

方式2：show create view 视图名


方式1实际操作：
desc view_test2

可以看到实际上能看到的是当前视图可以查询出的字段信息


方式2实际操作：
show create view view_test2

可以看到的是当前拿到的是创建视图的逻辑sql。


7.6.视图VS表

创建语法的关键字	是否实际占用物理空间	使用
视图	create view			只是保存了逻辑sql		增删改查，一般不能增删改查

表		create table			保存了数据				增删改查



8.变量

8.1.系统变量

说明：变量由系统提供的，不是银行定义，属于服务器层面
系统变量又可以细分为全局变量和局部变量

使用的语法：
1.查看所有的系统变量
show global|[session] variables

2.查看满足条件的部分系统变量
show global | [session] variables like '%char%';

3.查看指定的某个系统变量的值
select @@global | [session] .系统变量名

4.为某个系统变量赋值
方式一：
set global | [session] 系统变量名 = 值;

方式二：
Set @@global | [session] .系统变量名 = 值;

注意：如果是全局级别，则需要加global，如果是会话级别，则需要加session，如果不写，则默认是session级别


8.1.1.全局变量实际操作

作用域：服务器每次启动将为所有的全局变量赋初始值，针对所有会话（连接）

（1）查看所有的全局变量
show global variables;

（2）查看部分的全局变量
show global variables like '%char%';

（3）查看指定的全局变量的值
select @@global.autocommit;


（4）为某个指定的全局变量赋值
set @@global.autocommit = 0;

8.1.2.局部变量实际操作

作用域：仅仅针对于当前会话（连接）有效

（1）查看所有的会话变量
show variables
show session variables

（2）查看部分的会话变量
show variables like '%char%'
show session variables like '%char%'

（3）指定查看某个会话变量
select @@character_set_client
select @@session.character_set_client

（4）为某个会话变量赋值
set autocommit=0
set @@session.autocommit=1

8.2.自定义变量

说明：变量是用户自定义的，不是由系统自动生成的。
自定义变量又可以细分成用户变量（当前会话有效），局部变量
使用步骤：
声明
赋值
使用（查看，比较，运算等）

8.2.1.用户变量

作用域：针对于电器干会话（连接）有效，同于会话变量的作用域
应用在任何地方，也就是begin end里面或begin end外面



使用语法：

（1）声明并初始化：
set @用户变量名=值 或
set @用户变量名:=值 或
select @用户变量名:=值

（2）赋值（更新用户变量的值）
方式一：通过set和select
set @变量名=值 或
set @变量名:=值 或
select @变量名:=值

方式二：通过select into
select 字段 INTO @变量名
from 表

（3）使用（查看用户变量的值）
select @用户变量名


实际操作：

声明并且初始化：
# 声明并且初始化
set @name='欧阳荣';
set @name:=10;
select @name:='罗总';

赋值：
#赋值
set @count = 10;
set @count := 15;
select @count := 20;
select count(*) into @count from account;

使用：
select @count;





8.2.2.局部变量

作用域：仅仅在定义它的begin end中有效，应用在begin end的第一句

（1）声明
declare 变量名 类型;
declare 变量名 类型 default 值;

（2）赋值
方式一：通过set和select
set 局部变量名=值 或
set 局部变量名:=值 或
select @局部变量名:=值

方式二：通过select into
select 字段 INTO 局部变量名
from 表

（3）使用
select 局部变量名

8.2.3.用户变量 VS 局部变量

作用域		定义和使用的位置				语法
用户变量	当前会话	会话中的任何地方				必须加@符，不限定类型
局部变量	begin end中	只能在begin end中，且为第一句	不用加@符，需要限定类型

9.存储过程和函数
存储过程和函数：类似于java中的方法
好处：
1、提高代码的重用性
2、简化操作


9.1.存储过程



含义：一组预先编译好的SQL语句的集合，理解成批处理语句
1、提高代码的重用性
2、简化操作
3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率


9.1.1.创建&调用&查看&删除语法

创建语法：
create procedure 存储过程名(参数列表)
begin
存储过程体（一组合法的SQL语句）
end

注意：
1.参数列表包含三部分
参数模式	参数名	参数类型
in			student	varchar(20)

参数模式：
in：	该参数可以作为输入，也就是该参数需要调用方法传入值	
out：	该参数可以作为输出，也就是该参数可以作为返回值
inout：	该参数既可以作为输入也可以作为输出，也就是该参数即需要传入值，又可以返回值

2.如果存储过程体仅仅只有一句话，begin end可以省略。
存储过程的每条SQL语句的结尾要求必须加上分号。
存储过程的结尾可以使用delimiter重新设置
语法：
DELIMITER 结束标记
DELIMITER $


调用语法：
CALL 存储过程名（实参列表）


查看语法：
show create procedure 存储过程名;


删除语法：
drop procedure 存储过程名;

9.1.2.存储过程实战

（1）空参的存储过程：
# 创建存储过程
create procedure myp1()
begin
	insert into admin(name, money) values('z1', 11), ('z2', 22), ('z3', 33), ('z4', 44), ('z5', 55);
end;

#调用存储过程
CALL myp1();


带in模式参数的存储过程：
根据女神名获取男神信息
# 创建存储过程
create procedure myp1(in beauty_name varchar(25))
begin
	select bs.* from beauty b right join boys bs on b.boyfriend_id = bs.id where b.name = beauty_name;
end;
# 调用
call myp1('赵敏')

根据用户名和密码判断是否能登录成功
-- 创建存储过程
create procedure myp2(in username varchar(25), in password varchar(25))
begin
	declare result int default '0';
	select count(*) INTO result from admin a where a.username = username and a.`password` = password;
	select if(result > 0, '成功', '失败');
end;
-- 调用
call myp2('john', '8888')


（2）带out模式的存储过程：

根据女神名，返回对应的男神名
#创建
create procedure myp3(in beauty_name varchar(25), out boy_name varchar(25))
begin
	select bs.boyName into boy_name from beauty b right join boys bs on b.boyfriend_id = bs.id where b.name = beauty_name;
end;
-- 调用
set @boy_name='';
call myp3('赵敏', @boy_name);
select @boy_name;

根据女神名，返回对应的男神名和男神魅力值
创建
create procedure myp4(in beauty_name varchar(25), out boy_name varchar(25), out user_cp int)
begin
	select bs.boyName, bs.userCP into boy_name,user_cp  from beauty b right join boys bs on b.boyfriend_id = bs.id where b.name = beauty_name;
end;

-- 调用
set @boy_name='';
set @user_cp=0;
call myp4('赵敏', @boy_name, @user_cp);
select @boy_name, @user_cp;


（3）带inout模式参数的存储过程

传入a和b两个值，最终a和b都翻倍并返回
#创建
create procedure myp5(inout x int, inout y int)
begin
	set x = x*2;
	set y = y*2;
end;
#调用
set @x = 10;
set @y = 20;
call myp5(@x, @y);
select @x, @y;





9.2.函数

含义：一组预先编译好的SQL语句的集合，理解成批处理语句
1、提高代码的重用性
2、简化操作
3、减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

存储过程与函数的区别：
存储过程：可以有0个返回，也可以与多个返回，适合做批量插入，批量更新
函数：有且仅有一个返回，适合做处理数据后返回一个结果，适合做处理数据后返回一个结果


9.2.1.创建&调用&查看&删除语法

创建语法：
create function 函数名(参数列表) returns 返回类型
begin
函数体
end

注意：
1.参数列表包含两部分：参数名 参数类型
2.函数体：肯定会有return语句，如果没有会报错。
如果return语句没有放在函数体的最后也不报错，但不建议。
3.函数体重仅有一句话，则可以省略begin end
4.使用delimiter语句设置结束标记


调用语法：
select 函数名（参数列表）


查看语法：
show create function 函数名


删除语法：
dorp function 函数名



9.2.2.函数实战

（1）无参有返回

返回公司的员工个数：
#创建
create function myf1() returns int
begin
	declare count int default 0;
	select COUNT(*) into count from employees;
	return count;
end;
#调用
select myf1();


（2）有参返回

根据员工名返回工资：
#创建
create function myf2(username varchar(25)) returns double
begin
	declare money double default 0;
	select salary into money from employees where last_name = username;
	return money;
end;
#调用
select myf2('Kochhar');


根据部门名，发挥该部门的平均工资
#创建
create function myf3(dept_name varchar(25)) returns double
begin
	declare ave_salary double default 0;
	select avg(e.salary) into ave_salary from departments dept left join employees e on dept.department_id = e.department_id
	where dept.department_name = dept_name group by dept.department_id;
	return ave_salary;
end;
#调用
select myf3('Adm');

实现传入两个float，返回两者之和：

#创建
create function myf1(x float, y float) returns float
begin
	declare sum float default 0;
	set sum = x + y;
	return sum;
end;
#调用
select myf1(1, 5.1);


（3）查看函数
show create function myf3;



（4）删除函数
drop function myf3


10.流程控制结构
顺序结构：程序从上往下依次执行
分支结构：程序从两条或多条路径中选择一条去执行
循环结构：程序在满足一定条件的基础上重复执行一段代码


10.1.分支结构

（1）if函数
功能：实现简单的双分支
语法：
If(表达式1, 表达式2, 表达式3)
执行顺序：
如果表达式1成立，则if函数返回表达式2的值，否则返回表达式3的值
应用：任何地方
实战操作：
select if(1 > 2, 1, 2)
当前sql执行后会返回2


（2）case结构

在begin end 外面：

情况1：类似于java中的switch语句，一般用于实现等值的判断
case 变量|表达式|字段
when 要判断的值 then 返回的值1
when 要判断的值 then 返回的值2
....
else 返回的值n
end

情况2：类似于java的多重IF语句，一般用于视区间判断
case 
when 要判断的条件1 then 返回的值1
when 要判断的条件2 then 返回的值2
....
else 返回的值n
end


在begin end里面：

情况1：类似于java中的switch语句，一般用于实现等值的判断
case 变量|表达式|字段
when 要判断的值 then 要执行的语句1
when 要判断的值 then 要执行的语句2
....
else 要执行的语句n
end case;

情况2：类似于java的多重IF语句，一般用于视区间判断
case 
when 要判断的条件1 then 要执行的语句1
when 要判断的条件2 then 要执行的语句2
....
else 要执行的语句n
end case;

实战操作：

# 创建存储过程，根据传入的成绩，来显示登记，比如传入的成绩：90-100显示A，80-90显示B，60-80,显示C，否则显示D
create procedure show_grade(in grade int)
begin
	declare result varchar(2);
	CASE 
	WHEN grade>=90 and grade<=100 THEN set result = 'A';
	WHEN grade>=80 THEN set result = 'B';
	WHEN grade>=60 THEN set result = 'C';
	ELSE set result = 'D';
	END CASE;
	select result;
end;

CALL show_grade(99);


#根据传递的数据库类型，显示对应的数据库名
create procedure show_database_type(in val int)
begin
	declare result varchar(20);
	CASE val
	WHEN 1 THEN set result = 'mysql';
	WHEN 2 THEN set result = 'oracle';
	WHEN 3 THEN set result = 'sql server';
	ELSE set result = '不认识的类型，滚啊。。。';
	END CASE;
	select result;
end;

call show_database_type(1);


（3）if结构
功能：实现多重分类
语法：
if 条件1 then 语法1;
esleif 条件2 then 语法2;
...
[else 语句n;]
end if;

应用场景：应用在begin end中

实战操作：

# 创建存储过程，根据传入的成绩，来显示登记，比如传入的成绩：90-100显示A，80-90显示B，60-80,显示C，否则显示D
create function show_grade(grade int) returns char(1)
begin
	declare result char(1);
	if grade>=90 and grade<=100 THEN set result = 'A';
	elseif grade>=80 THEN set result = 'B';
	elseif grade>=60 THEN set result = 'C';
	else set result = 'D';
	end if;
	return result;
end;

select show_grade(55);


#批量插入，根据次数插入到admin表中多条记录，如果次数>20则停止
create procedure pro_while_insert2(in count int)
begin
	declare i int default 1;
	a:while i<=count do
		insert into admin(username, password) values(CONCAT('user', i), CONCAT('psw', i));
		if i>=20 then leave a;
		end if;
		set i=i+1;
	end while a;
end;

call pro_while_insert2(30);



10.2.循环结构

循环分类：
while、 loop、repeat

循环控制：
iterate类似于continue，结束本次循环，继续下一次循环
leave类似于break，结束当前所在循环。


三种循环语法：

（1）while
[标签:] while 循环条件 do
循环体
end while [标签]

（2）loop
[标签:] loop
循环体
end loop [标签]

（3）repeat
[标签:] repeat
循环体
until 结束循环的条件
end repeat [标签]


循环实战操作：

#批量插入，根据次数插入到amdin表中多条数据
create procedure pro_while_insert(in count int)
begin
	declare i int default 1;
	while i<=count do
		insert into admin(username, password) values(CONCAT('user', i), CONCAT('psw', i));
		set i=i+1;
	end while;
end;

call pro_while_insert(10);



while VS repeat VS loop
while：先判断后执行
repeat：先执行后判断
loop：没有条件的死循环

# 博客园所学

## 索引

索引分为单列索引(主键索引,唯一索引,普通索引)和组合索引.  
单列索引:一个索引只包含一个列,一个表可以有多个单列索引.   
组合索引:一个组合索引包含两个或两个以上的列。

### 索引的创建

1）单列索引

普通索引：
第一种方式 :
CREATE INDEX 索引名ON 表名(`字段名`(length))

第二种方式: 
ALTER TABLE award ADD INDEX account_Index(`account`)

唯一索引：
CREATE UNIQUE INDEX IndexName ON `TableName`(`字段名`(length));
ALTER TABLE TableName ADD UNIQUE (column_list)

主键索引：不允许有空值

2）组合索引：

语法：
CREATE INDEX IndexName On `TableName`(`字段名`(length),`字段名`(length),...);


### 索引的删除
语法：
DORP INDEX IndexName ON `TableName`

## 触发器

触发器：监视某种情况，并触发某种操作。

### 创建语法

触发器创建语法四要素：1.监视地点(table)
　　　　　　　　　　　2.监视事件(insert/update/delete)
　　　　　　　　　　 3.触发时间(after/before)
　　　　　　　　　　　4.触发事件(insert/update/delete)

语法：
create trigger triggerName after/before insert/update/delete
on 表名 for each row #这句话是固定的
 begin
     #需要执行的sql语句
 end
注意1:after/before: 只能选一个 ,afte表示后置触发, before表示前置触发
注意2:insert/update/delete:只能选一个

创建一个视图：
create trigger tag1 after insert on order_table
for each row
begin
	update goods set num=num-3 where id=1;
end;


我们如何在触发器引用行的值，也就是说我们要得到我们新插入的订单记录中的gid或much的值。
对于insert而言，新插入的行用new来表示，行中的每一列的值用new.列名来表示。
所以现在我们可以这样来改我们的触发器:
create trigger tg1 after insert on order_table
for each row
BEGIN
	update goods set num = num-new.much where id=new.gid; 
END;

当用户撤销一个订单的时候，我们这边直接删除一个订单，我们是不是需要把对应的商品数量再加回去呢？
 对于delete而言：原本有一行,后来被删除，想引用被删除的这一行，用old来表示旧表中的值，old.列名可以引用原(旧)表中的值。
create trigger tg2 after delete on order_table
for each ROW
BEGIN
	update goods set num = num+old.much where id=old.gid;
END;

### 删除触发器
语法：drop trigger 触发器名称;
使用：drop trigger dg1;