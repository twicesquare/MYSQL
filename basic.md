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
