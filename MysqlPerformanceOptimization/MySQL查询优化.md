# MySQL性能优化（二）-MySQL查询优化

### #前言

​	上一节呢介绍了索引和树，也知道了MySQL为何选择B+树当作其索引的数据结构，索引就是为了要提高SQL语句的效率的，所以这一节呢，就是给大家分析下查询执行流程和执行计划详解，看看SQL是怎么执行的以及根据执行计划判断使用了什么索引。



### #查询执行的路径

![picture9](D:\Skill\Git\Repository\Notebook\MysqlPerformanceOptimization\images\picture9.png)

**1，mysql 客户端/服务端通信** 

​	Mysql客户端与服务端的通信方式是“半双工”； 半双工通信： 在任何一个时刻，要么是有服务器向客户端发送数据，要么是客户端向服务端发 送数据，这两个动作不能同时发生。所以我们无法也无需将一个消息切成小块进行传输。

​	特点和限制： 客户端一旦开始发送消息，另一端要接收完整个消息才能响应。客户端一旦开始接收数据没法停下来发送指令。 

```Text
科普时间：
	全双工：双向通信，发送同时也可以接收 
	半双工：双向通信，同时只能接收或者是发送，无法同时做操作 
	单工：只能单一方向传送 
```

​	对于一个MySQL连接，时刻都会有一个状态码来标记这个连接的状态。查看的命令是

```mysql
show FULL PROCESSLIST / show PROCESSLIST
```

![picture10](D:\Skill\Git\Repository\Notebook\MysqlPerformanceOptimization\images\picture10.png)

​	**Sleep：** 线程正在等待客户端发送数据

​	**Query：** 连接线程正在执行查询

​	**Locked：** 线程正在等待表锁的释放

​	**Sorting result：** 线程正在对结果进行排序

​	**Sending data：**向请求端返回数据

**可通过kill {id}的方式进行连接的杀掉**

![picture11](D:\Skill\Git\Repository\Notebook\MysqlPerformanceOptimization\images\picture11.png)



**2，查询缓存** 

​	**工作原理：** 缓存SELECT操作的结果集和SQL语句；新的SELECT语句，先去查询缓存，判断是否存在可用的记录集；

​	**判断标准：** 与缓存的SQL语句，**是否完全一样，区分大小写** (简单认为存储了一个key-value结构，key为sql，value为sql查询结果集)

​	**缓存配置参数：**

​		**query_cache_type** 

​			0 -– 不启用查询缓存，默认值；

​			1 -– 启用查询缓存，只要符合查询缓存的要求，客户端的查询语句和记录集 都可以缓存起来，供

​				其他客户端使用，加上 SQL_NO_CACHE将不缓存 

​			2 -– 启用查询缓存，只要查询语句中添加了参数：SQL_CACHE，且符合查询 缓存的要求，客户端

​				的查询语句和记录集，则可以缓存起来，供其他客户端使用 

​		**query_cache_size** 

​			允许设置query_cache_size的值最小为40K，默认1M，推荐设置 为：64M/128M；

​		**query_cache_limit** 

​			限制查询缓存区最大能缓存的查询记录集，默认设置为1M show status like 'Qcache%' 命令可查看

​			缓存情况

​	**不会缓存的情况**

​		1，当查询语句中的结果有变化的数据时候，不会被缓存。比如当前时间的函数（NOW()），当前日期

​			（CURRENT_DATE()）等，这些都不会被缓存。

​		2，当查询的结果大于query_cache_limit设置的值时，结果不会缓存。

​		3，查询的表示系统表。

​	**MySQL为什么默认关闭了缓存**

​		1，在查询之前必须先检查是否命中缓存,浪费计算资源。

​		2，针对表进行写入或更新数据时，将对应表的所有缓存都设置失效。

​		3，如果这个查询可以被缓存，那么执行完成后，MySQL发现查询缓存中没有这 个查询，则会将结果存

​			入查询缓存，这会带来额外的系统消耗

​	**适用场景**

​		以读为主的业务，数据生成之后就不常改变的业务 比如门户类、新闻类、报表类、论坛类等。

**3，查询优化处理** 

​	**三个阶段：**

​		**解析SQL：**通过lex词法分析,yacc语法分析将sql语句解析成解析树 

​		**预处理阶段：** 根据mysql的语法的规则进一步检查解析树的合法性，如：检查数据的表和列是否存在，

​					解析名字和别名的设置。还会进行权限的验证 

​		**查询优化器：** 优化器的主要作用就是找到最优的执行计划

​	**怎么找到的最优执行计划**

​		Mysql的查询优化器是基于成本计算的原则。他会尝试各种执行计划。 数据抽样的方式进行试验（随机

​		的读取一个4K的数据块进行分析）​	

​	**执行计划**

​		![picture12](D:\Skill\Git\Repository\Notebook\MysqlPerformanceOptimization\images\picture12.png)

​		**1，Id**

```Text
select查询的序列号，标识执行的顺序。
如果id相同，执行顺序由上至下。
如果id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行。
```

​		**2，select_type**

```Text
查询的类型，主要是用于区分普通查询、联合查询、子查询等
SIMPLE：简单的select查询，查询中不包含子查询或者union 
PRIMARY：查询中包含子部分，最外层查询则被标记为primary 
SUBQUERY/MATERIALIZED：SUBQUERY表示在select 或 where列表中包含了子查询 
MATERIALIZED：表示where 后面in条件的子查询 
UNION：若第二个select出现在union之后，则被标记为union； 
UNION RESULT：从union表获取结果的select
```

​		**3，table**

```Text
查询涉及到的表
```

​		**4，type**

```Text
访问类型，SQL查询优化中一个很重要的指标，结果值从好到坏依次是：system > const > eq_ref > ref > range > index > ALL
system：表只有一行记录（等于系统表）。const类型的特例，基本不会出现，可以忽略不计 
const：表示通过索引一次就找到了，const用于比较primary key或者unique索引 
eq_ref：唯一索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键 唯一索引扫描 
ref：非唯一性索引扫描，返回匹配某个单独值的所有行，本质是也是一种索引访问 
range：只检索给定范围的行，使用一个索引来选择行 
index：Full Index Scan，索引全表扫描，把索引从头到尾扫一遍 
ALL：Full Table Scan，遍历全表以找到匹配的行
```

​		**5，possible_keys**

```Text

```

​		**6，key**

```Text

```

​		**7，key_len**

```Text

```

​		**8，ref**

```Text

```

​		**9，rows**

```Text

```

​		**10，Extra**

```Text

```





**4，查询执行引擎** 

​	查询执行引擎：通过API去调存储引擎，执行对应的执行计划。

**5，返回客户端**

​	结果返回给客户端。