
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [事务](#事务)
* [事务问题](#事务问题)
* [事务的隔离级别](#事务的隔离级别)
* [事务回滚机制](#事务回滚机制)
* [索引](#索引)
	* [B树和B+树的特点](#b树和b树的特点)
	* [索引优化](#索引优化)
* [内连接，外连接，自连接](#内连接外连接自连接)
* [mysql存储引擎](#mysql存储引擎)
	* [InnoDB](#innodb)
	* [MyISAM](#myisam)
	* [对比](#对比)
* [乐观锁与悲观锁](#乐观锁与悲观锁)
	* [乐观锁：](#乐观锁)
	* [悲观锁：](#悲观锁)
* [数据库的锁](#数据库的锁)
* [面试题](#面试题)
	* [1. mysql主从复制，读写分离？](#1-mysql主从复制读写分离)
		* [1. 主从复制的几种方式：](#1-主从复制的几种方式)
		* [2. master的写操作，slaves被动的进行一样的操作，保持数据一致性，那么slave是否可以主动的进行写操作？](#2-master的写操作slaves被动的进行一样的操作保持数据一致性那么slave是否可以主动的进行写操作)
		* [3. 主从复制中，可以有N个slave,可是这些slave又不能进行写操作，要他们干嘛？](#3-主从复制中可以有n个slave可是这些slave又不能进行写操作要他们干嘛)
		* [4. 当master的二进制日志每产生一个事件，都需要发往slave，如果我们有N个slave,那是发N次，还是只发一次？](#4-当master的二进制日志每产生一个事件都需要发往slave如果我们有n个slave那是发n次还是只发一次)
		* [5.主从复制中有master,slave1,slave2,...等等这么多MYSQL数据库，那比如一个JAVA WEB应用到底应该连接哪个数据库?](#5主从复制中有masterslave1slave2等等这么多mysql数据库那比如一个java-web应用到底应该连接哪个数据库)
		* [6.当一个select发往mysql proxy，可能这次由slave-2响应，下次由slave-3响应，这样的话，就无法利用查询缓存了。](#6当一个select发往mysql-proxy可能这次由slave-2响应下次由slave-3响应这样的话就无法利用查询缓存了)

<!-- /code_chunk_output -->

# 事务
- Atomicity原子性
  原子性任务是一个独立的操作单元，是一种要么全部是，要么全部不是的原子单位性的操作
- Consistency一致性
  一致性是指在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏。这是说数据库事务不能破坏关系数据的完整性以及业务逻辑上的一致性。
  - **完整性约束**：数据完整性约束指的是为了防止不符合规范的数据进入数据库，在用户对数据进行插入、修改、删除等操作时，DBMS自动按照一定的约束条件对数据进行监测，使不符合规范的数据不能进入数据库，以确保数据库中存储的数据正确、有效、相容。
- Isolation隔离性
  多个事务并发访问时，事务之间是隔离的，一个事务不应该影响其它事务运行效果。
- Durability持久性
  持久性表示一旦一个事务成功了，那么他的改变是永久性的被记录和操作。

# 事务问题
  - 脏读：事务A读取到事务B未提交的数据
    - 张三的工资为 5000,事务A中把他的工资改为8000,但事务A尚未提交。与此同时，事务B正在读取张三的工资，读取到张三的工资为 8000。 随后，事务A发生异常，而回滚了事务。张三的工资又回滚为500。最后，事务B读取到的张三工资为 8000 的数据即为脏数据， 事务B做了一次脏读。
  - 不可重复读：在一个事务内两次相同的查询，读取到不同的数据
    - 在事务A中，读取到张三的工资为5000，操作没有完成，事务还没提交。与此同时，事务B把张三的工资改为8000，并提交了事务。随后，在事务A中，再次读取张三的工资，此时工资变为8000。**在一个事务中前后两次读取的结果并不致，导致了不可重复读。**
  - 幻读：
  目前工资为5000 的员工有10人，事务A读取所有工资为5000的人数为10人。此时，事务B插入一条工资也为5000的记录。 这是，事务A再次读取工资为 5000 的员工， 记录为 11 人。 此时产生了幻读。
  - 不可重复读：**重点是修改：**
  同样的条件，你读取过的数据再次读取出来发现值不一样了。
  - 幻读的重点在于新增或者删除：
  同样的条件， 第 1 次和第 2 次读出来的记录数不一样。

# 事务的隔离级别
- 读未提交：脏读，不可重复读，幻读
  - 如果一个事务已经开始写数据，则另外一个数据则不允许同时进行写操作，但允许其他事务读此行数据，**该隔离级别可以通过“排他写锁”实现。**
- 读已提交：不可重复读，幻读
  - 这可以**通过“瞬间共享读锁”和“排他写锁”实现**。读取数据的事务允许其他事务继续访问该行数据，
- 可重复读：mysql默认，幻读
- 可串行化：性能较差

# 事务回滚机制

# 索引
数据库中排序的数据结构，协助快速查询，更新的操作，数据的实现一般是B树或者B+树

	1. 增加数据库的物理存储空间
	2. 插入和修改数据时要花费较多的时间（索引的位置也要变动）
	3. 维护索引需要耗费时间，随着数据量的增加而增加
优点：

	1. 唯一索引可以保证数据的唯一性
	2. 查询速度加快
	3. 在查询中使用索引，可以提高性能
使用：

	1. 在经常需要搜索的列上，加快检索速度
	2. 主键确保唯一性
	3. 需要排序的字段上简历索引
## B树和B+树的特点

## 索引优化

# 内连接，外连接，自连接
  - 内连接：求两张表之间的交集，在A中出现也在B中出现
  - 左外连接：求两张表之间的交集，在A中出现，B中没有出现，没有出现的用null代替  
  也称左连接，左表为主表，左表中的所有记录都会出现在结果集中，对于那些在右表中并没有匹配的记录，仍然要显示，右边对应的那些字段值以NULL来填充。
  - 右外连接：求两张表之间的交集，在A中没有出现，B中出现，没有出现的用null代替  
  也称右连接，右表为主表，右表中的所有记录都会出现在结果集中。左连接和右连接可以互换，MySQL目前还不支持全外连接。

# mysql存储引擎
## InnoDB

## MyISAM

## 对比
|   | InnoDB | MyISAM |
|:----:|:---:|:---:|
| 事务 | 支持  | 不支持 |
| 适合 | select  | insert，update  |
| 外键 | 支持  | 不支持 |
| 查询速度 |  慢 | 快  |
| 锁 | 表锁  | 行锁  |
| delete操作 | 一行一行删  | 先drop表，然后重建表  |
|  存储结构 | InnoDB 把数据和索引存放在表空间里面  | MyISAM 表被存放在三个文件 .frm 文件存放表格定义。 数据文件是MYD (MYData)索引文件是MYI (MYIndex)引伸  |
# 乐观锁与悲观锁
## 乐观锁：
  认为每次读取数据的时候，认为不会修改数据，一般适合读数据比较大的时候，一般采用版本号进行加锁
## 悲观锁：
  认为每次读取数据时候都认为别人会修改，每次读取的时候都会上锁，基于数据库的锁机制，比如行锁，表锁，读锁，写锁等
# 数据库的锁
  -
# 面试题
## 1. mysql主从复制，读写分离？
### 1. 主从复制的几种方式：
  1. 同步复制：master变化，等待其他slave同步完成才返回。
  2. 异步复制：master只需完成自己的数据操作，不需要管其他节点
  3. 半同步复制：只保证一个slave成功，其他的slave不管
### 2. master的写操作，slaves被动的进行一样的操作，保持数据一致性，那么slave是否可以主动的进行写操作？
- 不能进行写操作，slave写操作无法通知master，会导致数据分不一致，违背了读写分离的概念
### 3. 主从复制中，可以有N个slave,可是这些slave又不能进行写操作，要他们干嘛？
1. **数据备份**
2. **负载均衡**：读取任务分散给不同的slave
3. 实际应用上通常读取的操作次数大于写入的操作，其实也算负载均衡。
### 4. 当master的二进制日志每产生一个事件，都需要发往slave，如果我们有N个slave,那是发N次，还是只发一次？
slave-1是master的从，slave-1又是slave-2,slave-3,...的主，同时slave-1不再负责select。slave-1将master的复制线程的负担，转移到自己的身上。这就是所谓的**多级复制**的概念。
### 5.主从复制中有master,slave1,slave2,...等等这么多MYSQL数据库，那比如一个JAVA WEB应用到底应该连接哪个数据库?
通过一个mysql proxy负责web的连接，同时mysql proxy还负责主备之间的连接。如下图：

<img src = assets/markdown-img-paste-20180923141959832.png width = 500 height = 250>

### 6.当一个select发往mysql proxy，可能这次由slave-2响应，下次由slave-3响应，这样的话，就无法利用查询缓存了。
应该找一个共享式的缓存，比如memcache来解决。将slave-2,slave-3,...这些查询的结果都缓存至mamcache中。
