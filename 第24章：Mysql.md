## 数据库 - MySql

---

 **启动mysql 服务: net start mysql 【停止mysql服务指令 net stop mysql】**  

### 连接到 MySql 服务（MySql数据库)的指令

**mysql -h 主机IP -P 端口 -u 用户名 -p密码**

(1）-p密码不要有空格
(2) -p后面没有写密码，回车会要求输入密码
(3) 如果没有写 -h 主机，默认就是本机
(4) 如果没有写-P 端口，默认就是 3306



### Sqlyog 添加注释 ctrl + shift + c 

### **Sqlyog 删除注释 ctrl + shift + r** 



## MySQL 的三层结构：

### DBMS（数据库管理系统） -> 数据库 -> 表

数据库 - 表的本质仍然是文件

---



创建数据库:  CREATE DATABASE

删除数据库： DROP DATABASE



### **查看删除数据库**

显示数据库语句：SHOW DATABASE

显示数据库创建语句：SHOW CREATE DATABASE db_name

数据库删除语句[一定要慎用]： DROP DATABASE [IF EXIST]   db_name



### 备份和回复数据库

备份数据库（在DOS中）
mysqldump -u 用户名 -p -B 数据库1 数据库2 数据库n > 文件名.sql

恢复数据库(进入 Mysql 命令行)
Source 文件名.sql

---

### **备份和恢复数据库的表**

备份表： mysqldump -u 用户名 -p密码 数据库 表1 表2 表n > 文件名.sql

---

## **创建表**

**CREATE TABLE table_name (**
		**field1 datatype;**
		**field2 datatype;**
		**field3 datatype**
**)**

**character set** 字符集	**collate** 校对规则 **engine** 引擎
**field** 指定列名	**datatype** 指定列类型
**character set:** 如不指定则为所在数据库字符集
**collate:** 如不指定则为所在数据库校对规则

```mysql
#指令创建表
CREATE TABLE `user` (
	id INT,
	`name` VARCHAR(255),
	`password` VARCHAR(255),
	birthday DATE
)
	CHARACTER SET utf8 COLLATE utf8_bin ENGINE INNODB;
```

 

## 修改表：

```Mysql
# 添加列
ALTER TABLE tablename
ADD COLUMN column_name datatype [DEFAULT expr]
      [, column datatype]...;
# 修改列
ALTER TABLE tablename
MODIFY COLUMN column_name datatype [DEFAULT expr]
       [, column datatype]...;
# 删除列
ALTER TABLE tablename
DROP (column)
#查看表的结构
DESC 表名; 
#修改表名： 
RENAME TABLE 表名 TO 新表名
#修改表字符集：
ALTER TABLE 表名 CHARACTER SET 字符集
#修改列名
ALTER TABLE 表名 CHANGE COLUMN 原列名 新列名 VARCHAR(20);
```





---

### Mysql列类型 ( 数据类型 ）：

### 数值类型：	

说明：在能够满足需求的情况下，尽量选择占用空间小的；

​	整型: 	        tinyint [ 1个字节 ]
​				          smallint [ 2个字节 ]
​				          mediumint	[3个字节]
​				          **int  [4个字节]**
​				           bigint [ 8个字节 ]

```mysql
#演示整型的范围
#使用 tinyint 来演示范围； 有符号 -128 ~127 如果没有符号 0~ 255
#1. 如果没有指定 unsigned, 则 TINYINT 就是有符号
CREATE TABLE t3 (
	id TINYINT);
	
CREATE TABLE t4 (
	id TINYINT UNSIGNED);	#无符号
	
INSERT INTO t3 VALUES(127); #添加
SELECT * FROM t3;

INSERT INTO t4 VALUES(255); #添加
SELECT * FROM t4;
```

​		小数类型： float [单精度 4个字节]
​						   **double [双精度 8个字节]**
​						   **decimal [M,D]	大小不确定**

```mysql
#演示 decimal 类型、float类型、double 使用
#创建表
CREATE TABLE t06 (
	num1 FLOAT,
	num2 DOUBLE,
	num3 DECIMAL(30, 20));
#添加数据
INSERT INTO t06 VALUES(88.12345678912345, 88.12345678912345, 88.12345678912345);
#查询
SELECT * FROM t06;
# decimal 可以存放很大的数
CREATE TABLE t07 (
	num DECIMAL(65));
INSERT INTO t07 VALUES (899999999999999999999999923232392930);
SELECT * FROM t07;
```



**文本类型**：**char [0 ~ 255]**
				  **varchar [ 0 ~65535( 0 ~ 2^16 -1)	utf8 编码最大 21844（（65535-3） /  3） 字符 1-3个 字节用于记录大小 ]** 
				  **text 0 ~ 2 ^16 - 1**
				  longtext 0 ~ 2 ^ 32 - 1

```mysql
#字符串使用细节
#细节1：char(4)和 varchar(4) 这个 4表示的是字符，不是字节; 不区分这个字母是汉字还是字母
CREATE TABLE t11 (
	`name` CHAR(4));
#insert into t11 values('abcde');
INSERT INTO t11 VALUES('abcd');
SELECT * FROM t11;

CREATE TABLE t12 (
	`name` VARCHAR(4));
INSERT INTO t12 VALUES('HZX');
INSERT INTO t12 VALUES('ab北京');
SELECT * FROM t12;

#细节2：char(4)是定长（固定大小）； varchar(4)是变长（变化的大小），按照实际占用空间来分配
#细节3：如果一个数据是定长，推荐使用 char，比如md5的密码
#	如果一个字段的长度是不确定的，使用varchar，比如留言，文章
#	查询速度: char > varchar

#如果 varchar 不够用，可以考虑使用mediumtext 或者 longtext
CREATE TABLE t13 (
	content TEXT, 
	content2 MEDIUMTEXT,
	content3 LONGTEXT);
INSERT INTO t13 VALUES('HZX_2001', 'HZX_2001_01', 'HZX_2001_01_09');
SELECT * FROM t13;
```



**二进制数据类型：** blob [ 0 ~  2 ^ 16 - 1]
							 longblob [0 ~ 2 ^ 32 - 1]

**日期类型**：	date [日期 —— 年月日]
					  time  [时间—— 时分秒]
					  **datetime [ 年月日时分秒 YYY-MM-DD  HH:mm:SS]**
					  timestamp [时间戳]

```Mysql
#演示时间相关的类型
#创建一张表， date, datetime, timestamp
CREATE TABLE t14 (
	birthday DATE, --  生日
	job_time DATETIME, -- 记录年月日
	login_time TIMESTAMP 
		   NOT NULL DEFAULT CURRENT_TIMESTAMP
		   ON UPDATE CURRENT_TIMESTAMP);-- 登陆时间,如果希望 login_time 自动更新,需要配置

SELECT * FROM t14;
INSERT INTO t14(birthday, job_time) 
	VALUES('2022-11-11', '2022-11-11 10:10:10');
--  如果我们更新了 t14 的某条记录， login_time列会以当前时间更新
```



### bit类型的使用：

```mysql
#演示 bit 类型使用
#说明
#1. bit (m) m 在 1 - 64
#2. 添加数据 范围
#3. 显示按照 bit
#4. 查询时，仍然可以按照数来查询
CREATE TABLE t05 (num BIT(8));
INSERT INTO t05 VALUES(1);
INSERT INTO t05 VALUES(3);
INSERT INTO t05 VALUES(255);
SELECT * FROM t05 WHERE num = 1;
```



## **课堂练习**

```mysql
#创建表的课堂练习
CREATE TABLE `emp` (
	id INT,
	`name` VARCHAR(32),
	sex CHAR(1),
	birthday DATE,
	entry_date DATETIME,
	job VARCHAR(32),
	salary DOUBLE,
	`resume` TEXT) CHARSET utf8 COLLATE utf8_bin ENGINE INNODB;
-- 添加
INSERT INTO `emp`
	VALUES(100, 'HZX', '男', '2001-01-09', '2010-11-10 11:11:11', '巡山', 
	'3000', '大王叫我来巡山');
SELECT * FROM `emp`;

-- 增加一个 image 列，varchar 类型 （要求加在 resume 后面） 不能为空
ALTER TABLE `emp`
ADD COLUMN image VARCHAR(15) NOT NULL DEFAULT '' AFTER `resume`;

-- 修改job列，使得其长度为 60
ALTER TABLE `emp`
MODIFY COLUMN job VARCHAR(60) NOT NULL DEFAULT '';

-- 删除 sex 列
ALTER TABLE `emp`
DROP sex;

-- 表名改成 employee 
RENAME TABLE `emp` TO employee;

-- 修改表的字符集为 utf8
ALTER TABLE employee CHARACTER SET utf8

-- 将列名 name 修改为 user_name
ALTER TABLE employee CHANGE COLUMN `name` `user_name` VARCHAR(20);

SELECT * FROM employee;
```



## Insert 语句

```Mysql
INSERT INTO 表名 （列表名） VALUES （值） ;
```

**insert 语句的细节**

```Mysql
#说明 Insert 语句的细节
-- 1.插入的数据应该与列的数据类型相同
INSERT INTO `goods` (id, goods_name, price)
	VALUES('hsp', '小米手机', 2000);
-- 2.数据的长度应在规定范围内，例如：不能将一个长度为 80 的字符串加入到一个长度为 40 的列中
INSERT INTO `goods` (id, goods_name, price)
	VALUES(40, 'vovoovvovovovovov', 3000);	
-- 3.在 values 中列出的数据位置必须与被加入的列的排列位置相对应；
INSERT INTO `goods` (goods_name, id, price) 	-- 错误
	VALUES('vovo手机', 40, 2000);
-- 4.字符和日期的数据应包含在单引号里面
INSERT INTO `goods` (goods_name, id, price) 	-- 错误
	VALUES(40, vovo手机, 2000);
-- 5. 列可以插入空值[前提是该字段允许为空], INSERT INTO table VALUE(null)
INSERT INTO `goods` (id, goods_name, price)
	VALUES('hsp', '小米手机', NULL);
-- 6. insert into table_name (列名...) values(), (), () 形式添加多条记录
INSERT INTO `goods` (id, goods_name, price)
	VALUES(50, '小米手机', 2300), (60, '海尔手机', 1800),(...);
-- 7.如果是给表中所有字段添加数据，可以不写前面的字段名称
INSERT INTO `goods` 
	VALUES('hsp', '小米手机', 2000);
-- 8.默认值的使用，当不给某个字段值时，如果没有默认值就会添加默认值，否则会报错
   -- 如果某个列 没有指定 not null, 那么当添加数据时， 没有给定值，则会默认给 null
   -- 如果我们希望指定某个列的默认值， 可以在创建表时指定
INSERT INTO `goods` (id, goods_name)
	VALUES(80, 'xx手机');
```



## Update 语句：

```Mysql
UPDATE tb1_name
	SET col_name1=expr1 [, col_name2=expr2 ...] 
	[WHERE where_definition]
```

```Mysql
# 演示 update 语句
# 要求：在上面创建的employee 表中修改表中的记录
-- 1. 将所有员工薪水修改为 5000 元。[如果没有 where 条件，会修改所有的记录，因此要小心]
UPDATE  employee SET salary = 5000;
-- 2. 将姓名为 小妖怪 的员工薪水修改为 3000 元
UPDATE employee
	SET salary = 3000
	WHERE user_name = 'HZX';
-- 3. 将 老妖怪 的薪水在原有基础上增加 1000 元
INSERT INTO employee
	VALUES(200, '老妖怪', '1990-11-22', '2000-11-11 10:20:21', '捶背的', 5000, '给大王捶背', NULL);
UPDATE employee
	SET salary = salary + 1000, job = '出主意的'
	WHERE user_name = '老妖怪';

SELECT * FROM employee;
-- 使用细节:
-- UPDATE 语法可以用新值更新原表行中的各列,
-- SET 子句指示要修改哪些列和要给予哪些值
-- WHERE 子句指定应更新哪些行。 如没有 WHERE 子句，则更新所有的行。
-- 如果需要修改多个字段，可以通过 set 字段 1 = 值 1， 字段2 = 值2....
```



## Delete 语句：

```Mysql
DELETE from table_name
	[WHERE where_definition]
```

---



## Select 语句:

```MySql
SELECT [DISTINCT] * | {column1, column2, column3..}
	FROM tablename;

-- SELECT 指定查询的是哪些列的数据
-- column 指定列名
-- * 号代表查询所有列
-- From 指定查询哪张表
-- DISTINCT 可选，指显示结果时，是否去掉重复数据
```

**使用表达式对查询的列进行运算**

```Mysql
SELECT * | {column1 | expression, column2 | expression, ...}
	FROM tablename;
```

**在select语句中可使用as语句**

```Mysql
SELECT columnname as 别名 from 表名
```

### 在where子句中经常使用的运算符

1. < > <= >= =  !=

2. BETWEEN ... AND ... 显示在某个区间

3. IN(set)     显示in列表中的值，例：in(100, 200)

4. LIKE '张pattern'

   NOT LIKE ''	:		模糊查找

5. IS NULL :  判断是否为空

6. 逻辑运算符    and  or   not

   ```mysql
   -- 查询表中所有学生的信息。
   SELECT * FROM student;
   -- 查询表中所有学生的姓名和对应的英语成绩
   SELECT `name`, english FROM student;
   -- 过滤表中重复的数据
   SELECT DISTINCT * FROM student;
   SELECT DISTINCT english FROM student;
   -- 要查询的记录，每个字段都相同，会去去重
   
   -- 统计每个学生的总分
   SELECT 	`name`, (chinese + english + math) FROM student;
   
   -- 在所有的学生总分上加 10 分; 取别名
   SELECT `name` AS '姓名', (chinese + english + math + 10) 
   	AS total_score FROM student;
   
   -- 练习：
   -- 统计关羽的总分
   SELECT `name` AS '姓名', (chinese + english + math) AS total_score 
   	FROM student WHERE `name` = '关羽';
   -- 在赵云的总分上增加60%
   SELECT `name` AS '姓名', ((chinese + english + math) * 1.6) 
   	AS '赵云总分增加60%后' FROM student WHERE `name` = '赵云';
   -- 使用别名表示学生的数学分数
   SELECT `name` AS '学生姓名', math AS '数学成绩' FROM student;
   
   
   -- 查询姓名为赵云的学生成绩
   SELECT * FROM student
   	WHERE `name` = '赵云';
   -- 查询英语成绩大于90分的
   SELECT * FROM student WHERE english > 90;
   -- 查询总分大于200分的所有同学
   SELECT `name`, (chinese + english + math) 
   AS total_score FROM student WHERE chinese + english + math > 200;
   
   -- 课堂练习
   -- 查询math大于60并且（and）id大于4的学生成绩
   SELECT * FROM student  WHERE math > 60 AND id > 4;
   -- 查询英语成绩大于语文成绩的同学
   SELECT `name` AS '英语成绩大于语文成绩的学生姓名', chinese, english 
   FROM student WHERE english > chinese;
   -- 查询总分大于200分 并且 数学成绩小于语文成绩的,姓宋的学生
   SELECT * FROM student 
   WHERE `name` LIKE '宋%' AND math < chinese;
   
   -- 查询英语分数在 80 - 90 之间的同学
   SELECT * FROM student
   	WHERE english >= 80 AND english <= 90;
   -- 查询数学分数为 80， 90， 91 的同学
   SELECT * FROM student 
   	WHERE math IN (80, 90, 91);
   -- 查询数学分 > 80, 语文 分 > 80 的同学
   SELECT * FROM student 
   	WHERE math > 80 AND chinese > 80;
   -- 查询所有姓 李 的学生成绩
   SELECT * FROM student 
   	WHERE `name` LIKE '李%';
   
   -- 练习：
   -- 查询语文分数在 70 ~ 80 之间的同学
   SELECT * FROM student 
   	WHERE chinese >= 70 AND chinese <= 80;
   -- 查询总分为189，190，191的同学
   SELECT * FROM student 
   	WHERE (chinese + math + english) IN (198, 190, 191);
   -- 查询所有姓李 或 宋的学生成绩
   SELECT * FROM student
   	WHERE `name` LIKE '李%' OR `name` LIKE '宋%';
   -- 查询数学比语文少 10分的同学
   
   SELECT * FROM student 
   	WHERE (math - chinese) >= 10;
   	
   ```

   

### **使用 order by 子句排序查询结果**

```MySql
SELECT column1, column2, column3
	FROM table;
		order by column asc|dec,...

-- 演示OEDER BY 使用
-- 对数学成绩排序后输出 【升序】
SELECT * FROM student
	ORDER BY math;
-- 按总分从高到低的顺序输出 [降序]
SELECT `name`, (math + chinese + english) AS total_score FROM student
	ORDER BY total_score DESC;
-- 对姓黄的学生成绩排序输出（升序）
SELECT `name`, (chinese + english + math) AS total_score FROM student 
	WHERE `name` LIKE '黄%'
	ORDER BY total_score;
```

1.Order by 指定排序的列，排序的列既可以是表中的列名，也可以是 select 语句后指定的列名
2.Asc 升序（默认）； Desc 降序
3.ORDER BY子句应位于 SELECT 语句的结尾

---



### 统计函数 - count:

```Mysql
SELECT COUNT(*) | COUNT(列名) FROM tablename 
			[WHERE  where_definition]
			
-- 演示 mysql 的统计函数使用
-- 统计一个班级中有多少个学生
SELECT COUNT(*) FROM student;
-- 统计数学成绩大于 90 的学生有多少个
SELECT COUNT(*) FROM student
	WHERE math > 90;
-- 统计总分大于 250 的人数有多少
SELECT COUNT(*) FROM student
	WHERE (math + english + chinese) > 250;
-- count(*) 和 count(列）的区别
-- 解释：count(*) 返回满足条件的记录的行数
-- count(列): 统计满足条件的某列有多少个，但是会排除为 null
```

### 合计函数 - sum

```MYSQL
SELECT SUM(列名) {, SUM(列名)...} FROM tablename
		[WHERE where_definition]

-- 演示sum函数的使用
-- 统计一个班级数学总成绩
SELECT SUM(math) FROM student;
-- 统计一个班级语文、英语、数学各科的总成绩
SELECT SUM(chinese) AS chinese_total, SUM(english) AS english_total,
	SUM(math) AS math_total FROM student;
-- 统计一个班级语文、英语、数学的成绩总和
SELECT SUM(english + chinese + math) FROM student;
-- 统计一个班语文的平均分
SELECT SUM(chinese) / COUNT(*) FROM student;
```

### 合计函数 - avg

```Mysql
SELECT avg(列名) {,avg(列名)...} from tablename [WHERE where_definition]

-- 演示 avg
-- 求一个班数学的平均分
SELECT AVG(math) FROM student;
-- 求一个班级总分平均分
SELECT AVG(math + english + chinese) FROM student;
```

### 合计函数 - Max / Min

```Mysql
SELECT max(列名) from tablename [WHERE where_definition]

SELECT MAX(math + english + chinese), MIN(math + chinese + english) 
	FROM student;
```

**使用 group by 子句对列进行分组**

```Mysql
SELECT column1, column2, column3... FROM tablename
		group by columnname
```

**使用 having 子句对分组后的结果进行过滤**

```mysql
SELECT column1, column2, column3..
	FROM tablename
	group by columnname having
```

```MYSQL
-- 显示每个部门的平均工资和最高工资
SELECT AVG(sal), MAX(sal), deptno
	FROM emp GROUP BY deptno;
-- 显示每个部门的每种岗位为的平均工资和最高工资
SELECT AVG(sal), MIN(sal), deptno, job
	FROM emp GROUP BY deptno, job;
-- 显示平均工资低于 2000 的部门号和它的平均工资 // 别名
-- 分析：显示各个部门的平均工资和部门号
-- 	  在上一个基础之上进行筛选 保留 AVG(sal) < 2000
SELECT deptno, AVG(sal) AS avg_sal FROM emp 
	GROUP BY deptno
		HAVING avg_sal < 2000;
```



### 字符串函数:

```MYsql
-- 演示字符串相关函数的使用, 使用 emp
-- CHARSET(str) 返回字串字符集
SELECT CHARSET(ename) FROM emp;

-- CONCAT (string2 [,... ]) 连接字串
SELECT CONCAT(ename, ' job is ', job) FROM emp;

-- INSTR (string, substring) 返回 substring 在 string 中出现的位置，没有返回0
-- DUAL 亚元表 可以作为测试使用
SELECT INSTR('hanshunping', 'ping') FROM DUAL;

-- UCASE (string2) 转换大小写
SELECT UCASE(ename) FROM emp;

-- LCASE (string2) 转换成小写
SELECT LCASE(ename) FROM emp;

-- LEFT (string2, length) 从 string2 中左边起取出 length个字符
-- RIGHT (string2, length) 从 string2 中右边起取出 length个字符
SELECT RIGHT(ename, 2) FROM emp;

-- LENGTH (string) string 长度 [ 按照字节 ]
SELECT LENGTH(ename) FROM emp;

-- REPLACE(str, search_str, replace_str)
-- 在 str 中用 replace_str 替换 search_str
-- 如果是 manager 就替换成经理
SELECT ename, REPLACE(job, 'MANAGER', '经理') FROM emp;

-- STRCMP (string1, string2) 逐字符比较两字串大小
SELECT STRCMP('hsp', 'jsp') FROM DUAL;

-- SUBSTRING (str, position [, length])
-- 从str 的 position 开始[ 从1开始计算], 取length 个字符
-- 从 ename 列的第一个位置开始取出两个字符
SELECT SUBSTRING(ename, 1, 2) FROM emp;

-- LTRIM(string 2) RTRIM (string2) TRIM(string)
-- 去除前端空格或后端空格
SELECT LTRIM(' 韩顺平教育') FROM DUAL;
SELECT RTRIM('韩顺平教育 ') FROM DUAL;
SELECT TRIM(' HANSHUNPING ') FROM DUAL;

-- 以首字母小写的方式显示所有员工emp表的姓名
-- 1.选中所有员工emp表中的姓名的首字母
-- 2.将首字母转换陈小写
-- 方法一： 截取
SELECT CONCAT(LCASE(LEFT(ename, 1)), RIGHT(ename, (LENGTH(ename) - 1))) 
	AS low_first_char_name FROM emp;
-- 方法 二：直接替换
SELECT REPLACE(ename, LEFT(ename, 1), LCASE(LEFT(ename, 1))) 
	AS low_first_char_name FROM emp; 
```



### 数学函数：

```mysql
#演示数学相关函数
-- ABS(num） 绝对值
SELECT ABS(-10) FROM DUAL;
-- BIN(decimal_number)	十进制转为二进制
SELECT BIN(10) FROM DUAL;
-- CELING(number2) 向上取整，得到比num2大的最小整数
SELECT CEILING(1.1) FROM DUAL;
-- CONV(number2, from_base, to_base) 进制转化
SELECT CONV(8, 10, 2) FROM DUAL;
-- FLOOR(number2) 向下取整，得到比num2小的最大整数
SELECT FLOOR(1.1) FROM DUAL;
-- FORMAT(number, decimal_places) 保留小数位
SELECT FORMAT(78.23123, 2) FROM DUAL;
-- HEX (DecimalNumber) 转十六进制
-- LEAST (number1, number2, [,...]) 求最小值
SELECT LEAST(0, 1, -10, 4) FROM DUAL;
-- MOD（numerator, denominator) 求余
SELECT MOD(10, 3) FROM DUAL;
-- RAND([seed] RAND([seed]) 返回随机数 其范围为 0 <= v <= 1.0
-- 说明： 如果使用 RAND()每次返回不同的随机数， 在 0~1.0
--  	  如果使用 rand(seed) 返回随机数，seed不变 随机数不变
SELECT RAND() FROM DUAL;
```



### 时间函数

```Mysql
-- 日期时间相关函数
-- CURRENT_DATE() 当前日期
SELECT CURRENT_DATE FROM DUAL;
-- CURRENT_TIME() 当前时间
SELECT CURRENT_TIME() FROM DUAL;
-- CURRENT_TIMESTAM() 当前时间戳
SELECT CURRENT_TIMESTAMP() FROM DUAL;
-- 创建测试表 信息表
CREATE TABLE mes(
	id INT,
	content VARCHAR(30),
	send_time DATETIME);

-- 添加一条记录
INSERT INTO mes
	VALUES(1, '北京新闻', CURRENT_TIMESTAMP());
INSERT INTO mes VALUES(2, '上海新闻', NOW());
INSERT INTO mes VALUES(3, '广州新闻', NOW());

SELECT * FROM mes;

-- 显示所有新闻信息，发布日期只显示 日期 不显示时间
SELECT id, content, DATE(send_time) FROM mes;
-- 请查询10分钟内发布的新闻
SELECT * FROM mes
	WHERE DATE_ADD(send_time, INTERVAL 10 MINUTE) >= NOW();
-- 请在 mysql 的 sql 语句中求出 2011-11-11 和 1990-1-1 相差多少天
SELECT DATEDIFF('2011-11-11', '1990-01-01') FROM DUAL;
-- 用mysql 的 sql 语句求出你活了多少天?
SELECT DATEDIFF(NOW(), '2001-01-09') FROM DUAL;
-- 如果你能活80岁，求出你能活多少天
SELECT DATEDIFF(DATE_ADD('2001-01-09', INTERVAL 80 YEAR), '2001-01-09') 
	FROM DUAL;

SELECT TIMEDIFF('10:11:11', '06:10:10') FROM DUAL;

-- YEAR |Month | Day | Date (datetime)
SELECT YEAR(NOW()) FROM DUAL;
SELECT DAY(NOW()) FROM DUAL;
-- UNIX_TIMESTAMP() 返回的是 1970-1-1 到现在的秒数
SELECT UNIX_TIMESTAMP() FROM DUAL;
-- FROM_UNIXTIME() 可以吧一个unix_timestamp 秒数，专程指定格式的日期
SELECT FROM_UNIXTIME(1684838341, '%Y-%m-%d') FROM DUAL;
SELECT FROM_UNIXTIME(1684838341, '%Y-%m-%d  %H:%i:%s') FROM DUAL;
```



### 加密函数和系统函数

```MYSQL
#演示 加密函数 和系统函数
-- user() 查询用户
SELECT USER() FROM DUAL;	-- 用户@IP地址
-- DATABASE() 查询当前使用数据库名称
SELECT DATABASE();
-- MD5(str) 为字符串算出一个 MD5 32的字符串，常用（用户密码）加密
SELECT MD5('hsp') FROM DUAL;
-- PASSWORD(str) -- 加密函数; MySQL数据库的用户密码就是 PASSWORD 函数加密
SELECT PASSWORD('hsp') FROM DUAL;
```



### 流程控制函数

```MYSQL
# 演示流程控制语句
# IF(expr1, expr2, expr3) 如果 expr1 为 True, 则返回 expr2 否则返回 expr3
SELECT IF(TRUE, '北京', '上海') FROM DUAL;
# IFNULL（expr1, expre2) 如果 expr1 不为空 NULL, 则返回 expr1, 否则返回 expr2
SELECT IFNULL(NULL, '韩顺平教育') FROM DUAL;
# SELECT CASE WHEN expr1 THEN expr2 WHEN expr3 THEN expr4 ELSE expr5 END; 多重分支

SELECT CASE
	WHEN TRUE THEN 'jack' -- Jack
	WHEN TRUE THEN 'Tom'
	ELSE 'mary' END
	
-- 1.查询 emp 表，如果 comm 是 null，则显示 0.0
SELECT ename, IF(comm IS NULL, 0.0, comm)
	FROM emp;
SELECT ename, IFNULL(comm, 0.0)
	FROM emp;
-- 2. 如果 emp 表的job 是 CLERK 则显示 职员， 如果是 MANAGER 则显示 经理
-- 如果是 SALEMAN 则显示 销售人员，其他正常显示
SELECT ename, ( SELECT CASE
		 WHEN job = 'CLERK' THEN '职员' -- Jack
		 WHEN job = 'MANAGER'  THEN '经理'
		 WHEN job = 'SALESMAN'  THEN '销售人员'
		 ELSE job END) AS 'job', job
	FROM emp;
```



### 加强查询

```MYSQL
-- 查询加强
-- WHERE 子句
-- 查找 1992.1.1后入职的员工
SELECT * FROM emp
	WHERE hiredate > '1992-01-01';
-- like 模糊查询
--	%: 表示0到多个任意字符 _: 表示单个字符
--	如何显示首字符为 s 的员工姓名和工资
SELECT ename, sal FROM emp
	WHERE ename LIKE 'S%';
--	如何显示第三个字符为大写 O 的所有员工的姓名和工资
SELECT ename, sal FROM emp
	WHERE ename LIKE '__O%';
-- 如何显示没有上级的雇员情况
SELECT * FROM emp 
	WHERE mgr IS NULL;
-- 查询表的结构
DESC emp;

-- ORDER BY 子句
-- 按照工资的从低到高的顺序显示雇员信息
SELECT * FROM emp
	ORDER BY sal;
-- 按照部门好升序而雇员的工资降序排列，显示雇员信息
SELECT * FROM emp
	ORDER BY deptno ASC, sal DESC;
```



### 分页查询

```MYSQL
-- 分页查询
-- 按照雇员 id号升序取出，每页显示3条记录，请分别显示 第1页， 第2页， 第3页
-- 第一页
SELECT * FROM emp
	ORDER BY empno
	LIMIT 0, 3;
-- 第二页
SELECT * FROM emp
	ORDER BY empno
	LIMIT 3, 3;
```



### 增强 group by的使用

```MYSQL
-- 增强group by 的使用
-- 1.显示每种岗位的雇员总数、平均工资
SELECT COUNT(*), FORMAT(AVG(sal), 2), job FROM emp
	GROUP BY job;
-- 2. 显示雇员总数，以及获得补助的雇员数
SELECT COUNT(*), COUNT(comm)
	FROM emp;
-- 老师的扩展要求，统计没有获得补助的雇员数
SELECT COUNT(*), COUNT(IF(comm IS NULL, 1, NULL))
	FROM emp;
-- 显示管理者的总人数
SELECT COUNT(DISTINCT mgr)	-- 去重
	FROM emp;
-- 显示雇员的工资最大差额
SELECT MAX(sal) - MIN(sal)
	FROM emp;
```



### 数据分组的总结

如果 select 语句同时包含有 group by, having, limit, order by 那么他们的顺序是 
**group by, having, order by, limit**

```mysql
-- 应用案例：请统计各个部门 group by 的平均工资 avg
-- 并且是大于 1000的 having, 并且按照平均工资从高到低排序， order by
-- 取出前两行记录 limit
SELECT deptno, AVG(sal) AS avg_sal
	FROM emp
	GROUP BY deptno
	HAVING avg_sal > 1000
	ORDER BY avg_sal DESC
	LIMIT 0,2;
```



### MySql 多表查询

```Mysql
-- 多表查询
-- 显示雇员名字，雇员工资及所在部门的名字 [ 苗卡尔集】
SELECT ename, sal, dname, emp.deptno 
	FROM emp, dept
	WHERE emp.deptno = dept.deptno;
-- 如何显示部门号为 10 的部门名、员工名和工资
SELECT ename, dname, sal, emp.deptno
	FROM emp, dept
	WHERE emp.deptno = dept.deptno AND emp.deptno = 10;
-- 显示各个员工的姓名、工资及其工资的级别
SELECT ename, sal, grade FROM emp, salgrade
	WHERE sal BETWEEN losal AND hisal;

-- 显示雇员名，雇员工资集所在部门的名字，并按部门排序[降序]
SELECT ename, sal, dname FROM emp, dept
	WHERE emp.deptno = dept.deptno
	ORDER BY dname DESC;
```



### 多表查询 - 自连接

```MYSQL
-- 显示公司员工名字和他的上级的名字
-- 自连接特点：
-- 1. 把同一张表当做两张来使用
-- 2. 需要取别名 表名 表别名
-- 3. 如果列名不明确，可以指定列的别名
SELECT worker.ename AS '职员名', boss.ename AS '上级名'
	FROM emp worker, emp boss
	WHERE worker.mgr = boss.empno;
```



### 子查询

```MYSQL
-- 子查询的演示
-- 单行子查询是指返回一行数据的子查询语句
-- 如何显示与 SIMTH 同一部门的所有员工
SELECT deptno
	FROM emp
	WHERE ename = 'SMITH';

SELECT * FROM emp
	WHERE deptno = (
		SELECT deptno
			FROM emp
			WHERE ename = 'SMITH');


-- 多行子查询指返回多行数据的子查询 使用关键字 in
-- 如何查询和部门 10 的工作相同的雇员的 名字、 岗位、工资、部门号
-- 但是不含 10 号部门自己的雇员
/*
	1.查询10号部门有哪些工作
	2.把上面的查询结构当作子查询使用
*/
SELECT DISTINCT job FROM emp 
	WHERE deptno = 10;

SELECT ename, job, sal, deptno 
	FROM emp
	WHERE job IN (
		SELECT DISTINCT job FROM emp 
		WHERE deptno = 10
	) AND deptno != 10;

-- 子查询临时表
-- 问题： 查询 ecshop 中各个类别中，价格最高的商品
-- 查询 商品表
SELECT goods_id, cat_id, goods_name, shop_price
	FROM ecs_goods
-- 先得到 各个类别中 商品价格最高的商品 max + group by cat id
SELECT cat_id, MAX(shop_price)
	FROM ecs_goods
	GROUP BY cat_id;
-- 答案：
SELECT goods_id , ecs_goods.cat_id, 
	goods_name, shop_price
	FROM (
		SELECT cat_id, MAX(shop_price) AS max_shop_price
		FROM ecs_goods
		GROUP BY cat_id
	) table1, ecs_goods
	WHERE ecs_goods.cat_id = table1.cat_id
	AND ecs_goods.shop_price = table1.max_shop_price;

-----------------------------------------------------------------------------------------------

-- 多行子查询中使用all 和 any 操作符
-- all 和 any 的使用
-- 请思考：显示工资比部门30的所有员工的工资高的员工的姓名、工资和部门号

SELECT ename, sal, deptno
	FROM emp
	WHERE sal > ALL(
		SELECT sal 
			FROM emp
			WHERE deptno = 30);
-- 等同于
SELECT ename, sal, deptno
	FROM emp
	WHERE sal > (
		SELECT MAX(sal) 
			FROM emp
			WHERE deptno = 30);

-- any操作符
-- 显示工资比部门30得其中一个员工的工资高的员工姓名、工资和部门号
SELECT ename, sal, deptno
	FROM emp
	WHERE sal > ANY(
		SELECT sal 
			FROM emp
			WHERE deptno = 30);
-- 等于
SELECT ename, sal, deptno
	FROM emp
	WHERE sal > (
		SELECT MIN(sal) 
			FROM emp
			WHERE deptno = 30
		);
```



### 多列子查询

```MYSQL
-- 多列子查询
-- 如何查询与 smith 的部门和岗位完全相同的所有雇员（并且不包含smith本人）
-- 分析：1.得到 smith 的部门和岗位
SELECT deptno, job
	FROM emp
	WHERE ename = 'smith';
-- 分析 2. 把上面的查询当作子查询来使用，并且使用多列子查询的语法进行匹配
SELECT * 
	FROM emp
	WHERE (deptno, job) = (
		SELECT deptno, job
		FROM emp
		WHERE ename = 'smith'
		) AND ename != 'smith';
```



### 子查询练习：

```MYSQL
-- 查找每个部门工资高于本部门平均工资的人的资料
SELECT deptno, AVG(sal) AS avg_sal
	FROM emp GROUP BY deptno;

SELECT ename, emp.deptno, sal, temp.avg_sal
	FROM emp, (
		SELECT deptno, AVG(sal) AS avg_sal
		FROM emp 
		GROUP BY deptno
		) temp
	WHERE emp.deptno = temp.deptno
	AND emp.sal > temp.avg_sal;

-- 查找每个部门工资最高的人的详细资料
-- 1.先查找每个部门的最高工资
SELECT MAX(sal) AS max_sal, deptno
	FROM emp
	GROUP BY deptno;
-- 2. 查找对应员工
SELECT * FROM emp, (
	SELECT MAX(sal) AS max_sal, deptno
	FROM emp
	GROUP BY deptno) temp
	WHERE emp.sal = temp.max_sal
	AND emp.deptno = temp.deptno;


-- 显示每个部门的信息（包括：部门名，编号，地址）和人员数量
-- 1.先统计每个部门的人员数量
SELECT deptno, COUNT(*)
	FROM emp
	GROUP BY deptno;
-- 2.将每个部门的人数 和 每个部门的详细信息相合并
SELECT * FROM dept;

SELECT temp.*, dname, loc FROM dept, (
	SELECT deptno, COUNT(*) AS '部门总人数'
	FROM emp
	GROUP BY deptno
	) temp
	WHERE dept.deptno = temp.deptno;
```



### 表复制

```MYSQL
-- 演示如何自我复制
-- 1. 先把 emp 表的记录复制到 my_table01
INSERT INTO my_table01
	(id, `name`, sal, job, deptno)
	SELECT empno, ename, sal, job, deptno FROM emp;
-- 2. 自我复制
INSERT INTO my_table01
	SELECT * FROM my_table01;
```



### 经典例题： 表的去重

```MYSQL
-- 如何删除一张表的重复记录
-- 1.先创建一张表 my_table02;
-- 2.让 my_table02 有重复的记录
CREATE TABLE my_table02 LIKE emp;  -- 把 emp 表的结构，复制到 my_table02
INSERT INTO my_table02
	SELECT * FROM emp;
SELECT * FROM my_table02;
-- 3.去重
/*
  思路：
  1.先创建一张临时表 my_temp, 该表的结构和 my_table02 一样
  2.把my_temp表通过 DISTINCT 关键字处理后， 把记录复制到 my_temp
  3.清除掉 my_table02 的所有记录
  4.把 my_temp 表的记录复制到 my_table02
  5.删除掉 my_temp
*/
CREATE TABLE my_temp LIKE my_table02;
INSERT INTO my_temp
	SELECT DISTINCT * FROM my_table02;
DELETE FROM my_table02;
INSERT INTO my_table02 
	SELECT * FROM my_temp;
DROP TABLE my_temp;

SELECT * FROM my_table02
```



### 合并查询

```MYSQL
-- 合并查询
SELECT ename, sal, job FROM emp WHERE sal > 2500;
SELECT ename, sal, job FROM emp WHERE job = 'MANAGER';

-- union all 就是将两个结果合并, 不会去重
SELECT ename, sal, job FROM emp WHERE sal > 2500
UNION ALL
SELECT ename, sal, job FROM emp WHERE job = 'MANAGER';

-- union 会自动去重
SELECT ename, sal, job FROM emp WHERE sal > 2500
UNION 
SELECT ename, sal, job FROM emp WHERE job = 'MANAGER';
```



### MySql表的外连接

1.左外连接： 如果**左侧的表**完全显示我们就说的是 左外连接
2.右外连接： 如果**右侧的表**完全显示我们就说是  右外连接

```MYSQL
-- 列出部门名称和这些部门的员工信息（名字和工作），
-- 同时列出那些没有员工的部门名
-- 左连接
SELECT dept.dname, ename, job
	FROM dept LEFT JOIN emp
	ON dept.deptno = emp.deptno
	ORDER BY job DESC;
-- 右连接
SELECT dept.dname, ename, job
	FROM emp RIGHT JOIN dept
	ON dept.deptno = emp.deptno
	ORDER BY job DESC;
```

---



## Mysql 约束

**包括：**

**主键 - primary key (主键） - 基本使用**

1.主键不能重复也不能为空；

2.一张表最多只能有一个主键，但可以是复合主键

3.主键的指定方式： 直接在字段名后指定：字段名, primary key
								在表定义最后写 primary key (列名)

4.使用 desc 表名，可以查看 perimary key 的情况

---

**not null (非空)** 	如果在列上定义了 not null, 那么当插入数据时，必须为列提供数据

---

**unique(唯一)**		当定义了唯一约束后，该列值是不能重复的。
1: 如果没有指定 NOT NULL， 则 UNIQUE 字段可以有多个 null
2: 一张表可以有多个 unique 字段

---

### **foreign key (外键)**

用于定义主表和从表之间的关系：外键约束要定义在从表上，主表则必须具有主键约束或是unqiue约束。当定义外键约束后，要求外键数据必须在主表的主键列存在或是为null

细节：
1.外键指向的表的字段，要求是 primary key 或是 unique
2.表的类型是 innodb, 这样的表才支持外键
3.外键字段的类型和主键字段的类型一致（长度可以不同）
4.外键字段的值，必须在主键字段中出现过，或者为 null 【 前提是外键字段允许为 null】
5.一旦建立主外键的关系，数据就不能随意删除了

---

### 创建表案例

```MYSQl
-- 创建数据库
CREATE DATABASE shop_db;

-- 商品 goods
DROP TABLE goods;
CREATE TABLE goods (
	goods_id INT,
	goods_name VARCHAR(32) NOT NULL DEFAULT '',
	unitprice DOUBLE NOT NULL DEFAULT 0,
	category VARCHAR(32) NOT NULL DEFAULT 0,
	provider VARCHAR(32) NOT NULL DEFAULT 0,
	PRIMARY KEY (goods_id));

-- 客户 customer
DROP TABLE customer;
CREATE TABLE customer (
	customer_id INT,
	`name` VARCHAR(32) NOT NULL,
	address VARCHAR(32),
	email VARCHAR(32) UNIQUE ,
	sex ENUM('男', '女') NOT NULL,
	nums INT,
	card_id CHAR(18),
	PRIMARY KEY (customer_id)) CHARSET utf8;

-- purchase
CREATE TABLE purchase (
	order_id INT UNSIGNED NOT NULL PRIMARY KEY,
	customer_id INT NOT NULL,
	goods_id INT NOT NULL,
	nums INT NOT NULL DEFAULT 0,
	FOREIGN KEY (customer_id) REFERENCES customer(customer_id),
	FOREIGN KEY (goods_id) REFERENCES goods(goods_id)
	);

DESC goods;
DESC customer;
DESC purchase;	
```



### 自增长

细节：

1.一般来说自增长是和 peimary key 配合使用的
2.自增长也可以单独使用 【 但需要配合一个 unique1】
3.自增长修饰的字段为整数型的
4.默认从 1 开始，也可通过  ALTER TABLE AUTO_INCREMENT = 新的开始值

```MYSQL
-- 演示自增长的作用
-- 创建表
CREATE TABLE t24
	( id INT PRIMARY KEY AUTO_INCREMENT,
	email VARCHAR(32) NOT NULL DEFAULT '',
	`name` VARCHAR(32) NOT NULL DEFAULT '');

DESC t24
SELECT * FROM t24;
-- 测试自增长的使用
INSERT INTO t24
	VALUES(NULL, 'tom@qq.com', 'tom');
INSERT INTO t24 (email, `name`)
	VALUES('jools@qq.com', 'jools');
```



### MySql 的索引 [索引本身占空间]

CREATE INDEX  索引名 ON 表名（列名) ;

**索引的原理**：
快的原因：形成一个索引的数据结构，比如二叉树
慢的原因：因为进行全表扫描

**索引的代价：**
1.磁盘占用
2.对 update delete insert 语句有影响

**索引的类型：**
1.主键索引，主键自动的为主索引（类型 primary key)
2.唯一索引（UNIQUE)
3.普通索引（INDEX)
4.全文索引（FULLTEXT）【适用于MyISAM】
	开发中考虑使用：全文搜索 Solr 和 ElasticSearch（ES）

小结：
适合创建索引
1.较频繁的作为查询条件字段应该创建索引
2.唯一性太差的字段不适合单独创建索引，即使频繁作为查询条件
3.更新非常平凡的字段不适合创建索引
4.不会出现在where子句的字段不应该创建索引

```Mysql
-- 演示mysql的索引的使用
-- 创建索引
CREATE TABLE t25 (
	id INT,
	`name` VARCHAR(32));

-- 查询表是否有索引
SHOW INDEXES FROM t25;
-- 添加索引
-- 添加唯一索引
CREATE UNIQUE INDEX id_index ON t25 (id);
-- 添加普通索引 方法一
CREATE INDEX id_index ON t25 (id);
-- 方法二
ALTER TABLE t25 ADD INDEX id_index (id)
-- 如何选择
-- 1.如果某列的值，是不会重复的，则优先考虑使用 unique1索引，否则使用普通索引
-- 添加主键索引
CREATE TABLE t26 (
	id INT,
	`name` VARCHAR(32));
ALTER TABLE t26 ADD PRIMARY KEY (id);
SHOW INDEXES FROM t26;

-- 删除索引
DROP INDEX id_index ON t25;

-- 删除主键索引
ALTER TABLE t26 DROP PRIMARY KEY;

-- 修改索引，先删除，再添加新的索引

-- 查询索引
-- 1.
SHOW INDEX FROM t25
-- 2.
SHOW INDEXES FROM t25
-- 3.
SHOW KEYS FROM t25
-- 4.
DESC t25

-- 练习 1
-- 创建主键
CREATE TABLE order1 (
	id INT NOT NULL PRIMARY KEY,
	goods_name VARCHAR(32) NOT NULL DEFAULT '',
	orderer VARCHAR(32) NOT NULL DEFAULT '',
	num INT NOT NULL DEFAULT 0);
SHOW INDEXES FROM order1;

CREATE TABLE order2 (
	id INT NOT NULL,
	goods_name VARCHAR(32) NOT NULL DEFAULT '',
	orderer VARCHAR(32) NOT NULL DEFAULT '',
	num INT NOT NULL DEFAULT 0);
ALTER TABLE order2 ADD PRIMARY KEY (id);
SHOW INDEXES FROM order2;

-- 练习2
-- 创建 unique
CREATE TABLE menu1 (
	id VARCHAR(32) NOT NULL DEFAULT '',
	menu_name VARCHAR(32) NOT NULL DEFAULT '',
	cooker VARCHAR(32) NOT NULL DEFAULT '',
	orderer_id_card VARCHAR(32) UNIQUE NOT NULL DEFAULT '' ,
	price DOUBLE NOT NULL DEFAULT 0.0
	);
SHOW INDEXES FROM menu1;

CREATE TABLE menu2 (
	id VARCHAR(32) NOT NULL DEFAULT '',
	menu_name VARCHAR(32) NOT NULL DEFAULT '',
	cooker VARCHAR(32) NOT NULL DEFAULT '',
	orderer_id_card VARCHAR(32) NOT NULL DEFAULT '' ,
	price DOUBLE NOT NULL DEFAULT 0.0
	);
CREATE UNIQUE INDEX orderer_id_card_index ON menu2 (orderer_id_card);
SHOW INDEXES FROM menu2;

-- 练习三
-- 添加普通索引
CREATE TABLE sportman1 (
	id INT NOT NULL DEFAULT 0 PRIMARY KEY,
	`name` VARCHAR(32) NOT NULL DEFAULT '',
	hobby VARCHAR(32) NOT NULL DEFAULT ''
	);
CREATE INDEX name_index ON sportman1(`name`);
SHOW INDEXES FROM sportman1;

CREATE TABLE sportman2 (
	id INT NOT NULL DEFAULT 0 PRIMARY KEY,
	`name` VARCHAR(32) NOT NULL DEFAULT '',
	hobby VARCHAR(32) NOT NULL DEFAULT ''
	);
ALTER TABLE sportman2 ADD INDEX name_index(`name`);
SHOW INDEXES FROM sportman2;
```



## 事务

**1.start transaction	开始一个事务**
**2.savepoint 				保存点名  设置保存点**
**3.rollback to 			保存点名  回退事务**
**4.rollback  				回退全部事务**
**5.commit 				提交事务，所有的操作生效，不能回退**



细节：
1.如果不开始事务，默认情况下，dml操作是自动提交的，不能回滚;
2.如果没有创建保存点，执行rollback就会回退到事务开始时的状态。
3.可以创建多个保存点
4.你可以在提交之前，选择返回到哪个保存点
5.mysql事务机制需要 innodb 的存储引擎才可以支持事务
6.开始一个事务 start transaction,   set autocommit = off;

```Mysql
-- 事务的几个重要概念和具体操作
-- 事务操作的示意图
-- 1. 创建一张测试表
CREATE TABLE t27 
	( id INT,
	  `name` VARCHAR(32));
-- 2.开始事务
START TRANSACTION
-- 3. 设置保存点
SAVEPOINT a;
-- 4.执行操作
INSERT INTO t27 VALUES(100, 'tom3');
SELECT * FROM t27;

SAVEPOINT b;
INSERT INTO t27 VALUES(200, 'jack');
-- 回退到 B
ROLLBACK TO b;
-- 回退到 A
ROLLBACK TO a;
```



### 事务的隔离级别：

脏读：当一个事务读取另一个事务尚未提交的修改时
不可重复读： 同一个查询在同一事务中多次进行，由于其他提交事务所做的修改或删除，每次返回不同的结果集，此时发生不可重复读。
幻读：同一个查询在同一个事务中多次进行，由于其他提交事务所做的插入操作，每次返回不同的结果集。

**读未提交**（Read uncommited)		脏读：√		不可重复读：√		幻读：√
读已提交（Read commited)					   ×                             √                   √
可重复读（ Repeatable read)		             ×                             ×                   ×
可串行化（ Serializable)							 ×                             ×                    ×

```MYSQL
-- 演示 mysql 的事务隔离级别
-- 查看当前 mysql的隔离级别
SELECT @@tx_isolation;
-- 查看系统当前隔离级别
SELECT @@global.tx_isolation
-- 设置隔离级别
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED
-- 设置系统当前隔离级别
SET GLOBAL TRANSACTION ISOLATION LEVEL
```

---



## Mysql 表类型和存储引擎

**事务安全型：InnoDB**
**非事务安全型：其他**

**如何选择表的存储引擎**
1.如果你的应用不需要事务，处理的只是最基本的 CRUD 操作，那么 MyISAM 是不二选择，速度快
2.如果需要支持事务，选择 InnoDB
3.Memory 存储引擎就是将数据存储在内存中，由于没有磁盘 I / O 的等待，速度极快。
  但由于是内存存储引擎，所做的任何修改在服务器重启后都将消失。 【用户在线状态】

```MYSQL
-- 查看所有的存储应勤
SHOW ENGINES
-- 修改存储引擎
ALTER TABLE 表名 ENGINE = 
-- innodb 存储引擎是前面使用过的；
-- 1.支持事务；2. 支持外键； 3.支持行级锁

-- myisam 存储引擎
CREATE TABLE T28 (
	id INT,
	`name` VARCHAR(32)) ENGINE MYISAM;
-- 1.速度快 2. 不支持外键和事务 3.支持表级锁

START TRANSACTION; 
SAVEPOINT t1;
INSERT INTO t28 VALUES(1, 'jack');
SELECT * FROM t28;
ROLLBACK t1

-- memory 存储引擎
-- 1. 数据存储在内存中 [关闭Mysql服务，数据丢失，但是表结构还在]
--  2.执行速度很快 (没有 IO 读写） 3.默认支持索引（hash表）
CREATE TABLE T29 (
	id INT,
	`name` VARCHAR(32)) ENGINE MEMORY;
INSERT INTO t29
	VALUES(1, 'tom'),(2, 'jack'),(3, 'hsp');
SELECT * FROM t29;
```

---



## 视图

视图是一个虚拟表，其内容由查询定义。同真是的表一样，试图包含列，其数据来自对应的真实表

1.视图是根据基本来创建的，视图是虚拟的表
2.视图也有列，数据来自基表
3.通过视图可以修改基表的数据
4.基表的改变，也会影响到视图的数据

```MYSQL
-- 视图的使用
-- 创建一个视图 emp_view01, 只能查询 emp 表的(empno, ename, job 和 deptno) 信息
-- 创建视图
CREATE VIEW emp_view01
	AS 
	SELECT empno, ename, job, deptno FROM emp;
-- 查看视图
DESC emp_view01;

SELECT * FROM emp_view01;
SELECT empno, job FROM emp_view01;

-- 查看创建视图的指令
SHOW CREATE VIEW emp_view01;
-- 删除视图
DROP VIEW emp_view01;

-- 视图的细节
-- 1.视图只有一个视图结构文件
-- 2.视图的数据变化会影响到基表，基表的数据变化也会影响到视图
UPDATE emp_view01
	SET job = 'SALESMAN' WHERE empno = 7369;
SELECT * FROM emp_view01; -- 查询基表
-- 3. 从emp_view01 中选取两列做出新视图, 数据仍然来源于基表
CREATE VIEW emp_view02
	AS
	SELECT empno, ename FROM emp_view01;
SELECT * FROM emp_view02;


-- 三表联立
SELECT empno, ename, dname, grade
	FROM emp, dept, salgrade
	WHERE emp.deptno = dept.deptno AND
	( sal BETWEEN losal AND hisal);
-- 将得到的结果构建成视图
CREATE VIEW emp_view03
	AS
	SELECT empno, ename, dname, grade
	FROM emp, dept, salgrade
	WHERE emp.deptno = dept.deptno AND
	( sal BETWEEN losal AND hisal);
DESC emp_view03;
SELECT * FROM emp_view03;
```



### Mysql 用户管理

```MYSQL
-- Mysql 用户的管理
-- 可以根据不同的开发人员，赋给他相应的Mysql操作权限
-- 所以Mysql管理人员（root）根据需要创建不同的用户，赋予相应的权限

-- 1.创建新的用户
CREATE USER 'hsp_edu'@'localhost' IDENTIFIED BY '123456';
SELECT `host`, `user`, authentication_string 
	FROM mysql.user;
-- 2.删除用户
DROP USER 'hsp_edu'@'localhost';

-- 3.登录

-- 修改自己的密码
-- 自己修改自己
SET PASSWORD = PASSWORD('abcdef');
-- 修改其他人的密码，需要权限
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456') -- 权限不够
-- root 用户可以修改，权限高
```



### Mysql权限管理

**基本语法：** 
**授予权限：**
grant 	权限列表 	on 	库.对象名 	to 	'用户名'  	@	 '登录位置'  	[identified by '密码']

**回收权限：**
revoke 权限列表 on 库.对象名 from ''用户名' @  '登陆位置‘；

**权限生效：**
**如果权限没有生效，可以执行下面的语句**
FLUSH PRIVILEGES

```MYSQL
-- 演示用户权限的管理
-- 创建一个用户 zexi 密码 123
CREATE USER 'zexi'@'localhost' IDENTIFIED BY '123';
-- 使用 root 用户创建,表news
CREATE DATABASE testdb;
CREATE TABLE news(
	id INT,
	content VARCHAR(32)) CHARSET utf8;
-- 添加一条数据
INSERT INTO news VALUES(100, '北京新闻');
SELECT * FROM news;

-- 给zexi用户分配查看 news 表和 添加 news 的quanx1
GRANT SELECT, INSERT, UPDATE
	ON testdb.news
	TO 'zexi'@'localhost';
-- 修改 zexi的密码
SET PASSWORD FOR 'zexi'@'localhost' = PASSWORD('abc');
-- 回收 zexi 用户的所有权限
REVOKE SELECT, UPDATE, INSERT ON testdb.news FROM 'zexi'@'localhost';
-- 或者
REVOKE ALL ON testdb.news FROM 'zexi'@'localhost';
-- 删除用户
DROP USER 'zexi'@'localhost';
```



### 用户管理小细节

```Mysql
-- 用户管理细节
-- 在创建用户的时候，如果不指定Host,则为 %, %表示所有IP都有连接权限
CREATE USER jack;
SELECT `host`, `user` FROM mysql.user
-- CREATE USER 'xxx'@'192.168.1.%' 表示 xxx 用户 在 192.168.1.* 的ip可以登录 mysql
CREATE USER 'smith'@'192.168.1.%'
-- 在删除用户的时候，如果 host 不是 %，需要明确指定 '用户'@'host值'
DROP USER jack; -- 默认就是 DROP USER 'jack'@'%'
DROP USER 'smith'@'192.168.1.%';  -- 写清楚
```



## 本章作业：

```MYSQL
- 查看表结构
DESC dept;
DESC emp;

-- 1.显示所有部门名称
SELECT dname FROM dept;

-- 2.显示所有雇员名及其全年收入 13月（工资 + 补助）， 并指定列名为‘年收入’
-- 如果为null 返回0， 如果不为null 返回 comm
SELECT ename, (sal + IFNULL(comm, 0))* 13
	AS 'year_income' FROM emp;
	
-- 限制查询数据
-- 1.显示是工资超过 2850 的雇员姓名和工资
SELECT ename AS '姓名', sal AS '工资' 
	FROM emp
	WHERE sal > 2850;
	
-- 2.显示工资不在1500 到 2850 之间的所有雇员民及工资
-- 方法一：
SELECT ename, sal AS '工资' 
	FROM emp
	WHERE sal < 1500 OR sal > 2850;
-- 方法二：
SELECT ename, sal AS '工资'
	FROM emp
	WHERE NOT (sal >= 1500 AND sal <= 2850);
	
-- 3.显示编号为 7566 的雇员姓名及所在部门编号
SELECT empno, deptno FROM emp
	WHERE empno = 7566;
	
-- 4. 显示部门10和30中工资超过1500的雇员名及工资
SELECT ename, sal, deptno AS salary 
	FROM emp
	WHERE (deptno = 10 OR deptno = 30)
	HAVING sal > 1500;
	
-- 5. 显示无管理者的雇员名及岗位
SELECT ename, job AS 'positions' 
	FROM emp
	WHERE mgr IS null；

-- 排列数据
-- 1. 显示 1991年2月1日到 1991 年5月1日之间雇用的雇员名，岗位及雇佣日期，
-- 并以雇佣日期进行排序
SELECT ename, job, hiredate 
	FROM emp
	WHERE hiredate BETWEEN '1991-02-01' AND '1991-05-01'
	ORDER BY hiredate;
	
-- 2.显示获得补助的所有雇员名，工资及补助，并以工资降序的方式排列
SELECT ename, sal, comm AS '补助' FROM emp
	ORDER BY sal DESC;
	
-- homework03.sql
-- 1.选择部门30中的所有员工
SELECT * FROM emp
	WHERE deptno = 30;
	
-- 2.列出办事员（CLERK）的姓名，编号和部门编号
SELECT ename, empno, deptno, job
	FROM emp
	WHERE job = 'CLERK';
	
-- 3. 找出佣金高于酬金的员工
SELECT * FROM emp;
SELECT ename, sal, comm
	FROM emp
	WHERE sal < IFNULL(comm, 0);
	
-- 4. 找出佣金高于薪金 60 % 的员工
SELECT ename, sal AS salary, comm, sal * 1.6 AS '薪金百分之60'
	FROM emp
	WHERE IFNULL(comm,0) > (sal * 0.6);
	
-- 5. 找出部门10中所有经理(MANAGER)和部门20中所有办事员（CLERK）的详细资料
SELECT *
	FROM emp
	WHERE (deptno = 10 AND job = 'MANAGER')
	OR (deptno = 20 AND job = 'CLERK');

-- 6.找出部门10中所有经理（MANAGER),部门20中所有办事员(CLERK),
-- 还有既不是经理又不是办事员但薪酬金大于或等于2000的所有员工的详细资料
SELECT *
	FROM emp
	WHERE (deptno = 10 AND job = 'MANAGER')
	OR(deptno = 20 AND job = 'CLERK')
	OR ( NOT (job = 'MANAGER' OR job = 'CLERK')
		AND sal >= 2000);
		
-- 7.找出收取佣金的员工的不同工作
SELECT DISTINCT job
	FROM emp 
	WHERE comm IS NOT NULL;
	
-- 8. 找出不收取佣金或收取佣金低于100的员工
SELECT ename, comm
	FROM emp
	WHERE (comm IS NULL)
	OR (IFNULL(comm, 0) < 100);

-- 9. 找出各月倒数第3天受雇的所有员工
SELECT *
	FROM emp
	WHERE LAST_DAY(hiredate) - 2 = hiredate; 
	
-- 10. 找出早于12年前受雇的员工
SELECT *
	FROM emp
	WHERE DATE_ADD(hiredate, INTERVAL 12 YEAR) < NOW();

-- 11.以首字母小写的方式显示所有员工姓名
-- 方法一：
SELECT REPLACE(ename, LEFT(ename, 1), LCASE(LEFT(ename,1)))
	AS '首字母小写员工姓名'
	FROM emp;
-- 方法二：
SELECT CONCAT(LCASE(SUBSTRING(ename, 1, 1)), SUBSTRING(ename, 2))
	FROM emp;

-- 12.显示正好为5个字符的员工的姓名
SELECT ename AS '名字长度为5' 
	FROM emp
	WHERE LENGTH(ename) = 5;

=======================================================================
-- 显示不带有 "R"的员工姓名
SELECT ename
	FROM emp
	WHERE !(POSITION('R' IN ename));

-- 显示所有员工姓名的前三个字符
SELECT SUBSTRING(ename, 1, 3) AS '前三个字符'
	FROM emp;
	
-- 显示所有员工的姓名，用a替换所有"A“
SELECT REPLACE(ename, 'A', 'a')
	FROM emp;
	
-- 显示满10年服务年限的员工的姓名和受雇日期
SELECT ename, hiredate
	FROM emp
	WHERE DATE_ADD(hiredate, INTERVAL 10 YEAR) <= NOW();

-- 显示员工的详细资料，按姓名排列
SELECT * FROM emp
	ORDER BY ename;

-- 显示员工的姓名和受雇日期，1根据其服务年限，将最老的员工排在最前面
SELECT ename, hiredate
	FROM emp
	ORDER BY hiredate DESC;

-- 显示所有员工的姓名、工作和酬金，按照工作降序排列，若工作相同则按薪酬排序
SELECT ename, job, sal
	FROM emp
	ORDER BY job DESC, sal DESC;

-- 显示所有员工的姓名、加入公司的年份和月份，按受雇日期所在月排序，
-- 若月份相同则将最早年份的员工排在最前面
SELECT ename, YEAR(hiredate), MONTH(hiredate)
	FROM emp
	ORDER BY MONTH(hiredate), YEAR(hiredate);

-- 显示一个月为30天的情况所有员工的日酬金，忽略余数
SELECT ename, FLOOR((sal / 30))AS daily_sal
	FROM emp;
	
-- 找出在（任何年份的）2月受聘的所有员工
SELECT ename, hiredate, MONTH(hiredate)
	FROM emp
	WHERE MONTH(hiredate) = 2;

-- 对于每个员工，显示其加入公司的天数
SELECT ename, hiredate, DATEDIFF(NOW(), hiredate) AS '已经入职'
	FROM emp;

-- 显示姓名字段的任何位置包含 ’A‘ 的所有员工的姓名
SELECT ename 
	FROM emp
	WHERE ename LIKE '%A%';

-- 以年月日的方式显示所有员工的服务年限
SELECT ename, hiredate, 
	CONCAT(FLOOR((DATEDIFF(NOW(), hiredate) / 365)), '年 ',
	FLOOR(MOD(DATEDIFF(NOW(), hiredate), 365) / 30), '月 ', 
	MOD(MOD(DATEDIFF(NOW(), hiredate), 365),30), '天')  AS '服务年限' 
	FROM emp;
============================================================================

-- homework04
-- 1.列出至少有一个员工的所有部门
SELECT * FROM dept;
SELECT * FROM emp;
-- 列出所有部门的对应员工人数
SELECT dname, COUNT(empno)
	FROM dept, emp
	WHERE dept.deptno = emp.deptno
	GROUP BY dname;
-- 列出至少有一个员工的部门
SELECT temp.dname
	FROM (
	SELECT dname, COUNT(empno) AS total_nums
	FROM dept, emp
	WHERE dept.deptno = emp.deptno
	GROUP BY dname) AS temp
	WHERE total_nums > 1;	

-- 2.列出薪酬比 'SMITH' 多的所有员工
-- 查看 SMITH 的薪酬
SELECT  sal
	FROM emp
	WHERE ename = 'SMITH';
-- 查找比SMITH薪酬多的所有员工
SELECT ename, sal AS salary
	FROM emp,(
		SELECT sal AS salary_temp
		FROM emp
		WHERE ename = 'SMITH'
	)temp
	WHERE emp.sal > temp.salary_temp;

-- 3.列出受雇日期晚于其直接上级的所有员工
-- 查找所有上、下级员工的受雇日期
SELECT emp.empno AS '下级', emp.mgr AS '上级', 
	emp.hiredate AS '下级受雇日期', temp.hiredate AS '上级受雇日期'
	FROM emp, (
		SELECT empno, mgr, hiredate 
		FROM emp
		WHERE mgr
		) temp
	WHERE emp.mgr = temp.empno
	AND emp.hiredate > temp.hiredate;

-- 4.列出部门名称和这些部门的员工信息，同时列出那些没有员工的部门
-- 列出部门名称及其部门的员工信息
SELECT dname, emp.*
	FROM dept LEFT JOIN emp
	ON dept.deptno = emp.deptno;

-- 5.列出所有'CLERK'办事员的姓名及其部门名称
SELECT ename, job, dname
	FROM emp, dept
	WHERE emp.deptno = dept.deptno
	AND job = 'CLERK';

-- 6.列出最低酬金大于 1500 的各种工作
SELECT MIN(sal) AS min_sal, job
	FROM emp
	GROUP BY job
	HAVING min_sal > 1500;

-- 7. 列出部门 "SALES” 工作的员工的姓名
SELECT ename, dept.dname
	FROM emp, dept
	WHERE emp.deptno = dept.deptno
	AND dept.dname = 'SALES';

-- 8.列出薪酬高于公司平均酬金的所有员工
SELECT  ename,sal, temp.avg_sal
	FROM emp, (
		SELECT AVG(sal) AS avg_sal
		FROM emp) temp
	WHERE sal > temp.avg_sal;

-- 9. 列出与 "SCOTT" 从事相同工作的所有员工
SELECT ename, job
	FROM emp
	WHERE job = (
		SELECT job
			FROM emp
			WHERE ename = 'SCOTT')
	AND ename != 'SCOTT';

-- 10. 列出薪金高于在部门30工作的所有员工的薪金的员工姓名和薪金
SELECT ename, sal
	FROM emp
	WHERE sal > (
		SELECT MAX(sal)
			FROM emp, dept
			WHERE emp.deptno = dept.deptno
			AND dept.deptno = 30);

-- 11. 列出每个部门工作的员工数量、平均工资和平均服务期限
SELECT deptno, COUNT(*) AS total_nums, 
	FORMAT(AVG(sal), 2) AS avg_sal,
	FLOOR(AVG(DATEDIFF(NOW(), hiredate)) / 365) AS avg_year
	FROM emp
	GROUP BY deptno;

-- 12.列出所有员工的姓名、部门名称和工资
SELECT ename, sal, dname
	FROM emp, dept
	WHERE emp.deptno = dept.deptno;
	
-- 13.列出所有部门的详细信息和部门人数
SELECT dept.*, temp.total_nums
	FROM dept LEFT JOIN (
		SELECT COUNT(ename) AS total_nums, deptno
			FROM emp
			GROUP BY deptno) temp
	ON dept.deptno = temp.deptno;

-- 14.列出各种工作的最低工资
SELECT job, MIN((sal + IFNULL(comm, 0))) AS total_min_salary
	FROM emp
	GROUP BY job;
	
-- 15.列出 MANAGER （经理）的最低薪金
SELECT MIN(sal) AS min_manager_sal
	FROM emp
	WHERE job = 'MANAGER';
	
-- 16. 列出所有员工的年工资，按年薪从低到高排序
SELECT emp.*, ((sal + IFNULL(comm, 0)) * 12) AS year_salary
	FROM emp
	ORDER BY year_salary;
```



## 综合： 建表、查询、删除查找

```MYSQL
-- homework05
-- 创建表
DROP TABLE class;
CREATE TABLE class (
	classid INT NOT NULL DEFAULT 0,
	`subject` VARCHAR(32) NOT NULL DEFAULT '',
	deptname VARCHAR(32) NOT NULL DEFAULT '',
	enrolltime INT NOT NULL,
	num INT NOT NULL DEFAULT 0,
	PRIMARY KEY (classid),
	FOREIGN KEY (deptname) REFERENCES department(deptname)
	);

DROP TABLE student
CREATE TABLE student (
	studentid INT NOT NULL DEFAULT 0,
	`name` VARCHAR(32) NOT NULL DEFAULT '',
	age INT NOT NULL DEFAULT 0,
	classid INT NOT NULL DEFAULT 0,
	PRIMARY KEY (studentid),
	FOREIGN KEY (classid) REFERENCES class(classid));

DROP TABLE department	
CREATE TABLE department (
	departmentid INT NOT NULL DEFAULT 0,
	deptname VARCHAR(32) NOT NULL DEFAULT '' UNIQUE ,
	PRIMARY KEY(departmentid));

DESC class;
DESC student;
DESC department;

-- 插入数据
INSERT INTO department VALUES(001, '数学'), (002, '计算机'),
		(003, '化学'), (004, '中文'), (005, '经济');
SELECT * FROM department ORDER BY departmentid;

INSERT INTO class 
	VALUES(101, '软件', '计算机', 1995, 20),
	(102, '微电子', '计算机', 1996, 30),
	(111, '无机化学', '化学', 1995, 29),
	(112, '高分子化学', '化学', 1996, 25),
	(121, '统计数学', '数学', 1995, 20),
	(131, '现代语言', '中文', 1996, 20),
	(141, '国际贸易', '经济', 1997, 30),
	(142, '国际金融', '经济', 1996, 14);
SELECT * FROM class ORDER BY classid;

INSERT INTO student VALUES
	(8101, '张三', 18, 101),
	(8102, '钱四', 16, 121),
	(8103, '王玲', 17, 131),
	(8105, '李飞', 19, 102),
	(8109, '赵四', 18, 141),
	(8110, '李可', 20, 142),
	(8201, '张飞', 18, 111),
	(8302, '周瑜', 16, 112),
	(8203, '王亮', 17, 111),
	(8305, '董庆', 19, 102),
	(8409, '赵龙', 18, 101),
	(8510, '李丽', 20, 142);
SELECT * FROM student;

-- 完成下列查找功能
-- 找出所有姓李的学生
SELECT * FROM student
	WHERE `name` LIKE "%李%";

-- 找出所有开设超过一个专业的系的名字
SELECT COUNT(*) AS nums, deptname
	FROM class
	GROUP BY deptname HAVING nums > 1;
	
-- 列出人数大于等于30的系的编号和名字

SELECT SUM(num) AS nums, class.deptname, departmentid
	FROM class, department
	WHERE class.deptname = department.deptname
	GROUP BY deptname
	HAVING nums >= 30;

-- 学校增加了一个物理系，编号为006
INSERT INTO department VALUES(006, '物理');
SELECT * FROM department;

-- 学生张三退学了， 请更新相关的表
-- 开启事务
START TRANSACTION;
-- 班级人数 -1
UPDATE class SET num = num -1
	WHERE classid =( SELECT classid
				FROM student
				WHERE `name` = '张三');
DELETE FROM student
	WHERE `name` = '张三';

-- 提交事务
COMMIT

SELECT * FROM student;
SELECT * FROM class;
```

