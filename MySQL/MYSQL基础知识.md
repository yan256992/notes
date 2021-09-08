## 🔎 MySQL笔记

### 检索数据

- 检索不同的行

  ~~~mysql
  select distinct vend_id from products
  ~~~

- 限制结果，即限制输出的行数

  ```Mysql
  select prod_name from products LIMIT 4,5
  //表示的是从第四行开始 检索出五行数据，第一个为开始的位置，第二个为要检索的行数
  ```

  检索第一行是LIMIT 0 ,1。而LIMIT 1,1检索出来的是第二行而不是第一行

### 排序检索数据

如果需要对检索出的数据进行排序，可以使用`ORDER BY`，ORDER BY取一个或多个列的名字，据此对输出进行排序。

```Mysql
select prod_name from products ORDER BY prod_name
```

这里的列并不一定就是检索出来的列，也可以是没有检索的列

如果需要对多个列进行排序可以在后面加上对应的列就行，例如，需要先对检索出的商品按价格排序，价格相同的按照名称排序，就可以使用如下语句：

```mysql
select prod_id，prod_price，prod_name from products ORDER BY prod_price，prod_name；
```

默认的排序是升序，降序`DESC`

### 过滤数据

> 数据库表一般包含大量的数据，很少检索表中的所有行，通常只会根据特定的操作或报告的需要提取表数据中的子集，只检索所需数据需要指定搜索条件，搜索条件也称为过滤条件

在SELECT语句中，数据根据where字句中指定的搜索条件进行过滤。如果同时使用`ORDER BY`和`WHERE`，那么应该让WHERE在前。否则会产生错误

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906194823664.png" alt="image-20210906194823664" style="zoom: 67%;" />

#### 不匹配检查

如果想要检索不是由供应商1003制造的所有产品，则应使用如下语句

```mysql
select vend_id,prod_name from products where vend_id <> 1003
```

检查NULL值 

```mysql
select prod_name from products where prod_price IS NULL
```

### 数据过滤

> 前面的过滤数据采用的都是单一的条件，为了进行更强的过滤控制，MYSQL允许给出多个WHERE字句，这些字句可以采用AND或OR的方式使用

1. **AND操作符**

```mysql
select prod_id，prod_price，prod_name from products where vend_id =1003 AND prod_price<=10
```

2. **OR操作符**

```mysql
select prod_name，prod_price from products where vend_id=1002 OR vend_id=1003
```

如果同时出现了AND和OR会优先处理AND，所以如果想要先处理OR的话可以在OR字句上加（），括号的优先级更高

3. **IN操作符**

括号在WHERE字句中还有一种用法，即用来指定条件范围，范围中的每个条件都可以进行匹配

```mysql
select prod_name，prod_price from products where vend_id IN (1002,1003) ORDER BY prod_name
```

4. **NOT操作符**

WHERE字句中的NOT操作符有且只有一个功能。那就是否定之后跟的任何条件

```mysql
select prod_name，prod_price from products where vend_id NOT IN (1002,1003) ORDER BY prod_name
```

### 通配符过滤

**通配符**：用来匹配值得一部分的特殊字符

**搜索模式**：由字面值、通配符或两者组合构成的搜索条件

通配符本身实际是SQL的WHERE字句中有特殊含义的字符

在搜索字句中使用通配符必须使用`LIKE`操作符

* **%通配符**

  %表示任何字符出现任意次数,包括0次，例如，如下语句表示的是找到所有以jet开头的产品

  ```mysql
  select prod_id,prod_name from products where prod_name LIKE 'jet%'
  ```

* 下划线(_)通配符

  表示匹配一个字符

### 创建计算字段

存储在数据库表中的数据一般不是应用程序所需要的格式，如果想要得到自己想要的格式，可以采用计算字段，计算字段并不实际存在于数据库的表中。计算字段是运行时在SELECT语句内创建的

#### 拼接字段

在MYSQL的SELECT语句中可以使用Concat（）函数来拼接两个列

```mysql
select Concat(vend_name,'(',vend_country,')') from vendors ORDER BY vend_name
```

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906203311426.png" alt="image-20210906203311426" style="zoom:67%;" />

同时可以使用trim（字段名）来消除字段两端的空格，如果只想删除右边的就使用RTrim（），左边的就是LTrim（）。

SQL支持别名(Alias)。别名用AS关键字赋予

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906203640882.png" alt="image-20210906203640882" style="zoom:50%;" />

#### 执行算术计算

计算字段另一个用途就是对检索出的数据进行算术计算

```mysql
select prod_id，quantity，item_price,quantity*item_price AS expand_price  from orderitems where order_num=2005;
```

### 使用数据处理函数

#### 函数

>  SQL支持利用函数来处理数据，函数一般是在数据上执行的，它给数据的转换和处理提供了方便 

##### 文本处理函数

**Upper（）**

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906210348127.png" alt="image-20210906210348127" style="zoom:50%;" />

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906210617931.png" alt="image-20210906210617931" style="zoom:50%;" />

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906210633215.png" alt="image-20210906210633215" style="zoom:50%;" />

##### 日期和时间处理函数

日期和时间采用相应的数据类型和特殊的格式存储，以便能快速和有效地排序或过滤，并且节省物理存储空间

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906211057482.png" alt="image-20210906211057482" style="zoom:50%;" />

如果数据库表中的日期格式是yyyy-MM-dd HH:mm:SS ，这时候怎样去匹配数据呢，如果只知道具体的年月日，但是不知道时分秒，可以使用Date函数,这时候就会提取出日期的年月日去匹配。

```mysql
select cust-id,order_num from orders where Date(order_date) = '2005-09-01'
```

如果想要匹配某一个月中的数据

```mysql
select cust_id,order_num from orders WHERE YEAR(order_date) = 2005 AND Month(order_date) = 9;
```

##### 数值处理函数

数值处理函数一般仅处理数值数据

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906213803896.png" alt="image-20210906213803896" style="zoom:50%;" />

### 汇总数据

#### 聚集函数

> 我们经常需要汇总数据而不用把他们实际检索出来

聚集函数是指运行在行组上，计算和返回单个值的函数

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210906214300238.png" alt="image-20210906214300238" style="zoom:50%;" />

1. **AVG（）函数**

   会忽略值为NULL的行

   该函数主要用来计算列的平均值

   ```mysql
   select AVG(prod_price) AS avg_price from products
   ```

   同时还可以用来计算特定列的平均值

   ```mysql
   select AVG(prod_price) as avg_price from products where vend_id = 1003
   ```

2. **COUNT（）函数**

   COUNT（*）可以对行的数目进行计数，不管表列中包含的是否是NULL值

   COUNT（column）对特定列中具有的值进行计数，不包括NULL值

3. **MAX（）函数**

   MAX（）返回指定列中的最大值

4. **MIN（）函数**用来返回指定列中的最小值

5. **SUM（）函数**

   返回指定列值得和 忽略NULL值

### 数据分组

> 分组允许我们把数据分为多个逻辑组，以便能对每个组进行聚集计算

#### 创建分组

分组是在GROUP BY字句中建立的,GROUP BY 子句知识Mysql分组数据，然后对每个组而不是整个结果进行聚集

```mysql
select vend_id,COUNT(*) AS num_prods from products GROUP BY vend_id
```

GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前

#### 过滤分组

> 除了能用GROUP BY分组数据之外，MYSQL还允许过滤分组，规定包括哪些分组，排除哪些分组。HAVING类似于WHERE，只不过HAVING过滤的是分组 但是WHERE过滤的是行

```mysql
select cust_id,COUNT(*) AS orders from orders GROUP BY cust_id HAVING COUNT(*)>=2
```

**HAVING和WHERE的差别**

where在数据分组之前进行过滤，HAVING在数据分组之后进行过滤

#### 分组和排序

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210907184501998.png" alt="image-20210907184501998" style="zoom:50%;" />

#### SELECT子句顺序

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210907185150473.png" alt="image-20210907185150473" style="zoom:50%;" />

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210907185204885.png" alt="image-20210907185204885" style="zoom:50%;" />

### 使用子查询

> 子查询即嵌套在其他查询中的查询

```mysql
select cust_id from orders where order_num IN (select order_num from orderitems WHERE prod_id = 'TNT2')
```

### 联结表

#### 创建联结

```mysql
select vend_name,prod_name,prod_price from vendors,products where vendors.vend_id = products.vend_id ORDER BY vend_name,prod_name
```

在引用的列可能出现二义性时，必须使用完全限定列名（用一个点分割的表名和列名）

应该保证所有联结都有WHERE子句。否则会查询出很多无用的数据

#### 内部联结

```mysql
select vend_name,prod_name,prod_price from vendors INNER JOIN products ON vendors.vend_id = products.vend_id
```

使用`INNER JOIN`的时候，联结条件使用`ON`而不是WHERE

### 创建高级联结

#### 使用表别名

```mysql
select cust_name,cust_contat from customers AS c,orders AS o,orderitems AS oi where c.cust_id = o.cust_id AND oi.order_num = o.order_num
```

#### 自联结

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210907194255791.png" alt="image-20210907194255791" style="zoom:50%;" />

```mysql
select prod_id, prod_name from products WHERE vend_id = (select vend_id from products WHERE prod_id  = 'DTNTR')
```

使用联结的相同查询

```mysql
select p1.prod_id,p1.prod_name from products AS = p1,products AS p2 WHERE p1.vend_id = p2.vend_id AND p2.prod_id  = 'DTNTR'
```

> 自联结通常作为外部语句来替代从相同表中检索数据时使用的子查询语句。虽然最终的结果是一样的，但是使用联结远比处理子查询来的更快

#### 外部联结

LEFT OUTER JOIN 是将语句左边的全部行查询出来，RIGHT OUTER JOIN则是将语句右边的全部行查询

### 组合查询

> 多数SQL查询都只包含一个或多个表中返回的数据的单条SELECT语句，MYSQL也允许执行多个查询（多个SELECT语句），并将结果作为单个查询结果集返回。这些组合查询通常称为并（union）或复合查询

#### 使用UNION

给出每条查询语句并在每条语句之间放上关键字UNION

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210907195919436.png" alt="image-20210907195919436" style="zoom:50%;" />

单条语句

~~~mysql
select vend_id,prod_id,prod_price from products WHERE prod_price<=5
~~~

```mysql
select vend_id,prod_id,prod_price from products WHERE vend_id IN (1001,1002)
```

组合语句

```
select vend_id,prod_id,prod_price from products WHERE prod_price<=5
UNION
select vend_id,prod_id,prod_price from products WHERE vend_id IN (1001,1002)
```

:warning: 注意

- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔
- UNION的每个查询必须包含相同的列，表达式或聚集函数

#### 包含或取消重复的行

在使用UNION的时候，会自动去除重复的行，如果想要所有的匹配行可以使用UNION ALL

#### 对组合查询结果排序

使用UNION的时候只能使用一条ORDER BY字句，它必须出现在最后一条SELECT语句之后，不允许使用多条ORDER BY子句

### 插入数据

插入数据主要包含以下几个部分：

1. 插入完整的行
2. 插入行的一部分
3. 插入多行
4. 插入某些查询的结果

#### 插入完整的行

即插入一个完整的记录。只需要指明表名以及插入的值

```mysql
INSERT INTO Customers VALUES (NULL,'zs','100 main street','USA');
```

虽然这种语法简单，但是并不安全，应该尽量避免使用，上面的SQL语句高度依赖于表中列的定义顺序，并且依赖于其次序获得到的信息

更安全的方法如下

```mysql
INSERT INTO  Customers ('state','name','Address','state') VALUES (NULL,'zs','100 main street','USA');
```

#### 插入多行数据

将多个插入数据语句之间用分号隔开即可

#### 插入检索出的数据

INSERT语句还要一种形式就是将SELECT出的语句插入到表中，这就是所谓的INSERT SELECT

### 更新和删除数据

#### 更新数据

> 更新表中的数据可以使用update语句，可以采用两种方式update 
>
> 1. 更新表中特定的行
> 2. 更新表中的所有行

update语句由三部分组成：

- 要更新的表
- 列名和它们的新值
- 确定要更新行的过滤条件

```mysql
UPDATE customers SET cust_name = 'zs',cust_eamil = '268@qq.com' WHERE cust_id = 12
```

#### 删除数据

删除数据使用`DELETE`语句，主要分为两种方式；删除特定的行；删除所有行

```mysql
DELETE FROM customers where cust_id  = 12;
```

### 创建和操作表

### 创建表

#### 创建表基础

使用CREATE TABLE创建表，需要给出以下信息：

- 新表的名字，在上述关键字之后给出
- 表列的名字和定义，用逗号分割

```mysql
CREATE TABLE customers
(
cust_id    int   NOT NULL AUTO_INCREMENT,
cust_name  char(50) NOT NULL,
cust_city  char(50) NULL,
PRIMARY KEY (cust_id)
) ENGINE = InnoDB
```

如果需要指定默认值的话就在 NULL 或者NOT NULL的后面加上`DEFAULT xxx`

#### 更新表

为了更新表的定义，可以使用ALTER TABLE语句

- 添加一个列

  ```mysql
  ALTER TABLE venders ADD vend_phone CHAR(20);
  ```

- 删除列

  ```mysql
  ALTER TABLE venders DROP COLUMN vend_phone
  ```

- 定义外键

  ```mysql
  ALTER TABLE orderitems ADD CONSTRANIT fk_orderitems_orders 
  FOREIGN KEY (order_num) REFERENCES orders (order_num)
  ```

  

#### 删除表

`DROP TABLE xxx`

### 使用视图

#### 视图

> 视图是虚拟的表，与包含数据的表不一样，视图只包含使用时动态检索的数据的查询
>
> 作为视图，它不包含表中应有的任何列或数据，它包含的是一个SQL查询

#### 为什么使用视图

视图的常见应用：

- 重用SQL语句
- 简化复杂的SQL操作，在编写查询之后可以方便的重用它而不必知道它的基本细节
- 使用表的组成部分而不是整个表
- 保护数据，可以给用户授权表的特定部分的访问权限而不是整个表的访问权限
- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据

#### 视图的规则与限制

1. 视图必须唯一命名
2. 对于可以创建的视图数目没有限制
3. 为了创建视图，必须具有足够的访问权限
4. 视图可以嵌套，即可以利用从其他视图中检索出的数据的查询来构造一个视图
5. ORDER BY可以用在视图中
6. 视图不能索引，也不能有关联的触发器或默认值
7. 视图可以和表一起使用，例如，编写一条联结语句和视图的SELECT语句
8. 视图可以和表一起使用

#### 使用视图

**视图的创建**

使用CREATE VIEW语句来创建

用DROP VIEW viewname即可进行视图的删除

##### 利用视图简化复杂的联结

```mysql
CREATE VIEW productcustomers AS SELECT cust_name，cust_contact，pro_id from customers，orders，orderitems 
WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num
```

##### 使用视图过滤不需要的数据

```mysql
CREATE VIEW customeremailList AS SELECT cust_id,cust_name,cust_eamil FROM custmoers WHERE cust_eamil IS NOT NULL
```

### 使用存储过程

#### 存储过程

> 存储过程简单来说，就是为以后的使用而保存的一条或多条MYSQL语句的集合。可将其视为批文件，虽然他们的作用不仅限于批处理

#### 为什么要使用存储过程

- 通过把处理封装在容易使用的单元中，简化复杂的操作
- 由于不要求反复建立一系列处理步骤，这保证了数据的完整性。如果所有开发人员和应用程序都使用同一存储过程，则使用的代码都是相同的
- 简化对变动的管理。如果表名，列名或业务逻辑有变化，只需要更改存储过程的代码
- 提高性能，因为使用存储过程比使用单独的SQL语句要快

换句话说，使用存储过程有三个主要的好处，即简单，安全，高性能

### 执行存储过程

MYSQL称存储过程的执行调用，因此执行存储过程的语句为CALL。CALL接受存储过程的名字以及需要传递给它的任意参数

```mysql
CALL productpricing(@pricelow,
					@pricehigh,
                    @priceaverige);
```

存储过程可以显示结果，也可以不显示结果

#### 创建存储过程

eg：返回产品平均价格的存储过程

```mysql
CREATE PROCEDURE productpricing()
BEGIN
	SELECT Avg(prod_price) AS priceaverage
	FROM products;
END;
```

如果有参数的话将参数写在（）中

#### 删除存储过程

DROP PROCEDURE productpricing;

这里没有加（）

#### 使用参数

一般，存储过程并不显示结果，而是把结果返回给你指定的变量

```mysql
CREATE PROCEDURE productpricing(
OUT pl DECIMAL(8,2),
OUT ph DECIMAL(8,2),
OUT pa DECIMAL(8,2)
)
BEGIN
	SELECT Min(prod_price)
	INTO pl
	FROM products;
	SELECT Max(Prod_price)
	INTO ph
	FORM products；
	SELECT Avg(prod_price)
	INTO pa
	FROM products;
END;
```

在调用时，这条语句并不显示任何数据。它返回以后可以显示的变量

显示产品平均价格

```mysql
select @priceaverage
```

**有输入类型**

```mysql
CREATE PROCEDURE ordertotal(
IN onumber INT，
OUT ototal DECIMAL(8,2)
)
BEGIN
	SELECT Sum(item_price*quantity)
	FROM orderitems
	WHERE order_num = onumber
	INTO ototal;
END;
```

调用上述存储过程`CALL ordertotal(2005,@total)`

### 使用触发器

> MYSQL语句在需要的时候被执行，存储过程也是如此。如果想要某条语句在事件发生的时候执行，就可以使用触发器

例如：

- 每当增加一个顾客的到数据表中的时候都检查其手机号码格式是否正确
- 每当订购一个产品的时候，都从数据数量中减去订购的数量
- 无论何时删除一行，都在某个存档表中保留一个副本

以上都涉及到触发器的使用

触发器是MYSQL响应以下任意语句而自动执行的一条MYSQL语句（或位于BEGIN和END语句之间的一组语句）

`DELETE INSERT UPDATE`这些语句支持触发器

#### 创建触发器

创建触发器需要给出的信息

1. 唯一的触发器名
2. 触发器关联的表
3. 触发器应该响应的活动
4. 触发器何时执行

```mysql
CREATE TRIGGER newproduct AFTER INSERT ON products FOR EACH ROW SELECT 'Product added'
```

每个表最多支持6个触发器（每条INSERT，UPDATE，DELETE之前和之后）

#### 删除触发器

DROP TRIGGER newproduct；

#### 使用触发器

##### INSERT触发器

- 在INSERT触发器代码内，可以引用一个名为NEW的虚拟表。访问被插入的行
- 在BEFORE INSERT 触发器中，NEW中的值也可以被更新（允许更改被插入的值）
- 对于AUTO_INCREMENT列，NEW在INSERT执行之前包含0，在INSERT执行之后包含新的自动生成值

```mysql
CREATE TRIGGER neworder AFTER INSERST ON orders FOR EACH ROW SELECT NEW.order_num
```

![image-20210908210231244](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210908210231244.png)

### 管理事务处理

#### 使用ROLLBACK

```mysql
select * from ordertotals;
START TRANSACTION;
DELETE FROM ordertotals;
SELECT * FROM ordertotals;
ROLLBACK;
SELECT * FROM ordertotals;
```

<img src="https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210908210710353.png" alt="image-20210908210710353" style="zoom:50%;" />

#### 使用COMMIT

一般的MYSQL语句都是直接针对数据表执行和编写的。这就是所谓的隐含提交，即提交是自动进行的，但是，在事务处理块中提交不会隐含的进行。为进行明确的提交，使用COMMIT语句

```mysql
START TRANSACTION;
DELETE FROM orderitems WHERE order_num = 20010;
DELETE FROM orders WHERE order_num = 20021;
COMMIT;
```

