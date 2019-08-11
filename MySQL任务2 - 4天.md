# MySQL任务2 - 4天

### 2.1 MySQL基础（二）- 表操作学习内容

1.MySQL[表数据类型](https://www.yiibai.com/mysql/data-types.html)

* 数值数据类型
* 布尔数据类型
* 字符串数据类型
* 日期和时间数据类型
* 空间数据类型

2.用SQL语句创建表

* 语句解释

  ```sql
  --table_name在数据库中是唯一的，使用IF NOT EXISTS来防止创建已存在的新表而产生错误
  CREATE TABLE [IF NOT EXISTS] table_name(
  	column_list
      --指定表的列表，字段的列用“,”分隔
  )engine=table_type;
  --存储引擎，MySQL默认使用“InnoDB”
  ```

  要在`CREATE TABLE`语句中为表定义列：

  ```sql
  column_name data_type[size] [NOT NULL|NULL] [DEFAULT value]
  --列名 数据类型[大小] 该列是否接受NULL值 指定列的默认值
  [AUTO_INCREMENT]
  --指示每当将新行插入到表中时，列的值会自动增加。每个表都有一个且只有一个AUTO_INCREMENT列。
  ```

* 设定列类型 、大小、约束(如上)

* 设定主键

  如果要将表的特定列设置为主键：

  ```sql
  PRIMARY KEY (col1,col2,...)
  ```

3.用SQL语句向表中添加数据

* 语句解释

  插入一行或多行

  ```sql
  INSERT INTO table(column1,column2,...)
  VALUES (value1,value2,...),
  	   (value1,value2,...),
  	   (value1,value2,...),
  ...;
  ```

  如果为表中的所有列指定相应列的值，则可以忽略INSERT语句中的列表：

  ```sql
  INSERT INTO table
  VALUES (value1,value2,...),
  	   (value1,value2,...),
  	   (value1,value2,...),
  ...;
  ```

  > 不用为自动递增列(例如`taskid`列)指定值，因为MySQL会自动为自动递增列生成值。

* 具有SELECT子句的MySQL INSERT

  可以使用SELECT语句返回的列和值来填充`INSERT`语句的值，可以使用`INSERT`和`SELECT`子句完全或部分复制表

  ```sql
  INSERT INTO table_1
  SELECT c1,c2, FROM table_2;
  ```

  将tasks表复制到tasks_bak表:

  ```sql
  CREATE TABLE tasks_bak LIKE tasks;
  
  INSERT INTO tasks_bak
  SELECT * FROM tasks;
  ```

4.用SQL语句删除表

* DELETE

  ```sql
  delete  table table_name
  ```

  删除整个表的数据，删除内容，不删除定义，不释放空间。

  用delete删除 数据，然后添加,可以看到添加之后id标识不连续。（说明delete删除不释放空间）

  如果重新插入数据时对应的id在上次基础之上递增 4、5、6....

  ```sql
  DELETE FROM table_name
  WHERE condition;
  ```

* DROP

  ```sql
  drop table table_name
  ```

  删除内容和定义，释放空间。相当于删除了整个表，和结构。

  以后要新增数据是不可能的,除非新增一个表,下次要使用的时候要重新创建表的结构再插入数据

* TRUNCATE

  ```sql
  truncate table table_name
  ```

  删除内容，释放空间但不删除定义。与drop不同的是，truncate只是清空数据表而已，不删除表结构。

  truncate table test 后,向test表添加数据,id标识列连续了(体现了truncate删除是释放空间）插入的字段的id重新从1开始递增 1、2、3.....

* 不同方式的区别

  >  当你不再需要该表时，用 drop;
  >
  > 当你仍要保留该表，但要删除所有记录时，用 truncate; 
  >
  > 当你要删除部分记录时,用 delete.

  执行速度，一般来说: drop> truncate > delete。

5.用SQL语句修改表

* 修改列名

  ```sql
  ALTER TABLE 旧表名 CHANGE 新表名 varchar(10);
  ```

* 修改表中数据

  ```sql
  UPDATE 表名 SET 字段名 = 新数据 WHERE 过滤条件
  ```

* 删除行

  ```sql
  DELETE FROM 表名 WHERE 字段=值
  ```

* 删除列

  ```sql
  ALTER TABLE 表名 DROP COLUMN 列名
  ```

* 新建列

  ```sql
  ALTER TABLE 表名 ADD COLUMN 新增列名 varchar(20) not null after 原来的列
  ```

* 新建行

  ```sql
  INSERT INTO table(column1,column2,...)
  VALUES (value1,value2,...);
  ```

### 作业

#### 项目三：超过5名学生的课

创建如下所示的courses 表 ，有: student (学生) 和 class (课程)。
例如,表:
+---------+------------+
| student | class      |
+---------+------------+
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |
| A      | Math       |
+---------+------------+

编写一个 SQL 查询，列出所有超过或等于5名学生的课。
应该输出:
+---------+
| class   |
+---------+
| Math    |
+---------+
Note:
学生在每个课中不应被重复计算。

###### 解答：

制作表

```sql
CREATE TABLE courses (
	student VARCHAR(50) NOT NULL,
	class VARCHAR(50) NOT NULL
);

INSERT INTO courses
	VALUES('A','Math');
INSERT INTO courses
	VALUES('B','English');
INSERT INTO courses
	VALUES('C','Math');
INSERT INTO courses
	VALUES('D','Biology');
INSERT INTO courses
	VALUES('E','Math');
INSERT INTO courses
	VALUES('F','Computer');
INSERT INTO courses
	VALUES('G','Math');
INSERT INTO courses
	VALUES('H','Math');
INSERT INTO courses
	VALUES('I','Math');
INSERT INTO courses
	VALUES('A','Math');
```

查询

```sql
SELECT DISTINCT class FROM courses GROUP BY class HAVING count(*) >= 5;
```



#### 项目四：交换工资（难度：简单）

创建一个 salary表，如下所示，有m=男性 和 f=女性的值 。
例如:

| id   | name | sex  | salary |
| ---- | ---- | ---- | ------ |
| 1    | A    | m    | 2500   |
| 2    | B    | f    | 1500   |
| 3    | C    | m    | 5500   |
| 4    | D    | f    | 500    |

交换所有的 f 和 m 值(例如，将所有 f 值更改为 m，反之亦然)。要求使用一个更新查询，并且没有中间临时表。
运行你所编写的查询语句之后，将会得到以下表:

| id   | name | sex  | salary |
| ---- | ---- | ---- | ------ |
| 1    | A    | f    | 2500   |
| 2    | B    | m    | 1500   |
| 3    | C    | f    | 5500   |
| 4    | D    | m    | 500    |

###### 解答

创建表

```sql
CREATE TABLE salary(
	id INT NOT NULL PRIMARY KEY,
	name VARCHAR(50) NOT NULL,
	sex VARCHAR(50) NOT NULL,
	salary INT NOT NULL
);

INSERT INTO salary
	VALUES("1","A","m",2500);
INSERT INTO salary
	VALUES("2","B","f",1500);
INSERT INTO salary
	VALUES("3","C","m",5500);
INSERT INTO salary
	VALUES("4","D","f",500);
```

查询

```sql
UPDATE salary SET sex = 
case sex
	WHEN 'm' then 'f'
	WHEN 'f' then 'm'
END

SELECT * FROM salary;
```



#### 项目五：有趣的电影 （难度：简单）

某城市开了一家新的电影院，吸引了很多人过来看电影。该电影院特别注意用户体验，专门有个 LED显示板做电影推荐，上面公布着影评和相关电影描述。

作为该电影院的信息部主管，您需要编写一个 SQL查询，找出所有影片描述为非 boring (不无聊) 的并且 id 为奇数 的影片，结果请按等级 rating 排列。

例如，下表 cinema:

+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   1     | War       |   great 3D   |   8.9     |
|   2     | Science   |   fiction    |   8.5     |
|   3     | irish     |   boring     |   6.2     |
|   4     | Ice song  |   Fantacy    |   8.6     |
|   5     | House card|   Interesting|   9.1     |
+---------+-----------+--------------+-----------+
对于上面的例子，则正确的输出是为：

+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   5     | House card|   Interesting|   9.1     |
|   1     | War       |   great 3D   |   8.9     |
+---------+-----------+--------------+-----------+

###### 解答

创建表

```sql
CREATE TABLE cinima(
	id INT NOT NULL PRIMARY KEY,
	movie VARCHAR(255) NOT NULL,
	description VARCHAR(255) NOT NULL,
	rating FLOAT NOT NULL
);

INSERT INTO cinima
	VALUES("1","War","great 3D",8.9);
INSERT INTO cinima
	VALUES("2","Science","fiction",8.5);
INSERT INTO cinima
	VALUES("3","irish","boring",6.2);
INSERT INTO cinima
	VALUES("4","Ice song","fantacy",8.6);
INSERT INTO cinima
	VALUES("5","House card","intersting",9.1);
```

查询

```sql
SELECT * FROM cinema WHERE description <> "boring" AND id%2 = 1 ORDER BY rating DESC
```



### 2.2 MySQL基础（三）-表联结

* MySQL别名（使用MySQL别名来提高查询的可读性）

  * 列别名

    ```sql
    SELECT 
    	[column_1 | expression] AS descriptive_name
    FROM table_name;
    --如果别名包含空格，不许将内容``起
    --AS 可以省略
    ```

    查询选择员工的名字和姓氏，并将其组合起来生成全名。 CONCAT_WS函数用于连接名字和姓氏。
    ```shell
    mysql> SELECT CONCAT_WS(',',lastName,firstname) AS `Full name` FROM employees;
    +-------------------+
    | Full name         |
    +-------------------+
    | Murphy,Diane      |
    | Patterson,Mary    |
    | Firrelli,Jeff     |
    | Patterson,William |
    | Bondur,Gerard     |
    | Bow,Anthony       |
    | Jennings,Leslie   |
    | Thompson,Leslie   |
    | Firrelli,Julie    |
    | Patterson,Steve   |
    | Tseng,Foon Yue    |
    | Vanauf,George     |
    | Bondur,Loui       |
    | Hernandez,Gerard  |
    | Castillo,Pamela   |
    | Bott,Larry        |
    | Jones,Barry       |
    | Fixter,Andy       |
    | Marsh,Peter       |
    | King,Tom          |
    | Nishi,Mami        |
    | Kato,Yoshimi      |
    | Gerard,Martin     |
    +-------------------+
    23 rows in set (0.00 sec)
    ```

    可以用ORDER BY,GROUP BY,HAVING子句中的列别名来引用该列。

    ```sql
    SELECT
     orderNumber `Order no.`,
     SUM(priceEach * quantityOrdered) total
    FROM
     orderdetails
    GROUP BY
     `Order no.`
    HAVING
     total > 60000;
    ```

    > 不能在WHERE子句中使用列别名。原因是当MySQL评估求值`WHERE`子句时，`SELECT`子句中指定的列的值可能尚未确定。

  * 表别名（可以使用别名为表添加不同的名称。）

* INNER JOIN（内链接）

  `INNER JOIN`子句将一个表中的行与其他表中的行进行匹配，并允许从两个表中查询包含列的行记录。`INNER JOIN`子句是`SELECT`语句的可选部分，它出现在FROM子句之后。

  > 使用条件：
  >
  > 1. 在FROM子句中指定主表
  > 2. 表中要连接的主表应该出现在INNER JOIN 子句中。连接表的数量最好不要超过三个表
  > 3. 连接条件出现在INNER JOIN子句的ON关键字之后。连接条件是将主表中的行与其他表中的行进行匹配的规则。

  ```sql
  SELECT column_list
  FROM t1
  INNER JOIN t2 ON join_condition1
  INNER JOIN t3 ON join_condition2
  ...
  WHERE where_condition;
  ```

  > 两种相同的做法
  >
  > 　　语句返回相同的结果集，但是使用此语法，不必使用表的别名
  >
  > ```sql
  > SELECT
  > 	productCode,
  > 	productName,
  > 	textDescription
  > FROM
  > 	products t1
  > 		INNER JOIN
  > 	productlines t2 ON t1.productline=t2.productline；
  > 	
  > <=>
  > 
  > SELECT
  > 	productCode,
  > 	productName,
  > 	textDescription
  > FROM
  > 	products
  > 		INNER JOIN
  > 	productlines USING (productline)；
  > ```

* LEFT JOIN（左连接）

  `LEFT JOIN`子句允许您从匹配的左右表中查询选择行记录，连接左表(`t1`)中的所有行，即使在右表(`t2`)中找不到匹配的行也显示出来，但使用`NULL`值代替。

  > 使用LEFT JOIN子句来查找不匹配的行，只要在最后加上`WHERE xxx is null`

* CROSS JOIN（交叉连接）

  `CROSS JOIN`子句从连接的表返回行的笛卡儿乘积。

  假设使用`CROSS JOIN`连接两个表。 结果集将包括两个表中的所有行，其中结果集中的每一行都是第一个表中的行与第二个表中的行的组合。 当连接的表之间没有关系时，会使用这种情况。

  ```sql
  SELECT * FROM
  t1 CROSS JOIN t2;
  ```

  `CROSS JOIN`子句不具有连接条件。

  　　如果添加了`WHERE`子句，如果`T1`和`T2`有关系，则`CROSS JOIN`的工作方式与`INNER JOIN`子句类似，如以下查询所示：

  ```sql
  SELECT * FROM
  t1 CROSS JOIN t2
  WHERE t1.id=t2.id;
  ```

* 自连接

  在同一张表上自己连接自己。

  　　当您想将表中行与同一表中的其他行组合时，可以使用自连接。要执行自联接操作必须使用表别名来帮助MySQL在单个查询中区分左表与同一张表的右表。

  > 要获得整个组织结构
  >
  > 　　以使用`employeeNumber`和`reportsTo`列将`employees`表连接自身。`employees`表有两个角色：一个是经理，另一个是直接报告者(即，下属员工)。
  >
  > ```sql
  > SELECT
  > 	CONCAT(m.lastname,',',m.firstname) AS 'Manager',
  > 	CONCAT(e.lastname,',',e.firstname) AS 'Direct report'
  > FROM
  > 	employees e
  > 		INNER JOIN
  > 	employees m ON m.employeeNumber = e.reportsTo
  > ORDER BY manager;
  > ```

* UNION 

  UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中。多个 SELECT 语句会删除重复的数据。

  ```sql
  SELECT expression1, expression2, ... expression_n
  FROM tables
  [WHERE conditions]
  UNION [ALL | DISTINCT]
  SELECT expression1, expression2, ... expression_n
  FROM tables
  [WHERE conditions];
  ```

  [例子](https://www.runoob.com/mysql/mysql-union-operation.html)

* 以上几种方式的[区别和联系](https://blog.csdn.net/weixin_41404773/article/details/85124467)



### 作业

### 项目六：组合两张表 （难度：简单）

在数据库中创建表1和表2，并各插入三行数据（自己造）
表1: Person
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
PersonId 是上表主键

表2: Address
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
AddressId 是上表主键

编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：FirstName, LastName, City, State

#### 解答

创建表

```sql
CREATE TABLE person(
	personld INT PRIMARY KEY,
	firstname VARCHAR(255),
	lastname VARCHAR(255)
);

CREATE TABLE address(
	addressld INT PRIMARY KEY,
	personld INT,
	city VARCHAR(255),
	state VARCHAR(255)
);

INSERT INTO person
	VALUES(1,"Mary","Murphy");
INSERT INTO person
	VALUES(2,"Jeff","Bott");
INSERT INTO person
	VALUES(3,"Loui","King");
INSERT INTO person
	VALUES(4,"Cindt","Mart");

INSERT INTO address
	VALUES(1001,1,"Bei Jing","China");
INSERT INTO address
	VALUES(1002,2,"Shang Hai","China");
INSERT INTO address
	VALUES(1003,3,"Ning Bo","China");
```

查询

```sql
SELECT 
	firstname,
	lastname,
	city,
	state
FROM person p LEFT JOIN address a
ON p.personld = a.personld;
```

结果

```shell
mysql> SELECT
    -> firstname,
    -> lastname,
    -> city,
    -> state
    -> FROM person p LEFT JOIN address a
    -> ON p.personld = a.personld;
+-----------+----------+-----------+-------+
| firstname | lastname | city      | state |
+-----------+----------+-----------+-------+
| Mary      | Murphy   | Bei Jing  | China |
| Jeff      | Bott     | Shang Hai | China |
| Loui      | King     | Ning Bo   | China |
| Cindy     | Mart     | NULL      | NULL  |
+-----------+----------+-----------+-------+
4 rows in set (0.00 sec)
```



### 项目七：删除重复的邮箱（难度：简单）

编写一个 SQL 查询，来删除 email 表中所有重复的电子邮箱，重复的邮箱里只保留 Id 最小 的那个。
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
Id 是这个表的主键。
例如，在运行你的查询语句之后，上面的 Person表应返回以下几行:
+----+------------------+
| Id | Email            |
+----+------------------+
| 1  | a@b.com |
| 2  | c@d.com  |
+----+------------------+

#### 解答

```sql
DELETE e1 FROM email AS e1 INNER JOIN email AS e2
WHERE (e1.email = e2.Email) AND (e1.ID > e2.ID);
```



### 项目八：从不订购的客户 （难度：简单）

某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

Customers 表：

+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
Orders 表：

+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
例如给定上述表格，你的查询应返回：

+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+

#### 解答

创建表

```sql
CREATE TABLE customers(
	id INT PRIMARY KEY,
	name VARCHAR(255)
);

CREATE TABLE Orders(
	id INT PRIMARY KEY,
	customerid INT
);

INSERT INTO customers
	VALUES(1,'Joe');
INSERT INTO customers
	VALUES(2,'Henry');
INSERT INTO customers
	VALUES(3,'Sam');
INSERT INTO customers
	VALUES(4,'Max');

INSERT INTO Orders
	VALUES(1,3);
INSERT INTO Orders
	VALUES(2,1);
```

查询

```sql
SELECT name FROM customers c LEFT JOIN orders o
ON c.id = o.customerid
WHERE o.customerid IS NULL;
```



### 项目九：超过经理收入的员工（难度：简单）

Employee 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
给定 Employee 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

+----------+
| Employee |
+----------+
| Joe      |
+----------+

#### 解答

创建表

```sql
CREATE TABLE Employee (
	Id int,
	Name varchar(255),
	Salary int,
	ManagerId int
);

INSERT INTO Employee
	VALUES ('1', 'Joe', '70000', '3');
INSERT INTO Employee  
	VALUES ('2', 'Henry', '80000', '4');
INSERT INTO Employee  
	VALUES ('3', 'Sam', '60000', Null);
INSERT INTO Employee  
	VALUES ('4', 'Max', '90000', Null);
```

查询

```sql
SELECT e1.name,e1.ManagerId as employee FROM employee as e1 LEFT JOIN employee as e2
ON e2.Id = e1.ManagerId
WHERE e1.Salary > e2.Salary AND e1.ManagerId IS NOT NULL;
```

