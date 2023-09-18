# MYSQL_NOTE
记录之前常用的MYSQL，防止遗忘

## 数据库基本概念 ##
 
1. DB：数据库，存储数据的容器
2. DBMS：数据库管理系统，又称为数据库软件或数据库产品，用于创建或管理DB
 
    DBMS分2类：
    - 基于共享文件的DBMS(ACESS)
    - 基于客户机--服务器的DBMS(mysql、oracle、sqlserver)
3. SQL：结构化查询语言，用于和数据库通信的语言，不是某个数据库软件特有的，而是几乎所有的主流数据库软件通用的语言
 
## 服务器启动和停止 ##
```
#登陆
#本机mysql登录，cmd终端输入
mysql -u root -p
#密码：******
 
#退出
exit
```
 
## 常用基础命令 ##
```
#一、数据库操作
#1、查看所有数据库
show databases;
 
#2、查看当前使用的数据库
select database();
 
#3、创建数据库
#create database 数据库名 charset=utf8;
create database kiki charset=utf8;
 
#4、删除数据库
#drop database 数据库名;
drop database kiki;
 
#5、使用数据库
#use 数据库名;
use kiki;
 
#6、查看数据库中有哪些表
show tables;
show tables from 库名 #查看其它库的所有表
 
#二、表的操作
#1、创建表结构
    #语法：create table table_name(字段名 数据类型 可选的约束条);
    #示例：
create table classes(
id int unsigned primary key auto_increment not null,
name varchar(20) default '',
age tinyint unsigned default 0,
height decimal(5,2),
gender enum('男','女','人妖','保密'),
cls_id int unsigned default 0
);
#primary key主键约束【主键唯一、非null】
#auto_increment自动增长
 
create table students(
id INT,
name varchar(255),
Address varchar(255),
weight decimal(5,2)
);
 
#2、查看表结构
#desc 表名;
desc classes;
```
## 进阶1—基础查询 ##
### 语法：
```
USE 库名
SELECT 查询列表 FROM 表名;
 
#类似于 System.out.println(打印东西);
```
### tips:
 
1、“查询列表”可以是：表中的字段、常量值、表达式、函数
 
2、查询的结果是一个虚拟表格
 
### 示例：
```
#选择使用kiki这个database,库里有classes、students表格
use kiki;
 
#1.查询表中单个字段
SELECT id FROM classes;
 
#2.查询表中的多个字段
SELECT id,`name`,age FROM classes;
 
#3.查询表中所有字段
SELECT * FROM classes;
 
#4.查询常量值
SELECT 100;
SELECT 'tom';
 
#5.查询表达式
SELECT 100%12;  #取余
 
#6.查询函数
SELECT VERSION();   #查看版本
 
#7.起别名
#(1)便于理解
#(2)如果要查询字段有重名的情况，使用别名可以区分开来
#方式一：【AS】
SELECT 100%12 AS result;
SELECT id AS 编号,age AS 年龄 FROM classes;
SELECT id AS "new id" FROM classes; #新名称有特殊符号可以用""标注，防止报错
#方式二：【空格】
SELECT id 编号,age 年龄 FROM classes;
 
#8.去重
SELECT DISTINCT age FROM classes;
 
#9.+号的作用
#java中的+号：
#（1）运算符，两个操作数都为数值型
#（2）连接符，只有有一个操作数为字符串
#mysql中的+号：
#仅有一个功能，运算符，操作规则：
#（1）两个操作数都为数值型，则做加法运算
#（2）只要其中一方为字符型，试图将字符型数值转换为数值型
#   -如果转换成功，则继续做加法运算
#   -如果转换失败，则将字符型数值转换成0，进行计算
#（3）只要其中一方是null，则结果肯定为null
SELECT 100+90;
SELECT '123'+90;                        
SELECT 'john'+90;   
SELECT null+10;
 
#10.ISNULL和IFNULL
SELECT IFNULL(height,0) AS height_1,height FROM classes; #如果是空，填充为0
SELECT ISNULL(height) AS height_2,height FROM classes; #如果是空，写1，非空，写0
 
#字段拼接concat
#两个字段连接成一个字段
SELECT CONCAT(gender,age) AS 编号 FROM classes;
SELECT CONCAT(gender,',',age,',',height) AS OUT_PUT FROM classes;
SELECT CONCAT(gender,',',age,',',IFNULL(height,0)) AS OUT_PUT FROM classes;
```
 
## 进阶2—条件查询 ##
### 语法：
```
SELECT 查询列表 FROM 表名 WHERE 筛选条件;
```
### 分类：
1. 按条件表达式筛选
 
条件运算符：>  <  =  !=  <>  <=  >=
 
2. 按逻辑表达式筛选
 
逻辑运算符：&&  ||  !  and  or  not
 
-   &&和and：两个结果都为true，结果为true,否则false
-   ||和or：只要有一个结果为true，结果为true，否则为false
-   !或not：取反
 
3. 模糊查询
 
（1）like
 
一般和通配符搭配使用
-   通配符：
-   % 任意多个字符
-   _ 任意单个字符,如果涉及到_，可以用\进行转义，'\_'
 
（2）between and
 
-   可以提高语句简洁度
-   包含临界值
-   两个临界值不能调换顺序
 
（3）in
 
含义：判断某字段的值是否属于in列表中的某一项
 
-   可以提高语句简洁度
-   in列表的值类型必须统一或者兼容
     
（4）is null
 
-   = 或 <> 不能用于判断null值
-   is null 或者 is not null 可以判断null值
 
### 示例：
```
#1.按条件表达式查询
SELECT * FROM classes WHERE height>115;
SELECT * FROM classes WHERE gender <> '男';
 
#2.按逻辑表达式查询
SELECT * FROM classes WHERE height>100 AND gender='保密';
SELECT * FROM classes WHERE NOT(age>=0 and age<10) OR gender <>'保密';
 
#3.模糊查询
SELECT * FROM classes WHERE `name` LIKE '%小%';
SELECT * FROM classes WHERE `name` LIKE '小_';
SELECT * FROM classes WHERE `name` LIKE '_$_%' ESCAPE '$';  #此处定义$为转义符
SELECT * FROM classes WHERE age BETWEEN 5 and 12;
SELECT * FROM classes WHERE gender in('男','女');
SELECT * FROM classes WHERE height is null;
SELECT * FROM classes WHERE height is not null;
 
#4.安全等于 <=>
SELECT * FROM classes WHERE height <=> null;
SELECT * FROM classes WHERE age <=> 0;
```
 
## 进阶3—排序查询 ##
### 语法：
```
SELECT 查询列表 FROM 表 【WHERE 筛选条件】 ORDER BY 排序列表 【ASC|DESC】
```
 
### tips：
-   desc降序，asc升序，默认为升序
-   order by 子句可以支持单个字段、多个字段、表达式、函数、别名
-   order by 子句一般是放在查询语句的最后面，limit子句除外
 
### 示例：
```
#降序排列
SELECT * FROM classes ORDER BY height DESC;
 
#升序排列
SELECT * FROM classes ORDER BY height ASC;
SELECT * FROM classes ORDER BY height;
SELECT * FROM classes ORDER BY LENGTH(gender),cls_id DESC;
```
 
## 进阶4—常见函数 ##
### 概念：
 
类似于java的方法，将一组逻辑语句封装在方法中，对外暴露方法名
 
### 好处：
 
隐藏了实现细节，提高代码的重用性
 
### 语法：
```
SELECT 函数名(实参列表) 【FROM 表】;
```
 
### 分类：
#### 一、字符函数 ####
**length、concat、upper、lower、substr、instr、trim、lpad、rpad、replace**
```
#1.length 获取参数值的字节个数
SELECT length('张三丰hahaha');
 
SHOW VARIABLES LIKE '%char%';
 
#2.concat 拼接字符串
SELECT concat(`name`,'_',gender) AS result FROM classes;
 
#3.upper、lower大小写转换
SELECT upper('john');
SELECT lower('JOHN');
 
#4.substr、substring,截取字符
#注意：索引从1开始
#截取从指定索引后面所有字符
SELECT substr('李莫愁爱上了陆展元',7) out_put;
 
#截取从指定索引处指定字符长度的字符
SELECT substr('李莫愁爱上了陆展元',1,3) out_put;
SELECT substr(height,3) FROM classes;
 
#5.instr 返回字串第一次出现的索引，如找不到，返回0
SELECT instr('杨不悔爱上了殷六侠','爱上') AS out_put;
 
#6.trim 去掉首位空格或者某固定字符
SELECT trim('    张无忌   ') AS out_put;
SELECT trim('a' FROM 'aaaaaaaaa张aaaa翠山aaaa') AS out_put;
 
#7.lpad 用指定字符实现左填充指定长度
SELECT lpad('殷素素',7,'*') AS out_put;
 
#8.rpad 用指定字符实现右填充指定长度
SELECT rpad('殷素素',7,'*') AS out_put;
 
#9.replace 替换
SELECT replace('周芷若周芷若周芷若张无忌爱上了周芷若','周芷若','赵敏') AS out_put;
```
 
#### 二、数学函数 ####
**round、ceil、floor、truncate、mod**
```
#1.round 四舍五入
SELECT round(3.7);
SELECT round(3.456,2);
 
#2.ceil 向上取整,返回大于等于该参数的最小整数
SELECT ceil(12.456);
SELECT ceil(-12.456);
 
#3.floor 向下取整,返回小于等于该参数的最大整数
SELECT floor(12.456);
SELECT floor(-12.456);
 
#4.truncate 截断
SELECT truncate(1.69999,1);
 
#5.mod 取余数
/*
结果正负看a的正负号，a为正，结果为正，a为负，结果为负号
mod(a,b) : a-a/b*b
 
mod(-10,-3) : -10 - (-10)/(-3)*(-3) = -10 - 3 * (-3) = -1
*/
SELECT mod(-10,-3);
SELECT mod(10,-3);
SELECT mod(-10,3);
SELECT mod(10,3);
```
 
#### 三、日期函数 ####
**now、curdate、curtime、year、month、day、hour、minute、second、str_to_date、date_format**
```
#1.now 返回当前系统日期+时间
SELECT now();
 
#2.curdate 返回当前系统日期
SELECT curdate();
 
#3.curtime 返回当前时间
SELECT curtime();
 
#4.可以获取指定的部分，年、月、日、小时、分钟、秒
SELECT year(now()) AS 年,month(now()) AS 月,day(now()) AS 日,hour(now()) AS 小时,minute(now()) AS 分钟,second(now()) AS 秒;
 
#日期时间一些表达字符
%Y 4位的年份
%y 2位的月份
%m 月份(01,02,...11,12)
%c 月份(1,2,...,11,12)
%d 日(01,02,...30,31)
%H 小时(24小时制)
%h 小时(12小时制)
%i 分钟(00,01,...59)
%s 秒(00,01,...59)
 
#5.str_to_date 将日期格式的字符转换成指定格式的日期
SELECT str_to_date('1998-3-2','%Y-%c-%d') AS out_put;
 
#6.date_format 将日期转换成字符
SELECT date_format(now(),'%Y年 %m月 %d日') AS out_put; 
```
 
#### 四、其它函数 ####
**version、database、user**
```
SELECT version(); #查看版本
SELECT database(); #查看当前所在数据库
SELECT user();  #查看当前用户
```
 
#### 五、流程控制函数 ####
**if、case**
```
#1.if 函数
SELECT if(10>5,'大','小');
 
#2.case 函数
#语法一
case 要判断的字段或表达式
when 常量1 then 要显示的值1或语句1
when 常量2 then 要显示的值2或语句2
...
else 要显示的值n或语句n
end
 
#语法二
case
when 条件1 then 要显示的值1或语句1
when 条件2 then 要显示的值2或语句2
...
else 要显示的值n或语句n
end
 
#案例：
SELECT *,
CASE gender
WHEN '男' then 'male'
WHEN 10 then 'female'
else 'other'
end AS 性别
FROM classes;
 
SELECT *,
CASE
WHEN age=0 then '未知'
WHEN age between 1 and 10 then '幼童'
else '中小童'
end AS 类别
FROM classes;
```
 
#### 六、统计函数 ####
**sum、avg、max、min、count、datediff**
```
#1、简单使用
USE kiki;
SELECT sum(height) FROM classes;
SELECT avg(height) FROM classes;
SELECT max(age) FROM classes;
SELECT min(age) FROM classes;
SELECT count(height) FROM classes;
#datediff计算两个日期之间相差的天数
SELECT datediff('2020-7-1','1991-11-6');
SELECT datediff('20200701','19911106');
SELECT datediff('2020/7/1','1991/11/6');
 
#2、参数支持哪些类型
SELECT sum(`name`),avg(`name`) FROM classes; 
#sum和avg只能用于统计数值型字段，此处报错
 
SELECT max(`name`),min(`name`),count(`name`) FROM classes;
#max、min、count支持所有类型
 
#3、是否忽略null值
#sum和avg忽略null值
SELECT sum(height),avg(height),sum(height)/6,sum(height)/4 FROM classes;
#max和min忽略null值
SELECT max(height),min(height) FROM classes;
#count忽略null值
SELECT count(height) FROM classes;
 
#4、和distinct搭配
SELECT sum(distinct cls_id),sum(cls_id) FROM classes;
SELECT count(age),count(distinct age) FROM classes;
 
#5、count函数的详细介绍
SELECT count(*) FROM classes;
SELECT count(1) FROM classes;
#效率：
#MYISAM存储引擎下，counnt(*)效率高
#INNODB存储引擎下，counr(*)和count(1)的效率差不多，比count(字段)要高一些
 
#6、和分组函数一同查询的字段有限制
SELECT avg(age),cls_id FROM classes group by cls_id;
 
SELECT max(height),min(height),avg(height),sum(height) FROM classes;
SELECT count(*) FROM classes WHERE cls_id=0;
```
 
## 进阶5—分组查询 ##
### 语法 ###
```
SELECT column(要求出现在group by后面),group_function(column)
FROM 表
【WHERE 筛选条件】
group by group_by_expression
【having 分组后筛选条件】
【order by column】;
```
> 注意：group by 子句支持单个字段分组，也支持多个字段分组（多个字段用逗号隔开，不区分先后顺序）
 
### 示例 ###
```
#1.简单的分组查询
SELECT cls_id,min(age) FROM classes group by cls_id; 
SELECT count(*),cls_id FROM classes group by cls_id;
 
#2.添加筛选条件
SELECT count(*),avg(age),cls_id FROM classes where height is not null group by cls_id;
 
#3.添加复杂的筛选条件-having-分组后的筛选
SELECT count(*) AS 人数,cls_id 
FROM classes 
group by cls_id 
HAVING 人数<>1;
 
#4.添加排序
SELECT count(*) AS 人数,cls_id 
FROM classes 
group by cls_id 
HAVING 人数>=1
ORDER BY cls_id;
```
 
## 进阶6—多表查询 ##
### 含义 ###
当查询的字段来自于多个表时，就会用到多表查询
 
### 语法 ###
```
select 字段，...
from 表1
【inner|left outer|right outer|cross】join 表2 on  连接条件
【inner|left outer|right outer|cross】join 表3 on  连接条件
【where 筛选条件】
【group by 分组字段】
【having 分组后的筛选条件】
【order by 排序的字段或表达式】
```
```
#内连接
    等值连接
    非等值连接
    自连接
#外连接
    左外连接
    右外连接
    全外连接
#交叉连接
 
 
```
### 示例 ###
```
#连接查询学习
use kiki;
 
#建表
create table department(
id int,
name varchar(20) 
);
 
create table staff(
id int primary key auto_increment,
name varchar(20),
sex enum('male','female') not null default 'male',
age int,
dep_id int
);
 
#插入数据
insert into department values
(200,'挖矿小分队'),
(201,'人力资源'),
(202,'销售'),
(203,'运营');
 
insert into staff(name,sex,age,dep_id) values
('程咬金','male',38,200),
('露娜','female',26,201),
('李白','male',38,201),
('王昭君','female',28,202),
('典韦','male',118,200),
('小乔','female',16,204)
;
 
 
#查看表结构和数据
desc department;
desc staff;
 
#表department与staff
select * from department;
select * from staff;
 
#1、内连接
#找两张表共有的部分
SELECT * FROM staff s INNER JOIN department d ON s.dep_id=d.id;
#2、外连接
#（1）左外连接/右外连接
#左/右外连接：优先显示左/右表全部记录
SELECT * FROM staff s LEFT JOIN department d ON s.dep_id=d.id;
#（2）全外连接
#显示左右两个表全部记录
SELECT * FROM staff s LEFT JOIN department d ON s.dep_id=d.id
UNION
SELECT * FROM staff s RIGHT JOIN department d ON s.dep_id=d.id;
 
#TEST
#1、找挖矿小分队的所有队员的信息
SELECT * FROM staff s INNER JOIN department d ON s.dep_id=d.id
WHERE d.name='挖矿小分队';
 
#2、查找人力资源所有员工的名字
SELECT s.name FROM staff s RIGHT JOIN department d ON s.dep_id=d.id
WHERE d.name='人力资源';
 
#3、找出年龄大于38的员工的姓名，及其所在的部门名称
SELECT s.name,d.name FROM staff s INNER JOIN department d ON s.dep_id=d.id
WHERE s.age>38;
 
#4、以内连接方式查询staff和department表，并以age字段升序排列
SELECT * FROM staff s INNER JOIN department d ON s.dep_id=d.id
ORDER BY s.age;
 
#5、找到部门为"挖矿小分队"和"人力资源"的所有员工的名字
SELECT s.name FROM staff s INNER JOIN department d ON s.dep_id=d.id
WHERE d.name IN ("挖矿小分队","人力资源");
```
