### MYSQL的基本操作

#### 创建数据表

+ CREATE TABLE <表名>([表选项定义])[表选项] [分区选项]​;其中，表定义选项的各位为<列名1><类型名1>[,...]<列名n><类型名n>

  ```mysql
  CREATE TABLE tb_tmp1
  (
      id INT(11),
      name VARCHAR(25),
      deptId INT(11),
      salary FLOAT
  );
  ```

#### 修改数据表

+ ALTER TABLE <表名>[修改选项]

```mysql
### 修改表名
ALTER TABLE <旧表名> RENAME [TO] 新表名;
CREATE TABLE tb_tmp1 RENAME TO tb;
### 修改表字符集
ALTER TABLE 表名 [DEFAULT] CHARACTER SET <字符集名> [DEFAULT] COLLATE <校对规则名>;
ALTER TABLE tb_tmp1 CHARACTER SET gb2313 DEFAULT COLLATE gb2312_chinese_ci;
### 修改字段名称
ALTER TABLE <表名> CHANGE <旧字段名><新字段名><新数据类型>;
ALTER TABLE tb_tmp1 CHANGE col1 col2 CHAR(30);
### 修改字段的数据类型
ALTER TABLE <表名> MODIFY <字段名><数据类型>;
ALTER TABLE tb_tmp1 MODIFY id int(12);
### 删除某一个字段
ALTER TABLE <表名> DROP <字段名>;
### 删除字段
ALTER TABLE <表名> DROP <字段名>;
### 删除表操作
DROP TABLE [IF EXISTS] 表名 [,表名2,表名3...]
### 删除表的实例
```

#### 删除表的操作

数据表之间时常存在外间关联。

+ 如果想要删除父表的话，先删除与它相关的子表，再删除父表，但是这样的话会删除两个表。
+ 可以先将关联外键取消，然后再删除父表;适用于保留子表的数据，只删除父表的情况。

#### 查看表结构的命令

```mysql
DESCRIBE <表名>; or DESC <表名>;
SHOW CREATE TABLE <表名>;
使用\g 和\G的形式可以改变展示形式
```

#### 在数据表里面添加字段

```mysql
### 默认添加在表的末尾
ALTER TABLE <表名> ADD <新字段名> <数据类型> [约束条件];
### 指定表的添加位置
ALTER TABLE <表名> ADD <新字段名><数据类型>[约束条件] FIRST;
### 在指定的字段后面添加字段
ALTER TABLE <表名> ADD <数据类型> [约束条件] AFTER <已经存在的字段名>;
```

### 约束概述

约束是指一种对数据的约束，能够帮助数据管理员更好地管理数据库，保证数据的正确性和有效性。主要分为以下六种约束

```mysql
### 主键约束
主键约束使用最为频繁。创建数据表的时候，一般都会要求设置一个主键，这个主键要是唯一的。
### 外键约束
外间约束经常和主键约束一起使用，用来确保数据的一致性
### 唯一约束
唯一约束能够确保列的一致性，在表格里面可以设置多个唯一约束
### 检查约束
检查约束用来检查数据表里面的某一个字段是否有效。
### 非空约束
非空约束用来指定表中的字段不能为空
### 默认值约束
某人值约束用来指定如果这个字段为空的时候，自动为其添加一个设置好的值。
### 以上六个约束，除了主键约束唯一之外，其余的都可以指定多个。
```

#### 主键约束

```mysql
### 设置主键
<字段名><数据类型>PRIMARY KEY [默认值]
示例：
定义字段的时候设置主键
CREATE TABLE tb_emp3
(
    id INT(11) PRIMARY KEY,
    name VARCHAR(33),
    deptID INT(11),
    salary FLOAT
);
定义完所有的字段之后设置主键
[CONSTRAINT]<约束名>PRIMARY KEY[字段名]
CREATE TABLE tb_emp3
(
    id INT(11),
    name VARCHAR(33),
    deptID INT(11),
    salary FLOAT,
    PRIMARY KEY(id)
);
### 创建表格的时候设置联合主键
PRIMARY KEY [字段1,字段2,...]
### 在修改表的时候添加主键约束
ALTER TABLE<数据表名> ADD PRIMARY KEY(<字段名>);
### 删除主键
ALTER TABLE <数据表名> DROP PRIMARY KEY;
### 主键自增长
字段名 数据类型 AUTO_INCREMENT
默认情况下，AUTO_INCREMENT的初始值为1,每新增一条记录，字段值自动增加1。
### 我们可以自己指定字段增长的初始值。
```

#### 外键约束

```mysql
### 创建表格的时候创建外键
[CONSTRANT <外键名>] FOREIGN KEY 字段名[,字段名2,..REFERENCES <主表名> 主键列1,主键列2,...]
CREATE TABLE tb1
(
    id int(11) PRIMARY KEY,
    name VARCHAR(33)
);
CREATE TABLE tb2
(
    id int(11) PRIMARY KEY,
    cid int(11),
    CONSTRAINT fk_tb2_tb1
    FOREIGN KEY(cid) REFERENCES tb1(id)  #表格2里面的cid关联表格1里面的主键id，此时，表格2为外间，随着表格1里面的id的变化而变化
);
### 修改数据表的时候添加外间约束
ALTER TABLE<数据表名> ADD CONSTRAINT <外键名>
FOREIGN KEY(<列名>) REFERENCES <主表名>(<列名>);
### 删除外间约束
ALTER TABLE <表名> DROP FOREIGN KEY <外键约束名>;
```

#### 唯一约束

唯一约束指的是表格中的值不能重复出现，唯一约束确保了列的唯一性，我们发现，这与主键很相类似，但是，不同的是，唯一约束可以在表中存在多个。

```mysql
### 创建唯一约束
<字段名><数据类型> UNIQUE
### 修改表时添加唯一约束
ALTER TABLE <数据表名> ADD CONSTRAINT <唯一约束名> UNIQUE(<列名>);
### 删除唯一约束名
ALTER TABLE <数据表名> DROP INDEX <唯一约束名>;
```

#### 检查约束

检查约束是用来检查数据表里面的某一个字段是否有效的一种手段。

```mysql
### 选取设置检查约束的字段
CHECK <表达式>
CREATE TABLE tb
(
    id INT(11) PRIMARY KEY,
    name VARCHAR(33),
    deptID INT(11),
    salary FLOAT,
    CHECK(salary>0 AND salary<10),  #salary进行检查约束
    FOREIGN KEY(deptID) REFERENCES tb_dept1(id)
);
### 修改表的时候添加检查约束
ALTER TABLE <表名> ADD CONSTRAINT <检查约束名> CHECK(<检查约束表达式>);
### 删除检查约束
ALTER TABLE <数据表名> DROP CONSTRAINT <检查约束名>;
```

#### 默认约束值

默认约束值是指在某列没有被赋值的情况下，系统会自动为这个字段添加默认值。

```mysql
### 创建表时设置默认约束值
<字段名> <数据类型> DEFAULT <默认值>;
CREATE TABLE tb
(
    id INT(11) PRIMARY KEY,
    name VARCHAR(33) DEFAULT 'peterliang'
);
### 创建表格的时候添加默认约束
ALTER TABLE< 数据表名> CHANGE COLUMN <字段名><数据类型>DEFAULT <默认值>;
### 删除默认约束(也就是把它的默认值设置为NULL即可)
ALTER TABLE <数据表名> CHANGE COLUMN <字段名> <字段名> <数据类型> DEFAULT NULL;
```

#### 非空约束

指定的字段的值不能为空，对于使用了非空约束的字段，如果用户添加数据的时候没有指定值，系统就会报错

```mysql
### 创建表格的时候设置非空值
<字段名> <数据类型> NOT NULL;
### 修改表格的时候添加非空约束
ALTER TABLE <数据表名> CHANGE COLUMN <字段名> <字段名> <数据类型> NOT NULL;
### 删除非空约束
ALTER TABLE <数据表名> CHANGE COLUMN <字段名> <字段名><数据类型> NULL;
```

#### 查看表中的约束

```mysql
### 查看数据表中约束
SHOW CREATE TABLE <数据表名>;
```

### 常用的运算符概述

mysql中的运算符是四则运算的基本思路来的，对于除法，除不尽的会保留四位有效数字。

#### 算术运算符

```mysql
+ - * / MOD(%)
```

#### 逻辑运算符

```mysql
### NOT !
当操作符为NULL的时候，返回的也是NULL;其余的是取反，NOT的优先级低于+，但是！的优先级高于！
### AND &&
当几个条件之中存在NULL的时候，返回的结果就是NULL
### OR ||
当两个操作数都为非NULL的时候，如果有任意一个操作数为非0值，则返回1,否则返回0
当两个操作数里面有一个NULL的时候，如果任意一个操作数都不是0,则返回NULL。
当两个操作数均为NULL的时候，返回NULL
### XOR
当任意一个操作数为NULL的时候，返回值为NULL
```

#### 比较运算符

```mysql
### 等于（=）
如果比较的时候出现了NULL，则返回的值为NULL
### 安全等于（<=>)
如果比较的时候同时为NULL，则返回1,否则返回0
### 不等于（！=，<>)
### 大于等于(>=)，小于等于(<=)
### 判断是否为空IS NULL(ISNULL)或者判断是否不为空IS NOT NULL
例子：ISNULL(NULL),10 is NOT NULL;
### BETWEEN AND 运算符
expr BETWEEN min AND max;
```

#### 位运算符

```mysql
### | 位或
### & 位与
### ^ 位异或
### ～ 位取反
### << 位左移
### >> 位右移
```

#### IN和NOT IN的使用

```mysql
IN:
1、对于非空的情况，如果在一个列表里面。则返回1,否则返回0
2、对于可能存在空的情况，如果找不到匹配项，则返回NULL，否则返回1
NOT IN：
当存在空值的时候，如果找不到匹配项，则返回NULL，否则返回0,这里不能按照字面意思去理解NOT IN，而是更具判定定理进行相关的判断。
```

#### mysql函数

mysql可以允许用户使用数据库提供的内部函数，这些内部函数可以帮助用户更加方便地处理表中的数据，函数就像预订的公式一样存在数据库里面，用户可以调用相关的库函数实现某些需求。

### Mysql操作表中的数据

#### 数据表查询语句：SELECT

```mysql
SELECT
{*|<字段列名>}
[
    FROM <表1>,<表2>...
    [WHERE <表达式>
    [GROUP BY <group by definition>
    [HAVING <expression> [{<operator><expression>}...]
     [ORDER BY <order by definition>]
     [LIMIT[<offset>,]<row count>]
];
# {*|<字段列名>} 包含星号通配符的字段列表，表示所要查询字段的名称
# <表1>,<表2>...表示查询数据的来源，可以单个或者多个
# WHERE <表达式> 是可选项，如果选择该项，将限定查询的数据必须满足该查询条件
# GROUP BY<字段> 表示将查询出的数据进行排序，并且按照给定的字段进行排序
# [LIMIT[<offset>,]<row count>]表示每次显示查询出来的数据条数
```

#### 查询表中所有的字段

```mysql
SELECT * FROM 表名;
# * 是通配符,将返回所有的列，数据列按照创建表时的顺序显示（这种查询方法比较耗时间，如果只想查询某一条数据的话，可以只查询单独某一列的数据）
SELECT <列名1>,<列名2>,<列名3>... FROM <表名>;
# 查询表格里面对应列的数据，并且按照给出的顺序进行返回，这种写法有一定的灵活性，但是对于可操作性比较低。
```

#### 使用limit限制查询次数

+ 如果使用一个limit，那么就是默认从最开的那一行查找限制的数量。
+ 如果使用两个limit，那么我们就是从第一个限制的开始行进行查找，查找对应的行数的数据。

#### 使用DISTINCT过滤重复数据(去重)

```mysql
SELECT DISTINCT <字段名> FROM <表名>;
# 例如：SELECT DISTINCT age FROM student;
```

#### 为数据表或者字段指定别名

当表名很长或者执行一些特殊的查询的时候，为了方便操作，可以为表指定一个别名，用这个别名替代表原来的名称。

```mysql
<表名> AS <别名>;
# 表名不能和数据库里面其他表同名，字段的别名不能与该表的其他字段同名。
<字段名> AS <别名>;
```

#### 限制查询结果的条数

当数据表里面的数据很多的时候，一次性查询大量的数据会给数据库服务器造成极大的负担，所以我们可以限制每次查询的条数。

````mysql
LIMIT 指定初始位置
t:SELECT * FROM tb_students_info LIMIT 3,5;
LIMIT 初始位置，记录数
# 初始位置指的是从那条记录开始显示，“记录数”表示的是显示的记录的条数。第一条记录的位置为0,后面一次类推。
LIMIT 记录数
t:SELECT * FROM tb_studend_info LIMIT 5;
# 如果不指定初始位置的话，则默认是从第一条记录开始进行查找。
LIMIT OFFSET 组合使用
LIMIT 记录数 OFFSET 初始位置
t:SELECT * FROM tb_student_info LIMIT 5 OFFSET 3;
````

#### 对查询结果进行排序

在查找数据的时候，默认是按照添加到数据库里面的顺序进行查找的，但是我们也可以自定义显示的顺序。

```mysql
ORDER BY <字段名> [ASC|DESC]
# 字段名可以有多个，按照从左到右的优先顺序进行排序，ASC表示的是按照升序排列，DESC表示的是按照降序的顺序排列，默认时ASC，排序的话如果遇到空值的话，默认空值是最小的值。
#单字段排序
SELECT * FROM tb_students_info ORDER BY height;
# 多字段排序
SELECT name,height FROM tb_student_info ORDER BY height,name;
SELECT name,height FROM tb_student_info ORDER BY height DESC,name ASC;
```

#### 条件查询

当我们查询数据的时候，我们可以用WHERE指定查询条件。

```mysql
WHERE 查询条件
# 单条件查询
SELECT name,height FROM tb_student_info WHERE height>=170;
# 多条件查询
SELECT name,age,height FROM tb_students_info WHERE age>21 AND height>=175;
```

#### 模糊查询

我们可以使用LIKE关键字搜索匹配字段中的指定的内容。

```mysql
[NOT] LIKE `字符串`(注意匹配的字符串必须加单引号或者双引号)
# NOT 是可选参数，字段中的内容与指定的字符串不匹配时满足的条件。
# 字符串，用来指定匹配的字符串。字符串可以是一个完整的字符串，也可以包含通配符。
LIKE支持%和_这两种通配符
# %是mysql里面最常用的一个通配符，它能代表任何长度的字符串，字符串的长度可以为0。
# _只能代表单个字符，字符的长度不能为0
# 默认情况下，LIKE匹配的字符串是不区分大小写的，如果想区分大小写的话，可以加入BINARY关键字。
SELECT name FROM tb_students_info WHERE name LIKE 't%'; (不区分大小写)
SELECT name FROM tb_students_info WHERE name LIKE BINARY 't%';(区分大小写)



# 注意事项
+ 注意大小写，MYSQL默认是不区分大小写的。
+ 注意尾部空格，尾部空格会干扰通配符的匹配。
+ 注意NULL。%通配符可以匹配到任意字符，但是不能匹配NULL，也就是说%匹配不到tb_students_info 数据表中值为NULL的记录。


# 使用通配符注意的一些技巧
+ 尽量不要使用通配符，除非有必要，MYSQL对通配符的处理会比其他操作符花费的时间长。
+ 在确定一定要使用通配符的时候，尽量不要把通配符放在字符串的开始处，把通配符至于搜索模式的开始处，搜索起来是最慢的。
+ 注意通配符的位置。
# 如果查询的字符串里面有通配符的话，可以使用\进行转义
```

#### 范围查询

mysql提供了范围查询BETWEEN AND关键字，用来判断某一个字段的数值是否在指定的范围。

```mysql
[NOT] BETWEEN 取值1 AND 取值2（这里的取值是一个闭区间，包括区间的端点)
```

#### 空值查询

利用IS NULL判断某一个字段是否为空值。空值不同于0,也不同于空字符串

```mysql
IS [NOT] NULL
```

#### where和Having的区别

+ WHERE在数据库分组前进行过滤，Having在数据分组后进行过滤，所有对于使用聚合函数对WHERE没啥用，HAVING子句中基于这些值过滤掉的分组。

#### GROUP BY分组查询

```mysql
GROUP BY <字段名>
# 单独使用GROUP BY关键字的时候，只会显示每个分组的第一条记录
SELECT `name`,`sex` FROM tb_students_info GROUP BY sex;
# GROUP BY 关键字可以和GROUP_CONCAT()函数一起使用，GRUOP_CONCAT()函数会把每个分组的字段都显示出来。
# 多个字段分组查询的时候，会先按照第一个字段进行分组，如果第一个字段中有相同的值，MYSQL才会对第二个字段进行分组，如果第一个字段的值唯一，那么就不会对第二个字段进行分组。
SELECT `sex`, GROUP_CONCAT(name) FROm tb_students_info GROUP BY sex;
# GROUP BY 和聚合函数一起使用COUNT(),SUM(),AVE(),MAX(),MIN()
SELECT sex,COUNT(sex) FROM tb_students_info GROUP BY sex;
# GROUP BY与WITH ROLLUP
# WITH ROLLUP关键字用来在所有记录的最后一条加上一条记录，这条记录是上面所有记录的总和。
SELECT sex,GROUP_CONCAT(name) FROM tb_students_info GROUP BY sex WITH ROLLUP;
```

#### 过滤分组

MYSQL可以对分组后的数据进行过滤

```mysql
HAVING <查询条件>
# WHERE和HAVING函数的对比使用
+ WHERE 用于过滤数据行，HAVING用于过滤分组
+ WHERE根据数据表的字段直接进行过滤，而HAVING是根据前面已经查询出的字段进行过滤。
+ WHERE查询不可以使用字段的别名，而HAVING可以
+ HAVING可以使用聚合函数，而WHERE不可以
```

#### 多表查询

交叉连接一般返回连接表的笛卡尔级

```mysql
SELECT <字段名> FROM <表1> CROSS JOIN <表2> [WHERE子句]或
SELECT <字段名> FROM <表1>,<表2>[WHERE子句]
```

内连接

内连接主要通过设置连接的条件的方式，来移除查询结果中某些数据行的交叉连接。即利用条件表达式来消除交叉连接的某些数据行。

```mysql
SELECT <字段名> FROM <表1> INNER JOIN <表2>[ON子句](ON指明了查询的连接条件)
INNER JOIN也可以使用WHERE子句指定连接条件，但是INNER JOIN...ON语法是官方的标准写法，而且WHERE子句在某些时候会影响查询的性能
SELECT s.name,c.course_name FROM tb_students_info s INNER JOIN tb_course c ON s.course_id = c.id;
```

外连接

外连接分为左外连接和右外连接

```MYSQL
# 左连接
SELECT <字段名> FROM <表1> LEFT OUTER JOIN <表2> <ON字句>
+ 表1是基表，表2是参考表，在进行左连接查询的时候，基表的数据会被完全查找出来，然后在参考表里面满足条件的数据会被查询出来，如果查询不到，会返回一个控制NULL
# 右连接
SELECT <字段名> FROM <表1> RIGHT OUTER JOIN <表2><ON子句>
+ 表2是基表，表1是参考表，可以查询出表2所有的记录和表1满足条件的记录，如果匹配不到，则返回NULL
# 在进行外连接查询的时候，一定要分清楚是要查询出基表的所有数据，还是参考表里面满足条件的数据。
```

#### 子查询

子查询指将一个查询语句嵌套在另一个查询语句里面。它也可以实现类似于连接查询功能

```MYSQL
WHERE <表达式> <操作符> (子查询)
# 操作符IN|NOT IN,EXITS|NOT EXISTS
SELECT name FROM tb_students_info WHERE course_id IN (SELECT id FROM tb_course WHERE course_name = 'JAVA');
# 习惯上，我们通常把SELECT 查询称为父查询，而括号内的查询为子查询，但是，子查询必须包括到圆括号里面，先会进行子查询，后面会进行父查询。
=与<>,一个是等于，一个是不等于
SELECT(子查询)FROM 表名;
SELECT * FROM (子查询)AS 表的别名;
exp:SELECT * FROM (SELECT * FROM result)AS temp;
```

#### 正则表达式

正则表达式可以用于替换某一个模式的文本内容，如冲一个文件里面提取电话号码，查找一篇文章中重复的单词，文章中的敏感的词汇，这些都是可以使用正则表达式的。

```MYSQL
属性名 REGEXP `匹配方式`
```



| 选项        | 说明                             |
| ----------- | -------------------------------- |
| ^           | 匹配文本的开始的字符             |
| $           | 匹配文本的结束字符               |
| .           | 匹配任何一个字符                 |
| *           | 匹配零个或者多个在他前面的字符   |
| +           | 匹配前面的字符1次或者多次        |
| <字符串>    | 匹配包含指定字符的文本           |
| [字符集合]  | 匹配字符集合中的任意一个字符     |
| [^]         | 匹配不在括号里面的任何字符       |
| 字符串{n}   | 匹配前面字符串至少n次            |
| 字符串{n,m} | 匹配前面的字符串至少n次，至多m次 |

#### 数据处理

插入数据

在mysql查询语句里面，我们通常使用INSERT插入一行或者多行数据

```MYSQL
1、INSERT INTO <表名> [<列名>,<列名>] VALUES (值1)[...(值n)];
2、INSERT INTO <表名> SET 
<列名1> = <值1>,
<列名2> = <值2>,
...

INSERT INTO ... FROM 语句复制表的数据
```

修改数据

```MYSQL
UPDATE <表名> SET 字段1 = 值1,字段2=值2... [WHERE 子句] [ORDER BY 子句] [LIMIT 子句]
```

删除数据

```MYSQL
DELETE FROM <表名> [WHERE 子句] [ORDER BY 子句] [LIMIT 子句]
```

清空表的数据

```mysql
TRUNCATE [TABLE] 表名
DELETE和TRUNCATE的比较
#DELETE 是逐行进行记录的删除，而TRUNCATE是直接删除原来的表，然后重新创建一个一模一样的新表，执行的速度上面DELETE 快。
#DELETE 删除数据后，可以配合时间回滚进行数据的找回，而TRUNCATE不支持事务的回滚2,
# DELETE的使用范围广，删除后系统不会重新设置自增字段的计数器，而TRUNCATE会
# DELETE支持删除局部或者整体，而TRUNCATE则只支持删除整体
```

### MYSQL视图

视图是一种虚拟存在的表，同真实表一样，视图也由行或者列构成，但是视图不存在实际的数据库里面，行和列的数据来自于定义视图的查询的所有表，并且还是在使用视图时动态生成的。视图不会存在数据库里面被记录，视图可以按自己的需求生成对应的表，其数据来源于实际表。

视图的优点

+ 定制用户的数据，聚焦特定的数据
+ 简化数据操作
+ 提高数据的安全性
+ 恭喜所需数据
+ 更改数据格式
+ 重用SQL语句

```mysql
# 查看表结构
desc <表名>
# 查看视图的详细信息
SHOW CREATE VIEW 视图名 \G;
# 所有的视图的定义都是存储在VIEWs表里面的，也可以在这个表中查找所有的视图的详细信息
SELECT * FROM information_schema.views;
# 创建视图
CREATE VIEW <视图名> AS <SELECT语句>
CREATE VIEW view_students_info AS SELECT * FROM tb_students_info;
CREATE VIEW view_students_info (s_id,s_name,s_age) AS SELECT id,name,age FROM tb_students_info;
```

#### 修改视图

```mysql
ALTER VIEW <视图名> AS <SELECT 语句>
执行上述语句需要操作者拥有CREATE 和DROP 的权限
如果视图里面包含一下任何一种，那么它就是不可更新的。
+ 聚合函数
+ DISTINCT 关键字
+ GROUP BY子句
+ HAVING子句
+ UNION或者UNION ALL 运算符
```

#### 删除视图

删除视图指的是删除数据库里面已经存的视图，删除视图，只能删除定义，不能删除数据

```mysql
DROP VIEW <视图名>,[<视图名>...];
DROP VIEW IF EXISTS v_students_info;
```

#### MYSQL索引

索引是一种特殊的数据库结构，由数据表中的一列或者多列组成，用来快速查找数据表中有某一中特定值的记录。

索引就是根据表中的一列或者若干列按照一定顺序建立的列值与记录行之间的对应关系表，实质上就是一张描述索引列的列值和原表中记录行之间的一一对应的关系的有序表。

在MYSQL中，通常有两种方式访问数据库表的行数据。

+ 顺序访问，即从头到尾进行遍历。
+ 索引访问，就是通过遍历索引来直接访问表中的记录行的方式

索引的优缺点

+ 优点
  + 可以确保数据库表中每一行数据的唯一性
  + 可以给MYSQL的所有的列类型设置索引
  + 可以加快查找的效率
  + 可以实现数据的参考完整性方面可以加速表与表之间的连接
  + 在使用分组和排序字句进行数据查询是可以显著减少查询中分组和排序的时间

+ 缺点
  + 在创建和维护索引组要耗费时间，并且随着数据量的增大耗费时间也越多
  + 索引还是占用磁盘空间，除了创建数据表之外，还会占用一定的物理空间，如果有大量的索引，索引占用的空间可能比数据库本身还要大
  + 对数据进行添加，修改和删除操作时，需要动态维护，这样就降低了数据维护的速度。

#### 创建索引

```mysql
1、CREATE INDEX语句
CREATE <索引名> ON <表名> (<列名>[<长度>][ASC|DESC])
<索引名> 一个表可以创建多个索引，但是只有每个索引在数据表里面都是唯一的
<表名> 创建索引的表名
<列名> 指定要创建索引的列名，通常考虑将查询语句中在JOIN子句和WHERE字句经常出现的列作为索引列
<长度> 可选，指定使用列前的length个字符来创建索引
2、CREATE TABLE 语句
# 在创建表的时候同时创建
CONSTRAINT PRIMARY KEY [索引类型](<列名>,...)
KEY | INDEX [<索引名>][<索引类型>](<列名>...)
UNIQUE [INDEX|KEY][<索引名>][<索引类型>](<列名>,...) # 创建唯一索引
FOREIGN KEY <索引名> <列名>
3、ALTER TABLE 语句
# 可以在创建好的表上面创建索引
ADD INDEX [<索引名>][<索引类型>](<列名>,...)  # 修改表的同时添加该表的主键
ADD PRIMARY KEY [<索引类型>](<列名>,...)
UNIQUE [INDEX|KEY][<索引名>][<索引类型>](<列名>,...) # 创建唯一索引
ADD FOREIGN KEY <索引名> <列名>
exp:
CREATE TABLE tb_stu_info2
(
    id INT NOT NULL,
    name CHAR(45) DEFAULT NULL,
    dept_id INT DEFAULT NULL,
    height INT DEFAULT NULL,
    UNIQUE INDEX(height)
);
```

#### 查看索引

```mysql
SHOW INDEX FROM <表名> [FROM <数据库名>]
```

#### 删除索引

不用的索引建议删除，因为它会降低数据库查找的效率。

```mysql
DROP INDEX <索引名> ON <表名>
# 使用ALTER TABLE 语句
DROP PRIMARY KEY 删除表中的主键，一个表里面只有一个主键，主键也是一个索引。
DROP INDEX index_name 删除名称为index_name的索引
DROP FOREIGN KEY fk_symbol 删除外键
```

### MYSQL存储过程和触发器

一个存储过程就是一个可编程的函数，他在数据库中创建并保存，一般由SQL语句和一些特殊的控制结构组成，当希望在不同的平台执行相同的功能的时候存储过程尤其适用。

存储过程是数据库的一个重要的功能，存储过程可以用来转换数据，数据迁移，制作报表，它类似于编程语言，一次执行成功，就可以随时被调用，完成指定的功能操作。

存储过程的优点：

+ 封装性
+ 可增强SQL语句的功能和灵活性
+ 可减少网络流量
+ 高性能
+ 提高数据库的安全性和数据的完整性
+ 使数据独立

#### 创建存储过程

存储过程就是一些SQL语句的集合。

````mysql
CREATE PROCEDURE <过程名> ([过程参数[,...]])<过程体>
[过程参数[,...]]格式
[IN|OUT|INOUT]<参数名><类型>
# 过程名
存储过程的名称，默认在当前数据库中创建，若需要在特定的数据库中创建存储过程，则要在名称前面加上数据库的名称，即db_name.sp_name
# 括号
存储过程中的参数列表，其中，<参数名>为参数名，<类型> 为参数的类型，当有多个参数的时候，需要在参数列表彼此中间加上一个逗号进行分割，即使没有参数，仍需要加上一对。MYSQL支持三种类型的参数，输入参数，输出参数，输入/输出参数。分别用IN,OUT,IN/OUT三个关键字进行标识。其中输入参数可以传递一个存储过程，输出参数用于存储过程需要返回一个操作结果的情形，而输入/输出参数既可以充当输入参数，也可以充当输出参数。
# 过程体
过程体是存储过程的主题部分，也称为存储过程体，包含在存储过程中调用的时候必须执行的SQL语句。这个部分以关键字BEGIN开始，以关键字END结束。若存储过程中只有一条SQL语句，那么可以省略这个。
DELIMITER指令
在唇膏简爱年存储过程时，我们常常需要执行多条SQL语句，但是这些语句仍然是以分号进行结尾的，为了避免我们的过程体只执行一个语句，那么我们就使用DELIMITER命令将结束命令修改为其他的字符。
命令如下(注意用空格分隔)：
DELIMITER $$
注意这里应该避免使用/，因为会被转义
exp:DELIMITER ??
exp:
DELIMITER //
CREATE PROCEDURE ShowStuScore()
BEGIN
SELECT * FROM tb_students_score;
END
exp:
DELIMITER //
CREATE PROCEDURE GetScoreByStu
(IN name VARCHAR(30))
BEGIN
SELECT student_score FROM tb_students_score
WHERE studnet_name=name;
END //
````

#### 查看存储过程

```mysql
# 查看存储过程的状态
SHOW PROCEDURE STATUS LIKE '存储过陈名';
# 查看存储过程的定义
SHOW CREATE PROCEDURE 存储过程名;
```

#### 修改存储过程

在实际开发过程中，修改业务需求的情事时有发生。

```MYSQL
ALTER PROCEDURE 存储过程名 [特征...]
特征指定了存储过程的特性，可能的取值有:
+ CONTAINS SQL表示子程序包含SQL语句，但不包含读或者写数据的语句。
+ NO SQL表示子程序中不包含SQL语句。
+ READS SQL DATA 表示子程序包含读数据的语句
+ MODIFIES SQL DATA 表示子程序中包含写数据的语句
+ SQL SECURITY {DEFINER|INVOKER} 指明谁有全新来执行
+ DEFINER 表示只有定义者自己能够执行
+ INVOKER 表示调用者可以执行
+ COMMENT 'string' 表示注释信息
exp:
ALTER PROCEDURE showstuscore MODIFIES SQL DATA SQL SECURITY INVOKER;
如果想修改创建语句，可以删除存储过程重新定义，如果想修改存储过程名，可以删除存储过程重新定义一个另外名字的相同的存储过程。
```

#### 删除存储过程

```mysql
DROP PROCEDURE [IF EXISTS] <过程名>
```

#### 存储函数详解

存储过程没有返回值，而存储函数有返回值。

```mysql
CREATE FUNCTION sp_name[func_parameter[...]]
RETURNS type
[characteristic...]routine_body
sp_name：存储函数的名称
func_parameter:表示存储函数的参数列表
RETURNS type:指定返回值的类型
characteristic:指定存储函数的特性，该参数的取值与存储过程是一样的。
routine_body:表示SQL代码的内容，可以用BEGIN...END来标示SQL代码的开始和结束。
exp:
DELIMITER //
CTEATE FUNCTION func_student(id INT(11))
RETURNS varchar(20)
COMMENT '查询某一个学生的名字'
BEGIN
RETURN(SELECT name FROM tb_student WHERE tb_student.id=id);
END //
DELIMITER ;
# 查看存储函数
SHOW FUNCTION STATUS LIKE 存储函数名;
SHOW CREATE FUNCTION 存储函数名;
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME=存储函数名
# 修改存储函数
ALTER FUNCTION 存储函数名 [特征...]
# 删除存储函数
DROP FUNCTION [IF EXISTS] <函数名>
```

#### MYSQL调用存储过程和函数

存储函数和存储过程都是存储在服务器端的SQL语句集合，要想使用这些已经定义好的存储过程和存储函数就必须要通过调用的方法来实现。

在存储过程通过CALL语句来调用，存储函数的使用与MYSQL内部函数的使用方法相同，执行存储过程和存储函数需要拥有EXECUTE权限(EXECUTE权限的信息存储于information_schema数据库下的USER_PRIVILEGES表中)

```mysql
# 调用存储过程
CALL sp_name(parameter[...])
sp_name为存储过程的名称，parameter表示存储过程的参数。
# 调用存储函数
SELECT func_student(参数);
```

#### MYSQL变量的定义和赋值

```mysql
# 定义变量
+ DECLARE var_name[,...]type [DEFAULT value]
DEFAULT value将子句的变量默认值设置为value，若没有使用这个子句，那么默认值为NULL
exp:
DECLARE my_sql INT DEFAULT 10;
# 赋值变量
SET var_name=expr[,var_name=expr]...
exp:
SET my_sql = 30;
# 将查询的结构赋值给某一个变量
SELECT col_name [...]INTO var_name[,...]
FROM table_name WHERE condition
exp:
SELECT id INTO my_sql FROM tb_student WHERE id = 2;
```

#### 定义条件和处理程序

定义条件是指实现定义程序执行过程中遇到的问题，处理程序定义了在遇到这些问题时应当采取的处理方式和解决办法，保证存储过程和函数在遇到警告或错误时继续执行，从而增强程序处理问题的能力。避免程序出现异常被停止执行。

```mysql
# 定义条件
DECLARE condition_name CONDITION FOR condition_value condition value;
SQLSTATE [VALUE] sqlstate_value | mysql_error_code
exp:
如定义ERROR1146(42S02)这个错误，名称为can_not_find,可以用两种方法来定义
DECLARE can_not_find CONDITION FOR SQLSTATE '42S02';
DECLARE can_not_find CONDITION FOR 1146;
# 定义处理程序

```

#### 游标的定义和使用

游标是一个标识，用来表示数据取到了什么地方，可以理解为数组的下标。

```mysql
# 声明游标
DECLARE cursor_name CURSOR FOR select_statement;
cursor_name为游标的名称，select_statement表示SELECT语句，可以返回一行或者多行数据。
# 打开游标
声明游标之后，要想使用游标，需要先打开游标。
OPEN cursor_name;
# 使用游标
FETCH cursor_name INTO var_name[,var_name]...
# 关闭游标
CLOSE cursor_name;
游标会占用内存资源，所以不要使用的时候应该删除。
```

#### 流程控制语句详解

```mysql
# IF
IF search_condition THEN statement_list
[ELSEIF search_condition THEN statement_list]...
[ELSEIF statement_list]
END IF
# CASE
CASE case_value
WHEN when_value THEN statement_list
[WHEN when_value THEN statement_list]...
[ELSE statement_list]
END CASE
# LOOP
[begin_label:]LOOP
statement_list
END LOOP [end_label]
# LEAVE
LEAVE label 跳出循环的控制
# ITERTE
再次进入某一个循环
# REPEAT
[begin_label:]REPEAT
statement_list
UNTIL search_condition
END REPEAT [end_label]
每次进入循环的时候，对表达式进行判断，如果返回FALSE,则跳出循环。
# WHILE
当满足条件的时候，进入循环，
[begin_label:]WHILE search_condition DO
statement list
END WHILE [end label]
```

#### 触发器

mysql触发器不同于存储过程，它不需要执行CALL启动，而是通过执行相关的操作来触发这个效果。

当我们在处理一些业务逻辑的时候，当数据表发生更改的时候，自动进行一些处理，这时就可以使用触发器。

触发器的优点：

+ 执行自动。
+ 触发器的实施比FOREIGN KEY约束，CHECK 约束更为复杂的检查和操作
+ 可以实现数据的级联更改，在一定程度上面保持数据的完整性

缺点：

+ 出现问题很难定位，维护困难
+ 大量使用触发器容易导致代码的结构被打乱。
+ 需要变动的数据量较大的时候，触发器的执行效率会降低

mysql支持的触发器

+ INSERT
+ UPDATE
+ DELETE

#### 创建触发器

````mysql
CREATE TRIGGER<触发器名> <BEFORE | AFTER >
<INSERT | UPDATE | DELETE >
ON <表名> FOR EACH Row<触发器主体>
<触发器名> 默认在当前的数据库下面，如果要指定特定的数据库，需要指定数据库名
<表名>表名必须是实体表，不能呢个是临时表或者视图。一个数据表不能有两个相同时刻的触发器
<触发器主体>触发动作主体，包含激活触发器的时候需要执行的mysql语句，执行多个复合语句的话可以使用BEGIN...END 复合语句
# 创建BEFORE类型的触发器
exp:
CREATE TRGGER SunOfSalary BEFORE INSERT ON tb_tmp8 FOR EACH Row SET @sum=@sum+NEW.salary;
# 创建AFTER类型
````

#### 查看触发器

触发器的信息存储在infromation_schema数据库里面的triggers数据表里面。

```mysql
SHOW TRIGGERS;
```

#### 修改和删除触发器

修改触发器可以先删除触发器，然后创建名称相同的新的触发器

```mysql
DROP TRIGGER [IF EXISTS][数据库名]<触发器名>
```

### MYSQL用户管理

mysql的用户权限都放在名字为mysql数据库里面，mysql数据库中存储的都是用户权限表，用户登录以后，mysql会根据这些权限表的内容为每个用户赋予对应的权限

user表是mysql中最重要的一个权限表，用来记录允许连接到服务器的帐号信息，需要注意的是，在user表里启用的所有权限都是全局级的，适用于所有的数据库。

在mysql数据库里面需要输入的信息，需要注意的是mysql版本不再使用Password作为存储密码的字段，而改成了authentication_string.

#### user表

存储了用户的使用信息和相关的权限

#### db表

权限表，存储了用户对某一个数据库的操作权限，表中的字段大致可以分为两类，分别为用户列和权限列。db表中的权限列和user表中的权限列大致相同，只是user表中的权限是针对所有的数据库的，而db表中的权限只针对指定的数据库。如果希望用户只对某一个数据库有操作权限，可以先将user表中对应的权限设置为N，然后在db表中设置对应的数据库的操作权限。

#### 创建用户

在mysql安装的时候，会默认创建一个名为root的用户，该用户拥有超级权限，可以控制整个mysql服务器。

mysql提供了一下3种方法创建用户

+ 使用CREATE USE语句创建用户
+ 在mysql.user表中创建
+ 使用GRANT语句创建



```mysql
+ CREATE USER 语句创建用户
CREATE USER <用户> [IDENTIFIED BY [PASSWORD]'password']
用户:指定创建用户帐号，格式为user_name@host_name.这里的user_name是用户名，host_name 为主机名，即用户连接mysql时所用的主机的名字。如果在创建过程中，只给出了用户名:没指定主机名的话，默认为‘%’,表示一组主机，对所有的主机开放权限。
IDENTIFIED BY子句:用于指定用户密码，新用户可以没有初始密码，若该用户不设密码，可省略此子句。
PASSWORD'password’,PASSWORD表示使用哈希值设置密码，该参数可选，如果是一个普通的密码，可以使用'password'表示用户登录时的密码，需要用单引号括起来。
exp:CREATE USER peter@localhost IDENTIFIED BY 'test1';
添加一个用户，用户名为peter,主机名为localhost,密码为test1.


+ 使用INSET语句新建用户
可以使用INSRET语句将用户的信息添加到mysql.user表中，但必须有对mysql.user表的INSERT权限，通常只添加Host,User,authentication_string这三个字段。
INSERT INTO mysql.user(Host,authentication_string,ssl_cipher,x509_issuser,x509_subject) VALUES ('hostname','username',PASSWORD('password'),",","");
之后使用FLUSH PRIVILEGES 命令让用户生效。


+ 使用GRANT语句新建用户
虽然CREATE USER 和INSERT INTO语句都可以创建普通用户，但是这两种方式不便授予用户权限，于是mysql提供了GRANT语句。
GRANT priv_type ON database.table TO user[IDENTIFIED BY [PASSWORD]'password']
priv_type表示的是赋予什么权限，database.table表示给该用户哪个表的操作权限，user表示用户名，use.name@user.host形式，IDENTIFIED BY表示设置密码
exp:GRANT SELECT ON *.* TO 'peter'@localhost IDENTIFIED BY 'test3';


+ 修改用户
RENAME USER <旧用户> TO <新用户>


+ 删除用户
DROP USER <用户1> [,<用户2>]...
用户:用户名@主机名


+ 使用DELETE语句删除普通用户
DELETE FROM mysql.user WHERE Host='hostname' AND User='username';

```

#### 用户权限

```mysql
+ mysql查看用户权限
SELECT * FROM mysql.user;
SHOW GRANTS FOR 'username'@'hostname';
可以通过查询mysql.user表中的数据记录来查看相应的用户权限，也可以使用SHOW GRANTS语句查询用户的信息
若出现USAGE ON *.*表示该用户对任何数据库和任何表都没有权限


+ 用户授权
授权就是为某一个用户赋予某些权限，例如，可以为新建的用户赋予查询所有数据库和表的权限，MYSQL提供了GRANT语句来设置权限。
GRANT priv_type [(column_list)] ON databases.table TO user [IDENTIFIED BY [PASSWORD]'password'][, user[IDENTIFIED BY [PASSWORD]'password']]...
[WITH with_option [with_option]...]
priv_type 参数表示权限类型
columns_list参数表示作用在哪些列上，
database.table表示权限的级别
user参数表示用户账户，由用户名和主机名构成，格式是"'username'@'hostname'"
IDENTIFIED BY 参数用来为用户设置密码
password参数是用户的新密码
WITH关键字后面带有一个或者多个with_option参数，这个参数有5个选项。
GRANT OPTION：被授权的用户可以将这些权限授权给别的用户
MAX_QUERIES_PER_HOUR count;设置每个小时的执行的count次查询
MAX_UPDATES_PER_HOUR count;设置每个小时执行的count次更新。
...
可以授权的权限是：
列权限，表权限，数据库权限，用户权限。
 

+ 删除用户权限
使用REVOKE语句删除某一个用户的某些权限，在一定程度上可以保证系统的安全。
p1:REVOKE prvi_type [column)list]...ON database.table FROM user [,user]...
删除用户的所有权限
p2:REVOKE ALL PRIVILEGES, GRANT OPTION FROM user,[user]...
```

#### 登录或者退出服务器

```mysql
# 启动服务器
mysql -h hostname|hostIP -p port -u usrname -P DatabaseName -e "SQL语句"]
-h 指定主机 默认localhost
-p 指定端口 默认3306
-u 指定用户
-P 提示输入密码
DatabaseName 指定连接到mysql服务器后，登录到哪个数据库，如果没有，默认为mysql数据库
-e 指定需要执行的SQL语句，登录MYSQL服务器后执行这个SQL语句，然后退出服务器。
```

#### 修改密码

```mysql
# root修改普通用户的密码
root权限很高，可以修改普通用户的密码
SET PASSWORD FOR 'username'@'hostname' = PASSWORD('newpwd');
# 使用UPDATE语句修改普通用户的密码
UPDATE MySQL.user SET authentication_string=PASSWORD('newpwd') WHERE User="username" AND Host="hostname";
# 使用GRANT语句修改普通用户密码
GRANT USAGE ON *.* TO 'user'@'hostname' IDENTIFIED BY 'newpwd';
指定某一个账户的密码而不影响当前账户的权限，需要注意的是，使用GRANT语句修改密码，必须拥有GRANT权限，一般情况下最好使用该方法来指定或修改密码。
# 修改root密码
mysqladmin -u usename -h hostname -p password "newpwd";
# 修改mysql数据库的user表
UPDATE mysql.user set authentication_string=PASSWORD("rootpwd") WHERE User="root" and Host="localhost";
# 使用SET语句修改root用户的密码(修改自身的密码)
SET PASSWORD = PASSWORD("rootpwd");

# 修改密码的三种方式
1）使用SET PASSWORD命令
+ 先登录mysql，如果没有配置环境变量，请在MySQL的bin目录下面登录操作
+ set password for username@localhost=password(newpwd);
2)使用mysqladmin修改root密码
+ mysqladmin -u用户名 -p旧密码 password 新密;(注意不要在-u，-p后面加空格);
3)使用UPDATE直接编辑user表
+ 输入命令mysql -u root -p登录
+ 进入mysql数据库
+ 使用 UPDATE mysql.user set authentication_string=password('新密码') where user='用户名' and Host='localhost';
+ 输入FLUSH PRIVILEGES刷新权限
+ 退出重新登录
```

### MySQL备份

数据库的备份主要是对数据进行保存和维护，所以备份数据是数据库管理中常用的操作，为了防止数据库意外崩溃或者硬件损伤而导致的数据丢失，数据库系统提供了备份和恢复策略。

数据库的备份指的是导出数据或者复制表文件的方式来制作数据库的副本，当数据库遭到破坏的时候，备份的数据库会加载到系统里。

####  备份类型

+ 热备

  热备份指的是在数据库运行中直接备份，对正在运行的数据库操作没有任何的影响，数据库的读写操作可以正常运行。

  + 逻辑备份，指的是备份出文件的内容是可读的，一般是文本内容，内容一般是由一条条SQL语句，这类方法适用于数据库的升级和迁移，缺点是恢复时间较长。
  + 裸文件备份，指的是复制数据库的物理文件，既可以在数据库运行的时候复制，也可以在在数据库停止的时候复制，这类备份恢复的时间较短。

+ 冷备

  冷备份指的是在数据库停止的情况下进行备份，数据库的读写操作不能执行，这种备份最为简单。

+ 温备

  这种的是在数据库运行中进行的，但是会对当前数据库的操作有所影响，备份时仅支持读操作，不支持写操作。

+ 一般备份的数据有：表数据，二进制日志，InnoDB日志，代码，服务器配置文件
+ 几种备份工具，mysqldump; cp,tar等归档复制工具;Ivm2 snapshot, mysqlhotcopy; xtrabackup;

### MySQL日志

日志记录了数据库的运行情况，日常操作和错误信息等，可以帮助我们判断数据库出现的各种问题。

日志分为二进制日志，错误日志，通用查询日志和慢查询日志。

二进制日志记录了用户对数据库更改的所有操作，如INSERT语句，UPDATE语句，CREATE语句，等。

通用查询日志用来记录用户的所有操作，包括启动和关闭MySQL服务，更新语句和查询语句。

慢询日志用来记录在MySQL中执行时间超过指定时间的查询语句，通过慢查询日志，可以查找出哪些查询语句的执行效率低。

```mysql
# 删除二进制日志
RESET MASTER
# 删除指定编号二进制日志
PURGE MASTER LOGS TO 'filename.number';
# 停止或者开启二进制日志
SET SQL_LOG_BIN=0/1;
# 二进制还原数据库的命令
mysqlbinlog filename.number | mysql -u root -p
```

启用或者设置通用查询日志

通过在MySQL配置文件中添加log选项来开启通用查询日志。

