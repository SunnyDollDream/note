![[Pasted image 20250715133535.png]]

![[Pasted image 20250715133541.png]]

## SQL体系结构

1. 连接层:最上层是一些客户端和链接服务，主要完成一些类似于连接处理(如是否超过最大连接数)、授权认证(如校验密码)、及相关的安全方案。服务器也会为安全接入的每个客户端验证它所具有的操作权限。
    
2. 服务层:第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如 过程、函数等。
    
3. 引擎层:存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。(索引在这一层实现,默认引擎为InnoDB,早期默认为MyISAM)
    
4. 存储层:主要是将数据存储在文件系统之上，并完成与存储引擎的交互
    

## 存储引擎

> 存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式。存储引擎是基于表的，而不是基于库的，所以存储引擎也可被称为表类型。

要查看使用的什么引擎可以使用 `show creat table account` 查询建表语句来查看

引擎是在建表时指定的,在create table (...) engine=<引擎名>指定

要查看当前数据支持什么引擎,可以使用`show engines`来查询

#### InnoDB:

特点:

- 遵循ACID模型，支持事务(只有InnoDB支持事务)
    
- 行级锁，提高并发访问性能;
    
- 支持 外键 FOREIGN KEY约束，保证数据的完整性和正确性
    

逻辑存储结构:

数据库里的一行数据都是一个个的Row,存在page里,Row里有trx_id(最后一次操作事务的id),roll_pointer和每个字段的信息,InnoDB中操作最小单元是page,page又都存放于extent(区)中,一个extent的大小是固定的,为1M,一个page是16K,即一个extent可以包含64个page,extent存放于segment(段)中,segment又存放于tablespace(表空间)中

#### MyISAM

特点:

- 不支持事务，不支持外键
    
- 支持表锁，不支持行锁
    
- 访问速度快
    

#### Memory:

Memory引擎的表数据时存储在内存中的，由于受到硬件问题、或断电问题的影响，只能将这些表作为临时表或缓存使用

特点:

- 内存存放
    
- hash索引(默认)
    

![[Pasted image 20250715133601.png]]

## 索引

> 索引(index)是帮助MySQ高效获取数据的数据结构(有序)。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用(指向)数据，这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

![[Pasted image 20250715133607.png]]

不同的存储引擎有不同的索引结构:

![[Pasted image 20250715133614.png]]

![[Pasted image 20250715133620.png]]

#### 1.B+Tree索引:

(查询复杂度低于二叉树,因为在存储相同数据量时二叉树会高很多,虽然B+树必须查到叶子节点才能拿到数据,但是它到达叶子节点也更快)

不指定索引结构时默认为B+树结构

![[Pasted image 20250715133626.png]]

存储数据时,会先在一个节点里存,左小右大,存到超过上限时会在存入后将当前节点的中间值向上级节点推,余下四个节点在按照大小存在推上去的节点的子节点中,在查找时会将数据与当前节点的各个数比较,比如小于20进入最左边下级节点查找,大于20小于30进入左边第二个节点进行查找,以此类推.

B+树则是在B树的基础上,在向上级推节点的时候不仅向上推,还要把推的数据保留在叶子节点上(会保留在右侧部分),即B+树的所有元素在叶子节点都有,叶子结点会形成类似链表的结构,非叶子节点只起索引作用

![[Pasted image 20250715133642.png]]
![[Pasted image 20250715133648.png]]
#### 2.Hash索引:

哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中。如果两个(或多个)键值，映射到一个相同的槽位上，他们就产生了hash冲突(也称为hash碰撞)，可以通过链表来解决。

特点:

1. Hash索引只能用于对等比较(=，in)，不支持范围查询(between，>，<，…)
    
2. 无法利用索引完成排序操作
    
3. 查询效率高，通常只需要一次检索就可以了，效率通常要高于B+tree索引
    

#### 索引分类:

![[Pasted image 20250715133700.png]]
![[Pasted image 20250715133707.png]]
**聚集索引****选取规则**:

- 如果存在主键，主键索引就是聚集索引。
    
- 如果不存在主键，将使用第一个唯一(UNIQUE)索引作为聚集索引。
    
- 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引.
    

**联合索引****的数据结构:**

和单列索引类似,都是用存储的字段值做key,叶子节点放聚集索引,聚合索引只是把同一条数据中多列的值拼接为了一个key进行存储,叶子节点仍为聚集索引.

**回表查询:**

即当用非主键字段去查询其他字段时,会先从该字段的二级索引中得到对应行的主键值,在回到聚集索引中去得到对应行的数据.

#### 索引语法:

1. 创建索引:`CREATE [UNIQUE|FULLTEXT] INDEX <index_name> ON <table_name> (<index_col_name>...)`(一个索引可以关联多列,只关联一列时叫做单列索引,否则为联合索引或组合索引)
    
2. 查看索引:`SHOW INDEX FROM <table_name>`
    
3. 删除索引:`DROP INDEX <index_name> ON <table name>`
    

## SQL性能分析

#### SQL执行频率:

MySQL 客户端连接成功后，通过`show [session|global] status`命令可以提供服务器状态信息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次:`SHOW GLOBAL STATUS LIKE 'Com_______'`(七个下划线,模糊匹配)

#### 慢查询日志:

![[Pasted image 20250715133729.png]]

#### profile详情:

![[Pasted image 20250715133734.png]]

![[Pasted image 20250715133749.png]]

#### explain执行计划:

![[Pasted image 20250715133756.png]]

**EXPLAIN 执行计划各字段含义:**

1. ld:select查询的序列号，表示查询中执行select子句或者是操作表的顺序(id相同，执行顺序从上到下;id不同，值越大，越先执行)。
    
2. select_type:表示 SELECT的类型，常见的取值有 SIMPLE(简单表，即不使用表连接或者子查询)、PRIMARY(主查询，即外层的查询),UNION(UNION 中的第二个或者后面的查询语句)、SUBQUERY(SELECT/WHERE之后包含了子查询)等
    
3. type:表示连接类型，性能由好到差的连接类型为NULL(不查询任何表时)、system(只查询系统表时)、const(根据唯一索引或主键来查询表时)、eg_ref、ref(使用非唯一索引)、range、index(使用索引但仍对索引的全部数据扫描时)、all (全表扫描时)。
    
4. possible_key:显示可能应用在这张表上的索引，一个或多个.
    
5. key:实际使用的索引，如果为NULL，则没有使用索引。
    
6. key_len:表示索引中使用的字节数，该值为索引字段最大可能长度,并非实际使用长度，在不损失精确性的前提下，长度越短越好.
    
7. rows:MySQL认为必须要执行查询的行数，在innodb引擎的表中是一个估计值，可能并不总是准确的。
    
8. filtered:表示返回结果的行数占需读取行数的百分比，filtered的值越大越好。
    

#### 索引使用:

**最左前缀法则:**

如果索引了多列(联合索引)，要遵守最左前缀法则。最左前缀法则指的是査询从索引的最左列开始(如果是在wher之后作条件时只要存在即可,在SQL语句中并不一定必须放在左边,但是如果是order或group之后的则必须按照联合索引中的顺序写SQL,或者也可以在前边用where带了左边的索引,这样后边的排序和分组可以从where之后的索引开始)，并且不跳过索引中的列。如果跳跃某一列，索引将部分失效(后面的字段索引失效)，

**范围查询:**

联合索引中，出现范围查询(>，<)，范围查询右侧的列索引失效(但是使用≥或≤不会导致这个问题,所以尽可能使用≥和≤)

**索引列运算:**

不要在索引列上进行运算操作(即使用函数)，索引将失效

**字符串不加引号:**

字符串类型字段使用时，不加引号，索引将失效

**模糊查询:**

如果仅仅是尾部模糊匹配('软件%')，索引不会失效。如果是头部模糊匹配('%工程')，索引失效

**or连接的条件:**

用or分割开的条件，如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。

**数据分布影响:**

如果MySQL评估使用索引比全表更慢，则不使用索引。

**SQL提示:**

SQL提示，是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

- **use index** : `select * from tb_user use index(idx_user_pro) where profession='软件工程'`(如果指定的索引不正确SQL仍会自己去选择使用那个索引)
    
- **ignore index** : `select * from tb_user ignore index(idx_user_pro)where profession='软件工程'`
    
- **force index** : `select * from tb_user force index(idx_user_pro) where profession='软件工程'`(即使不正确也会使用指定的索引)
    

**覆盖索引:**

尽量使用覆盖索引(查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到),减少select *,因为当要查询的字段不在当前索引(聚合索引)中时,SQL就会进行回表,这可以在explain的extra中看出:

- usingindex condition :查找使用了索引，但是需要回表查询数据
    
- using where; using index:查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询数据
    

**前缀索引:**

当字段的内容为一串很长的字符串时,为其建立索引可能会浪费很多空间,此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

语法:`create index idx_<索引名> on table <表名>(<字段名>(前缀长度))`;

索引长度的选取可以根据索引的选择性来决定，而选择性是指不重复的索引值(基数)和数据表的记录总数的比值，索引选择性越高则查询效率越高,唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的.选择性可以这样计算得出:

> `select count(distinct email)/ count(*) from tb_user`
> 
> `select count(distinct substring(email,1,5))/ count(*)from tb_user ;`

**索引设计原则:**

1. 针对于数据量较大，且查询比较频繁的表建立索引。
    
2. 针对于常作为查询条件(where)、排序(orderby)、分组(group by)操作的字段建立索引。
    
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。
    
4. 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。
    
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。
    
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。
    
7. 如果索引列不能存储NULL值，请在创建表时使用NOTNUL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。
    

## SQL性能优化

#### 插入优化

1. 插入多条数据时建议使用一条SQL批量插入,防止反复连接
    
2. 手动提交事务,所有insert执行完再commit,而不是一条SQLcommit一次
    
3. 插入时尽量按照主键的顺序插入
    
4. 当一次性插入大批量数据时建议使用load指令,使用方法如下:
    

![[Pasted image 20250715133807.png]]

#### 主键优化

在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表,也正因如此插入数据时应按照主键顺序由小到大插入.

页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(如果一行数据很大，会行溢出)

**页分裂:**

在存储数据时,会按照主键顺序插入page中,当前页存满之后会申请新的页继续插入,两页之间由双向指针连接,但如果插入的主键为乱序,则会在顺序位置上尝试插入,如果有空间则插入,没空间则进行页分裂,即将想要插入的页中的后一半数据连带新数据一块申请一块新的页,放入这个页中,并更改原来页指针指向新的一页,新的一页指向原来的下一页

**页合并:**

当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记(flaged)为删除并且它的空间变得允许被其他记录声明使用。

当页中删除的记录达到 MERGE THRESHOLD(默认为页的50%,可以在建表或索引的时候指定)，InnoDB会开始寻找最靠近的页(前或后)看看是否可以将两个页合并以优化空间使用,这就是页合并.

1. 尽量降低主键长度
    
2. 使用自增主键(不要使用uid或身份证号之类的不连续数据)
    
3. 尽量不要修改主键
    

#### 排序优化

使用Order by排序时有两种情况

1.Using filesort:通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sortbuffer(存在大小,默认256K,超出会在磁盘中进行排序,应尽量避免,可以调大这个空间)中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫FileSort排序。

2.Using index:通过有序索引顺序扫描直接返回有序数据，这种情况即为using index，不需要额外排序，操作效率高。

想要使用索引排序,则排序顺序也要和索引的一致,创建索引的时候可以指定这个索引是升序还是降序.

1. 尽量使用覆盖索引,否则会filesort
    

#### 分页优化

因为SQL分页时会把(起始索引+页面大小)条数据排序后只返回页面大小的数据,所以起始索引越大分页越慢,要解决这个问题可以通过联子表的方式解决,如分页只查id主键(有索引),在内连接select*来查想要的数据,防止回表过多无效数据

#### 更新优化

update会根据where后的条件对对应数据上锁,如果是根据有索引的字段更改,则只会上行锁,如果没有索引或where后索引失效则会上表锁,应尽量避免

## 视图

创建视图:`CREATE [OR REPLACE] VIEW <视图名称(列名列表)> AS <SELECT语句>[WITH[CASCADED|LOCAL] CHECK OPTION]`

查看创建视图语句:`SHOW CREATE VIEW <视图名称>`

查看视图数据:`SELECT * FROM <视图名称>`(和表的查询方式一致,可以把视图当做表来查询)

修改:`ALTER VIEW <视图名称>[(列名列表)] AS SELECT语句[WITH[CASCADED|LOCAL]CHECK OPTION]`(也可以使用Or replace来替换)

删除:`DROP VIEW 「IF EXISTS] <视图名称> [,视图名称]...`

视图也可以和表一样进行增删改查,但是视图并不存储数据,所以对视图的修改都会反映到其所对应的表上.

要使视图可更改，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一项，则该视图不可更改

1. 聚合函数或窗口函数(SUM()、MIN()、MAX()、COUNT()等)
    
2. DISTINCT
    
3. GROUP BY
    
4. HAVING
    
5. UNION 或者 UNION ALL
    

#### 检查选项:

在创建视图的时候如果指定了where条件,那么当你向视图中插入了一条不满足这个条件的数据,在使用select查询的时候会查询不出来,为了防止这一现象,我们可以使用检查选项,当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插入，更新，删除，以使其符合视图的定义。MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，mysq!提供了两个选项:CASCADED和 LOCAL，默认值为CASCADED。

CASCADED:除了会检查当前视图是否能够插入当前数据,如果当前视图是根据另一张视图创建的,那么他还会去检查是否符合那张视图的条件,不论他有没有checkoption子句

LOCAL:也会和CASCAEDE一样去检查依赖的视图,但是如果依赖的视图没有checkoption子句,那么就不会进行检查

#### 作用:

1. 简单:视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件。
    
2. 安全:数据库可以授权，但不能授权到数据库特定行和特定的列,通过视图用户只能查询和修改他们所能见到的数据,
    
3. 数据独立:视图可帮助用户屏蔽真实表结构变化带来的影响。
    

## 存储过程

创建

```SQL
CREATE PROCEDURE
<存储过程名称>([参数列表])
BEGIN
-- SQL语句
END
```

调用

`CALL 名称([参数]);`

查看

`SHOW CREATE PROCEDURE <存储过程名称>;` -- 查询某个存储过程的定义

删除

`DROP PROCEDURE [IF EXISTS] <存储过程名称>;`

tips:在命令行中因为他会认为出现分号就代表sql语句结束,所以需要通过关键字 delimiter 指定SQL语句的结束符,如delimiter $$就会把SQL结束的标志替换为$$

#### 自定义变量

用户定义变量是用户根据需要自己定义的变量，用户变量不用提前声明(即不声明使用也不会报错,但是其值为null)，在用的时候直接用“@变量名”使用就可以。其作用域为当前会话。

**赋值**

SET @var_name = expr [, @var name = expr] ... ;(= 也可以替换为:=以和逻辑等于=区分开)

SELECT <字段名> INTO @var_name FROM <表名>

**使用**

SELECT @var_name

#### 局部变量

局部变量是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN . END块。

**声明**

DECLARE 变量名 变量类型 [DEFAULT ...];

**赋值**

SET 变量名:= 值

SELECT 字段名 INTO 变量名 FROM 表名

#### if语法:

```SQL
IF 条件1 THEN
...
ELSEIF 条件2 THEN -- 可选
...
ELSE -- 可选
...
END IF;
```

#### 参数类型

![[Pasted image 20250715133823.png]]

out类型的返回参数不需要Return,应该在形参表中对应位置放入用户变量来接收;而inout类型的参数也应该放入已经声明了值的用户变量而不是常量.

#### case

语法一

CASE <变量名>

WHEN <when_value1> THEN <语句>

[ WHEN <when_value2> THEN <语句>]

[ELSE <语句>]

END CASE;

语法二

CASE

WHEN <条件1> THEN <语句>

[WHEN <条件2> THEN <语句>]

[ELSE <语句>]

END CASE;

#### 循环

**while:(类似for)**

```SQL
--先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑
WHILE <条件> DO
SQL逻辑...
END WHILE;
```

**repeat:(类似do...while)**

```SQL
--先执行一次逻辑，然后判定逻辑是否满足，如果满足，则退出。如果不满足，则继续下一次循环
REPEAT
SQL逻辑...
UNTIL <条件>
END REPEAT;
```

**loop:(类似while)**

LOOP实现简单的循环，如果不在SQL逻辑中增加退出循环条件，可以用其来实现简单的死循环。LOOP可以配合以下两个语句使用:

LEAVE:配合循环使用，退出循环。(break)

ITERATE:必须用在循环中，作用是跳过当前循环剩下语句，直接进入下一次循环。(continue)

```SQL
[begin label:](类似于FLAG) LOOP
SQL逻辑...
END LOOP [end label];
LEAVE label;--退出指定标记的循环体
ITERATE label;-- 直接进入下一次循环
```

#### 游标

游标(CURSOR)是用来存储查询结果集的数据类型，在存储过程和函数中可以使用游标对结果集进行循环的处理。游标的使用包括游标的声明、OPEN、FETCH和 CLOSE，其语法分别如下

**声明游标 :**`DECLARE 游标名称 CURSOR FOR 查询语句`(游标的声明应该在一般变量之后)

**打开****游标** **:**`OPEN 游标名称`

**获取****游标****记录 :**`FETCH 游标名称 INTO 变量[,变量];`(这里的变量是提前声明好的对应类型的局部变量,应该与游标存储的字段数一致)

**关闭****游标** **:**`CLOSE 游标名称`

#### 条件处理程序

条件处理程序(Handler)可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。具体语法为:

`DECLARE <handler_action> HANDLER FOR <condition_valuel>,[<condition value>....] statement(可以额外执行SQL语句);`

handler_action:(满足条件时要执行的动作)

CONTINUE:继续执行当前程序

EXIT:终止执行当前程序

condition_value:(需要满足的条件)

SQLSTATE '<状态码>':程序报的状态码，如 02000

> 状态码的集合:(可以代替具体的状态码)
> 
> SOLWARNING: 所有以01开头的SOLSTATE代码的简写
> 
> NOT FOUND:所有以02开头的SOLSTATE代码的简写
> 
> SOLEXCEPTION:所有没有被SOLWARNING或 NOT FOUND捕获的SQLSTATE代码的简写

## 存储函数

存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的(因为形参类型默认就是IN,所以可以省略)。具体语法如下:

```SQL
CREATE FUNCTION 存储函数名称([ 参数列表 ])
RETURNS type(返回值类型) [characteristic ...(返回值特性)]
BEGIN
-- SQL语句
RETURN ...
END ;
```

> characteristic说明:
> 
> DETERMINISTIC:传入的参数一样返回值就也是一样的
> 
> NO SQL:当前存储函数中不包含SQL语句,
> 
> READS SQL DATA:只包含读取数据的语句，但不包含写入数据的语句,

## 触发器

触发器是与表有关的数据库对象，指在 insert/update/delete 之前或之后，触发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作(类似于拦截器)

使用别名 OLD 和 NEW 来引用触发器中发生变化的记录内容，这与其他的数据库是相似的(old和new即代表新插入的哪一行数据,使用方法类似实体类)。现在触发器还只支持行级触发，不支持语句级触发(即有几行数据发生变化就会执行几次,而不是执行几次SQL触发几次)。

![[Pasted image 20250715133837.png]]

**创建:**

```SQL
CREATE TRIGGER trigger name
BEFORE/AFTER INSERT/UPDATE/DELETE
ON tbl_name(对于那张表触发) FOR EACH ROW --行级触发器
BEGIN
trigger _stmt ;
END;
```

**查看:**`SHOW TRIGGERS`

**删除:**`DROP TRIGGER [schema_name.]trigger_name;--如果没有指定 schema name，默认为当前数据库`

## 事件

使用“事件”功能之前必须确保event_scheduler已开启

```SQL
-- 开启功能命令：
SET GLOBAL event_scheduler = 1;
SET GLOBAL event_scheduler = ON;
-- 关闭功能命令：
SET GLOBAL event_scheduler = 0;
SET GLOBAL event_scheduler = OFF;
```

但是通过命令开启当数据库重启后会自动关闭

持久化开启方式：将event_scheduler=1写到my.cnf配置文件中;如下图：

![[Pasted image 20250715133843.png]]

#### 常见命令

- 关闭指定事件： ALTER EVENT 事件名称 ON COMPLETION PRESERVE DISABLE;
    
- 开启指定事件：ALTER EVENT 事件名称 ON COMPLETION PRESERVE ENABLE;
    
- 查看当前事件：SHOW EVENTS ;
    

**创建事件:**

```SQL
CREATE EVENT [IFNOT EXISTS] event_name
    　　 ON SCHEDULE schedule(调度时间设置)
    　　 [ON COMPLETION [NOT] PRESERVE]
    　　 [ENABLE | DISABLE | DISABLE ON SLAVE]
    　　 [COMMENT 'comment']
    　　 DO sql_statement;
```

![[Pasted image 20250715133848.png]]

**schedule的写法:**

```SQL
AT timestamp [+ INTERVAL interval] ...
# 或者
EVERY interval
    [STARTS timestamp [+ INTERVAL interval] ...]
    [ENDS timestamp [+ INTERVAL interval] ...]
```

AT TIMESTAMP表示该事件只执行一次，TIMESTAMP表示一个具体的时间点，后面可以加上一个时间间隔，表示在这个时间间隔后事件发生。[+ INTERVAL INTERVAL]表示延迟触发时间,需要注意的是，TIMESTAMP是和具体字符串连用的，如果不是具体字符串而是调用函数（如CURRENT_TIMESTAMP取当前时间等），则不加TIMESTAMP直接写函数就可以;

```SQL
ON SCHEDULE AT TIMESTAMP '2020-11-20 00:00:00' 
ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 5 HOUR
```

EVERY表示循环执行该事件，其中`STARTS`子句用于指定开始时间；`ENDS`子句用于指定结束时间。

```SQL
ON SCHEDULE EVERY 1 HOUR 
STARTS CURRENT_TIMESTAMP+INTERVAL 1 DAY 
ENDS CURRENT_TIMESTAMP+INTERVAL 3 DAY
# 不需要写timestamp了
ON SCHEDULE EVERY 10 MINUTE STARTS '2020-11-20 12:00:00' 
```

**修改事件:**

```SQL
ALTER EVENT event_name
    　　 [ONSCHEDULE schedule]
    　　 [old_NAME TO new_NAME]
    　　 [ON COMPLETION [NOT] PRESERVE]
    　　 [COMMENT 'comment']
    　　 [ENABLE | DISABLE]
    　　 [DO sql_statement]
```

**删除事件:**

```SQL
DROP EVENT [IF EXISTS] event_name
```

## 锁

#### 全局锁

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都将被阻塞。

其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性

上锁:`flush tables with read lock ;`

解锁:`unlock tables ;`

#### 表级锁

表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、InnoDB、BDB等存储引擎中

对于表级锁，主要分为以下三类

**表锁:**

对于表锁，分为两类:表共享读锁(只能读不能写,上锁的客户端进行写入会直接报错,其他客户端会阻塞直到锁释放)和表独占写锁(上锁的客户端仍可以读写,但是其他客户端的读写会被阻塞)

加锁:`locktables 表名... read/write`

释放锁:`unlock tables`/客户端断开连接，

**元数据锁(meta data lock，MDL):**

元数据就是表结构,MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维护表元数据的数据一致性，在表上有事务没有提交的时候，不可以对元数据进行写入操作,避免DML和DDL语句冲突

在MYSQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享);当对表结构进行变更操作的时候，加MDL写锁(排他)。(可以兼容的锁的操作不会互相阻塞)

![[Pasted image 20250715133859.png]]

**意向锁:**

意向锁主要是用来解决加表锁时需要检查表内行锁情况效率低的问题的,在加行锁时会加上对应类型的意向锁,意向锁是表锁,所以在加表锁的时候就只要检查意向锁的兼容情况就可以判定是否可以加上表锁,而不用扫描每一行的锁情况了

1.意向共享锁(IS):由语句 select ... lock in share mode添加。与表锁共享锁(read)兼容，与表锁排它锁(write)互斥

2.意向排他锁(IX):由insert、update、delete、select... for update 添加。与表锁共享锁(read)及排它锁(write)都互斥。意向锁之间不会互斥。

#### 行级锁

InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的锁。对于行级锁，主要分为以下三类:

1. 行锁(Record Lock):锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC(Read Commit)、RR(Repeatable Read)隔离级别下都支持。(S,REC_NOT_GAP)
    

![[Pasted image 20250715133906.png]]

2. 间隙锁(GapLock):锁定索引记录间隙(不含该记录)，确保索引记录间隙不变，防止其他事务在这个间隙进行inser，产生幻读。在RR隔离级别下都支持。(间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。)(S,GAP)
    

![[Pasted image 20250715133911.png]]

3. 临键锁(Next-Key Lock):行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持。(S)
    

![[Pasted image 20250715133918.png]]

InnoDB实现了以下两种类型的行锁:

1.共享锁(S):允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁，(排斥X,类比读锁)

2.排他锁(X):允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。(都排斥,类比写锁)

![[Pasted image 20250715133923.png]]

默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key锁进行搜索和索引扫描，以防止幻读。

针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。给不存在的记录加锁时,优化为间隙锁

索引上的等值查询(普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock退化为间隙锁

索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁,

## InnoDB引擎

#### 逻辑存储结构

![[Pasted image 20250715133931.png]]
- 表空间(ibd文件)，一个mysql实例可以对应多个表空间，用于存储记录、索引等数据。(表空间可以使用ibd2sdi <文件名>来查看)
    
- 段，分为数据段(Leafnodesegment)、索引段(Non-leafnode segment)、回滚段(Rollbacksegment)，InnoDB是索引组织表，数据段就是B+树的叶子节点，索引段即为B+树的非叶子节点。段用来管理多个Extent(区)。
    
- 区，表空间的单元结构，每个区的大小为1M。默认情况下，InnoDB存储引擎页大小为16K，即一个区中一共有64个连续的页。
    
- 行，InnoDB 存储引擎数据是按行进行存放的。其中有两个特殊元素
    

Trx_id:上次执行的事务的id,每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。

Roll pointer:每次对某条引记录进行改动时，都会把旧的版本写入到undo日志中，然后这个隐藏列就相当于一个指针，可以通过它找到该记录修改前的信息。

#### 内存架构

Buffer Pool:缓冲池是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据(若缓冲池没有数据，则从磁盘加载并缓存)，然后再以一定频率刷新到磁盘，从而减少磁盘IO，加快处理速度。缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型:

- free page:空闲page，未被使用。
    
- clean page:被使用page，数据没有被修改过
    
- dirty page:脏页，被使用page，数据被修改过，其中数据与磁盘的数据产生了不一致
    

Change Buffer:更改缓冲区(针对于非唯一二级索引页)，在执行DML语句时，如果这些数据Page没有在BufferPool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区Change Buffer 中，在未来数据被读取时，再将数据合并恢复到BufferPool中，再将合并后的数据刷新到磁盘中。

这么做是因为二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新可能会影响索引树中不相邻的二级索引页，如果每一次都操作磁盘，会造成大量的磁盘IO。有了ChangeBuffer之后，我们可以在缓冲池中进行合并处理，减少磁盘IO。

Adaptive Hash Index:自适应hash索引，用于优化对Buffer Pool数据的查询。InnoDB存储引擎会监控对表上各索引页的查询，如果观察到hash索引可以提升速度，则建立hash索引，称之为自适应hash索引。

Log Buffer:日志缓冲区，用来保存要写入到磁盘中的log日志数据(redolog、undolog)，默认大小为 16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘IO。

相关参数:

innodb_log_buffer_size:缓冲区大小

innodb_flush_log_at trx_commit:日志刷新到磁盘时机(1:日志在每次事务提交时写入并刷新到磁盘.0:每秒将日志写入并刷新到磁盘一次。2:日志在每次事务提交后写入，并每秒刷新到磁盘一次。)

#### 磁盘结构

**System Tablespace:**系统表空间是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或通用表空间中创建的，它也可能包含表和索引数据。(在MYSQL5.x版本中还包含InnoDB数据字典、undolog等)

参数:innodb_data_file_path

**File-Per-Table Tablespaces:**每个表的文件表空间包含单个InnoDB表的数据和索引，并存储在文件系统上的单个数据文件中。

参数:innodb_file_per_table

**General Tablespaces:**通用表空间，需要通过CREATE TABLESPACE语法创建通用表空间，在创建表时，可以指定该表空间。

`CREATE TABLESPACE xxxx(空间名) ADD DATAFILE 'file_name(对应磁盘文件(不存在会新建))' ENGINE = engine_name;`

**Undo Tablespaces:**撤销表空间，MYSQL实例在初始化时会自动创建两个默认的undo表空间(初始大小

16M)，用于存储undolog日志。

**TemporaryTablespaces:**InnoDB使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据。

**Doublewrite Buffer Files:**双写缓冲区，innoDB引擎将数据页从Buffer Pool刷新到磁盘前，先将数据页写入双写缓冲区文件中，便于系统异常时恢复数据。

**RedoLog:**重做日志，用来实现事务的持久性。该日志文件由两部分组成:重做日志缓冲(redo logbuffer)以及重做日志文件(redolog),前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都会存到该日志中,用于在刷新脏页到磁盘时,发生错误时,进行数据恢复使用。

#### 后台线程

**1.Master Thread**

核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中,保持数据的一致性还包括脏页的刷新、合并插入缓存、undo页的回收。

**2.lO Thread**

在InnoDB存储引擎中大量使用了AIO来处理IO请求,这样可以极大地提高数据库的性能，而IO Thread主要负责这些IO请求的回调。

![[Pasted image 20250715133943.png]]

**3.Purge Thread**

主要用于回收事务已经提交了的undolog，在事务提交之后，undolog可能不用了，就用它来回收。

**4.Page Cleaner Thread**

协助 Master Thread 刷新脏页到磁盘的线程，它可以减轻 Master Thread 的工作压力，减少阻塞。

#### 事务原理

事务的ACID中,原子性(A),一致性(C)和持久性(D)是由redolog和undolog保证的,隔离性(I)是由锁和MVCC(多版本并发控制)保证的

**Undo log:**

回滚日志，用于记录数据被修改前的信息，作用包含两个:提供回滚 和 MVCC(多版本并发控制).undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undolog中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行rollback时，就可以从undolog中的逻辑记录读取到相应的内容并进行回滚。

Undo log销毁:undolog在事务执行时产生，事务提交时并不会立即删除undolog，因为这些日志可能还用于MVCC。

当insert的时候，产生的undolog日志只在回滚时需要，在事务提交后，可被立删除。

而update、delete的时候，产生的undolog日志不仅在回滚时需要，在快照读时也需要，不会立即被删除

Undo log存储:undolog采用段的方式进行管理和记录，放在回滚段(rollback segment)中，内部包含1024个undo log segment.

#### MVCC

全称Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本，使得读写操作没有冲突，快照读为MYSQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需要依赖于数据库记录中的三个隐式字段、undolog日志、readView。

**1.当前读:**

读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。对于我们日常的操作，如:select... lock in share mode(共享锁),select ... for update、update、insert、delete(排他锁)都是一种当前读。

**2.快照读:**

简单的select(不加锁)就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读

Read committed:每次select，都生成一个快照读。

Repeatable Read:开启事务后第一个select语句才是快照读的地方。

Serializable:快照读会退化为当前读。

**实现原理:**

1.三个隐藏字段

![[Pasted image 20250715133952.png]]

2.undo log版本链

不同事务或相同事务对同一条记录进行修改，会导致该记录的undolog生成一条记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录。

3.readview:

ReadView(读视图)是 快照读 SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务(未提交的)id。

ReadView中包含了四个核心字段:

![[Pasted image 20250715133959.png]]

trx_id指的是版本链中记录的事务id,不是当前正在执行的事务id

![[Pasted image 20250715134004.png]]

如果四个条件都不符合那么就会顺着版本连向上找知道有可访问的版本

不同的隔离级别，生成Readview的时机不同

READ COMMITTED:在事务中每一次执行快照读时生成Readview。

REPEATABLE READ:仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。

## MYSQL管理

#### 系统数据库

![[Pasted image 20250715134010.png]]

![[Pasted image 20250715134025.png]]

![[Pasted image 20250715134034.png]]

![[Pasted image 20250715134043.png]]