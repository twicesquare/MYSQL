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
