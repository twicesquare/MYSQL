## 1. 数据库操作（管理房子的地基）
在存数据之前，你得先有个数据库。

查看所有数据库： `SHOW DATABASES`;

创建数据库： `CREATE DATABASE 数据库名`;

使用/进入数据库： `USE 数据库名`;

删除数据库： `DROP DATABASE 数据库名`;

## 2. 表操作（设计房间和家具）
表（Table）是存放数据的具体地方。

创建表：
```
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT, -- 唯一标识，自动增长
    username VARCHAR(50) NOT NULL,     -- 字符串，不能为空
    age INT,                           -- 整数
    created_at DATETIME DEFAULT NOW()  -- 时间，默认为当前时间
);
```
查看当前库的所有表： `SHOW TABLES`;

查看表结构（看有哪些列）： `DESC 表名`;

删除表： `DROP TABLE 表名`;

## 3. 数据操作（增删改）
这是最常用的“搬砖”指令。

插入数据 (Insert)：
`INSERT INTO users (username, age) VALUES ('张三', 25)`;

修改数据 (Update)：

注意： 别忘了 WHERE 子句，否则会修改全表！
`UPDATE users SET age = 26 WHERE username = '张三'`;

删除数据 (Delete)：
`DELETE FROM users WHERE id = 1`;

## 4. 数据查询 (Select) —— 核心重点
查询是 MySQL 的灵魂，大部分开发时间都花在这。

基础查询： `SELECT username, age FROM users`;（或者用 * 代表所有列）

条件过滤 (WHERE)： `SELECT * FROM users WHERE age > 18`;

模糊查询 (LIKE)： `SELECT * FROM users WHERE username LIKE '%王%'`; （查找名字里带“王”的人）

排序 (ORDER BY)： `SELECT * FROM users ORDER BY age DESC`; （按年龄从大到小排）

聚合函数：

统计人数：`SELECT COUNT(*) FROM users`;

平均年龄：`SELECT AVG(age) FROM users`;

分组查询 (GROUP BY)：
`SELECT age, COUNT(*) FROM users GROUP BY age`; （统计每个年龄各有多少人）

## 5. 常见注意事项
分号结尾： 每一条 SQL 语句写完，记得加 ;，这是命令结束的标志。

关键词不区分大小写： SELECT 和 select 是一样的，但为了可读性，建议关键词大写，表名/字段名小写。

删库跑路警告： 执行 UPDATE 或 DELETE 时，一定要先核对 WHERE 条件，防止误杀整张表的数据。

## 常见关键词
| 执行顺序 | 关键字 | 数据库在干什么？ | 实战用法指南（怎么写） |
| :--- | :--- | :--- | :--- |
| 1 | FROM | 锁定数据源 | `FROM table_name`：指定你要从哪张表拿数据。 |
| 2 | ON / JOIN | 多表关联 | `JOIN table2 ON table1.id = table2.sid`：通过公共列把两张表拼成一张大表。 |
| 3 | WHERE | 初次过滤 | `WHERE age > 18`：在分组前排除掉不符合条件的原始行。 |
| 4 | GROUP BY | 聚合分类 | `GROUP BY department`：把数据按某列（如部门、性别）切成一个个“小盒子”。 |
| 5 | HAVING | 二次过滤 | `HAVING COUNT(*) > 5`：专门过滤分组后的结果（比如只看人数多于 5 人的部门）。 |
| 6 | SELECT | 提取字段 | `SELECT name, price`：从处理好的数据里挑出要展示的列，或者进行计算。 |
| 7 | DISTINCT | 结果去重 | `SELECT DISTINCT city`：把选出来的结果里一模一样的行删掉，只留一份。 |
| 8 | ORDER BY | 最终排序 | `ORDER BY price DESC`：按某列排队。DESC 降序，ASC 升序。 |
| 9 | LIMIT | 结果截取 | `LIMIT 5 OFFSET 10`：跳过前 10 行，只取接下来的 5 行（常用于分页）。 |

## 字符串处理与过滤
| 知识点 | 关键字 / 语法 | 说明 |
| :--- | :--- | :--- |
| 计算字节长度 | `LENGTH(column)` | 返回字段内容的字节长度。 |
| 计算字符长度 | `CHAR_LENGTH(column)` | 返回字段内容的字符个数（最推荐用于统计字数）。 |

## 笛卡尔积

笛卡尔积	CROSS JOIN，将左表的每一行与右表的每一行组合，生成所有可能的配对。

```
SELECT 
    s.student_id, 
    s.student_name, 
    sub.subject_name, 
    COUNT(e.student_id) AS attended_exams
FROM Students s
CROSS JOIN Subjects sub
LEFT JOIN Examinations e 
    ON s.student_id = e.student_id 
    AND sub.subject_name = e.subject_name
GROUP BY s.student_id, sub.subject_name
ORDER BY s.student_id, subject_name;
```

**1. 笛卡尔积 (CROSS JOIN) —— 生成“考勤表”底本**

这一步是为了保证：即使学生“张三”没参加过“音乐”考试，结果里也会出现 张三 | 音乐 | 0。

**2. 双条件左连接 (LEFT JOIN)**

连接条件必须同时满足 ID相同 且 科目相同。

如果匹配上了，`e.student_id` 就有值。

如果没匹配上（没去考试），`e.student_id` 就是 NULL。

**3. 分组统计 (GROUP BY)**

`GROUP BY s.student_id, sub.subject_name`

这就像是在白纸上画格子，每个格子代表“某人-某课”。COUNT(e.student_id) 会数一数每个格子里有多少条实际的考试记录。

## 表限定符（Qualifiers）的使用
| 场景 | 是否加前缀 | 示例 | 目的 |
| :--- | :--- | :--- | :--- |
| 多表同名字段 | 必须加 | s.student_id = e.student_id | 消除歧义（Ambiguity） |
| 多表唯一字段 | 强烈建议加 | s.student_name | 提高可读性，防止未来表结构变动报错 |
| 单表查询 | 不需加 | SELECT name FROM Students | 简化代码 |
| 别名定义处 | 禁止加 | FROM Students AS s | 此处是定义别名，不是使用别名 |

## 自关联 (Self Join)
```
SELECT m.name
FROM Employee AS e
JOIN Employee AS m ON e.managerId = m.id
GROUP BY m.id, m.name
HAVING COUNT(e.id) >= 5;
```
逻辑：通过 `e.managerId = m.id` 把员工和他的经理拼在一行，然后按经理分组统计下属数量。

## 常用的五大聚合函数

| 函数      | 说明                              | 注意事项                                      |
| :-------- | :-------------------------------- | :-------------------------------------------- |
| COUNT()   | 统计行数或非空值的个数            | COUNT(*) 统计总行数；COUNT(列名) 统计该列非 NULL 的行数。 |
| SUM()     | 求和                              | 只能作用于数字类型。                          |
| AVG()     | 求平均值                          | 结果会自动处理为浮点数。                      |
| MAX()     | 求最大值                          | 可用于数字、字符串（按字母顺序）和日期。      |
| MIN()     | 求最小值                          | 同上。                                        |

### 注意事项
#### 去重聚合 (DISTINCT)
如果你想统计有多少个不同的城市，可以在函数内加 DISTINCT：

`SELECT COUNT(DISTINCT city) FROM Users;`

#### 布尔聚合 (MySQL 特有)
在 MySQL 中，True 被视为 1，False 被视为 0。我们可以利用这一点统计符合条件的行数：

`SELECT SUM(score > 60) AS pass_count FROM Students;`

(这比写 CASE WHEN 简洁得多！)

#### 聚合函数中的 NULL 处理
如果某组的所有值都是 NULL：

SUM 会返回 NULL。

COUNT 会返回 0。

**建议：配合 `IFNULL(SUM(col), 0)` 来确保返回一个确定的数值。**

### sum的用法
```
SELECT 
    s.user_id, 
    ROUND(IFNULL(SUM(c.action = 'confirmed') / COUNT(*), 0), 2) AS confirmation_rate
FROM Signups s
LEFT JOIN Confirmations c 
    ON s.user_id = c.user_id
GROUP BY s.user_id;
```

## WHERE vs HAVING (过滤)

WHERE：在分组前过滤。它直接作用于原始表中的行，不能使用聚合函数（因为此时还没开始聚合）。

HAVING：在分组后过滤。它作用于聚合后的结果。

示例：查找平均工资大于 5000 的部门
```
SELECT department, AVG(salary)
FROM Employees
WHERE status = 'Active'        -- 1. 先排除离职员工
GROUP BY department            -- 2. 按部门分组
HAVING AVG(salary) > 5000;     -- 3. 在分组结果中筛选平均分
```

where示例
```
SELECT 
    ROUND(AVG(order_date = customer_pref_delivery_date) * 100, 2) AS immediate_percentage
FROM Delivery
WHERE (customer_id, order_date) IN (
    -- 这一步锁定每个顾客的首次订单日期
    SELECT customer_id, MIN(order_date)
    FROM Delivery
    GROUP BY customer_id
);
```

## 标量子查询
```
SELECT 
    contest_id, 
    ROUND(COUNT(user_id) * 100 / (SELECT COUNT(*) FROM Users), 2) AS percentage
FROM Register
GROUP BY contest_id
ORDER BY percentage DESC, contest_id ASC;
```
**`(SELECT COUNT(*) FROM Users)`**

这叫标量子查询。它会先跑出来一个固定的数字（比如总共 3 个用户），然后像一个常量一样，让每个赛事的报名人数去乘以它。

**`COUNT(user_id)`**

因为我们已经 `GROUP BY contest_id` 了，所以这里的 COUNT 只会数这一个比赛里有多少人。

**乘 100 的位置**

在 SQL 中，为了避免某些数据库把 1/3 直接截断成 0，习惯上我们会先乘 100 再做除法。

## 日期操作
### 日期格式化 `DATE_FORMAT(date, format)`
这是最常用的功能，用于将日期转为特定的字符串格式（如月报、周报）。

语法：`DATE_FORMAT(date, format)`

常用格式符：

`%Y`: 4位年份 (2024)

`%m`: 月份 (01-12)

`%d`: 天 (01-31)

`%H`: 24小时制小时

`%i`: 分钟

### 日期加减：`DATE_ADD()` 和 `DATE_SUB()`

用于计算“昨天”、“上个月”或“七天后”。

`DATE_ADD(NOW(), INTERVAL 7 DAY)`：七天后。

`DATE_SUB('2024-01-01', INTERVAL 1 MONTH)`：一个月前。

### 提取日期部分

`YEAR(date)、MONTH(date)、DAY(date)`：直接提取年、月、日。

`DATEDIFF(d1, d2)`：返回两个日期相差的天数。

`TIMEDIFF(t1, t2)`：返回两个时间相差的时分秒。

## 逻辑控制
### `IF()` 函数
语法：`IF(expr1, expr2, expr3)`

逻辑：如果 expr1 为真，返回 expr2；否则返回 expr3。

```
SELECT name, IF(score >= 60, '及格', '不及格') AS result FROM Students;
```

### `CASE WHEN` (标准 SQL)
当逻辑超过两个分支时，`IF` 嵌套会很难看，此时用 `CASE WHEN` 就像程序员的 switch。
```
CASE 
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ELSE result3
END
```
示例：
```
SELECT amount,
       CASE 
           WHEN amount > 1000 THEN '大额'
           WHEN amount > 500 THEN '中额'
           ELSE '小额'
       END AS level
FROM Orders;
```

### 空值防御：`IFNULL()` 和 `COALESCE()`
`IFNULL(val, 0)`：如果 val 是 NULL，就把它变成 0。常用于防止计算报错。
