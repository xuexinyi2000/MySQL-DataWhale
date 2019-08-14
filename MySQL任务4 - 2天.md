## MySQL实战 - 复杂项目

### 项目十四：树节点 （难度：中等）

对于 **tree** 表，*id* 是树节点的标识，*p_id* 是其父节点的 *id*。

+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
每个节点都是以下三种类型中的一种：

* Leaf: 如果节点是根节点。
* Root: 如果节点是叶子节点。
* Inner: 如果节点既不是根节点也不是叶子节点。

写一条查询语句打印节点id及对应的节点类型。按照节点id排序。上面例子的对应结果为：
+----+------+
| id | Type |
+----+------+
| 1  | Root |
| 2  | Inner|
| 3  | Leaf |
| 4  | Leaf |
| 5  | Leaf |
+----+------+
**说明**

* 节点’1’是根节点，因为它的父节点为NULL，有’2’和’3’两个子节点。
* 节点’2’是内部节点，因为它的父节点是’1’，有子节点’4’和’5’。
* 节点’3’，‘4’，'5’是叶子节点，因为它们有父节点但没有子节点。
  **注意**
  如果一个树只有一个节点，只需要输出根节点属性。

##### 解答

```sql
SELECT id ,'root' AS type FROM tree WHERE p_id IS NULL
-- 只要查询出父节点为null的就是根节点
UNION
SELECT id,'inner' AS type FROM tree
WHERE id IN
(SELECT DISTINCT p_id FROM tree WHERE p_id IS NOT NULL)
AND
p_id IS NOT NULL
-- p_id 集合里有自己(排除根节点),且自己的p_id不为null(排除根节点)
UNION
SELECT id,'leaf' AS type FROM tree
WHERE id NOT IN
(SELECT DISTINCT p_id FROM tree WHERE p_id IS NOT NULL)
AND
p_id IS NOT NULL
-- 就是说节点’3’，‘4’，'5’在p_id 集合里不存在,
```



### 项目十六 分数排名  （难度：中等）

依然是昨天的分数表，实现排名功能，但是排名需要是非连续的，如下：

+-------+------+

| Score | Rank |

+-------+------+

| 4.00  | 1    |

| 4.00  | 1    |

| 3.85  | 3    |

| 3.65  | 4    |

| 3.65  | 4    |

| 3.50  | 6    |

+-------+------

##### 解答

```sql
SELECT score,
(SELECT COUNT(score) FROM score AS s2 WHERE s2.score > s1.score)+1 AS 'Rank'
FROM score AS s1
ORDER BY score DESC
```

### 项目十七：查询回答率最高的问题 （难度：中等）

求出**survey_log**表中回答率最高的问题，表格的字段有：**uid, action, question_id, answer_id, q_num, timestamp**。

uid是用户id；action的值为：“show”， “answer”， “skip”；当action是"answer"时，answer_id不为空，相反，当action是"show"和"skip"时为空（null）；q_num是问题的数字序号。

写一条sql语句找出回答率最高的问题。

**举例：**

**输入**

+------+-----------+--------------+------------+-----------+------------+
| uid  | action    | question_id  | answer_id  | q_num     | timestamp  |
+------+-----------+--------------+------------+-----------+------------+
| 5    | show      | 285          | null       | 1         | 123        |
| 5    | answer    | 285          | 124124     | 1         | 124        |
| 5    | show      | 369          | null       | 2         | 125        |
| 5    | skip      | 369          | null       | 2         | 126        |
+------+-----------+--------------+------------+-----------+------------+ 

**输出**

 +-------------+
 | survey_log  |
 +-------------+
 |    285      |
 +-------------+

**说明**

问题285的回答率为1/1，然而问题369的回答率是0/1，所以输出是285。

**注意：** 最高回答率的意思是：同一个问题出现的次数中回答的比例。

##### 解答

```sql
-- 先算出每个问题的回答率，并从高到底排序，只输出一个，即是最高回答率
mysql> SELECT question_id,(
    -> SUM(CASE
    -> WHEN action LIKE 'answer' THEN 1 ELSE 0
    -> END
    -> ) /
    -> SUM(CASE
    -> WHEN action LIKE 'show' THEN 1 ELSE 0
    -> END)
    -> ) AS `rate`
    -> FROM survey_log
    -> GROUP BY question_id
    -> ORDER BY `rate` DESC
    -> LIMIT 1;
+-------------+--------+
| question_id | rate   |
+-------------+--------+
|         285 | 1.0000 |
+-------------+--------+
1 row in set (0.00 sec)

-- 再从这个表中，只查询question_id
SELECT question_id AS survey_log FROM
(SELECT question_id,(
SUM(CASE
WHEN action LIKE 'answer' THEN 1 ELSE 0
END
) /
SUM(CASE
WHEN action LIKE 'show' THEN 1 ELSE 0
END)
) AS `rate`
FROM survey_log
GROUP BY question_id 
ORDER BY `rate` DESC
LIMIT 1) AS r
```

### 项目十八：各部门前3高工资的员工（难度：中等）

将项目7中的employee表清空，重新插入以下数据（其实是多插入5,6两行）：

+----+-------+--------+--------------+

| Id | Name  | Salary | DepartmentId |

+----+-------+--------+--------------+

| 1  | Joe   | 70000  | 1            |

| 2  | Henry | 80000  | 2            |

| 3  | Sam   | 60000  | 2            |

| 4  | Max   | 90000  | 1            |

| 5  | Janet | 69000  | 1            |

| 6  | Randy | 85000  | 1            |

+----+-------+--------+--------------+

编写一个 SQL 查询，找出每个部门工资前三高的员工。例如，根据上述给定的表格，查询结果应返回：

+------------+----------+--------+

| Department | Employee | Salary |

+------------+----------+--------+

| IT         | Max      | 90000  |

| IT         | Randy    | 85000  |

| IT         | Joe      | 70000  |

| Sales      | Henry    | 80000  |

| Sales      | Sam      | 60000  |

+------------+----------+--------+

此外，请考虑实现各部门前N高工资的员工功能。

```sql
SELECT d.`name` AS department,e1.`name` AS employee,e1.salary
FROM employee AS e1,
employee AS e2,
department AS d
WHERE e1.departmentid = e2.departmentid
AND e1.salary <=e2.salary
AND e1.departmentid = d.id
GROUP BY e1.id
HAVING COUNT(DISTINCT e2.salary) <= 3 -- 这里的数值表示第几名
ORDER BY d.`name`,e1.salary DESC;
```

### 项目十九：平面上最近距离

**point_2d** 表包含一个平面内一些点（超过两个）的坐标值（x，y）。

写一条查询语句求出这些点中的最短距离并保留2位小数。

| **x** | **y** |
| ----- | ----- |
| -1    | -1    |
| 0     | 0     |
| -1    | -2    |

最短距离是1，从点（-1，-1）到点（-1，-2）。所以输出结果为：

| **shortest** |
| ------------ |
| 1.00         |

**注意：** 所有点的最大距离小于10000。

##### 解答

```sql
SELECT MIN(SQRT(distance)) AS shortest
FROM
(SELECT POW((p1.x - p2.x),2) + POW((p1.y - p2.y),2) AS distance
FROM point_2d AS p1
INNER JOIN 
point_2d AS p2
ON !(p1.x = p2.x AND p1.y = p2.y)) AS d;
```

### 项目二十：行程和用户（难度：困难）

Trips 表中存所有出租车的行程信息。每段行程有唯一键 Id，Client_Id 和 Driver_Id 是 Users 表中 Users_Id 的外键。Status 是枚举类型，枚举成员为 (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’)。

+----+-----------+-----------+---------+--------------------+----------+

| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|

+----+-----------+-----------+---------+--------------------+----------+

| 1  |     1     |    10     |    1    |     completed      |2013-10-01|

| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|

| 3  |     3     |    12     |    6    |     completed      |2013-10-01|

| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|

| 5  |     1     |    10     |    1    |     completed      |2013-10-02|

| 6  |     2     |    11     |    6    |     completed      |2013-10-02|

| 7  |     3     |    12     |    6    |     completed      |2013-10-02|

| 8  |     2     |    12     |    12   |     completed      |2013-10-03|

| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 

| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|

+----+-----------+-----------+---------+--------------------+----------+

Users 表存所有用户。每个用户有唯一键 Users_Id。Banned 表示这个用户是否被禁止，Role 则是一个表示（‘client’, ‘driver’, ‘partner’）的枚举类型。

+----------+--------+--------+

| Users_Id | Banned |  Role  |

+----------+--------+--------+

|    1     |   No   | client |

|    2     |   Yes  | client |

|    3     |   No   | client |

|    4     |   No   | client |

|    10    |   No   | driver |

|    11    |   No   | driver |

|    12    |   No   | driver |

|    13    |   No   | driver |

+----------+--------+--------+

写一段 SQL 语句查出 **2013年10月1日** 至 **2013年10月3日** 期间非禁止用户的取消率。基于上表，你的 SQL 语句应返回如下结果，取消率（Cancellation Rate）保留两位小数。

+------------+-------------------+

|     Day    | Cancellation Rate |

+------------+-------------------+

| 2013-10-01 |       0.33        |

| 2013-10-02 |       0.00        |

| 2013-10-03 |       0.50        |

+------------+-------------------+

 ##### 解答

创建表trips

```sql
CREATE TABLE trips(
	id int PRIMARY KEY,
	client_id int,
	driver_id int,
	city_id int,
	`status` VARCHAR(255),
	request_at DATE,
	CONSTRAINT FOREIGN KEY(client_id) REFERENCES users(users_id),
	CONSTRAINT FOREIGN KEY(driver_id) REFERENCES users(users_id)
);
```

查询

```sql
SELECT request_at AS day,ROUND(SUM(status != 'completed') / COUNT(*),2) AS 'Cancellation Rate' -- round(num,x)保留x位小数
FROM trips
WHERE client_id NOT IN
(SELECT users_id
FROM users WHERE 
banned = 'yes' AND role = 'client')
AND DATEDIFF(request_at,'2013-10-01') >= 0 AND DATEDIFF(request_at,'2013-10-01') <= 2
GROUP BY request_at;
```

