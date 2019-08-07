

## MySQL任务1 - 3天

### 【任务一】

#### 1.1 **MySQL 软件安装及数据库基础**

1.软件安装及服务器设置

* 安装数据库执行命令`mysqld install`命令时，报错` Install/Remove of the Service Denied!`

  > 安装/删除服务被拒绝！
  >
  > 是权限的问题
  >
  > 解决办法：进入系统盘找到C:\Windows\System32\找到cmd.exe右键以管理员身份运行，就可以了

* MySQL启动及关闭MySQL服务

  * 启动服务
    * mysqld --console　　
    * 或　　net start mysql　
  * 关闭服务
    * mysqladmin -uroot shudown　　
    * 或　　net stop mysql

* 登录本机服务器`mysql -u root -p`(进入到bin文件夹下)

* 修改root用户密码`ALTER USER 'root'@'localhost' IDENTIFIED BY '******';`

2.使用图形化界面Navicat for SQL

* 连接时遇到问题1251 -Client does not support authentication protocol requested by server; consider upgrading MySQL client

  > 解决办法：[详见](https://my.oschina.net/u/3295928/blog/1811804)

3.数据库基础知识

* 数据库定义

  * Database是按照数据结构来组织、存储和管理数据的仓库

* 关系型数据库管理系统
  * RDBMS，是建立在关系模型基础上的数据库，借助于数学概念和方法来处理数据库的数据。
  * 特点：
    * 数据以表格形式出现
    * 每行为各种记录名称
    * 每列为记录名称所对应的数据域
    * 许多的行和列组成一张表单
    * 若干的表单组成database

* 二维表： 由行列组成，知道行号列号即可确定一个表中的数据。

* 行：每一行用来描述某条记录的具体信息

* 列：一列(数据元素) 包含了相同类型的数据, 例如邮政编码的数据

* 主键：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。

* 外键：外键用于关联两个表。

  > 学生表(学号，姓名，性别，班级) 
  > 其中每个学生的学号是唯一的，学号就是一个主键 
  > 成绩表(学号,课程号,成绩) 
  > 成绩表中单一一个属性无法唯一标识一条记录。
  >
  > 成绩表中的学号不是成绩表的主键，但它和学生表中的学号相对应，并且学生表中的学号是学生表的主键，则称成绩表中的学号是学生表的外键 

4.MySQL数据库管理系统(DBMS)

* 数据库:数据库是一些关联表的集合。
* 数据表:表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
* 视图:是从一个或多个表导出的虚拟的表，其内容由查询定义。具有普通表的结构，但是不实现数据存储。
* 存储过程:为以后使用而保存的一条 或多条 SQL语句。

#### 1.2 MySQL基础（一）- 查询语句

```mysql
SELECT 
    column_1, column_2, ...
FROM
    table_1
    
#SELECT和FROM语句是必须的，其他部分是可选的。
[INNER | LEFT |RIGHT] JOIN table_2 ON conditions
#JOIN 根据某些连接条件从其他表中获取数据
WHERE
    conditions
#WHERE 过滤结果集中的行
GROUP BY column_1
#GROUP BY将一组行组合成小分组，并对每个小分组应用聚合函数。
HAVING group_conditions
#HAVING过滤器基于GROUP BY子句定义的小分组
ORDER BY column_1
#ORDER BY指定用于排序的列的列表。
LIMIT offset, length;
#LIMIT限制返回行的数量
```

1.导入示列数据库-[教程]( <https://www.yiibai.com/mysql/how-to-load-sample-database-into-mysql-database-server.html>)

2.SQL,MySQL

* SQL 代表结构化查询语言，是一种专门用来与数据库沟通的语言。
* MySQL是一个数据库管理系统，也是一个关系数据库。

3.查询语句 SELECT FROM 

* 语句解释

  * 使用`SELECT`语句从表或[视图](http://www.yiibai.com/mysql/views.html)获取数据。表由行和列组成，如电子表格。 通常，我们只希望看到子集行，列的子集或两者的组合。`SELECT`语句的结果称为结果集，它是行列表，每行由相同数量的列组成。

* 去重语句

  * 检索不同的值DISTINCT(作用于所有的列，不仅仅是跟在后面的列)

    > ```mysql
    > SELECT DISTINCT vend_id FROM ...; 
    > ```

* 前N个语句

  * 返回不超过n行的数据

    > ```mysql
    > SELECT vend_id FROM ... LIMIT n;
    > ```

  * 返回从n行起的m行数据

    > ```mysql
    > SELECT vend_id FROM ... LIMIT m OFFSET n;
    > ```
    >
    > 注意：第一个被检索的是第0行，所以LIMIT 1 OFFSET 1会检索第2行。

* CASE...when...then...END判断语句(有点难理解，具体请看[教程](https://www.cnblogs.com/vincentbnu/p/9495609.html))

  * 简单Case函数：

    ```mysql
    case 列名
    	when 条件值1 then 选择项1
    	when 条件值2 then 选择项2
    	else 默认值 end
    ```

  * Case搜索函数

    ```mysql
    case
    	when 判断条件1 then 选择项1
    	when 判断条件2 then 选择项2
    	else 默认值 end
    ```

4. 筛选语句 WHERE 

* 语句解释

  * WHERE子句允许根据指定的过滤表达式或条件来指定要选择的行。

* 运算符/通配符/操作符

  * 操作符

    | 操作符       | 描述                                    |
    | ------------ | --------------------------------------- |
    | `=`          | 等于，几乎任何数据类型都可以使用它。    |
    | `<>` 或 `!=` | 不等于                                  |
    | `<`          | 小于，通常使用数字和日期/时间数据类型。 |
    | `>`          | 大于，                                  |
    | `<=`         | 小于或等于                              |
    | `>=`         | 大于或等于                              |

    | 逻辑运算符（操作符） | 描述                                                         |
    | -------------------- | ------------------------------------------------------------ |
    | AND                  | AND 运算符允许在一个 SQL 语句的 WHERE 子句中的多个条件的存在。 |
    | BETWEEN              | BETWEEN 运算符用于在给定最小值和最大值范围内的一系列值中搜索值。 |
    | EXISTS               | EXISTS 运算符用于在满足一定条件的指定表中搜索行的存在。      |
    | IN                   | IN 运算符用于把某个值与一系列指定列表的值进行比较。          |
    | NOT IN               | IN 运算符的对立面，用于把某个值与不在一系列指定列表的值进行比较。 |
    | LIKE                 | LIKE 运算符用于把某个值与使用通配符运算符的相似值进行比较。  |
    | GLOB                 | GLOB 运算符用于把某个值与使用通配符运算符的相似值进行比较。GLOB 与 LIKE 不同之处在于，它是大小写敏感的。 |
    | NOT                  | NOT 运算符是所用的逻辑运算符的对立面。比如 NOT EXISTS、NOT BETWEEN、NOT IN，等等。它是否定运算符。 |
    | OR                   | OR 运算符用于结合一个 SQL 语句的 WHERE 子句中的多个条件。    |
    | IS NULL              | NULL 运算符用于把某个值与 NULL 值进行比较。                  |
    | IS                   | IS 运算符与 = 相似。                                         |
    | IS NOT               | IS NOT 运算符与 != 相似。                                    |
    | \|\|                 | 连接两个不同的字符串，得到一个新的字符串。                   |
    | UNIQUE               | UNIQUE 运算符搜索指定表中的每一行，确保唯一性（无重复）。    |

    > ```sql
    > mysql> SELECT employeeNumber, lastName,firstName,officeCode FROM employees WHERE officeCode BETWEEN 5 AND 7 ORDER BY officeCode;
    > +----------------+-----------+-----------+------------+
    > | employeeNumber | lastName  | firstName | officeCode |
    > +----------------+-----------+-----------+------------+
    > |           1621 | Nishi     | Mami      | 5          |
    > |           1625 | Kato      | Yoshimi   | 5          |
    > |           1088 | Patterson | William   | 6          |
    > |           1611 | Fixter    | Andy      | 6          |
    > |           1612 | Marsh     | Peter     | 6          |
    > |           1619 | King      | Tom       | 6          |
    > |           1501 | Bott      | Larry     | 7          |
    > |           1504 | Jones     | Barry     | 7          |
    > +----------------+-----------+-----------+------------+
    > 8 rows in set (0.00 sec)
    > ```
    >
    > 注意：ORDER BY 要放在WHERE之后，否则报错

  * LIKE 通配符(用来匹配的一部分的特殊字符) [教程](https://www.cnblogs.com/hrhguanli/p/3826218.html)

    | 通配符         | 特点                       |
    | -------------- | -------------------------- |
    | 百分号%（或*） | 表示任何字符出现任意次数   |
    | 下划线_        | 只匹配单个字符             |
    | 方括号[]       | 匹配一个字符集中的一个字符 |
    | [^]            | 表示否定                   |

    > ```sql
    > mysql> SELECT employeeNumber,lastName,firstName,officeCode FROM employees WHERE firstName LIKE 'P%';
    > +----------------+----------+-----------+------------+
    > | employeeNumber | lastName | firstName | officeCode |
    > +----------------+----------+-----------+------------+
    > |           1401 | Castillo | Pamela    | 4          |
    > |           1612 | Marsh    | Peter     | 6          |
    > +----------------+----------+-----------+------------+
    > 2 rows in set (0.00 sec)
    > ```
    >
    > ```sql
    > mysql> SELECT officeCode,country FROM offices WHERE country REGEXP '^[AU]';
    > +------------+-----------+
    > | officeCode | country   |
    > +------------+-----------+
    > | 1          | USA       |
    > | 2          | USA       |
    > | 3          | USA       |
    > | 6          | Australia |
    > | 7          | UK        |
    > +------------+-----------+
    > 5 rows in set (0.00 sec)
    > ```

  * 运算符

    | 算数运算符 | 描述                                    |
    | ---------- | --------------------------------------- |
    | +          | 加法 - 把运算符两边的值相加             |
    | -          | 减法 - 左操作数减去右操作数             |
    | *          | 乘法 - 把运算符两边的值相乘             |
    | /          | 除法 - 左操作数除以右操作数             |
    | %          | 取模 - 左操作数除以右操作数后得到的余数 |

5.分组语句 GROUP BY [教程](https://blog.csdn.net/bingogirl/article/details/52559302)

* [聚集函数](https://www.jianshu.com/p/5e1f9b8a2ec9)
  * 是以值为一个集合（集或者多重集）为输入，返回单个值的函数。SQL提供了五个固有聚集函数
    * 平均值：avg
    * 最大/小值：max，min
    * 总和：sum
    * 计数：count

* 语句解释

  * group by可以将数据分为多个逻辑组，再*对每个组进行聚集计算。注意，先分组，再聚集计算

  * ```
    SELECT vend_id,COUNT(*) AS num_prods
    FROM ...
    GROUP BY vend_id;
    ```

    除了聚集计算语句外，SELECT语句中的每一列都必须在GROUP BY子句中给出

* HAVING子句

  * HAVING 类似于 WHERE (WHERE过滤行，HAVING过滤组)

6.排序语句 ORDER BY 

* 语句解释
  * 该子句用于排序，可以按照单个列和多个列进行排序，该子句必须是select的最后一个子句。 默认排序方式是升序排序，`ORDER BY 列名 DESC`可以实现降序排序
  * 按多个列排序，只要列名之间用`,`分开即可
  * 要想在多个列上进行降序排序，必须对每一列指定DESC关键字。
* 正序（ASC默认）、逆序（DESC）

7.函数 [教程](https://www.yiibai.com/mysql/functions.html)

* 时间函数

* 数值函数

* 字符串函数

8.SQL注释

* `#`单行注释
* `--`单行注释（注意中间要有一个空格才能生效）
* `/**/`多行注释

9.SQL代码规范

* 多条SQL结束语句必须以（;）分隔

* SQL语句不区分大小写，但关键字使用大写，列名、表名使用大小写，这样更易阅读和调试

* 是用空格，分行写，和写在一行，效果是一样的。

​    [SQL编程格式的优化建议] <https://zhuanlan.zhihu.com/p/27466166>

​    [SQL Style Guide] <https://www.sqlstyle.guide/>

> 查看表结构`show tables `,`desc table_name`



###### 创建数据库

```sql
CREATE DATABASE [IF NOT EXISTS] database_name;
```

###### 显示数据库

```sql
SHOW DATABASE;
```

###### 选择要使用的数据库

```sql
USE database_name;
```

###### 删除数据库

```sql
DROP DATABASE [IF EXISTS] database_name;
```





#### 作业

##### 项目一：查找重复的电子邮箱（难度：简单）

创建 email表，并插入如下三行数据

+----+---------+

| Id | Email   |

+----+---------+

| 1  | a@b.com |

| 2  | c@d.com |

| 3  | a@b.com |

+----+---------+



编写一个 SQL 查询，查找 Email 表中所有重复的电子邮箱。

根据以上输入，你的查询应返回以下结果：

+---------+

| Email   |

+---------+

| a@b.com |

+---------+

说明：所有电子邮箱都是小写字母。

###### 创建表和插入数据

```sql
CREATE TABLE email (
ID INT NOT NULL PRIMARY KEY,
Email VARCHAR(255)
);

INSERT INTO email VALUES('1','a@b.com');
INSERT INTO email VALUES('2','c@d.com');
INSERT INTO email VALUES('3','a@b.com');
```

##### 解答

```sql
SELECT Email FROM email
GROUP BY Email
HAVING count(*) >1;
```



##### 项目二：查找大国（难度：简单）

创建如下 World 表

+-----------------+------------+------------+--------------+---------------+

| name            | continent  | area       | population   | gdp           |

+-----------------+------------+------------+--------------+---------------+

| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |

| Albania         | Europe     | 28748      | 2831741      | 12960000      |

| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |

| Andorra         | Europe     | 468        | 78115        | 3712000       |

| Angola          | Africa     | 1246700    | 20609294     | 100990000     |

+-----------------+------------+------------+--------------+---------------+

如果一个国家的面积超过300万平方公里，或者(人口超过2500万并且gdp超过2000万)，那么这个国家就是大国家。

编写一个SQL查询，输出表中所有大国家的名称、人口和面积。

例如，根据上表，我们应该输出:

+--------------+-------------+--------------+

| name         | population  | area         |

+--------------+-------------+--------------+

| Afghanistan  | 25500100    | 652230       |

| Algeria      | 37100000    | 2381741      |

+--------------+-------------+--------------+

###### 创建表和插入数据

```sql

CREATE TABLE World (
name VARCHAR(50) NOT NULL,
continent VARCHAR(50) NOT NULL,
area INT NOT NULL,
population INT NOT NULL,
gdp INT NOT NULL

);


INSERT INTO World
  VALUES('Afghanistan','Asia',652230,25500100,20343000);

INSERT INTO World 
  VALUES('Albania','Europe',28748,2831741,12960000);

INSERT INTO World 
  VALUES('Algeria','Africa',2381741,37100000,188681000);

INSERT INTO World
  VALUES('Andorra','Europe',468,78115,3712000);

INSERT INTO World
  VALUES('Angola','Africa',1246700,20609294,100990000);
```

##### 解答

```sql
SELECT name,population,area FROM world WHERE area > 3000000 OR (population > 25000000 AND gdp > 20000000);
```

