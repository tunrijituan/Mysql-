# Mysql
Mysql作为关系型数据库在日常应用日趋广泛，简单对Mysql的只是做一个总结。
## Mysql四种存储引擎：
### （1）MyISAM存储引擎：不支持事务、也不支持外键，优势是访问速度快，对事务完整性没有要求或者以select，insert为主的应用基本上可以用这个引擎来创建表
支持3种不同的存储格式，分别是：静态表；动态表；压缩表
静态表：表中的字段都是非变长字段，这样每个记录都是固定长度的，优点存储非常迅速，容易缓存，出现故障容易恢复；缺点是占用的空间通常比动态表多（因为存储时会按照列的宽度定义补足空格）ps：在取数据的时候，默认会把字段后面的空格去掉，如果不注意会把数据本身带的空格也会忽略。
动态表：记录不是固定长度的，这样存储的优点是占用的空间相对较少；缺点：频繁的更新、删除数据容易产生碎片，需要定期执行OPTIMIZE TABLE或者myisamchk-r命令来改善性能
压缩表：因为每个记录是被单独压缩的，所以只有非常小的访问开支
### （2）InnoDB存储引擎*
该存储引擎提供了具有提交、回滚和崩溃恢复能力的事务安全。但是对比MyISAM引擎，写的处理效率会差一些，并且会占用更多的磁盘空间以保留数据和索引。 
InnoDB存储引擎的特点：支持自动增长列，支持外键约束
### (3)：MEMORY存储引擎
Memory存储引擎使用存在于内存中的内容来创建表。每个memory表只实际对应一个磁盘文件，格式是.frm。memory类型的表访问非常的快，因为它的数据是放在内存中的，并且默认使用HASH索引，但是一旦服务关闭，表中的数据就会丢失掉。 
MEMORY存储引擎的表可以选择使用BTREE索引或者HASH索引，两种不同类型的索引有其不同的使用范围
Hash索引优点： 
Hash 索引结构的特殊性，其检索效率非常高，索引的检索可以一次定位，不像B-Tree索引需要从根节点到枝节点，最后才能访问到页节点这样多次的IO访问，所以 Hash 索引的查询效率要远高于 B-Tree 索引。 
Hash索引缺点： 那么不精确查找呢，也很明显，因为hash算法是基于等值计算的，所以对于“like”等范围查找hash索引无效，不支持；
Memory类型的存储引擎主要用于哪些内容变化不频繁的代码表，或者作为统计操作的中间结果表，便于高效地对中间结果进行分析并得到最终的统计结果，。对存储引擎为memory的表进行更新操作要谨慎，因为数据并没有实际写入到磁盘中，所以一定要对下次重新启动服务后如何获得这些修改后的数据有所考虑。
### （4）MERGE存储引擎
Merge存储引擎是一组MyISAM表的组合，这些MyISAM表必须结构完全相同，merge表本身并没有数据，对merge类型的表可以进行查询，更新，删除操作，这些操作实际上是对内部的MyISAM表进行的。
## 日常开发中使用最多的是InnoDB,毕竟是支持事务的，平常使用中由于大部分都会涉及到事务，所以InnoDB应用更广泛些。
* 既然提到事务，下面就介绍下数据库中事务的一些特性
#### 事务一般满足4大特性ACID:
* 原子性：一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
* 一致性：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
* 隔离性：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
* 持久性：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。
#### 事务并发存在的问题
* 1、脏读：事务A读取了事务B更新的数据，然后B回滚操作，那么A读取到的数据是脏数据
* 2、不可重复读：事务A多次读取同一数据，事务B在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果不一致。
* 3、幻读：系统管理员A将数据库中所有学生的成绩从具体分数改为ABCDE等级，但是系统管理员B就在这个时候插入了一条具体分数的记录，当系统管理员A改结束后发现还有一条记录没有改过来，就好像发生了幻觉一样，这就叫幻读。
### 不可重复读的和幻读很容易混淆，不可重复读侧重于修改，幻读侧重于新增或删除。解决不可重复读的问题只需锁住满足条件的行，解决幻读需要锁表
#### MySQL事务隔离级别
![](https://github.com/tunrijituan/Mysql-/blob/master/TIM%E5%9B%BE%E7%89%8720180527191835.png)
mysql中默认事务隔离级别是可重复读
 

 

 

 

 
