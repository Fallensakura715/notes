# MySQL

### DDL

#### 设计数据库

##### 建立模型

- 概念模型 `entity` + `attributes` 
- 逻辑模型 `entity` + `attributes` + `datatypes`
- 实体模型 `MySQLtables` + `column` + `datatype` + `attributes`
- 命名和数据类型的一致性
##### Primary Key 

- 唯一标识表里的行
- 可以有多重主键
##### Foreign Key 

![Pasted image 20250514131451.png](https://imgu.falnsakura.top/PicGo/2025/12/5b3994a5e4f81e2d8ff69d5eaab9a4dc.png)

``` sql
CREATE TABLE `default`.`enrollment`  (
  `student_id` int NOT NULL,
  `course_id` int NOT NULL,
  `date` datetime NOT NULL,
  `price` decimal(10, 2) NOT NULL,
  PRIMARY KEY (`student_id`, `course_id`),
  
  CONSTRAINT `fk_enrollment_studests` 
    FOREIGN KEY (`student_id`) 
      REFERENCES `default`.`studests` (`student_id`) 
        ON UPDATE CASCADE,
        
  CONSTRAINT `fk_enrollment_courses` 
    FOREIGN KEY (`course_id`) 
      REFERENCES `default`.`courses` (`course_id`) 
        ON UPDATE CASCADE
);
```
###### CONSTRANT 约束
```mysql
CONSTRAINT `fk_enrollment_studests` 
FOREIGN KEY (`student_id`) 
	REFERENCES `default`.`studests` (`student_id`),
	-- ON DELETE CASCADE 删除students的时候，也会自动删除enrollment相应的行
	-- ON DELETE RESTRICT 拒绝跟着删除(跟不写一样)
	-- ON DELETE SET NULL 会跟着变成NULL
	
	-- ON UPDATE CASCADE 更新students的时候，也会自动更新enrollment相应的行
```

#### 链接表(N to N)
- 添加一个新表，用多个外键实现
- `courses`和`tags`之间是`N : N` 的关系, 不能直接用外键，要多个用来链接的表`course_tags` ![Pasted image 20250514134354.png](https://imgu.falnsakura.top/PicGo/2025/12/455010ddc61b116a23260f83604d021e.png)
#### 标准化

##### 1NF 第一范式
- 表中每个属性（列）只能存储一个值，不能是集合、数组、列表或多值组合。
- 例如，`tags` 列中如果存放 `'Java, Python, Golang'` 就不符合 1NF，应该拆成多行或多列。
- `tags` 应该单独拿出来创建一个表![Pasted image 20250514134316.png](https://imgu.falnsakura.top/PicGo/2025/12/455010ddc61b116a23260f83604d021e.png)

##### 2NF 第二范式
- 符合 **1NF**
- 一张表只有一个目的，一张表的every column都描述该表的entity
- 例如，`courses`的几个column只描述了`courses`的性质，如果多了个`enrollment_date`就违反了2NF
- `courses`里的`instructor`违反了 **2NF**, `instructor`是一个entity，不属于`courses`，一个`instructor`讲多门课，名字会在`courses`表中重复
![Pasted image 20250514135734.png](https://imgu.falnsakura.top/PicGo/2025/12/ad3fc2960ede1b115be58d8ddea4b776.png)

##### 3NF 第三范式
- 符合 **2NF**
- 呃呃呃呃呃定义`R的每个非键属性是R的每个候选键的非传递依赖`看不懂，举个例子吧
- 表中的列不能派生其他列![Pasted image 20250514191912.png](https://imgu.falnsakura.top/PicGo/2025/12/834b24dd3708e6652d892868b10ff5c9.png)

- 如果前两列数据改变，`balance`会出现数据不一致![Pasted image 20250514191953.png](https://imgu.falnsakura.top/PicGo/2025/12/bd332e1a730bbbe35f834eb96476bcea.png)
- 违反了3NF，drop`balance`
##### *不要什么都遵循范式或者建模！！！*

#### 正向工程 逆向工程
...
#### 数据库操作

###### SHOW DATABASES 查询所有数据库
```mysql
SHOW DATABASES;
```

###### SELECT DATABASE 查询当前数据库
```mysql
SELECT DATABASE();
```

###### CREATE DATABASE 创建数据库
```mysql
CREATE DATABASE [IF NOT EXISTS] DB_NAME [DEFAULT CHARSET] [COLLATE];
```

###### DROP 
```mysql
DROP DATABSE [IF EXISTS] DB_NAME;
```

###### USE
```mysql
USE DB_NAME;
```

#### 表中数据类型

##### 整数类
```mysql
TINYINT       1 bytes   	(-128, 127)
SMALLINT      2 bytes   	(-32768, 32767)
MEDIUMINT     3 bytes   	(-8388648, 8388647)
INT/INTEGER   4 bytes   	(-2147483648, 2147483467)
BIGINT        8 bytes   	(-2^63, -2^63-1)
```

##### 定点数和浮点数类
```mysql
FLOAT         4 bytes
DOUBLE        8 bytes
DECIMAL(p, s) 精度(1-65) 小数位数
```

##### 字符串类
通用 VARCHAR(50),  VARCHAR(255)
```mysql
CHAR 		  0-255 bytes        定长字符串，未存储用空格   CHAR(10)
VARCHAR		  0-65535 bytes		 变长字符串				 VARCHAR(10)

TINYTEXT	  0-255 bytes        最好用VARCHAR(50)
TEXT		  0-65535 bytes      最好用VARCHAR(255)
MEDIUMTEXT    16MB               1600万字符, 存中短长度书、json
LONGTEXT      4GB                存长书、久远的日志文件
```

##### Boolean类
```mysql
BOOL          1B (TINYINT)       TRUE(1) / FALSE(0)
```

##### 日期类
```mysql
DATE		  3             1000-01-01 to 9999-12-31                     YYYY-MM-DD
TIME		  3             -838:59:59 to 838:59:59						 HH:MM:SS
YEAR  		  1             1901 to 2155								 YYYY
DATETIME	  8		        1000-01-01 00:00:00 to 9999-12-31 23:59:59     
TIMESTAMP     4				1970-01-01 00:00:01 to 2038-01-19 03:14:07
```

##### Blob类
二进制长对象
图像视频文本...
```mysql
TINYBLOB	  0-255 bytes            
BLOB  		  0-65535 bytes(65KB)      
MEDIUMBLOB    16MB
LONGBLOB      4GB
```

##### Json类
```mysql
UPDATE prodeucts 
SET properties = 
'{
	"key": value,
	"dimensions": [1, 2, 3], 
	"weight": 10,
	"manufacturer": { "name": "sony" }
}'
```

###### 创建
```mysql
UPDATE products
SET properties = JSON_OBJECT(
  'weight', 10,
  'dimensions', JSON_ARRAY(1, 2, 3),
  'manufacturer', JSON_OBJECT('name', 'sony')
)
WHERE product_id = 2;
```

###### 修改
```mysql
UPDATE products
SET properties = JSON_SET(
  properties,
  '$.weight', 20,
  '$.age', 10
)
WHERE product_id = 1;
```

###### 删除
```mysql
UPDATE products
SET properties = JSON_REMOVE(
  properties,
  '$.age'
)
WHERE product_id = 1;
```

###### 查询
```mysql
SELECT product_id, JSON_EXTRACT(properties, '$.weight') AS weight
FROM products WHERE product_id = 1;
```

```mysql
SELECT product_id, properties -> '$.dimensions[0]' AS dimensions
FROM products WHERE product_id = 1;
```

```mysql
SELECT product_id, properties ->> '$.manufacturer.name' AS manufacturer
FROM products WHERE product_id = 1;
```

```mysql
SELECT product_id, properties -> '$.weight' AS weight
FROM products WHERE properties ->> '$.manufacturer.name' = 'sony';
```

###### 枚举和集合类
不好使
```mysql
ENUM('small', 'mediun', 'large')  ...
SET(...)
```

#### 表操作

##### SHOW
```mysql
SHOW TABLES;
```

##### DESC 查询表结构
```mysql
DESC TB_NAME;
```

##### 查询创建指定表的语句
```mysql
SHOW CREATE TABLE TB_NAME;
```

##### CREATE 

```mysql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,    -- 用户ID，自动递增  
    username VARCHAR(50) NOT NULL,        -- 用户名，非空
    email VARCHAR(255) NOT NULL UNIQUE,   -- 电子邮箱，确保唯一性
    points INT NOT NULL DEFAULT 0, 
    password VARCHAR(255) NOT NULL,       -- 用户密码，非空
    password1 VARCHAR(255) CHARACTER SET latin1 NOT NULL,
    creat_at DATETIME DEFAULT CURRENT_TIMESTAMP  -- 创建时间，默认当前时间
)[COMMENT '用户表'];
```

###### CREATE/INSERT 创建表复制
```mysql
CREATE TABLE _table_archived AS SELECT * FROM _table;
-- MySQL会忽略自增主键之类的属性
INSERT INTO _table_archived SELECT * FROM _table WHERE date < '2019-1-1';
-- 指定往复制表插入的数据
```

###### 创建表的约束条件
```mysql
PRIMARY KEY                
用于唯一标识表中的每一行数据,每个表只能有一个 PRIMARY KEY；
可以由单个或多个列组成,自动隐式包含 NOT NULL 属性
FOREIGN KEY
用于在两个表之间建立关联，确保列中的值必须在另一个表的 PRIMARY KEY 或 UNIQUE 列中存在
可设置级联更新（ON UPDATE）和删除（ON DELETE）策略
UNIQUE
限制列中的所有值都是唯一的
表中可允许 NULL 值（具体表现依赖于不同数据库的实现）
NOT NULL
确保列中不能存放 NULL 值，必须提供实际数据
DEFAULT
提供一个默认值，当插入数据时如果没有指定该列的值就使用该默认值
CHECK
用于确保列中的值满足指定的条件表达式
该约束能限制输入数据的范围或格式
AUTO_INCREMENT
主要用于主键列，自动生成递增数值
```

##### ALTER TABLE

```mysql
ALTER TABLE customers 
	ADD last_name VARCHAR(255) NOT NULL AFTER first_name,
	MODIFY COLUMN first_name VARCHAR(255) DEFAULT '',
	CHANGE city1 city VARCHAR(255),
	DROP city2,
	RENAME TO renamed_customers;
	CHARACTER SET latin1;
```
######  ADD 添加表中字段
```mysql
ALTER TABLE customers 
	ADD FIELD_NAME TYPE,
	ADD last_name VARCHAR(255) NOT NULL AFTER first_name;
```

###### CHANGE 修改字段名和类型
```mysql
ALTER TABLE TB_NAME CHANGE PRE_FIELD_NAME NEW_FIELD_NAME TYPE [COMMENT ‘’] [];
```

###### MODIFY 调整字段类型
```mysql
ALTER TABLE TB_NAME MODIFY FIELD_NAME TYPE [];
```

###### DROP 删除字段
```mysql
ALTER TABLE TB_NAME DROP FIELD_NAME;
```

###### ALTER TABLE 修改表名
```mysql
ALTER TABLE TB_NAME RENAME TO NEW_TB_NAME;
```

##### DROP TABLE
```mysql
DROP TABLE [IF EXISTS] TB_NAME;
```

##### TRUNCATE TABLE 
删除指定表，并重新创建改表
```mysql
TRUNCATE TABLE TB_NAME;
```

#### Relationships

##### CREATE KEYS
```mysql
CREATE TABLE orders (
	order_id INT PRIMARY KEY,
	customer_id INT NOT NULL,
	FOREIGN KEY fk_order_customers (customer_id) 
		REFERENCES customers (customer_id)
		ON UPDATE CASCADE
		ON DELETE NO ACTION
	CONSTRAINT fk_order_customers FOREIGN KEY (customer_id)
		REFERENCES customers (customer_id)
		ON UPDATE CASCADE
		ON DELETE NO ACTION
)
```

##### ALTER 
```mysql
ALTER TABLE orders
	DROP PRIMARY KEY,
	ADD PRIMARY KEY (order_id),
	DROP FOREIGN KEY fk_order_customers,
	ADD FOREIGN KEY fk_order_customers (customer_id)
		REFERENCES customers (customer_id)
		ON UPDATE CASCADE
		ON DELETE NO ACTION,

	ADD CONSTRAINT fk_order_customers FOREIGN KEY (customer_id)
		REFERENCES customers (customer_id)
		ON UPDATE CASCADE
		ON DELETE NO ACTION;
```

#### INDEX

INDEX 基于二叉树存储
要基于Query创建索引

```mysql
EXPLAIN SELECT customer_id FROM customers WHERE state = 'CA';
```
##### Features
- 加速查询
- 但是
- 增加数据库体积
- 减慢数据写入

##### CREATE
```mysql
CREATE INDEX idx_state ON customers (state);
```
##### SHOW
```mysql
ANALYZE TABLE customers;
```

```mysql
SHOW INDEXES IN customers;
```
##### DROP
```mysql
DROP INDEX idx_state ON customers;
```

##### Prefix

```mysql
CREATE INDEX idx_lastnamne ON customers (last_name(5));
-- last_name 前五个字符为index
```
###### 确定prefix长度
```mysql
SELECT COUNT(DISTINCT LEFT (last_name, 1)) FROM customers;
SELECT COUNT(DISTINCT LEFT (last_name, 5)) FROM customers;
```

##### FULLTEXT

- 用于很长很长文章内容的检索
- 短的`string`用`prefix index`就好

```mysql
SELECT * FROM posts WHERE title LIKE '%react redux%' OR body LIKE '%react redux%';
-- 非常慢
```

```mysql
CREATE FULLTEXT INDEX idx_title_body ON posts (title, body);
```

`FULLTEXT`的结果：这些单词可以按任何顺序排列，也可以是分开的
```mysql
SELECT * FROM posts WHERE MATCH(title, body) AGAINST('react redux');
```
###### BOOLEAN MODE
```mysql
SELECT * FROM posts 
WHERE MATCH(title, body) AGAINST('react -redux +from "handling a form"' 
	IN BOOLEAN MODE);
-- '-redux' 表示 包含 react 但是不包含 redux
-- '+from' 每一行都必须包含 from, title 和 body 都得有 from
-- "handling a from" 表示精确搜索，词的顺序不改变也不分割
```

##### Composite

- 对多列建立索引
- 最多16列

```mysql
CREATE INDEX idx_state_points ON customers (state, points);
```
###### 列顺序

- 最常查的列放到最前面
- `cardinality` (基数--唯一值的数量) 最高的列放到最前面 ，不一定必须这样，看实际情况

##### When Ignored

```mysql
EXPLAIN SELECT * FROM customers WHERE state = 'CA' OR points > 1000;
-- 全扫描
```
重写查询
```mysql
EXPLAIN
  SELECT customer_id FROM customers WHERE state = 'CA'
  UNION
  SELECT customer_id FROM customers WHERE points > 1000;
```
给`UNION`之后的查询添加索引优化
```mysql
CREATE INDEX idx_points ON customers (points);
```

```mysql
EXPLAIN SELECT customer_id FROM customers WHERE points + 10 > 1010;
-- 这个points + 10表达式也会让mysql全扫描
```

```mysql
EXPLAIN SELECT customer_id FROM customers WHERE points > 1000;
-- 和上面完全一样，但是扫描行数少了很多
```

##### Sorting

###### 能优化的排序
- `INDEX: (a, b)`
- `a`
- `a, b`
- `a DESC, b DESC`
- `a = xxx, b`

在有`idx_state_points`下提高了排序效率
```mysql
EXPLAIN SELECT customer_id FROM customers ORDER BY state, points;
SHOW STATUS LIKE 'last_query_cost';
```

加入其他排序列或者改变排序顺序就没用了
```mysql
EXPLAIN SELECT customer_id FROM customers ORDER BY state, name, points;

EXPLAIN SELECT customer_id FROM customers ORDER BY state, points DESC;
SHOW STATUS LIKE 'last_query_cost';
```

都相反`index`是有效果的
```mysql
EXPLAIN SELECT customer_id FROM customers ORDER BY state DESC, points DESC;
```

在只有`idx_state_points`下，以下语句没什么优化效果
- `MySQL`先将`customers`按照`state`排序，再按`points`排序
- 单独拎出所有`state`的`points`排序没优化效果
```mysql
EXPLAIN SELECT customer_id FROM customers ORDER BY points;
```

以下是有优化的
```mysql
EXPLAIN SELECT customer_id FROM customers 
WHERE state = 'CA' ORDER BY points;
```

##### Cover

- `SELECT`未包含在索引里的列开销很大

- 在用复合索引，MySQL自动把主键放到索引中
```mysql
-- idx_state_points
SELECT customer_id, state FROM customers WHERE state = 'CA';
-- customer_id也包含在索引中
```

```mysql
SELECT * FROM customers WHERE state = 'CA';
-- * 是全表扫描，查询开销很大
```

##### Redundant Indexes

创建索引之前，确保没有重复的索引
现有`index(a, b)`
- `(a)`是多余的
- `(b)`不是多余的
- `(b, a)`不是多余的

#### Users and Privileges



#### 字符集和排序规则

##### CREATE 
```mysql
CREATE DATABASE db_name CHARACTER SET latin1;
CREATE TABLE db_name (
	pwd VARCHAR(255) CHARACTER SET latin1 NOT NULL
) CHARACTER SET latin1;
```

##### ALTER
```mysql
ALTER DATABASE db_name CHARACTER SET latin1;
ALTER TABLE table1 CHARACTER SET latin1;
```

#### ENGINE

```mysql
SHOW ENGINES;
```

##### MyISAM
老，不用了

##### InnoDB
```mysql
CREATE TABLE table1 (
	...
) ENGINE = InnoDB;
```

```mysql
ALTER TABLE table1 ENGINE = InnoDB;
```

### DML

##### INSERT INTO 

###### 给指定字段添加数据
```mysql
INSERT INTO TB_NAME (n1, n2...) VALUE (value1, value2, DEFAULT);
```

###### 给全部字段添加数据
```mysql
INSERT INTO TB_NAME VALUES (value1, DEFAULT, NULL);
INSERT INTO customers VALUES (DEFAULT, 'John', 'Smith');
```

###### 批量添加数据
```mysql
INSERT INTO TB_NAME (n1, n2...) VALUE (value1, value2...),(value1, value2...);
```

```mysql
INSERT INTO TB_NAME VALUES (value1, value2...),(value1, value2...);
```

###### 多表插入数据
```mysql
INSERT INTO order_items VALUES 
	(LAST_INSERT_ID(), 1, 2, 3.3),
	(LAST_INSERT_ID(), 2, 2, 2.3);
-- LAST_INSERT_ID()的column是自增主键+1，不用自己一个一个加
-- 往子母表里插入数据
```

##### UPDATE 修改数据

```mysql
UPDATE _table SET n1 = value1, n2 = value2 WHERE n3 = value3;
-- 只修改 n3 = value3的行
```

##### UPDATE 子查询

```mysql
UPDATE _table SET n1 = value1 
WHERE id = (SELECT id FROM clients WHERE name = 'John');

UPDATE _table SET n1 = value1 
WHERE id IN (SELECT id FROM clients WHERE name = 'John');
-- 如果后面的SELECT返回多行，就要用IN
```

##### DELETE 删除数据

```mysql
DELETE FROM TB_NAME [WHERE];
DELETE FROM customers WHERE balance = 0;
DELETE FROM customers WHERE id = (SELECT id FROM users WHERE age > 10);
```


### DQL

##### 基本语法

```mysql
SELECT    字段列表
FROM      表名列表
WHERE     条件列表
GROUP BY  分组字段列表
HAVING    分组后条件列表
ORDER BY  排序字段列表
LIMIT     分页参数
```

##### 查询多个字段

```mysql
SELECT * FROM TB_NAME;
SELECT f1 * 10 - 10 / 1 % 1 + 3 FROM TB_NAME;
```

```mysql
SELECT 
	f1, 
	f2 * 10 AS 'f1 times 10' 
FROM TB_NAME;
```

##### 子查询

```mysql
SELECT * FROM employees WHERE salary > (
SELECT AVG(salary) FROM employees) 
ORDER BY salary;
```

##### 相关子查询

```mysql
-- 相当于两重循环
SELECT * FROM employees e 
WHERE salary > (
	SELECT AVG(salary) 
	FROM employees
	WHERE office_id = e.office_id;
);

SELECT * FROM invoices i JOIN (
	SELECT client_id, AVG(invoices.invoice_total) AS total 
	FROM invoices 
	GROUP BY client_id  
) t 
USING (client_id) 
WHERE i.invoice_total > t.total;
```

##### AS 设置别名
```mysql
SELECT f1 [AS 别名1],f2 [AS 别名2] ... FROM TB_NAME;
```

##### DISTINCT 去重
```mysql
SELECT DISTINCT field_list FROM TB_NAME;
```

##### WHERE

```mysql
SELECT * FROM users WHERE age < 20 && age >= 10;
SELECT * FROM users WHERE age BETWEEN 10 AND 20;
SELECT * FROM users WHERE age IN(10, 11, 20); -- age是10、11、20的
```

```mysql
SELECT * FROM users WHERE email IS NULL;
SELECT * FROM users WHERE email IS NOT NULL;
-- '_'是精准字符
-- '%'是任意字符
SELECT * FROM users WHERE name LIKE '__'; -- 两个字符的
SELECT * FROM users WHERE name LIKE '%X%'; -- 任意名字里有m的
SELECT * FROM users WHERE name NOT LIKE '___X'; -- 不以X为结尾前面有三个字符的
```

```mysql
SELECT * FROM users WHERE _column A;
-- 比较运算> < >= = <= !=(<>)
-- 逻辑运算|| && !
-- BETWEED ... AND ..
SELECT * FROM products WHERE product_id NOT IN(
	SELECT DISTINCT product_id FROM order_items);
-- IN(..) in之后列表值，多选一
-- LIKE 占位符 (_匹配单个字符，%匹配任意个字符)
-- IS NULL
```

##### 聚合函数

```mysql
COUNT()		统计数量
MAX()		最大值
MIN()		最小值
AVG()		平均值
SUM()		求和
-- 只计算 NOT NULL 的
```

```mysql
SELECT 聚合函数(_column) FROM _table;
SELECT COUNT(*) FROM _table;
SELECT COUNT(_column + 3) FROM _table;
SELECT COUNT(DISTINCT _column) FROM _table; -- 去重
```

##### 派生表

```mysql
SELECT *  
FROM (SELECT client_id,  
             name,  
             (SELECT SUM(invoice_total)  
              FROM invoices i  
              WHERE c.client_id = client_id)           AS total_sales,  
             (SELECT AVG(invoice_total) FROM invoices) AS average,  
             (SELECT total_sales - average)            AS difference  
      FROM clients c) AS sales_summary  
WHERE total_sales IS NOT NULL;
```

##### ALL/ANY
```mysql
-- ALL是所有，> ALL 是大于最大值
-- ANY是一个, > ANY 是大于最小值
SELECT * FROM invoices 
WHERE invoice_total > (
	SELECT MAX(invoice_total) FROM invoices 
	WHERE client_id = 3
);

SELECT * FROM invoices 
WHERE invoice_total > ALL (
	SELECT invoice_total FROM invoices 
	WHERE client_id = 3
);

SELECT * FROM invoices 
WHERE invoice_total > (
	SELECT MIN(invoice_total) FROM invoices 
	WHERE client_id = 3
);

SELECT * FROM invoices 
	WHERE invoice_total > ANY (
	SELECT invoice_total FROM invoices 
	WHERE client_id = 3
);
```

##### EXISTS
```mysql
SELECT * FROM clients 
WHERE client_id IN (
	SELECT DISTINCT client_id
	FROM invoices
);

SELECT * FROM clients c
WHERE EXISTS (
	SELECT client_id
	FROM invoices
	WHERE c.client_id = client_id
);

-- EXISTS提高性能，子查询不用返回一个很大的ResultSet
```

##### REGEXP/LIKE
```mysql
SELECT * FROM _table WHERE _column LIKE   '%X%__';
SELECT * FROM _table WHERE _column REGEXP 'field';
SELECT * FROM _table WHERE _column REGEXP '^field'; -- ^：以字符串开头
SELECT * FROM _table WHERE _column REGEXP 'field%'; -- $：以字符串结尾
SELECT * FROM _table WHERE _column REGEXP '^field|mac$|rose'; -- |：包含多种单词
SELECT * FROM _table WHERE _column REGEXP '[gi]e[ab]'; -- 查gea, iea, geb, meb
SELECT * FROM _table WHERE _column REGEXP '[a-h]e[g-z]';
```

##### GROUP BY
```mysql
SELECT SUM(orders) FROM customers GROUP BY date1;
-- 按照date1分组返回查询结果
```

##### HAVING 分组后的条件
```mysql
SELECT SUM(orders) FROM customers 
GROUP BY date1
HAVING date1 > '1990-1-1';
-- WHERE 只能在分组之前筛选, 分组之后筛选只能用 HAVING
```

##### ROLLUP
```mysql
SELECT SUM(price) FROM orders GROUP BY name WITH ROLLUP;
-- 会另外返回一个所有结果的和
-- ROLLUP 用于聚合函数的 column
```

##### ORDER BY
```mysql
SELECT * FROM _table ORDER BY _column1;
SELECT * FROM _table ORDER BY _column1 DESC, _column2 DESC; -- 反过来
```

##### LIMIT
```mysql
SELECT * FROM _table LIMIT 3; -- 只返回3个数据
SELECT * FROM _table LIMIT 6, 3; -- 跳过前6个，返回第三个
```

##### LAST_INSERT_ID() 
```mysql
SELECT LAST_INSERT_ID();
```

### DQL-Joins

##### JOIN 查询

```mysql
SELECT _table1.id, name FROM _table1 JOIN _table2 ON _table1.id = _table2.id; 
-- 查两个表中id相同的

-- 简化
SELECT t1.id, name 
FROM _table1 t1
JOIN _table2 t2
		ON t1.id = t2.id;

-- 跨数据库查询
-- 需要给除了use的数据库的表 加数据库前缀
SELECT _table1.id, name FROM _table1 
JOIN _database._table2 dt2
	ON _table1.id = dt2.id;
```

##### JOIN 自连接

```mysql
SELECT e.employ_id, e.first_name, m.frist_name AS manager 
FROM employees e
JOIN employees m
	ON e.manager = m.employee_id;
```

##### JOIN 多张表

```mysql
SELECT * 
FROM orders o 
JOIN customers c
	ON o.customer_id = c.customer_id;
JOIN order_statuses os
	ON o.status = os.order_status_id;
```

##### JOIN 复合连接条件

```mysql
SELECT * 
FROM orders o 
JOIN customers c
	ON o.customer_id = c.customer_id 
	AND o.status = c.status;
```

##### OUTER JOIN 外连接

```mysql
SELECT * 
FROM orders o 
LEFT JOIN customers c
	ON o.customer_id = c.customer_id 
-- 返回所有左表(FROM)记录

SELECT * 
FROM orders o 
RIGHT JOIN customers c
	ON o.customer_id = c.customer_id 
	AND o.status = c.status;
-- 返回所有右表(JOIN)记录
```

##### OUTER JOIN 多表外连接

```mysql
SELECT * 
FROM orders o 
LEFT JOIN customers c
	ON o.customer_id = c.customer_id 
LEFT JOIN order_statuses os
	ON os.customer_id = c.customer_id;
-- 尽量使用LEFT JOIN
```

##### OUTER JOIN 自外连接

```mysql
SELECT e.employ_id, e.first_name, m.frist_name AS manager 
FROM employees e
LEFT JOIN employees m
	ON e.manager = m.employee_id;
```

##### USING JOIN 连接

```mysql
SELECT * 
FROM orders o 
JOIN customers c
	-- ON o.customer_id = c.customer_id;
	USING (customer_id) 
JOIN statuses s
	USING (status_id);
-- 多表column名字相同的

SELECT * 
FROM orders o 
JOIN customers c
	-- ON o.customer_id = c.customer_id
	-- AND c.order_id = o.order_id;
	USING (customer_id, order_id);
```

##### NATURAL JOIN 自然连接

```mysql
SELECT * 
FROM orders o 
NATURAL JOIN customers c
-- 所有俩表相同的列, 最好不要用
```

##### CROSS JOIN 交叉连接

```mysql
SELECT * 
FROM orders o 
CROSS JOIN customers c
-- 排列组合连接(笑)
```

##### UNION 联合

```mysql
-- 合并多段查询记录
SELECT * 
FROM orders o 
LEFT JOIN customers c
	ON o.customer_id = c.customer_id 
UNION
SELECT * 
FROM orders o 
RIGHT JOIN customers c
	ON o.customer_id = c.customer_id 
	AND o.status = c.status;
```

### Function

##### ROUND 四舍五入
```mysql
SELECT ROUND(5.75, 1); -- 5.8
-- ROUND(num, precision)
```

##### TRUNCATE 截断数字
```mysql
SELECT TRUNCATE(5.7345, 3); -- 5.734
-- TRUNCATE(num, precision)
```

##### CEILIING 向上取整
##### FLOOR 向下取整
##### ABS 绝对值

##### RAND 0-1间随机浮点数

##### LENGTH 字符串长度

##### UPPER 字符串转换大写

##### LOWER 字符串转换小写

##### LTRIM 去除左侧空格

##### RTRIM 去除右侧空格

##### TRIM 去除前后空格

##### LEFT 截取字符串左侧字符
```mysql
SELECT LEFT('skyyyyy', 3) -- sky
-- LEFT(string, length)
```

##### RIGHT 截取字符串右侧字符

##### SUBSTRING 子串
```mysql
SELECT SUBSTRING(string, offset, length)
```

##### LOCATE 字符串查找
```mysql
SELECT LOCATE('ky', 'sky') -- returns 2 没有返回0
```

##### REPLACE 字符串替换
```mysql
SELECT REPLACE('kingdergarten', 'garten', 'garden');
```

##### CONCAT 拼接字符串
```mysql
SELECT CONCAT('kingdergarten', 'abc');
```

##### 日期函数

```mysql
NOW() -- 当前时间

CURDATE() -- 当前日期

CURTIME() -- 当前时间

YEAR(NOW()) -- 当前年份
HOUR(NOW())
MINUTE(NOW())
SECOND(NOW())

DAYNAME(NOW()) -- 返回string 'Monday'...
MONTHNAME(NOW()) -- 返回string 'April'...

EXTRACT(DAY FROM NOW()) -- 提取DAY/YEAR/MONTH
```

##### 日期格式化

```mysql
SELECT DATE_FORMAT(NOW(), '%y')
SELECT TIME_FORMAT(NOW(), '%H')

-- %y 两位数年份
-- %Y 四位数年份
-- %M 月份
-- %d day
-- 
-- 
-- %H 小时
-- %i 分钟
-- %p 显示pm/am
```

##### 日期计算

```mysql
DATE_ADD(NOW(), INTERVAL 1 DAY) -- 加一天
DATE_ADD(NOW(), INTERVAL -1 DAY) -- 减一天
DATE_SUB(NOW(), INTERVAL 1 DAY) -- 减一天

DATEDIFF(NOW(), '2020-1-1')  -- 只返回天数间隔

TIMESTAMPDIFF(SECOND, '2020-1-1', '2019-1-1') -- 返回 SECOND... 类型的

TIME_TO_SEC('09:00') -- 变成秒数 
```

##### IFNULL 
```mysql
IFNULL(column, 'Not assigned') -- 如果是null，返回 Not assigned
```

##### COALESCE
```mysql
COALESCE(column, comments, 'Not assigned') 
-- 如果null，返回comments，如果comments null，返回 Not assigned
```

##### IF
```mysql
IF(expression, first, second) -- return expression ? first : second
```

##### CASE
```mysql
CASE
	WHEN YEAR(order_date) = YEAR(NOW()) THEN 'Active'
	WHEN YEAR(order_date) = YEAR(NOW()) - 1 THEN 'Last Year'
	WHEN YEAR(order_date) < YEAR(NOW()) - 1 THEN 'Archived'
	ELSE 'Future'
END
```


### Views

##### CREATE 创建
```mysql
-- 可以把一段语句保存为视图
-- 可以把视图当作表查询(不存数据)
CREATE VIEW sales_by_client AS
	SELECT 
		c.client_id,
		c.name,
		SUM(invoice_total) as total_sales
	FROM clients c
	JOIN invoices i USING (client_id)
	GROUP BY client_id, name
```

##### DROP 删除
```mysql
DROP VIEW _view;
```

##### REPLACE 替换
```mysql
CREATE OR REPLACE VIEW sales_by_client AS
	SELECT 
		c.client_id,
		c.name,
		SUM(invoice_total) as total_sales
	FROM clients c
	JOIN invoices i USING (client_id)
	GROUP BY client_id, name
```

##### UPDATE 更新

```mysql
-- 没有 DISTINCT GROUP BY UNION HAVING 聚合函数 的才可以更新
```

```mysql
DELETE FROM sales_by_client WHERE total_sales = 0;
```

```mysql
UPDATE sales_by_client SET total_sales = 10 WHERE client_id = 1;
```

```mysql
INSERT INTO sales_by_client (client_id, name, total_sales) VALUES (1, 'zz', 1);
```
	
##### WITH CHECK OPTION

```mysql
-- 防止 update/delete 将行从视图删除
CREATE OR REPLACE VIEW sales_by_client AS
	SELECT 
		c.client_id,
		c.name,
		SUM(invoice_total) as total_sales
	FROM clients c
	JOIN invoices i USING (client_id)
	GROUP BY client_id, name
WITH CHECK OPTION;
```

### Stored Procedures

##### CREATE
```mysql
DELIMITER $$ -- 自定义分隔符

CREATE PROCEDURE get_client() 
BEGIN  
   SELECT * FROM customers;  
END$$  

DELIMITER ; -- 分隔符换为;
```

##### CALL 调用
```mysql
CALL get_clients();
```

##### DROP 删除
```mysql
DROP PROCEDURE IF EXISTS get_clients;
```

##### PARAMETERS 形参

```mysql
DELIMITER $$  
CREATE PROCEDURE get_clients_by_state(state CHAR(2))  
BEGIN   
	SELECT * FROM clients WHERE clients.state = state;  
END $$  
DELIMITER ;
```

```mysql
-- 默认参数
DELIMITER $$  
CREATE PROCEDURE get_clients_by_state(state CHAR(2))  
BEGIN   
	IF state IS NULL THEN SET state = 'CA';
	END IF;
	
	SELECT * FROM clients WHERE clients.state = state;  
END $$  
DELIMITER ;
```

##### 形参验证

```mysql
DELIMITER $$  
CREATE PROCEDURE make_payment(  
    p_invoice_id INT,  
    payment_amount DECIMAL(9, 2),  
    payment_date DATE  
)  
BEGIN   
IF payment_amount <= 0 THEN  
        SIGNAL SQLSTATE '22003'  -- 错误类型(字符串)，22003是数据超出范围
            SET MESSAGE_TEXT = 'Invalid payment amount';  
    END IF;  
  
    UPDATE invoices i  
    SET i.payment_total = payment_amount,  
        i.payment_date = payment_date  
    WHERE p_invoice_id = invoice_id;  
END $$  
DELIMITER ;
```

##### 输出参数

```mysql
DELIMITER $$  
CREATE PROCEDURE get_unpaid_invoices_for_client(  
    client_id INT,  
    OUT invoices_count TINYINT,  
    OUT invoices_total DECIMAL(9, 2)  
)  
BEGIN  
    SELECT COUNT(*) ,SUM(invoice_total)  
    INTO invoices_count, invoices_total  
    FROM invoices i  
    WHERE i.client_id = client_id  
        AND payment_total = 0;  
END $$  
DELIMITER ;
```

```mysql
SET @client_id = 0;
CALL get_unpaid_invoices_for_client(  
        @client_id,  
        @invoices_count,  
        @invoices_total  
     ); 
SELECT @invoices_count, @invoices_total;
```

##### 变量

```mysql
-- 用户会话变量
SET @client_id = 0;

-- 本地变量
DELIMITER $$  
CREATE PROCEDURE get_risk_factor()  
BEGIN  
    DECLARE risk_factor DECIMAL(9, 2) DEFAULT 0;  
    DECLARE invoices_total DECIMAL(9, 2);  
    DECLARE invoices_count INT;  
      
    SELECT COUNT(*), SUM(invoices_total)  
    INTO invoices_count, invoices_total  
    FROM invoices;  
      
    SET risk_factor = invoices_total / invoices_count * 5;  
      
    SELECT risk_factor;  
END;  
DELIMITER ;
```

##### 函数

```mysql
CREATE FUNCTION get_risk_factor_for_client(  
    client_id INT  
)  
    RETURNS INT -- 返回值  
	-- 属性  
	-- DETERMINISTIC  -- 确定性: 一样的输入对象会返回一样的输出对象  
    READS SQL DATA -- 函数里有 SELECT
    -- MODIFIES SQL DATA -- 函数里有 INSERT/UPDATE/DELETEBEGIN  
    DECLARE risk_factor DECIMAL(9, 2) DEFAULT 0;  
    DECLARE invoices_total DECIMAL(9, 2);  
    DECLARE invoices_count INT;  
  
    SELECT COUNT(*), SUM(invoice_total)  
    INTO invoices_count, invoices_total  
    FROM invoices i  
    WHERE i.client_id = client_id;  
  
    SET risk_factor = invoices_total / invoices_count * 5;  
  
    RETURN IFNULL(risk_factor, 0);  
END;
```

### Trigger

##### CREARE
```mysql
DELIMITER $$  
CREATE TRIGGER payments_after_insert  
    AFTER INSERT -- BEFORE/AFTER INSERT/UPDATE/DELETE  
    -- 在插入行之后触发
    ON payments  -- 作用在payments表
    FOR EACH ROW -- 作用在每一个受影响的行  
BEGIN  
    UPDATE invoices  
    SET payment_total = payment_total + NEW.amount  
    WHERE invoice_id = NEW.invoice_id; -- NEW/OLD  
END $$  
DELIMITER ;
```

##### SHOW
```mysql
SHOW TRIGGERS;
SHOW TRIGGERS LIKE 'payments%';
```

##### DROP
```mysql
DROP TRIGGER IF EXISTS payments_after_insert;
```

##### LOGGING

```mysql
-- 记录活动
DELIMITER $$  
CREATE TRIGGER payments_after_insert  
    AFTER INSERT -- BEFORE/AFTER INSERT/UPDATE/DELETE  
    -- 在插入行之后触发
    ON payments  -- 作用在payments表
    FOR EACH ROW -- 作用在每一个受影响的行  
BEGIN  
    UPDATE invoices  
    SET payment_total = payment_total + NEW.amount  
    WHERE invoice_id = NEW.invoice_id; -- NEW/OLD  

	INSERT INTO payments_audit
	VALUES (NEW.client_id, NEW.date, NEW.amount, 'Insert');
END $$  
DELIMITER ;
```

### Events

```mysql
SHOW VARIABLES LIKE 'event%';  
SET GLOBAL event_scheduler = ON;
```
##### CREATE

```mysql
DELIMITER $$  
CREATE EVENT yearly_delete_state_audit_rows  
ON SCHEDULE  -- 事件触发计划
    -- AT '2025-01-01'  
    -- EVERY 2 DAY
    EVERY 1 YEAR STARTS '2025-01-01' ENDS '2030-01-01'  
DO BEGIN  
    DELETE FROM payments_audit  
    WHERE action_date < NOW() - INTERVAL 1 YEAR;  
END $$  
DELIMITER ;
```

##### SHOW
```mysql
SHOW EVENTS;
```

##### DROP
```mysql
DROP EVENTS IF EXISTS yearly_delete_state_audit_rows;
```

##### ALTER

```mysql
DELIMITER $$  
ALTER EVENT yearly_delete_state_audit_rows  
ON SCHEDULE  -- 事件触发计划
    -- AT '2025-01-01'  
    -- EVERY 2 DAY
    EVERY 1 YEAR STARTS '2025-01-01' ENDS '2030-01-01'  
DO BEGIN  
    DELETE FROM payments_audit  
    WHERE action_date < NOW() - INTERVAL 1 YEAR;  
END $$  
DELIMITER ;
```

```mysql
ALTER EVENT yearly_delete_state_audit_rows ENABLE;
ALTER EVENT yearly_delete_state_audit_rows DISABLE;
```

### Transactions

##### ACID Properties

- Atomicity 原子性，不可分离，要不全执行成功，要不全部撤销
- Consistency 一致性，数据库保持一致性
- Isolation 事务相互隔离，不相互干扰 (synchronized)
- Durability 持久性，事务产生的修改是持久的
##### Create

```mysql
START TRANSACTION;  
  
INSERT INTO orders (customer_id, order_date, status)  
VALUES (1, '2025-01-01', 1);  
  
INSERT INTO order_items  
VALUES (LAST_INSERT_ID(), 1, 1,1);  
  
COMMIT;
-- 或者ROLLBACK 手动退回事务，检查错误
```

##### Concurrency and Locking

```mysql
...
```

##### Transaction Isolation Levels

- Lost Updates: 两个事务更新同一行，最后提交的事务覆盖了前一次的更改
- Dirty Reads: 读了未提交的数据
- Non-repeating Reads: 事务中读相同数据两次，得到了不同的结果
- Phantom Reads: 查询丢失行，还没COMMIT的时候，其他事务正在修改
![Pasted image 20250509112547.png](https://imgu.falnsakura.top/PicGo/2025/12/0fc0fef934775278ae7be9b6eae599f2.png)

```mysql
SHOW VARIABLES LIKE 'transaction_isolation';
SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE;
   -- GLOBAL
```

##### Deadlocks

```mysql
START TRANSACTION;
UPDATE customers SET state = 'VA' WHERE customer_id = 1;
UPDATE orders SET status = 1 WHERE order_id = 1;
COMMIT;

START TRANSACTION;
UPDATE orders SET status = 1 WHERE order_id = 1;
UPDATE customers SET state = 'VA' WHERE customer_id = 1;
COMMIT;
```

###### 死锁
- 两个事务同时进行的时候
- 第一个事务的 第一行更新 锁住了第二个事务的第二行更新
- 第二个事务的 第一行更新 锁住了第一个事务的第二行更新
- 俩事务互相锁住，都无法继续运行
- 检测到死锁事务会被退回

###### 解决
- 事务因死锁被退回可以重新恢复
- 事务 要按照相同的顺序 更新数据
- 精简事务
- 把长的事务避开高峰期运行

### Users and Privileges

##### CREATE

###### Connection Restraint
```mysql
CREATE USER John@127.0.0.1;
-- John只能从localhost连接到MySQL
```

```mysql
CREATE USER John@localhost;
CREATE USER John@codewithme.com;

CREATE USER John@%.codewithme.com;
-- codewithme.com的所有子域名
```

##### PASSWORD
```mysql
CREATE USER John@localhost IDENTIFIED BY '1234';
```
###### SET
```mysql
SET PASSWORD = '12345'; -- 设置当前用户密码
SET PASSWORD FOR John = '12345';
```

##### Show
```mysql
SELECT * FROM mysql.user;
```

##### DROP
```mysql
CREATE USER Bob@localhost IDENTIFIED BY '1234';
DROP USER Bob@localhost;
```

##### Privileges

###### GRANT
```mysql
GRANT SELECT, INSERT, UPDATE, DELETE, EXECUTE
ON sql_store.customers, sql_store.*
TO moon_app@moon.com;
```

- `ALL`
- `CREATE`
- `CREATE VIEW`
- `CREATE ROUTINE`
- `...`

```mysql
GRANT ALL ON *.* TO John;
```

###### SHOW
```mysql
SHOW GRANTS FOR John;
```

###### REVOKE
```mysql
REVOKE CREATE ON sql_store.* FROM John;
```
