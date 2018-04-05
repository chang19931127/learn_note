# 数据库

## 什么是数据库

 数据库->表->行->列

 客户机服务器软件，所以存在连接，通信,MySQL支持集群，主从，等等

### 分解数据(构造好的表)

>正确地将数据分解为多个列极为重要，例如，城市，州，邮政编码应该总是独立的列，通过把他们分解开，才有可能利用特定的列对数据进行排序和过滤(如，找出特定州或特定城市的所有顾客)

### 主键的最好习惯(表应该总包含主键)

>除MySQL强制实施的规则外，应该坚持的几个普遍认可的最好习惯：

1. 不更新主键列中的值
2. 补充用主键列的值
3. 不在主键列中使用可能会更改的值。(例如，如果使用一个名字作为主键以标识某个供应商，当该供应商合并和更改其名字时，必须更改这个主键)

### 优雅的定义数据类型

>定要好的表，就要针对列的数据类型正确选择，主要四大类，要使用，易扩展

    主要问题在串数据类型和数值数据类型如何抉择！
    规则：
    如果数值是计算(求和，平均等)中使用的数值，则应该存储在数值数据类型列中。
    如果作为字符串(可能只包含数字)使用，则应该保存在串数据类型列中。
    但还有最重要的一定，就是复合大家用户习惯，别人第一点认为这个应该属于什么数据类型，例如年龄，第一反应是数值，你非要给一个串就有问题了把

#### 串数据类型

>除了计算的数据，尽可能来用串

- CHAR          1-255个字符的定长串，指定CHAR(n)
- VARCHAR       长度可变，最多不超过255长度，VARCHAR(n)指定最大n个字符，新老版本意思不一样
- ENUM          接受组多64K个串组成的预定义集合串
- SET           接受最多64个串组成的预定义集合的零个或多个串
- LONGTEXT      与TEXT，长度4GB
- WEDIUMTEXT    与TEXT，长度16K
- TEXT          最大长度64K的变长文本
- TINYTEXT      与TEXT，长度255字节

#### 数值数据类型

>尽可能仅进行计算操作的用数值来存储BIT，默认有符号，可以使用UNSIGNED关键字

- BIT       位字段，1-64位.
- BIGINT    整数值
- BOOLEAN   布尔标志
- DECIMAL   精度可变的浮点值，一般用这个表示货币
- DOUBLE    双精度浮点值
- FLOAT     单精度浮点值
- INT       整数值
- MEDIUMINT 整数值
- REAL      4字节的浮点值
- SMALLINT  整数值
- TINYINT   整数值

#### 日期和时间数据类型

| 数据类型     | 说明          |
| ------------- |:-------------:|
| DATE          | 表示YYYY-MM-DD |
| DATETIME      | DATE和TIME的组合      |
| TIMESTAMP     | 功能和DATETIME相同，但范围较小      |
| TIME          | 表示HH:MM:SS           |
| YEAR          | 两位数字，四位数字      |

#### 二进制数据类型

| 数据类型     | 说明          |
| ------------- |:-------------:|
| BLOB          | 最大64KB |
| MEDIUMBLOB    | 最大16MB    |
| LONGBLOB      | 最大4GB     |
| TINYBOLB      | 最大255字节  |

### MySQL常用工具

>客户服务器，就有相对应的工具来操作

1. MySQL命令行实用程序

    mysql --help获得详细情况 ，要使用;或者\g结束
2. MySQL Administrator

    MySQL管理器，需要专门去下载
    - Server Information(服务器信息)
    - Service Control(服务控制)
    - User Administration(用户管理)
    - Catalogs(目录)
3. MySQL Query Browser

    业务要专门去下载
    - 输入MySQL命令到屏幕顶上的窗口中。然后交给软件执行
    - 如果有结果，显示在屏幕左边的大区域网格中
    - 多条语句和结果显示在他们自己的标签中
    - 屏幕右边是一个标签，列出所有数据源，均可查看
    - 可以选择提供的内容让MQB帮你生成
    - 右边就是历史
    - 以及相关的语法

4. 其实，我们开发都使用Navicat for MySQL

### 使用MySQL

#### 连接数据库

    - 主机名
    - 端口
    - 用户名
    - 口令

#### 选择数据库

    USE database;
    SHOW DATABASES;  //显示该数据源下面的所有数据库
    SHOW TABLES;    //显示库中的所有表
    SHOW COLUMNS FROM table;    //显示表中的列以及某些列的类型，属性，和 DESCRIBE 等价 = DESCRIBE table;
    SHOW STATUS;    //显示服务器状态
    SHOW CREATE DATABASE;
    SHOW CREATE TABLE;      //分别显示动作的MySQL的语句
    SHOW GRANTS;    //显示授予用户
    SHOW ERRORS;
    SHOW WARNINGS;  //显示错误或者警告信息
    也可以直接HELP SHOW;了解更多！

#### 检索数据

>数据库对我们来讲，重中之重就是检索数据了，如何更好更快的检索数据呢，两条信息：选择什么，从什么地方选择。SQL语句不区分大小写，并且所有空格都被忽略。

```sql
SELECT prod_name FROM products;
SELECT prod_id,prod_name,prod_price FROM products;
SELECT * FROM products; //一般不这么操作，低效
SELECT DISTINCE vend_id FROM products;  //去重
SELECT prod_name FROM products LIMIT 5; //同义0,5
SELECT prod_name FROM products LIMIT 5,5;   //$1 开始位置(行数)，$2 检索的行数
SELECT products.prod_name FROM crashcourse.products;//库名.表名，表名.列名
```

#### 排序检索数据

>程序中，动不动就排个序哈哈,使用ORDER BY 非必须子句进行操作

```sql
SELECT prod_name FROM products ORDER BY prod_name;//派序列可以不是查询列
SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price,prod_name;    //先按价格，再按名称排序
SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price DESC; //默认顺序，DESC逆序，从高到底,
SELECT prod_id,prod_price,prod_name FROM products ORDER BY prod_price DESC,prod_name;   //仅针对prod_price逆序
SELECT prod_price FROM products ORDER BY prod_price DESC LIMIT 1;   //结合就可以查出来最大的
```

#### 过滤数据

>查询离不开条件搜索，使用WHERE子句,有一点，能通过数据库来过滤数据最好，否则会传递过多的无用数据，在客户端被过滤，性能差，WHERE子句先于ORDER BY 否则报错

WHERE子句操作符号
| 数据类型     | 说明          |
| ------------- |:-------------:|
| =             | 等于        |
| <>            | 不等于    |
| !=            | 不等于     |
| <             | 小于  |
| <=            | 小于等于 |
| >             | 大于    |
| >=            | 大于等于     |
| BETWEEN       | 在制定的两个值之间  |

> **重点**：
WHERE 子串中，如果条件涉及到字符串比较，一定要通过单引号来进行限定，否则会少掉很多特点，例如索引

```sql
SELECT prod_name,prod_price FROM products WHERE prod_price = 2.50;
SELECT cust_id FROM customers WHERE cust_email IS NULL; //可以这么写，但是我们设计数据库的时候请避免有NULL的值，默认值都比NULL强
```

#### 数据过滤

> WHERE子句中如何使用多个子句，AND或OR的使用,SQL优先处理AND子句,所以请使用()来代替默认计算次序.IN的效率比OR要高.

```sql
SELECT prod_id,prod_price,prod_name FROM products WHERE vend_id = 1003 AND prod_price <= 10;
SELECT prod_name,prod_price FROM products WHERE vend_id = 1002 OR vend_id = 1003;
SELECT prod_name,prod_price FROM products WHERE (vend_id = 1002 or vend_id = 1003) AND prod_price >= 10;
SELECT prod_name,prod_price FROM products WHERE vend_id IN (1002,1003) ORDER BY prod_name;
SELECT prod_name,prod_price FROM products WHERE vend_id NOT IN (1002,1003) ORDER BY prod_name;
```

#### 使用通配符进行过滤

>查询没有模糊查询怎么可以，MYSQL提供通配符，LIKE关键字，%匹配多个,_匹配一个,REGEXP关键字子串匹配，通配符需要合理使用，否则性能很差

```sql
SELECT prod_id,prod_name FROM products WHERE prod_name LIKE 'jet%'; //通配符使用不当会降低性能
SELECT prod_id,prod_name FROM products WHERE prod_name LIKE '_ ton anvil';
SELECT 'hello' REGEXP '[0-9]';  //请认真理解操作符 SELECT
SELECT prod_name FROM products WHERE prod_name REGEXP '^[0-9\\.]'   //匹配以0-9或,开头的
```

#### 创建计算字段

>在某中情况，库里存储的数据和客户端的数据有所差别，但是计算过程如果子客户端可能效率不够高，因此，MySQL客户端应该也具有一定的计算能力，例如：数据拼接，数值计算，格式转化等等，Concat()连接函数，RTrim()去除串右边的空格LTrim()，

```sql
SELECT Concat(vend_name,' (',vend_country,')') FROM vendors ORDER BY vend_name;   //可以拼接多个串,查出来的类似 ACME (USA)
SELECT Concat(RTrim(vend_name),' (',RTrim(vend_country),')') AS vend_title FROM vendors ORDER BY vend_name;
SELECT prod_id,quantity,item_price,quantity*item_price AS expanded_price FROM orderitems WHERE order_num = 20005;
```

#### 使用数据处理函数

>MySQL肯定也支持函数喽，处理文本串，数值计算，日期处理，特殊系统函数操作，Upper()，

文本处理函数
| 函数        | 说明          |
| ------------- |:-------------:|
| Left()            | 返回串左边的字符        |
| Langth()            | 返回串的长度        |
| Locate()            | 找出串的一个子串        |
| Lower()            | 串转化小写        |
| LTrim()            | 去掉左边的空格        |
| Right()            | 返回串右边的字符        |
| RTrim()            | 去掉右边空格        |
| Soundex()            | 返回串的SOUNDEX值，发音类似        |
| SubString()            | 返回子串的字符        |
| Upper()            | 串转化为大写        |

日期和时间处理函数
| 函数        | 说明          |
| ------------- |:-------------:|
| AddDate()            | 增加一个日期(天，周)        |
| AddTime()            | 增加一个时间(时，分)        |
| CurDate()            | 当前日期        |
| CurTime()            | 当前时间        |
| Date()            | 日期时间的日期部分        |
| DateDiff()            | 计算两个日期只差        |
| Date_Add()            | 高度灵活的日期运算函数        |
| Date_Format()            | 格式化的日期或时间串   |
| Day()            | 一个日期的天数部分        |
| DayOfWeek()            | 一个日期对应的星期部分        |
| Hour()            | 一个时间的小时部分        |
| Minute()            | 一个日期对应的分钟部分        |
| Month()            | 一个日期对应的月份部分        |
| Now()            | 当前日期和时间        |
| Second()            | 一个日期的秒部分        |
| Time()            | 一个日期时间的时间部分        |
| Year()            | 一个日期对应的年份部分        |

>数据库日期格式存储最好是**yyyy-mm-dd**

数值处理函数
| 函数        | 说明          |
| ------------- |:-------------:|
| Abs()            | 绝对值        |
| Cos()            | 余弦        |
| Exp()            | 指数值        |
| Mod()            | 余数        |
| Pi()            | 圆周率        |
| Rand()            | 随机数        |
| Sin()            | 正弦        |
| Sqrt()            | 平方根        |
| Tan()            | 正切        |

```sql
SELECT vend_name,Upper(vend_name) AS vend_name_upcase FROM vendors ORDER BY vend_name;  //大写
SELECT cust_id,order_num FROM orders WHERE Date(order_date) = '2005-09-01'; //使用函数，否则不准确，加分秒的
SELECT cust_id,order_num FROM orders WHERE Year(order_date) = 2005 AND Month(order_date) = 9;
```

#### 汇总数据

>同样作为数据存储，当然也迟滞检索数据，以便分析和报表生成，主要行数，行组的和，最大最小值等

MySQL提供的聚合函数
| 函数        | 说明          |
| ------------- |:-------------:|
| AVG()            | 某列平均值        |
| COUNT()            | 某列的行数        |
| MAX()            | 某列的最大值        |
| MIN()            | 某列的最小值        |
| SUM()            | 某列值之和        |

```sql
SELECT AVG(DISTINCT prod_price) AS avg_price FROM products;//忽略null的行,不包含重复值
SELECT COUNT(*) AS num_cust FROM customers;//列名也会会略null的行
SELECT MAX(prod_price) AS max_price FROM products;//忽略null值的行
SELECT COUNT(*) AS num_items,MIN(prod_price) AS price_min,MAX(prod_price) AS price_max,AVG(prod_price) AS price_avg FROM products;  //组合，哈哈，使用聚合函数后，最好使用别名。
```

#### 分组数据

>数据多了，我们就需要进行分组，GROUP BY子句和HAVING子句就来了,汇总表内容的子集,可以理解成HAVING就是为了补充GROUP BY子句的条件关键字么？WHERE在分组前过滤，HAVING在分组后过滤，因此导致过滤的数据不同，一般使用GROUP BY子句后顺序不固定，最好跟上ORDER BY来排序

GROUP BY子句的规定

- GROUP BY 子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制

- 如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总，换句话说，在建立分组时，指定的所有列都一起计算

- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式(但不能是聚合函数)，如果SELECT中使用表达式，则必须在GROUP BY 子句中指定相同的表达式。不能使用别名。

- 除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子句中给出。

- 如果分组列中具有NULL值，则NULL将作为一个分组返回，如果列中有多行NULL，他们被分为一组。

- GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。

```sql
SELECT vend_id,COUNT(*) AS num_prods FROM products GROUP BY vend_id;    //根据vend_id分组，并且找出每组的个数
SELECT cust_id,COUNT(*) AS orders FROM orders GROUP BY cust_id HAVING COUNT(*) >= 2 //两个以上的订单，通常使用这个语句来进行重复查询！
SELECT order_num,SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price) >= 50 ORDER BY ordertotal;   //根据订单号分组，计算订单价格>=50的，并且顺序输出
```

SELECT子句顺序

    SELECT      返回的列或表达式        必须
    FROM        从中检索数据            仅在从表选择数据时使用
    WHERE       行级过滤                不必须
    GROUP BY    分组说明                仅在按组计算集聚时使用
    HAVING      组级过滤                不必须
    ORDER BY    输出排序顺序            不必须
    LIMIT       检索的行数               不必须

#### 多表查询

>很多场景，我们一张表提供不了我们所需要的数据，因此需要进行多表查询，就需要联结表，根据其他表的结果再次进行查询，等等,针对复杂查询可以通过格式化SQL来进行了解，这里需要思考下表连接和微服务有什么关系？大表拆小表，链接查询

```sql
# 嵌套查询
SELECT cust_name,cust_contact
FROM customers
WHERE cust_id IN(SELECT cust_id
                    FROM orders
                    WHERE order_num IN (SELECT order_num
                        FROM orderitems WHERE prod_id = 'TNT2');
);  //实际执行三条语句，从内到外，为了性能不要嵌套太多
SELECT cust_name,cust_state,(SELECT COUNT(*) FROM orders WHERE orders.cust_id = customers.cust_id) AS orders FROM customers ORDER BY cust_name; //利用子查询来计算列，还有一点就是表和列一定要对应

# 表链接
表链接中的 ON 和 WHERE 怎么使用一定要学会
ON:连接的时候进行链接的约束
WHERE:连接完毕之后进行过滤的约束
左连接，就是左边全表，右连接，就是右边全表
SELECT customers.cust_id,orders.order_num FROM customers LEFT JOIN orders ON customers.cust_id = orders.cust_id
SELECT vend_name,prod_name,prod_price FROM vendors,products WHERE vendors.vend_id = products.vend_id ORDER BY vend_name,prod_name;  //查询结果来自两个表
SELECT cust_name,cust_contact FROM customers,orders,orderitems WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num AND prod_id = 'TNT2';   //这条SQL就是上面那个三条语句的嵌套查询简单化，因此记住，能转化吃成连接查询，尽可能不要使用嵌套查询

# 组合查询
//UNION 默认去除重复，UNION ALL 是全部都有
SELECT vend_id,prod_id,prod_price
FROM products
WHERE prod_price <= 5
UNION
SELECT vend_id,prod_id,prod_price
FROM products
WHERE vend_id IN (1001,1002)
ORDER BY vend_id;
//ORDER BY 只能出现在UNION中最后一个SELECT子句
//就是这条语句
SELECT vend_id,prod_id,prod_price FROM products WHERE prod_price <= 5 OR vend_id IN (1001,1002);
```

#### 全文本搜索

>记得MySQL有一个全文本搜索功能就好。和引擎有关，MyISAM支持MATCH()是匹配的列，Againist()包含的内容

```sql
SELECT note_text FROM WHERE productnotes WHERE MATCH(note_text) Against('anvils');  //查找出来文本里面有anvils的行

其他细节不细究，记得可以全文本搜索
```

#### 数据插入

>记录了那个多查询相关，证明对于一个数据中心，最重要的就是它的查询能力，但是数据怎么进入数据中心呢，插入INSERT就来临了。INSERT语句一般不会产生输出

```sql
# 插入完整的行
INSERT INTO customers VALUES(NULL,'Pep E. LaPew','100 Main Street','Los Angeles','CA','90046','USA',NULL,NULL)  //插入语句，这种语句容易出错误，因为顺序必须对应
INSERT INTO customers(cust_name,cust_contace,cust_email,cust_address,cust_city,cust_state,cust_zip,cust_country) VALUES('Pep E. LaPew',NULL,NULL,'100 Main Street','Los Angeles','CA','90046','USA')    //可以给定列改变默认顺序
INSERT INTO customers(cust_name,cust_contace,cust_email,cust_address,cust_city,cust_state,cust_zip,cust_country) SELECT cust_name,cust_contace,cust_email,cust_address,cust_city,cust_state,cust_zip,cust_country FROM custnew;
```

    不论那种INSERT语法，都必须给出VALUES的正确数目，如果不提供列名，则必须给每个表列提供一个值，如果提供列名，则必须对每个列出的列给出一个值，如果不给，就会产生错误消息，插入失败。
    总结：如果表的定义不允许NULL值并且不给出默认值，这个列插入被忽略就会报错。
    如果插入多条数据的时候又不想影响查询性能，可以INSERT LOW_PRIORITY INTO 这种插入方式，也试用于UPDATE和DELETE语句。
    使用INSERT SELECT，可以直接插入查询出来的结果。

#### 更新和删除

> 当然作为数据中心，数据的更新和删除肯定也需要进行支持了。UPDATE 和 DELETE 两个关键字。ALTER TABLE,

```sql
# 更新，不要忘记WHERE
UPDATE customers SET cust_email = 'changzhendong@qq.com',cust_name = 'changzhendong' WHERE cust_id = 10005;
```

    UPDATE有的时候会更新多行数据，但是中途有一行失败了，怎么办可以使用IGNORE来操作，UPDATE IGNORE customers SET ...

```sql
DELETE FROM customers WHERE cust_id = 10006;
```

    更快的删除操作，TRUNCATE customers 删除速度更快(实际是删除原来的表，并且重新建一个表，所以速度块)

UPDATE和DELETE使用习惯

- 除非确实打算更新和删除每一行，否则绝对不要使用不带WHERE子句的UPDATE和DELETE语句
- 保证每个表都有主键，尽可能的像WHERE子句那样使用它。
- 在对UPDATE或DELETE语句使用WHERE子句前，最好使用SELECT进行测试，确保编写语句正确
- 如果真的为了数据，那么就强制使用引用完整性的数据库，这样子MySQL就不允许删除具有和其他表级联的数据的行

#### 创建和操作表

> MySQL维度 库，表，列，所以我们应该知道关于表的相关操作

```sql
CREATE TABLE customers(
    cust_id         int         NOT NULL AUTO_INCREMENT,
    cust_name       char(50)    NOT NULL,
    cust_address    char(50)    NULL,
    cust_city       char(50)    NULL,
    cust_state      char(5)     NULL,
    cust_zip        char(10)    NULL,
    cust_country    char(50)    NULL,
    cust_contract   char(50)    NULL,
    cust_email      char(255)   NULL,
    PRIMARY KEY(cust_id)
) ENGINE=InnoDB;
1.可以加上 IF NOT EXISTS
2.允许NULL的值插入的时候可以不指定
3.一张表，尽可能的要有主键，且唯一，也有组合主键的例子
4.以为主键不能重复，我们MySQL提供给我们自动增量，如何获得这个增量呢，试试last_insert_id()这个函数。
5.必要的数据对列增加默认值
6.针对表，选择适合的引擎
```

MySQL引擎

1. InnoDB：一个可靠的事物处理引擎，不支持全文本搜索。
2. MEMORY：功能上等同域MyISAM，但是由于数据仅存储在内存中，速度快，适合临时表。
3. MyISM：性能极高的引擎，支持全文本搜索，但是不支持事物。

```sql
# 更改表
ALTER TABLE vendors ADD vend_phone CHAR(20);    //添加一个字段
ALTER TABLE vendors DROP COLUMN vend_phone; //删除一个字段
还可以通过这个操作，添加约束，索引等等

# 删除表，重命名
DROP TABLE customers2;
RENAME TABLE customers2 TO customers;
```

#### 使用视图

>视图究竟是什么，我也很迷惑，或者是存在的意义是什么？书上说视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。这么理解把，作为视图，他不包含表中应该有的任何列或数据，它包含的是一个SQL查询。然后客户端在这个视图的基础上去查询需要的数据,视图最常见的应用就是隐藏复杂的SQL

为什么使用视图？

- 重用SQL语句。
- 简化复杂的SQL操作，在编写查询后，可以方便的重用它而不必知道基本查询细节。
- 使用表的组成部分而不是整个表。
- 保护数据，视图可以给用户授予部分权限。
- 视图可以更改数据格式和表示。视图可能返回与底层表的表示和格式不同的数据

视图的规则和限制

- 与表一样，视图必须唯一命名
- 对于可以创建的视图没有限制
- 为了创建视图，必须具有足够的访问权限
- 视图可以嵌套，就是通过视图检索数据的查询来构造一个视图
- ORDER BY可以用在视图中，如果构建视图的SQL含有ORDER就会覆盖视图中的
- 视图不能**索引**，也不能有关联的触发器或默认值。
- 视图可以和表一起使用。表和视图连接查询。

关于视图的操作

- CREATE VIEW 创建视图
- SHOW CREATE VIEW viewname;查看创建视图的语句
- DROP VIEW viewname;删除视图
- 如何更新视图，CREATE OR REPLACE VIEW 或者，先DROP再CREATE

```sql
CREATE VIEW productcustomers AS SELECT cust_name,cust_contace,prod_id FROM customers,orders,orderitems WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num;    //创建视图
SELECT cust_name,cust_contact FROM productcustomers WHERE prod_id = 'TNT2';

//使用视图格式化检索的数据
CREATE VIEW vendorlocations AS SELECT Concat(RTrim(vend_name),' (',RTrim(vendcountry),')') AS vend_title FROM vendors ORDER BY vend_name;
SELECT * FROM vendorlocations;
//通过这个就知道视图就是SQL，那么我们SQL的作用都可以来使用，过滤，计算

#最重要的一定，视图主要是用来检索的，其实这么理解最棒，视图提供了一种MySQL的SELECT语句层次的封装，用来简化数据处理以及重新格式化基础数据或保护基础数据的。
```

#### 存储过程

>虽说现在针对数据库的操作要避免使用存储过程，但是仅仅使用MySQL的时候还是需要了解存储过程的。简单的说存储过程就是针对MySQL编程，针对数据表进行业务逻辑操作。就把存储过程理解成方法把

```sql
CREATE PROCEDURE productpricing()
BEGIN
    SELECT Avg(prod_pricd) AS priceaverage
    FROM products;
END;
CALL productpricing();  //调用存储过程
DROP PROCEDURE productpricing;  //删除存储过程

# 使用参数，IN OUT ，MySQL中存储过程中的变量@开头
CREATE PROCEDURE ordertotal(
    IN onumber INT,
    OUT ototal DECIMAL(8,2)
)
BEGIN
    SELECT Sum(item_price * quantity)
    FROM orderitems
    WHERE order_num = onumber
    INTO ototal;
END;
CALL ordertotal(2005,@total);
SELECT @total;  //就能计算存储过程输出的值

# 在来一个具有程序控制的存储过程把
CREATE PROCEDURE ordertotal(
    IN onumber INT,
    IN taxable BOOLEAN,
    OUT ototal DECIMAL(8,2)
) COMMENT 'obtain order total,optionally adding tax'
BEGIN
    -- Declare variable for total
    DECLARE total DECIMAL(8,2);
    -- Declare tax precentage
    DECLARE taxrate INT DEFAULT 6;

    -- Get the order total
    SELECT Sum(item_price * quantity)
    FROM orderitems
    WHERE order_num = onumber
    INTO total;

    -- Is this taxable?
    IF taxable THEN
        -- Yes,so add taxrate to the total
        SELECT total + (total / 100 * taxrate) INTO total;
    END IF;
    -- And finally,save to out variable
    SELECT total INTO ototal;
END;
CALL ordertotal(20005,0,@total);
SELECT @total;
-- 这是注释，COMMIT字段注释，然后就是逻辑操作，自己去理解程序

SHOW CREATE PROCEDURE ordertotal;           //显示语句
SHOW PROCEDURE STATUS LIKE 'ordertotal';    //查看存储过程状态

-- 存储过程中还有一个操作就是游标，CURSOR，游标可以OPEN和CLOSE，游标打开后，恶意FETCH来移动游标
CREATE PROCEDURE processorders()
BEGIN
    DECLARE o INT;
    DECLARE ordernumbers CURSOR
    FOR
    SELECT order_num FROM orders;
    OPEN ordernumbers;
    FETCH ordernumbers INTO o;
    CLOSE ordernumbers;
END;
```

#### 触发器

>这个不用介绍了把，见名知意，就如同数字电路里面的触发器一样，当某见事情发生后，触发触发器逻辑。MySQL中UPDATE仅DELETE，INSERT，UPDATE支持触发器。只有表支持触发器，视图不支持。每张表每一个事件每次只允许一个触发器，因此，一张表最多支持6个触发器。BEFORE和AFTER

```sql
CREATE TRIGGER newproduct AFTER INSERT ON products FOR EACH ROW SELECT 'Product added'; //对每个插入行插入之后，显示Product added

DROP TRIGGER newproduct;    //删除触发器
```

#### 事务管理

>重点来了，事物，事情务必成功，不成功就失败，操作全部撤销。MySQL的InnoDB支持事物。

事物相关的名次

- 事务：(transaction)指一组SQL语句
- 回退：(rollback)指撤销指定SQL语句的过程
- 提交：(commit)指将未存储的SQL语句结果写入数据库表
- 保留点：(savepoint)指事物处理中设置的临时站位符，你可以对保留点进行回退。

事物的基本要素

1. 原子性（Atomicity）：事务开始后所有操作，要么全部做完，要么全部不做，不可能停滞在中间环节。事务执行过程中出错，会回滚到事务开始前的状态，所有的操作就像没有发生一样。也就是说事务是一个不可分割的整体，就像化学中学过的原子，是物质构成的基本单位。
2. 一致性（Consistency）：事务开始前和结束后，数据库的完整性约束没有被破坏 。比如A向B转账，不可能A扣了钱，B却没收到。
3. 隔离性（Isolation）：同一时间，只允许一个事务请求同一数据，不同的事务之间彼此没有任何干扰。比如A正在从一张银行卡中取钱，在A取钱的过程结束前，B不能向这张卡转账。
4. 持久性（Durability）：事务完成后，事务对数据库的所有更新将被保存到数据库，不能回滚。

事务的并发问题

1. 脏读：事务A读取了事务B更新的数据，然后B回滚操作，那么A读取到的数据是脏数据
2. 不可重复读：事务 A 多次读取同一数据，事务 B 在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果 不一致。
3. 幻读：系统管理员A将数据库中所有学生的成绩从具体分数改为ABCDE等级，但是系统管理员B就在这个时候插入了一条具体分数的记录，当系统管理员A改结束后发现还有一条记录没有改过来，就好像发生了幻觉一样，这就叫幻读。

    再通俗说下，三种并发问题。
    脏读：可以读取到其他人未提交的数据        更新操作
    不可重复读：可以读取到其他人提交的数据     更新操作
    幻读：可以读取到其他人提交的数据          插入操作或者删除操作
    这下明白了把，其实幻读针对的是增删操作，可重复读针对的是更新操作

MySQL事务的隔离级别,隔离级别貌似是由锁来完成的

1. 读未提交（read-uncommitted）
2. 不可重复读（read-committed）    解决脏读
3. 可重复读（repeatable-read）    解决不可重复读
4. 串行化（serializable）        解决幻读

```sql
-- 回退
SELECT * FROM ordertotals;
START TRANSACTION;
DELETE FROM ordertotals;
SELECT * FROM ordertotals;
ROLLBACK;
SELECT * FROM ordertotals;

-- 提交
START TRANSACTION;
DELETE FROM orderitems WHERE order_num = 20010;
DELETE FROM orders WHERE order_num = 20010;
COMMIT;     //两条都删除，要么都不删除

-- 带上保留点
SAVEPOINT delete1;
ROLLBACK TO delete1;

--M ySQL默认的是自动提交，需要改成手动提交
SET autocommit = 0;
-- 使用完毕后在设置成自动提交
SET autocommit = 1;
```

#### 安全管理

>身为一个系统肯定要有访问控制。可以使用MySQL Administrator来进行管理

```sql
-- MySQL中提供一个user表,包含所有用户帐号
USE mysql;
SELECT user FROM user;

--用户
CREATE USER ben INENTIFIED BY 'p@$$WwOrd';  //创建
RENAME USER ben TO bforta;  //重命名
DROP USER bforta;   //删除用户

-- 设置访问权限
SHOW GRANTS FOR bforta;
GRANT SELECT ON crashcourse.* TO bforta; //crashcourse库中的所有表
REVOKE SELECT ON crashcourse.* FROM bforta; 取消授予的访问权限

-- 整个服务器，GRANT ALL 和 REVOKE ALL
-- 整个数据库，ON database.*
-- 特定的表，ON database.table
-- 特定的列
-- 特定的存储过程

-- 更改口令
SET PASSWORD FOR bforta = Password('n3w p@$$wOrd');
```

#### 数据库维护

>数据库存储核心数据，很重要，因此需要对数据库进行维护。

备份数据库

1. 使用命令行实用程序mysqldump转储所有数据库内容到某个外部文件。在进行常规备份前这个使用程序应该正常运行，以便能正确的备份转储文件。
2. 可用命令行实用程序mysqlhotcopy从一个数据库复制所有数据
3. 可以使用MySQL的BACKUP TABLE 或SELECT INTO OUTFILE 转储所有数据到某个外部文件

    备份前刷新数据到磁盘 FLUSH TABLES

```sql
ANALYZE TABLE orders;   //状态信息
CHECK TABLE orders;     //
```

诊断启动问题  mysqld  linux刚学完这个d明白么，sshd   demon的意思

1. --help
2. -safe-mode
3. --verbose
4. --version

MySQL要学会看日志文件，慢查询等都会在这里出现。

1. 错误日志，错误信息，位于data目录 hostname.err，日志名可以更改
2. 查询日志，记录MySQL的活动，也位于data目录，名字也可以更改
3. 二进制日志，记录更新过数据的所有语句，同样data目录，名字也可以该
4. 缓慢查询日志，记录慢查询的

    使用日志时，可用FLUSH LOGS 来刷新和重新开始所有日志文件。

#### 改善性能

>当然，系统就存在调优，那么数据库我们也要改善性能，以及我们优化我们的SQL语句。如果设计的表是优秀的，以及表的瓶颈点。

一些改善建议

- 首先，MySQL具有特定的硬件建议。因此对于生产环境下的服务器应该性能很高
- MySQL是用一系列的默认设置预先配置的。从这些设置开始通常是很好的。但过一段时间后你可能需要调整内存分配，缓冲区大小等(SHOW VARIABLES;SHOW STATUS)
- MySQL一个多用户多线程的DBMS，换言之，它经常同时执行多个任务，如果这些任务中的某一个执行缓慢，则所有请求都会执行缓慢，如果遇到显著的性能不良，可以使用SHOW PROCESSLIST显示所有活动的线程，还可以通过KILL命令来终结特定的线程
- 总是有不止一种方法编写同一条SELECT语句，应该实验链接，并，子查询，找出最佳的，最好看下SQL的执行计划 EXPLAIN
- 一半来说，存储过程执行得比一条条的执行要快的多
- 应该总使用正确的数据类型
- 绝不要检索比需求还要多的数据，换言之，不要使用SELECT * (除非你需要每个列)
- 有的操作(包括INSERT)支持一个可选的DELAYED关键字，如果使用它，将把控制立即返回给调用程序，并且一旦有可能就实际执行该操作
- 在导入数据时，应该关闭自动提交，你可能还想删除索引(包括FULLTEXT索引)，然后在导入完成后在重建他们。
- 必须索引数据库表以改善数据检索的性能。确定索引什么不是一件微不足道的任务，需要分析使用SELECT语句找出重复的WHERE和ORDER BY子句。如果一个简单的WHERE子句返回结果花的时间太长，则可以断定其中使用的列(或几个列)就是需要索引的对象。
- 你的SELECT语句中有一系列复杂的OR条件吗？通过使用多条SELECT语句和连接他们的UNION语句，你能看到极大的性能改进。
- 索引改善数据检索的性能，但损害数据插入和删除以及更新的性能，如果你有一些表，他们收集数据且不经常被搜索，则在有必要之前不要索引他们。
- LIKE很慢。一般来说最好使用FULLTEXT而不是LIKE。之后改善 LIKE X%也是可以走索引，这个需要了解以下
- 数据库是不断变化的实体。一组优化良好的表一会儿后可能就面目全非了，由于表的使用和内容的更改。理想的优化和配置也会改变。
- 最重要的一点，浏览官网文档[MySQL官网](http:dev.mysql.com/doc/)获得更多姿势

    以上是通过阅读MySQL必知必会的阅读笔记

下面是总结的内容：

killed slow query

##### 索引

``INDEXES``

索引，脑洞打开，思考。。。MySQL中的indexes是怎么实现的B-tree indexes是一个什么东东呢？回答：多节点树,会根据顺序访问叶子和节点

B-tree支持 "equality"(where id = 12) 和 "range"(date > '2013-01-01' and date < '2013-07-01' 和 id in (6,12,18)).

```sql
-- 这三种语句都支持索引
select * from table where id = 12;
select * from table where id in (6,12,18);

-- 上面是单索引，那么复合索引呢
-- Comb(CountryCode,District,Population) 复合索引靠左原则
-- MySQL can use:
-- CountryCode only part
-- CountryCode + District
-- CountryCode + District + Population
-- 其他的组合方式或者顺序改变都会导致索引出问题


-- 格式化执行计划
explain format=JSON select * from
City where CountryCode = 'USA' and District =
'California'\G

-- mysql 子句执行先手顺序
-- where 部分
-- group by 
-- order by 子句
-- select 部分

-- 一个建议，针对复合索引创建的情况，尽可能=靠左边，范围靠右边，因为这种方式定位速度较快索引结构好，也就是等于优先级高低范围，这就是directly直接定位，然后range scan扫描
```

##### 查询

    针对查询，我们要避免MySQL生成临时表的情况，因为这种情况特别影响性能！

其实慢查询经常会出现在group by 和 order by 子句，因为这两个子句增加了额外的功能会降低查询新能，因此我们需要针对这两种查询去分析优化

使用group by 子句的时候如果查询条件有问题就会导致MySQL create a temporary table 这样子性能肯定会特别的慢，因此针对group by子句的查询一定要去走索引，否则很可怕

###### "Group by" and Covered Indexes

```sql
-- 仅 where 索引 导致创建临时表
mysql> alter table ontime_2012 add key
(dayofweek);
mysql> explain select max(DepDelayMinutes),
Carrier, dayofweek from ontime_2012 where
dayofweek =7
group by Carrier\G
type: ref
possible_keys: DayOfWeek
key: DayOfWeek
key_len: 2
ref: const
rows: 817258
Extra: Using where; Using temporary;
Using flesort

-- group by 走索引消除临时表
mysql> alter table ontime_2012
add key covered(dayofweek, Carrier,
DepDelayMinutes);
explain select max(DepDelayMinutes), Carrier,
dayofweek from ontime_2012 where dayofweek =7
group by Carrier \G
...
possible_keys: DayOfWeek,covered
key: covered
key_len: 2
ref: const
rows: 905138
Extra: Using where; Using index
```

上面的是针对 equal的情况，如果是range 怎么办？

###### "Group by" and a Range Scan

```sql
-- 原始状态
mysql> explain select max(DepDelayMinutes),
carrier, dayofweek from ontime_2012
where dayofweek > 5 group by Carrier,
dayofweek\G
...
type: range
possible_keys: covered
key: covered
key_len: 2
ref: NULL
rows: 2441781
Extra: Using where; Using index; Using
temporary; Using flesort

-- 使用UNION 来进行优化
(select max(DepDelayMinutes), Carrier,
dayofweek
from ontime_2012
where dayofweek = 6
group by Carrier, dayofweek)
union
(select max(DepDelayMinutes), Carrier,
dayofweek
from ontime_2012
where dayofweek = 7
group by Carrier, dayofweek)
···
************** 1. row ***********************
table: ontime_2012
key: covered
...
Extra: Using where; Using index
************** 2. row ***********************
table: ontime_2012
key: covered
...
Extra: Using where; Using index
************** 3. row ***********************
id: NULL
select_type: UNION RESULT
table:
type: ALL
possible_keys: NULL
key: NULL
key_len: NULL
ref: NULL
rows: NULL
Extra: Using temporary

-- 使用in就沦陷了
mysql> explain
-> select max(DepDelayMinutes), Carrier,
-> dayofweek
-> from ontime
-> where dayofweek in (6,7)
-> group by Carrier, dayofweek\G
*********** 1. row ***************************
id: 1
select_type: SIMPLE
table: ontime
type: range
possible_keys: DayOfWeek
key: DayOfWeek
key_len: 2
ref: NULL
rows: 79882092
Extra: Using index condition; Using
temporary; Using flesort
1 row in set (0.00 sec)

-- 其实这些例子官网都有

```

其实针对分组的sql 需要特殊的索引才能有好的效果**"Group by" and a Loose Index Scan**松散索引就是，索引结构先精准在范围

- The query is over a single table.
- The GROUP BY names only columns that form a leftmost prefx of the index and no other columns.
- The only aggregate functions used in the select list (if any) are MIN() and MAX(), same column其他函数无效

```sql
-- loose_index_scan 索引 松散索引
KEY loose_index_scan
(Carrier,DayOfWeek,DepDelayMinutes)

-- equal
mysql> explain select max(DepDelayMinutes)
as ddm, Carrier, dayofweek from ontime_2012
where dayofweek = 5 group by Carrier,
dayofweek
table: ontime_2012
type: range
possible_keys: covered
key: loose_index_scan
key_len: 5
ref: NULL
rows: 201
Extra: Using where; Using index
for group-by

-- range
mysql> explain select max(DepDelayMinutes) as
ddm, Carrier, dayofweek from ontime_2012
where dayofweek > 5 group by Carrier,
dayofweek;
table: ontime_2012
type: range
possible_keys: covered
key: loose_index_scan
key_len: 5
ref: NULL
rows: 213
Extra: Using where; Using index
for group-by;
```

##### 基准(benchmark)

    一般针对查询，松散索引扫描(loose index scan)在0.1毫秒左右，紧凑索引扫描(tight index scan)在0.4毫秒左右，临时表一半在1.4毫秒左右，总归都是使用了索引。没有使用索引会慢的超级夸张

###### ORDER BY and Filesort

MySQL 在使用order by子句的时候可能会使用 filesort 操作来排序，需要去避免

```sql
-- 没有索引就是 filesort
table: City
type: ALL
possible_keys: NULL
key: NULL
key_len: NULL
ref: NULL
rows: 4079
Extra: Using where; Using filesort

-- 通过索引祛除了filesort
mysql> alter table City
add key my_sort2 (CountryCode, population);
mysql> explain select district, name,
population from City where CountryCode = 'USA'
order by population desc limit 10\G
table: City
type: ref
key: my_sort2
key_len: 3
ref: const
rows: 207
Extra: Using where
```

MySQL的索引T-tree是有序的所以可以很好的顺序读取索引的叶子

###### Sorting and Limit

如果我们的查询中有 LIMIT ，并且限制相对较少小（即，限制10或限制100）与数量相比表中的行，MySQL可以避免使用flesort并可以使用索引。

```sql
mysql> explain select * from ontime_2012
where dayofweek in (6,7) order by
DepDelayMinutes desc limit 10\G
type: index
possible_keys: DayOfWeek,covered
key: DepDelayMinutes
key_len: 5
ref: NULL
rows: 24
Extra: Using where
```

一个 limit 10的操作是

1. 通过索引顺序扫描整个表
2. 通过where子句过滤结果，但是不使用索引
3. 在匹配where条件的10条之后停止

这里有一个问题，如果通过索引遍历后还是empty result set MySQL就不得不回去全表遍历

导致的结果就是偏移量越大会越慢，优化的化可以通过 where 条件来减少偏移量

###### Calculated Expressions and Indexes

```sql

-- 计算会丢失掉索引的特性，因为每次MySQL都是通过计算再去进行比较
SELECT carrier, count(*)
FROM ontime
WHERE year(FlightDate) = 2013
group by carrier\GPractical MySQL Performance Optimization 29
Benchmarks
The explain plan:
mysql> EXPLAIN SELECT carrier, count(*) FROM
ontime
WHERE year(FlightDate) = 2013 group by
carrier\G
************** 1. row ************************
id: 1
select_type: SIMPLE
table: ontime
type: ALL
possible_keys: NULL
key: NULL
Key_len: NULL
ref: NULL
rows: 151253427
Extra: Using where; Using temporary;
Using flesort
Results:
16 rows in set (1 min 49.48 sec)

-- 解决上面的问题就是不要使用计算，MySQL的查询条件简单化

mysql> SELECT carrier, count(*)
FROM ontime
WHERE FlightDate >= '2013-01-01' and
FlightDate < '2014-01-01'
group by carrier\G;
mysql> EXPLAIN SELECT carrier, count(*) FROM
ontime
WHERE FlightDate between '2013-01-01'
and '2014-01-01'
GROUP BY carrier\G
******************** 1. row *****************
id: 1
select_type: SIMPLE
table: ontime
type: range
possible_keys: FlightDate
key: FlightDate
key_len: 4
ref: NULL
rows: 10434762
Extra: Using index condition; Using
temporary; Using flesort
Results:
16 rows in set (11.98 sec)

-- 范围都会使用索引

-- 其实针对FlightDate 如果关于月份的化，增加一个字段 Flight_dayofweek tinyint(4)GENERATED ALWAYS AS (dayofweek(FlightDate VIRTUAL,
Virtual or Generated columns 来生成列使用索引
```

总结，一定要去看一遍官方的手册更加的了解MySQL，今年的目标就是MySQL和Linux，加油送给自己

### MySQL Performance Optimization

#### 应用性能和用户习惯

    software is eating the world!!!

系统性能：

- Software programming(软件编程)
- Caching issues(缓存问题)
- CPU issues(CPU问题)
- Operating System deficits(操作系统赤字)
- Network fluctuations(网络波动)
- Memory issues(内存问题)
- Database design(数据库设计)

数据库一般的性能维持在200ms 如果在这个范围左右，就不要再考虑数据库的优化了

控制请求数量,限流，降级，缓存

#### 应用为什么慢

##### Poor Architecture Design and 

合理的程序，架构，涉及点，数据库的责任，缓存，分片，复制，可以依赖NoSql和ElasticSearch配合Hadoop来配合数据库操作

程序实现也需要最优。数据库的滥实现数据库有慢查询，不使用索引，多次查询，或者一次查询很多数据，或者全表查询，逻辑计算压力都在数据库

针对数据库，做全面的分析，多少数据量，什么样的分布，水平和竖直拆分，那些需要进行索引，以及联合查询，需要了解Mysql的执行计划，优化器和引擎

MySQL的 qps在32个线程的时候性能较好，能达到160000的qps

总归应用慢在多方面都有可能，软件，硬件，网络等等，所以需要去宏观考量，逐步排查，定位问题，那么你就会比别人强了，一定要有定位问题的能力，定位到问题就可以解决问题了，事情简单化！


