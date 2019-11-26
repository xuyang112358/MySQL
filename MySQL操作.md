# MySQL
启动数据库服务：net start（stop） mysql80 //80是版本号

若不成功，则command+x用管理员方式打开cmd，再启动

省力方法：command+R,输入services.msc,找到mySQL启动

登陆数据库：mysql -h（ip） -u root -p//输入ip可登陆他人数据库，端口要一样

解决问题：<br>
远程登陆若出现错误 Authentication plugin 'caching_sha2_password' cannot be loaded 需要兼容新老版本的认证方式<br>
>> ALTER USER 'root'@'localhost' IDENTIFIED BY 'root' PASSWORD EXPIRE NEVER; #修改加密规则 <br>
>> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root'; #更新一下用户的密码<br>
>> FLUSH PRIVILEGES; #刷新权限<br>

进入一个库：use 数据库名

使用数据库：
	查询当前正在使用的数据库名称：select database();
	使用数据库：use <数据库名>;

单行注释：-- 注释 或者 #注释<br>
多行注释：/*注释*/<br>

数据库的关键字不区分大小写<br>

退出数据库：exit（quit）<br>


/*授予用户通过外网IP对于该数据库的全部权限*/<br>

　　grant all privileges on `test`.* to 'test'@'%' ;<br>

　 /*授予用户在本地服务器对该数据库的全部权限*/<br>

　　grant all privileges on `test`.* to 'test'@'localhost';   <br>
  
   grant select on test.* to 'user1'@'localhost';  /*给予查询权限*/<br>

   grant insert on test.* to 'user1'@'localhost'; /*添加插入权限*/<br>

   grant delete on test.* to 'user1'@'localhost'; /*添加删除权限*/<br>

   grant update on test.* to 'user1'@'localhost'; /*添加权限*/<br>

　 flush privileges; /*刷新权限*/<br>


# 数据定义语言DDL（Data Ddefinition Language）
create,drop,alter主要为以上操作 操作数据库和表。

## 查询：
	查看所有的数据库：show databases;
	查看当前在使用的数据库：select database();
	查看某个数据库(表)的创建语句(可查看字符集)：show create database(table) <数据库名>(<表名>);
	显示所有表：show tables
 
	查询表结构：desc <表名>




## 创建：
	创建数据库：create database (if not exists) <数据库名> (character set gbk(utf8))

	创建表：
	create table <表名>(
	列名1 数据类型1,
	列名2 数据类型2,
	...
	列名n 数据类型n
	);	
        设置主键并设置其自增，列名 INT PRIMARY KEY AUTO_INCREMENT
	数据类型：
	int：整数类型 age int
	double：小数类型 score double(5,2)
	date：日期，只包含年月日，yyyy-MM-dd
	datetime：日期，包含年月日，时分秒，yyyy-MM-dd HH：mm：ss
	timestamp:时间戳，赋默认值 DEFAULT CURRENT_TIMESTAMP后会自动添加系统时间
	varchar：字符串 name varchar(20):姓名最大20个字符
	create table (if not exists) student(
	id int,
	name varchar(32),
	age int,
	score double(4,1),
	birthday date,
	insert_time timestamp
	);
	复制表：
	create table 表名 like 被复制的表名;
## 修改：
	修改表名：alter table 表名 rename to 新的表名;
	修改数据库(表)字符集：alter database(table) <数据库名>(<表名>) character set utf8;
	添加一列：alter table 表名 add 列名 数据类型;
	修改列名称、类型：alter table <表名> change 列名 新列名 新数据类型;
	只修改列类型：alter table <表名> modify 列名 新数据类型;
	

## 删除：
 	删除一个数据库：drop (if exists) database <数据库名>
	删除表：drop table (if exists) <表名>
	删除列：alter table <表名> drop 列名;



# 数据查询语言DQL（Data Query Language）
查询表中数据

## 例子：
    create table student3(
    id int, -- 编号
    name varchar(20), -- 姓名
    age int, -- 年龄
    sex varchar(5), -- 性别
    address varchar(100), -- 地址
    math int, -- 数学
    english int -- 英语
    );

    insert into student3(id,name,age,sex,address,math,english) values
    (1,'马云',55,'男','杭州',66,78),
    (2,'马化腾',45,'女','深圳',98,87),
    (3,'马景涛',55,'男','香港',56,77),
    (4,'柳岩',20,'女','湖南',76,65),
    (5,'柳青',20,'男','湖南',86,NULL),
    (6,'刘德华',57,'男','香港',99,99),
    (7,'马德',22,'女','香港',99,99),
    (8,'德玛西亚',18,'男','南京',56,65);

## 查询表中的记录：
	select
		字段列表
	from
		表名列表
	where
		条件列表
	group by         
		分组字段
	having
		分组之后的条件
	order by
		排序
	limit
		分页限定 
## 基础查询：
	多个字段的查询:
		select 字段名1,字段名2,字段名3.... from <表名>;如果查询所有字段，则可以使用*来替代字段列表
	去除重复：
		select distinct 字段名1,字段名2,字段名3.... from <表名>;需要所有的字段一样才会去重
	计算列：
		一般可以使用四则运算来计算一些数值列的值，但是null参与的运算，计算结果都为null
		ifnull(表达式1，表达式2)： 
			表达式1：哪个字段需要判断是否null
			表达式2：如果该字段为null后的替换值
	起别名：
		as:as也可以省略
		例：SELECT NAME,math 数学,english 英语,math+IFNULL(english,0) AS 总分 FROM student3;
## 条件查询：
	where子句后跟条件
	运算符：
		> < <= >= = <>(不等号)
		between...and...(范围里)
		in(集合范围)
		like：模糊查询
			占位符：
				_:单个任意字符
				%:任意多个字符
		is null;null的判断要用is或is not
		and 或 &&
		or 或 ||
		not 或 ！
## 排序查询：
	语法：order by 字段1 排序方式1,字段2 排序方式2...字段n 排序方式n...
	排序方式：ASC:升序，默认的。DESC：降序。如果有多个排序条件，则当前边的条件值一样时才会去判断第二条件。
## 聚合函数：
	将一列数据作为一个整体，作一个纵向计算	
	count：计算个数
	max：计算最大值
	min：计算最小值
	sum：计算和
	avg：计算平均值
	注意：聚合函数的计算会排除null值
		解决方案：选择不包含null的列进行计算，或者使用ifnull函数
		一般选择非空的列：主键

## 分组查询：
	语法：group by 分组字段;
	注意：
		分组之后查询的字段：分组字段、聚合函数
		where和having的区别？
			where在分组之前进行限定：如果不满足条件，则不参与分组。having在分组之后进行限定，如果不满足结果，则不会被查询出来
			where后不可以跟聚合函数，having可以进行聚合函数的判断
			
	
## 分页查询：
	语法：limit 开始的索引，每页查询的条数;
	公式：开始的索引 = (当前的页码 - 1) * 每页显示的条数
	SELECT * FROM student3 LIMIT 0,3; -- 第一页

	SELECT * FROM student3 LIMIT 3,3; -- 第二页

	SELECT * FROM student3 LIMIT 6,3; -- 第三页
	注意：limit是一个mysql的“方言”
  
## 多表查询：
	查询语法：
		select
			列名列表
		from 
			表名列表
		where
			...


	准备sql：
		-- 创建部门表
	CREATE TABLE dept(
		id INT PRIMARY KEY AUTO_INCREMENT,
		NAME VARCHAR(20)
	);
	INSERT INTO dept (NAME) VALUES ('开发部'),('市场部'),('财务部');

	-- 创建员工表
	CREATE TABLE emp(
		id INT PRIMARY KEY AUTO_INCREMENT,
		NAME VARCHAR(20),
		gender CHAR(1), -- 性别
		salary DOUBLE, -- 工资
		join_date DATE, -- 入职日期
		dept_id INT,
		CONSTRAINT emp_dept_id FOREIGN KEY (dept_id) REFERENCES dept(id) -- 外键，关联部门表（部门表的主键）
	);

	INSERT INTO emp(NAME,gender,salary,join_date,dept_id) VALUES
		('孙悟空','男',7200,'2013-02-24',1),
		('猪八戒','男',3600,'2010-02-24',2),
		('唐僧','男',9000,'2008-08-08',2),
		('白骨精','女',5000,'2015-10-07',3),
		('蜘蛛精','女',4500,'2011-03-14',1);
	笛卡尔积：
		有两个集合A，B，取这两个集合的所有组成情况
		要完成多表查询，需要消除无用数据
### 多表查询的分类：
		1.内连接查询
			隐式内连接
				SELECT 
					t1.`NAME`,
					t1.`gender`,
					t2.name
				FROM
					emp t1,
					dept t2
				WHERE 
					t1.`dept_id` = t2.`id`;
			显式内连接
				语法：select 字段列表 from 表名1 inner join 表名2 on 条件
			内连接查询：
				1.从哪些表中查询数据
				2.条件是什么
				3.查询哪些字段
		2.外链接查询：
			1.左外连接
				语法：select 字段列表 from 表1 left join 表2 on 条件;
				查询的是左表所有的数据以及其交集部分
			2.右外连接：
				语法：select 字段列表 from 表1 right join 表2 on 条件;
				查询的是右表所有的数据以及其交集部分
				注：左右是相对的概念，表1和表2可以互换
		3.子查询：
			概念：查询中嵌套查询，称嵌套查询为子查询
			-- 子查询
			SELECT * FROM emp WHERE emp.`salary` = (SELECT MAX(salary) FROM emp);
			不同情况：
				1.子查询的结果是单行单列的
				子查询可以作为条件，使用运算符取判断

				2.子查询的结果是多行单列的
				SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept WHERE NAME='财务部' OR NAME='市场部');

				3.子查询的结果是多行多列的
				子查询可以作为一张虚拟表。
				-- 查询员工入职日期是2011-11-11日之后的员工信息和部门信息
				SELECT * FROM (SELECT * FROM dept,emp WHERE emp.`dept_id` = dept.`id`) t2 WHERE t2.join_date>'2011-11-11';
				或者 SELECT * FROM dept t1,(SELECT * FROM emp WHERE emp.`join_date`>'2011-11-11') t2 WHERE t1.id = t2.dept_id;
				-- 内连接模拟子查询
				SELECT * FROM emp t1,dept t2 WHERE t1.`dept_id` = t2.`id` AND t1.`join_date` >'2011-11-11';

## 约束
	概念：对表中的数据进行限定，保证数据的正确性、有效性和完整性。
	分类：
		主键约束：primary key
		非空约束：not null
		唯一约束：unique
		外键约束：foreign key

### 非空约束：
		创建表时添加约束：
			CREATE TABLE stu(
			id INT PRIMARY KEY AUTO_INCREMENT, -- 设置主键，设置自增
			NAME VARCHAR(20) NOT NULL -- 非空
			);
		创建表完后，添加非空约束：
			ALTER TABLE stu MODIFY NAME VARCHAR(20) NOT NULL;
		删除name的非空约束：
			ALTER TABLE stu MODIFY NAME VARCHAR(20);
### 唯一约束：值不能重复
			CREATE TABLE stu(
			id INT PRIMARY KEY AUTO_INCREMENT, -- 设置主键，设置自增
			phone_number VARCHAR(20) UNIQUE -- 添加了唯一约束
			);
			注意mysql中，唯一约束的列的值可以有多个null而不冲突
			
			-- 删除唯一约束
			ALTER TABLE stu DROP INDEX phone_number;

			-- 在创建表后，添加唯一约束
			ALTER TABLE stu MODIFY phone_number varchar(20) unique;
### 主键约束：
		注意：	
			非空且唯一（综合了以上两种约束）		
			一张表只能有一个字段为主键
			主键就是表中记录的唯一标识

		在创建表时，添加主键约束：
			CREATE TABLE stu(
			id INT PRIMARY KEY, -- 给id添加主键约束
			phone_number VARCHAR(20)
			);
		
		删除主键：
			alter table stu drop primary key;
		
		创建完表后添加主键：
			alter table stu modify id int primary key;
		自动增长：
			概念：如果某一列是数值类型的，使用auto_increment可以来完成值的自动增长,它的值和上一条数值有关

			在创建表时，添加主键约束，并且完成主键自增长
			CREATE TABLE stu(
			id INT PRIMARY KEY auto_increment, -- 给id添加主键约束
			phone_number VARCHAR(20)
			);
			
			删除自动增长：
				alter table stu modify id int;
			添加自动增长：
				alter table stu modify id int auto_increment;
	
### 外键约束：
		在创建表时，添加外键：
			语法：
				create table <表名>(
				...
				-- 外键列
				constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称)
				-- 简化写法
				foreign key (外键列名称) references 主表名称(主表列名称)
				)
		删除外键：
			ALTER TABLE <表名> DROP FOREIGN KEY 外键名;

		在创建表后，添加外键：
			alter table <外表名> add constraint <外键名> foreign key (外键列名称) references 主表名称(主表列名称);	
			注：外键值是可以为null的
		级联操作：
			添加级联操作：
				语法：alter table <外表名> add constraint <外键名> foreign key (外键列名称) references 主表名称(主表列名称)
					ON UPDATE CASCADE ON DELETE CASCADE;-- 级联更新 -- 级联删除	
			
		
				分类：
					级联更新：ON UPDATE CASCADE
					级联删除：ON DELETE CASCADE

# 数据库的设计
	1.多表之间的关系
		分类：
			1.一对一
			2.一对多
			3.多对多
		实现关系：
			1.一对多：在多的一方建立外键，指向一的主键
			2.多对多：需要借助第三张中间表
			3.一对一：可以在任意一方添加唯一外键指向另一方的主键
	
	2.数据库设计的范式
		概念：在设计数据库时，需要遵循的规范
		第一范式：数据库表的每一列都是不可分割的原子数据项，而不能是集合，数组，记录等非原子数据项。
		第二范式：在1NF的基础上，非码属性必须完全依赖于候选码（在1NF基础上消除非主属性对主码的部分函数依赖）
			几个概念：
				1.函数依赖：如果通过A属性（属性组）的值，可以唯一确定B属性的值。则称B依赖于A
					例如：学号（属性）-->姓名  （姓名，课程）（属性组）-->分数
				2.完全函数依赖：依赖的属性的值必须由被依赖的属性组里的所有属性共同确定（完全的含义）
				3.部分函数依赖：依赖的属性的值只需由被依赖的属性组里的某一些属性确定
				4.传递函数依赖：A-->B,B-->C,A最终能确定唯一C，则称C传递依赖于A
				5.码：如果在一张表中，一个属性或属性组，被其他所有属性所完全依赖，则称这个属性（属性组）为该表的码
		第三范式：在2NF基础上，任何非主属性不依赖于其它非主属性（在2NF基础上消除传递依赖）

## 数据库的备份和还原

	1.命令行：
		语法：
			备份：mysqldump -u用户名 -p密码 数据库名 > 保存的路径
			还原:
				1.登录数据库
				2.创建数据库
				3.使用数据库
				4.执行文件。 source 文件路径
	2.图形化工具： 备份/导出 ------ 执行SQL脚本
	

# 数据操纵语言DML（Data Manipulation Language）
insert,update,delete

## 增删改表中数据：
    添加数据：
      insert into 表名(列名1，列名2...列名n) values(值1，值2...值n)列名和值要一一对应；如果表名后不定义列名，则默认给所有列添加值，但数量要一样，要补NULL
      除了数字类型，其他类型的值需要引号
    删除数据：
      delete from <表名> where 条件;如果不加条件，则删除表中所有列,但这样效率低
      truncate table <表名>;删除表，然后再创建一个一模一样的新表，效率高
    修改数据：
      update <表名> set 列名1=值1,列名2=值2...列名n=值n where 条件;如果不加任何条件，则会将表中所有记录全部修改			



# 数据控制功能DCL（Data Control Language）
grant,revoke,commit,rollback管理用户、授权。

				
				

# 事务：
	1.事务的基本介绍：
		1.概念：如果一个包含多个步骤的业务操作，被事务管理，那么这些步骤要么同时成功，要么同时失败。
		2.操作：
			CREATE TABLE account(
			id INT PRIMARY KEY AUTO_INCREMENT,
			NAME VARCHAR(10),
			balance DOUBLE
			);

			-- 添加数据
			INSERT INTO account (NAME,balance) VALUES ('张三',1000),('李四',1000);
		
			-- 0.开启事务
			START TRANSACTION;
	
			-- 1.张三账户 -500	
			UPDATE account SET balance = balance - 500 WHERE NAME='zhangsan'; 
			
			出错了...

			-- 2.李四账户 +500
			UPDATE account SET balance = balance + 500 WHERE NAME='lisi';
	
			-- 发现执行没有问题，提交事务
			COMMIT;
	
			-- 发现出问题了，回滚事务
			ROLLBACK;
		注意：MySQL中事务默认自动提交：
			一条DML（增删改）语句会自动提交一次事务，除非开启一个事务，那么DML就会变成手动提交。
		修改事务的默认提交方式：
			查看事务的默认提交方式：SELECT @@autocommit；--
	2.事务的四大特征
		1.原子性：是不可分割的最小单位，要么同时成功，要么同时失败
		2.持久性：当事务提交或回滚后，数据库会持久化的保存数据
		3.隔离性：多个事务之间。相互独立。
		4.一致性：实务操作前后数据总量不变
	3.事务的隔离级别（了解）
		概念：多个事务之间是隔离的，相互独立的。但是如果多个事务操作同一批数据，则会引发一些问题，设置不同的隔离级别可以解决这些问题。
		存在问题：
			1.脏读：一个事务，读取到另一个事务中没有提交的数据
			2.不可重复读（虚读）：在同一个事务中，两次读取到的数据不一样
			3.幻读：一个事务操作（DML）数据表中所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改。
		隔离级别：
			1.read uncommitted：读未提交
				产生的问题：脏读、不可重复读、幻读
			2.read committed：读已提交
				产生的问题：不可重复读、幻读
			3.repetable read:可重复读
				产生的问题：幻读
			4.serializable:串行化
				解决所有问题

			安全性越来越大，效率越来越低
			数据库查询隔离级别：
				select @@tx_isolation;
			数据库设置隔离级别：
				set globle transaction isolation level 级别字符串；
			


		 






