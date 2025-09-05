## 日志

#### 错误日志

错误日志是 MySQL 中最重要的日志之一，它记录了当 mysqld 启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息,当数据库出现任何故障导致无法正常使用时，建议首先查看此日志。

该日志是默认开启的，默认存放目录 /var/log/，默认的日志文件名为 mysqld.log 。查看日志位置:`show variables like '%log_error%'`

#### 二进制日志

二进制日志(BINLOG)记录了所有的 DDL(数据定义语言)语句和 DML(数据操纵语言)语句，但不包括数据查询(SELECT、SHOW)语句。

作用:

1. 灾难时的数据恢复;
    
2. MySQL的主从复制。在MySQL8版本中，默认二进制日志是开启着的，涉及到的参数如下: `show variables like '%log_bin%'`
    

**日志格式:**

MYSQL服务器中提供了多种格式来记录二进制日志，具体格式及特点如下:

![[Pasted image 20250715134055.png]]

`show variables like '%binlog_format%';`可以查看当前数据库采用的日志格式

**查看日志:**

由于日志是以二进制方式存储的，不能直接读取，需要通过二进制日志查询工具 mysqlbinlog 来查看，具体语法

`mysqlbinlog [参数选项] logfilename`(cmd在日志文件夹下运行)

参数选项:

- -d 指定数据库名称，只列出指定的数据库相关操作。
    
- -o 忽略掉日志中的前n行命令
    
- -v 将行事件(数据变更)重构为SQL语句
    
- -w 将行事件(数据变更)重构为SQL语句，并输出注释信息
    

如果数据库是row模式则要加上 -v 来显示SQL语句和数据,statement则不需要

**删除日志:**

![[Pasted image 20250715134100.png]]

也可以在mysql的配置文件中配置二进制日志的过期时间、设置了之后，二进制日志过期会自动删除

`show variables like '%binlog_expire_logs_seconds%';`(默认30天)

#### 查询日志

查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的SQL语句。默认情况下，查询日志是未开启的,可以使用`show variables like '%general%'`来查看开启状态和日志位置,如果需要开启查询日志，可以设置my.cnf配置文件中增添general_log=1即可,还可以设置general_log_file来更改日志名字

#### 慢查询日志

慢查询日志记录了所有执行时间超过参数 long_query_time设置值并且扫描记录数不小于 min examined_row_limit的所有的SQL语句的日志，默认未开启。long_query_time 默认为 10 秒，最小为0，精度可以到微秒。

在配置文件中增加slow_query_log和long_query_time即可修改相应内容

默认情况下，不会记录管理语句，也不会记录不使用索引进行查找的查询。可以更改配置文件中log_slow_admin statements和log_queries_not_using_indexes更改此行为.

```Shell
#记录执行较慢的管理语句 log_slow_admin statements =1
#记录执行较慢的未使用索引的语句 log_queries_not_using_indexes=1
```

## 主从复制

主从复制是指将主数据库(Master)的DDL和 DML 操作通过二进制日志传到从库(Slave)服务器中，然后在从库上对这些日志重新执行(也叫重做)，从而使得从库和主库的数据保持同步。MySQL支持一台主库同时向多台从库进行复制，从库同时也可以作为其他从服务器的主库，实现链状复制。

MySQL复制的优点主要包含以下三个方面:

- 主库出现问题，可以快速切换到从库提供服务。
    
- 实现读写分离，降低主库的访问压力。
    
- 可以在从库中执行备份，以避免备份期间影响主库服务。
    

#### 原理

![[Pasted image 20250715134109.png]]

1. Master 主库在事务提交时，会把数据变更记录在二进制日志文件 Binlog 中。
    
2. 从库读取主库的二进制日志文件 Binlog ，写入到从库的中继日志 Relay Log
    
3. slave重做中继日志中的事件，将改变反映它自己的数据。
    

#### 搭建

**主库配置:**

1.修改配置文件 /etc/my.cnf(windows系统叫做my.ini)

```Shell
#mysql服务ID，，保证整个集群环境中唯一，取值范围:1-2^32 -1,默认为1
server-id=1
#是否只读,1 代表只读,0代表读写
read-only=0
#忽略的数据,指不需要同步的数据库
binlog-ignore-db=mysql
#指定同步的数据库
binlog-do-db=db01
```

2.重启MySQL服务器

`systemctl restart mysqld`

3.登录mysql，创建远程连接的账号，并授予主从复制权限

```Shell
#创建itcast用户，并设置密码,该用户可在任意主机连接该MYSQL服务
CREATE USER 'itcast'@'%'(表示可以在任意主机连接) IDENTIFIED WITH mysql_native_password BY 'Root@123456'(密码)
#为'itcast'@'%'用户分配主从复制权限
GRANT REPLICATION SLAVE ON . TO 'itcast'@'%';
```

4.通过指令，查看二进制日志坐标

`show master status;`

查询结果字段含义说明:

- file:从哪个日志文件开始推送日志文件
    
- position:从哪个位置开始推送日志
    
- binlog_ignore_db:指定不需要同步的数据库
    

**从库配置:**

1.修改配置文件 /etc/my.cnf

```Shell
#mysql服务ID，保证整个集群环境中唯一，取值范围:1-2^32-1，和主库不一样即可
server-id=2
#是否只读,1 代表只读,0 代表读写
read-only=1
```

P.S.:这里的read-only是只针对普通用户的,如果想让root用户也无法写入则要添加super-read-only=1

2.重新启动MySQL服务

`systemctl restart mysqld`

3.登录mysql，设置主库配置

`CHANGE REPLCATION SOURCE TO SOURCE HOST='X.X', SOURCE USER='%O, SOURCE PASSWORD='OK, SOURCE LOG FILE='X, SOURCE LOG POS-xK;`

![[Pasted image 20250715134118.png]]

4.开启同步操作

`start replica ;`

5.查看主从同步状态

`show replica status\G ;`

## 分库分表

分库分表的中心思想都是将数据分散存储，使得单一数据库/表的数据量变小来缓解单一数据库的性能问题，从而达到提升数据库性能的目的

#### 拆分方法

可以分为垂直拆分和水平拆分(维度区别),这两种方式又都有分库和分表()两种方式,分库是指把一个数据库中的数据存放到多个数据库中
![[Pasted image 20250715134125.png]]

![[Pasted image 20250715134130.png]]

#### 实现技术

- shardingJDBC:基于AOP原理，在应用程序中对本地执行的SQL进行拦截，解析、改写、路由处理。需要自行编码配置实现，只支持java语言，性能较高
    
- MyCat:数据库分库分表中间件，不用调整代码即可实现分库分表，支持多种语言，性能不及前者。
    

#### MyCat(感觉似乎不是开发的工作?)

Mycat是开源的、活跃的、基于lava语言编写的MYSQL数据库中间件。可以像使用mysql一样来使用mycat，对于开发人员来说根本感觉不到mycat的存在。

可以理解为mycat是一个专门存储数据库路径的数据库(角色类似分布式中的网关),他有一个数据库,里边有很多逻辑表,逻辑表里的数据节点(可以理解为一条数据)对应一个数据库

**使用:**

1.先在各个服务器的数据库中创建好对应的表(不需要创建字段)

2.配置mycat的配置文件

![[Pasted image 20250715134142.png]]

其中逻辑库名可以自己起,逻辑表是你要进行分表的表,数据节点是你所连接的其他数据库以及存放的表,节点主机就是这些数据库所在的数据库,可以在`<dataHost>`里配置,java项目需要把dbdriver改为JDBC

![[Pasted image 20250715134157.png]]

修改用户名和密码

3.启动

![[Pasted image 20250715134204.png]]

## 读写分离

读写分离,简单地说是把对数据库的读和写操作分开,以对应不同的数据库服务器。主数据库提供写操作，从数据库提供读操作，这样能有效地减轻单台数据库的压力。(MYCat也提供了实现)