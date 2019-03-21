---
title: mysql基础知识整理
date: 2017-12-02 16:35:54
tags:
	- 数据库
categories:
	- 数据库
---

### 约束：
        
**按功能划分：**

`NOT NULL`, `RPIMARY KEY`, `UNIQUE KEY`, `DEFAULT`，`FOREIGN KEY`

**按数据列的数目划分：**

表级约束&列级约束

**外键约束：**

**外键约束的要求：**
> * 1.父表和子表必须使用相同的存储引擎，而且禁止使用临时表
> * 2.数据表的存储引擎只能为InnoDB
> * 3.外键列和参照列必须具有相似的数据类型。其中数字的长度或是否有符号位必须相同；而字符的长度可以不同。
>  * 4.外键列和参照列必须创建索引。如果外键列不存在索引的话，MySQL将自动创建索引。

**外键约束的参照操作：**

> * 1.CASCADE:从父表删除或更新且自动删除或更新子表中匹配的行
> * 2.SET NULL:从父表删除或更新行，并设置子表中的外键列为NULL.如果使用该选项，必须保证子表列没有指定NOT NULL.
> * 3.RESTRICT:拒绝对父表的删除或更新操作。
> * 4.NO ACTION:标准SQL的关键字，在MySQL中与RESTRICT相同。

**表级约束与列级约束**

> * 对一个数据列建立的约束，称为列级约束。
> * 对多个数据列建立的约束，称为表级约束。
> * 列级约束既可以在列声明时声明，也可以在列定以后声明。
> * 表级约束只能在列定义后声明。

### 修改数据表
        
**添加单列**

```
ALTER TABLE tbl_name ADD [COLUMN] col_name column_definition [FIRST | AFTER col_name]
```

**添加多列**

```
ALTER TABLE tbl_name ADD[COLUMN] (col_name column_defination,........)
```

**删除列**

```
ALTER TABLE tbl_name DROP [COLUMN] col_name
ALTER TABLE user DROP password，DROP username
```

**添加唯一约束**

```
ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]] UNIQUE [INDEX | KEY] [index_name] [index_type] (index_col_name,.....)
```
`index_name` : 索引名字

（显示数据表中索引名字：`SHOW INDEXS FROM tbl_name`）

**添加外键约束**

```
ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]] FOREIGN KEY  [index_name]  (index_col_name,....)  reference_definition
```

```
例：ALTER TABLE user2 ADD FOREIGN KEY (pid) REFERENCE provinces (id)
```

**删除主键约束**

```
ALTER TABLE tbl_name DROP PRIMARY KEY
```

**删除唯一约束**

```
ALTER TABLE tbl_name DROP {INDEX | KEY} index_name
例：ALTER TABLE user DROP INDEX username
(注意是删除username上的唯一约束，而不是删除掉这个字段)
```

**删除外键约束**

```
ALTER TABLE tbl_name DROP FOREIGN KEY fk_symbol
```
        
**修改列定义**

```
ALTER TABLE tbl_name MODIFY [COLUMN] col_name column_definition [FIRST | AFTER  col_name]
例：ALTER TABLE user MODIFY id SMALLINT UNSIGNED NOT NULL FIRST
```

**修改列名称**

```
ALTER TABLE tbl_name CHANGE [COLUMN] old_col_name new_col_name column_definition [FIRST | AFTER col_name]
```

### 数据表更名

#### 方法一：

```
ALTER TABLE tbl_name RENAME [TO | AS]  new_tbl_name 
例：ALTER TABLE user2 RENAME user3
```

#### 方法二：

```
RENAME TABLE tbl_name TO new_tbl_name [, tbl_name2  TO new_name2] .....
```

        
**插入记录 INSERT**

```
INSERT [INTO] tbl_name [(col_name)] {VALUES | VALUE} ({expr | DEFAULT},.......),{......},......
例：INSERT  users VALUES (NULL，"tom"，"456"，25，1)
```

```
INSERT [INTO] tbl_name SET col_name = {expr | DEFAULT},......
```

说明：与第一种方式的区别在于，此方法可以使用子查询（SubQuery）

```
INSERT [INTO] tbl_name [(col_name,.....)] SELECT .....
```

说明：此方法可以将查询结果插入到指定数据表
            
**更新记录（单表更新）UPDATE**

```         
UPDATE [LOW_PRIORITY] [IGNORE] table_reference SET col_name1 = {expr | DEFAULT}  [,  col_name2 = 
            {expr2 | DEFAULT}].....  [WHERE where_condition]
```

若不加where条件 ， 表示修改所有的表中记录

**DELETE 删除记录（单表删除）**

```
DELETE FROM tbl_name [WHERE where_condition]
```

**SELECT 查找记录**

```
SELECT select_expr [, select_expr ...  ][FROM table_references][WHERE where_condition][GROUP BY {col_name | position} [ASC | DESC],  .....  ][HAVING where_condition][ORDER BY {col_name | expr | position} [ASC | DESC],..... [LIMIT  {[offset,] row_count | row_count OFFSET offset} ]
```

**查询表达式**

每一个表达式表示想要的一列，必须有至少一个
多个列之间以英文逗号分隔
星号`（*）`表示所有列。`tbl_name.*` 可以表示命名表的所有列。

查询表达式可以使用 `[ AS ] alias_name` 为其赋予别名。

别名可用于`GROUP BY`，`ORDER BY` 或 `HAVING` 字句。

```
例：SELECT username, id FROM users
SELECT users.username, users.id FROM users(使用多表连接时使用) WHERE 条件表达式对记录进行过滤，如果没有指定WHERE字句，则显示所有记录。
```

在WHERE表达式中，可以使用MySQL支持的函数或运算符。 

**GROUP BY查询结果分组**

```        
[GROUP BY {col_name | position} [ASC | DESC （升序降序） ],...... ] HAVING  对分组条件进行指定
例：SELECT sex FROM users GROUP BY sex;
SELECT sex, age FROM users GROUP BY sex HAVING age > 35 ;
```

**ORDER BY对查询结果进行排序**

```
[ORDER BY {col_name | expr | position}  [ASC | DESC],...... ]
按id降序排列：SELECT * FROM users ORDER BY id DESC;
```

**LIMIT限制查询结果返回的数量**

```
[LIMIT  {[offset,]  row_count | row_count  OFFSET offset  }]
 例：SELECT * FROM users LIMIT 2;       （返回前两条记录）
SELECT * FROM users LIMIT 2,2;  （返回第三四条记录）  
```

**子查询**

子查询（Subquery）是指出现在其他SQL语句内的SELECT子句。

```
例如：SELECT  *  FROM t1 WHERE col1 = ( SELECT col2 FROM t2 );
其中SELECT *  FROM t1  ,  称为Outer Query / Outer Statement
SELECT col2 FROM t2 , 称为SubQuery
```

子查询指嵌套在查询内部，且必须始终出现在圆括号内。

子查询可以包含多个关键字或条件，
如`DISTINCT`,  `GROUP BY` ,   `ORDER BY`,   `LIMIT`,  函数等。

子查询的外层查询可以是：`SELECT` ,  `INSERT` , `UPDATE` , `SET` 或 `DO`。

子查询可以返回标量、一行、一列或子查询。

子查询条件关键字：`ANY SOME ALL`

**使用[ NOT ]  IN 的子查询**

**使用[ NOT ]  EXISTS 的子查询**

**INSERT .... SELECT**

将查询结果写入到数据表.

```
INSERT [INTO] tbl_name [(col_name,...)] SELECT ...
```

**多表更新**

```
UPDATE table_references SET col_name1 = {expr | DEFAULT}[, col_name = {expr | DEFAULT}]...[WHERE where_condition]
```
        

**CREATE ... SELECT**

创建数据表同时将查询结果写入到数据表

```
CREATE TABLE [IF NOT EXISTS]  tbl_name [(create_definition,....)]select_statement
```

**连接**

MySQL在SELECT语句，多表更新，多表删除语句中支持JOIN操作。

![](/uploads/171202mysql/1.png)

连接条件

使用ON关键字来设定连接条件，也可以使用WHERE来代替。

通常使用ON关键字来设定连接条件。

使用WHERE关键字进行结果集记录的过滤。

> * 1、内连接：在MySQL中JOIN,INNER JOIN,CROSS JOIN是等价的
> * 2、外连接：LEFT JOIN左外连接；RIGHT JOIN右外连接
> * 3、连接条件：使用ON设定连接条件，也可以用WHERE代替
(ON：设定连接条件;WHERE：进行结果集记录的过滤)
> * 4：内连接是返回左表及右表符合连接条件的记录
> * 5、例如：SELECT * FROM tabA JOIN tabB ON tabA.name = tabB.name;表示返回都含有的name值对应的字段

![](/uploads/171202mysql/2.png)

![](/uploads/171202mysql/3.png)

**外连接**

```
A LEFT JOIN B join_condition
```

数据表B的结果集依赖数据表A

数据表A的结果集根据左连接条件依赖所有数据表（B表除外）

左外连接条件决定如何检索数据表B（在没有指定WHERE条件的情况下）

如果数据表A的某条记录符合WHERE条件，但是在数据表B不存在符合连接条件的记录，将生成一个所有列为空的额外的B行。

如果使用内连接查找的记录在连接数据表中不存在，并且在WHERE字句中尝试以下操作：col_name IS NULL时，如果col_name被定义为NOT NULL，MySQL将在找到符合连接条件的记录后停止搜索更多的行。

**自身连接**

同一个数据表对其自身进行连接。

```
例：SELECT s.type_id , s.type_name , p.type_id  FROM tdb_goods AS s LEFT JOIN  tdb_goods AS p;
```

**多表删除**

```
DELETE tbl_name [.*] [, tbl_name[.*]]... FROM table_references[WHERE where_condition]
DELETE t1 FROM tdb_goods AS t1 LEFT JOIN (SELECT goods_id, goods_name FROM tdb_goods  GROUP BY  goods_name HAVING count(goods_name) >=2 ) AS t2 ON
t1.goods_name = t2.goods_name  WHERE t1.goods_id > t2.goods_id;
```

**子查询:**

出现在sql语句中的select子句，而子查询必须要出现在小括号()内，他的外层可以是select语句，insert语句，update语句，delete语句，而且在子查询中可以包含多个关键词:group by ，order by ，limit以及相关函数。引发子查询的情况有比较运算符 ，in(NOT)in运算符,exist(NOT EXIST)引发的子查询.

**连接:**

连接分为外连接和内连接，内连接主要显示的是左表和右表当中都符合连接条件的记录。外链接有分为左外连接和右外连接，左外连接显示的是左表中的全部记录和右表符合条件的记录，右外连接和左外连接相反，mysql中的select，多表更新和删除都可以用到内连接，左，右连接。

**函数**

查询api吧。。。