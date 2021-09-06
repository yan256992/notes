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

