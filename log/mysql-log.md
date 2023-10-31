#### 0、Win10安装MySQL
* 以 管理员身份运行 终端
* mysqld是mysql的守护进程。每次在使用mysql前必须先用它

#### 1、mysql数据库默认对表名大小写敏感

~~~
问题来源

我的操作：
1、使用idea导入xxx项目
2、创建mysql库
create database `xxx` default character set utf8mb4 collate utf8mb4_general_ci;
3、执行sql脚本
xxx/db/xxx.sql
4、配置好Mysql数据库和Redis
5、启动XxxApplication
控制台提示：Table ‘xxx.USER’ doesn’t exist

~~~

> 需要配置不区大小写

查询目前是否区分大小写

* 1、连接Mysql并进入mysql命令行
* 2、执行
~~~sql
show global variables like ‘%lower_case%’;
~~~
查看当前mysql的大小写敏感配置


| Variable_name          | Value |
| ---------------------- | ----- |
| lower_case_file_system |  OFF  |
| lower_case_table_names |   0   |

* 3、lower_case_file_system
表示当前系统文件是否大小写敏感，只读参数，无法修改。
ON：表示当前系统对大小写不敏感
OFF：表示当前系统对大小写敏感

* 4、lower_case_table_names
表示创建的数据表名，是否区分大小写
lower_case_table_names = 1 ：表示不区分大小写
lower_case_table_names = 0 ：表示区分大小写

* 5、进入配置文件
~~~sql
/etc/my.cnf
~~~

* 6、修改配置（将数据库表名对大小写敏感）
~~~shell
[mysqld]

lower_case_table_names=1
~~~

* 7、将数据库重启

#### 2、mysql中，"mysql"和"mysqld"命令区别

在MySQL中，"mysql"和"mysqld"是两个不同的命令，具有不同的用途。
1、mysql命令：
mysql命令是一个客户端命令行工具，用于与MySQL服务器建立连接并执行各种数据库操作。您可以使用mysql命令执行SQL查询、创建和管理数据库、创建和管理表、插入、更新和删除数据等。它提供了一个用户友好的界面，您可以在终端或命令提示符下使用它。
示例用法：
~~~sql
mysql -u username -p
~~~
其中，-u选项指定连接的用户名，-p选项表示需要输入密码来进行身份验证。您可以使用其他参数和选项来设置连接的主机、端口等。
2、mysqld命令：
mysqld命令是MySQL服务器的守护进程或后台进程。它负责处理客户端请求、管理数据库和表格、执行查询等。当您安装和配置MySQL服务器后，可以通过启动mysqld命令来启动MySQL服务器。
示例用法：
~~~sql
mysqld
~~~
通常情况下，您无需直接启动mysqld命令。在安装MySQL服务器之后，它会自动作为服务或进程在后台运行，可以通过其他方式管理和控制，如使用服务管理工具、配置文件等。

请注意，命令的具体用法和语法可能因MySQL版本和操作系统而有所差异。在使用这些命令时，请参考MySQL文档和相关文档以获得准确的用法和参数信息。

#### 3、"mysql"和"mysqld"命令的用法
"mysql"和"mysqld"都是MySQL数据库管理系统中的命令。

mysql命令：mysql是用于连接、操作和管理MySQL数据库的命令行客户端工具。可以使用mysql命令执行SQL语句，管理数据库用户和权限，导入和导出数据等。以下是一些常见的用法：

* 连接到MySQL服务器：mysql -h 主机名 -u 用户名 -p 密码
* 执行SQL语句：mysql -e "SQL语句"
* 退出MySQL命令行：quit 或者 exit

mysqld命令：mysqld是MySQL数据库管理系统的守护进程，也称为MySQL Server。它负责监听连接请求，处理SQL查询，管理数据库和表等。通常情况下，不需要直接使用mysqld命令，而是通过启动或停止MySQL服务器来操作mysqld进程。

* 启动MySQL服务器：sudo service mysql start
* 停止MySQL服务器：sudo service mysql stop
* 重启MySQL服务器：sudo service mysql restart
* 查看MySQL服务器状态：sudo service mysql status
需要注意的是，以上命令的具体用法会根据不同的操作系统和MySQL版本有所差异。在使用这些命令时，建议参考相关文档或使用命令的帮助选项来获取更详细的使用说明。

#### 4、Mysql连接错误 `1129 Host is blocked because of many connection errors; unblock with 'mysqladmin flush-host'`

> 原因：同一个ip在短时间内产生太多（超过mysql数据库max_connection_errors的最大值）中断的数据库连接而导致的阻塞

使用 `mysqladmin flush-hosts` 命令清理一下 `hosts` 文件：
在查找到的目录下使用命令修改：

~~~shell
/usr/bin/mysqladmin flush-hosts -h192.168.1.1 -P3308 -uroot -prootpwd;
~~~

* 备注：其中端口号，用户名，密码都可以根据需要来添加和修改；配置有master/slave主从数据库的要把主库和从库都修改一遍的。

**最简单的方法：可以在数据库中进行，命令如下：flush hosts;**
~~~shell
# 执行： `mysqladmin flush-hosts -h 127.0.0.1 -u[用户名] -p` 命令，然后输入密码。

# 或者 在 linux 端， `mysql mysql -uroot -p` 输入root密码，进入mysql，执行 `flush hosts;` 重新连接即可
~~~

#### 5、MySQL中反引号（`）和单引号（'）区别

> 在MySQL语句中，反引号（`）主要用于标识数据库对象（如表名、列名、别名）。

* 反引号可用于标识包含特殊字符（如空格、关键字），或者与MySQL保留字相同的对象名称。使用反引号可以避免语法错误或歧义。例如：
~~~
CREATE TABLE `my table` ...
SELECT `column name` ...
SELECT `select` ...
~~~

* 单引号（'）主要用于表示字符串值。在SQL语句中，**字符串值必须用单引号引起来**。例如：
~~~
SELECT customerId, customerName FROM customers WHERE customerName = 'John Smith'
INSERT INTO products (productName, price) VALUES ('Apple', 2.99)
~~~

如：
~~~
select * from `sys_source_type_config` where `s_type` = 'mysql';
~~~

#### 6、MySQL时间列数据类型 `datetime` 和 `timestamp`

> 考虑方面：存储空间、时区支持、时间范围等等

是否能够根据项目中实际需求做出合理的数据库设计决策

* 1、`datetime`和`timestamp`的区别

**存储空间：**

  timestamp：四个字节
  
  datetime：八个字节

**时区支持：**

  timestamp存储的是一个UTC时间，会根据系统的时区进行自动转化，满足需要在不同时区之间工作可以考虑
  
  datetime不会做时区转化，存储的是字面值的时间

**时间范围：**

  timestamp时间范围是1970年到2038年
  
  datetime时间范围是1000年到9999年

* 2、选择建议

需要存储的时间超出timestamp的范围，或者不想让时区影响存储的时间值-->使用datetime

需要考虑不同地区的时区问题或者希望节省存储空间-->使用timestamp

* 3、实际开发注意方面

明确项目的需求和期望的行为，不要盲目选择

在代码中统一时间的处理方式，以避免时区转换等问题

#### 7、binlog（Binary Log） 和 redolog 有什么区别？

**考察点：**

binlog和redolog都是Mysql里面用来**记录数据库数据变更操作的日志**，其中binlog主要用来做**数据备份、数据恢复和数据同步**。初步接触这个概念的时候，是在Mysql的主从数据同步的场景里，master节点的数据变更，会写入到binlog中，然后把binlog中的数据通过网络传输给slave节点，实现数据同步。如图所示：



而redolog主要是在Mysql数据库事务的ACID特性里面，用来**保证数据的持久化特性**，但是其实它还有很多的作用。当我们数据库崩溃的时候，可以通过Redo log来恢复未完成的数据，保证数据的完整性。通过合理的配置RedoLog的大小和数量，还可以优化Mysql的性能。这个问题就是面试官想考察我们是不是平日里善于积累，仔细思考这方面的知识！

```
Binlog（Binary Log）

* 数据库系统: 主要在MySQL数据库中使用。
* 数据格式: 以二进制形式存储，通常更加紧凑。
* 存储内容: 记录了所有会改变数据（或可能改变数据）的SQL语句。
* 作用:
  用于主从复制：Slave从Master的binlog中读取数据，并执行这些操作，以达到与Master同步的目的。
  用于数据恢复：在数据库崩溃后，可以通过重放binlog来恢复数据。
* 可配置性: 在MySQL中可以很灵活地配置binlog，包括是否开启、存储位置、过期时间等。
* 事务性: 不是事务日志，不参与事务的提交或回滚。

Redo Log

* 数据库系统: 主要在InnoDB存储引擎（MySQL）和一些其他RDBMS（如Oracle）中使用。
* 数据格式: 可以是二进制，也可以是其他格式，取决于具体实现。
* 存储内容: 记录了对数据页（Data Page）的实际修改，更底层。
* 作用:
  主要用于崩溃恢复：当数据库系统崩溃时，Redo log保证了事务的原子性。
  在某些配置下，也用于实现主从复制。
* 可配置性: 通常配置选项比较少，因为它是存储引擎级别的一部分。
* 事务性: 是事务日志，直接参与到事务的提交和回滚。

对比

* 粒度: Redo Log通常更底层，记录的是数据页的实际修改，而Binlog记录的是SQL层面的操作。
* 用途: Binlog更多用于复制和审计，而Redo Log主要用于崩溃恢复。
* 性能: Redo Log通常写入速度更快，因为它是循环利用的固定大小的文件，并且只在事务提交时刷新到磁盘。而Binlog是追加写入的，每个SQL操作都会写入，可能导致I/O更高。
```

**回答：**

binlog和redolog的区别很多，我从以下三点来阐述

1.使用场景不同，binlog主要用来做数据备份，数据恢复，以及主从集群的数据同步；Redo Log主要用来实现Mysql数据库的事务恢复，保证事务ACID的特性，当数据库出现崩溃的时候，RedoLog可以把未提交的事务回滚，把已提交的事务进行持久化，从而保证数据的一致性和持久性。

2.记录的信息不同，binlog是记录数据库的逻辑变化，它提供了三种日志格式分别是statement,row以及mixed; redo log记录的是物理变化，也就是数据页的变化结果

3.记录的时机不同，binlog是在执行SQL语句的时候，在主线程中生成逻辑变化写入磁盘中，所以它是语句级别的记录方式；redolog是在innoDB存储引擎层面的操作，它是在Mysql线程中生成并写入到磁盘中的，所以它是事务级别的记录方式，一个事务操作完成以后才会被写入到redo log中。

```
拓展1：重放binlog什么意思？

"重放binlog" 是一种数据库恢复技术，主要应用于MySQL数据库系统。在这个过程中，MySQL服务器会读取binlog（Binary Log）文件，并按照其中记录的SQL操作或行级别变更来重新执行（重放）这些操作。

-- 为什么需要重放binlog？

* 崩溃恢复: 如果数据库由于某种原因（如硬件故障、软件bug等）突然崩溃，你可以通过重放binlog来恢复到崩溃前的状态。
* 数据迁移和备份: 通过重放特定时间范围内的binlog，你可以实现数据库的增量备份或将数据迁移到另一个数据库实例。
* 审计和故障排查: 通过查看和重放binlog，可以了解数据库在特定时间段内的所有更改，这对于审计或故障排查非常有用。

-- 如何重放binlog？

1、找到binlog文件: binlog文件通常保存在数据库服务器上的特定目录中。你需要知道要重放的binlog文件的具体路径。

2、使用mysqlbinlog工具: MySQL提供了一个名为mysqlbinlog的命令行工具，用于处理binlog文件。例如，你可以使用以下命令将binlog内容转换为SQL语句：
~~~bash
mysqlbinlog /path/to/binlog-file > /path/to/output.sql
~~~

3、执行SQL文件: 最后，你需要在目标MySQL服务器上执行这些SQL语句。这通常可以通过MySQL命令行工具或其他数据库管理工具来完成。
~~~bash
mysql -u username -p < /path/to/output.sql
~~~

-- 注意事项

* 时间戳和位置: 在重放binlog时，你通常需要注意起始和结束的时间戳或位置，以便只重放特定范围内的操作。
* 数据一致性: 由于binlog是追加写入的，重放时要确保目标数据库的初始状态与源数据库在你开始记录binlog时的状态一致。

-- 万一重放binlog的数据时间范围过长有一部分数据是在数据库正常存在的怎么办

**如果重放的binlog数据时间范围过长，其中一部分数据可能与数据库中已存在的数据发生冲突或重复。这可能会引发多种问题，包括但不限于：**

* 主键/唯一键冲突: 如果数据库表有主键或唯一键约束，重放binlog时可能会违反这些约束，导致错误。
* 数据不一致: 如果binlog中的某些操作是基于原有数据进行的（比如更新或删除操作），则这些操作在重放时可能会导致数据不一致。
* 性能影响: 如果重放大量不必要的数据，将消耗更多的CPU、内存和磁盘资源，影响系统性能。

解决这类问题有多种策略：

* 1、精确指定时间范围或位置

使用mysqlbinlog工具时，你可以通过--start-datetime、--stop-datetime、--start-position或--stop-position等选项来限制重放的数据范围。

~~~bash
# 示例：仅重放2023-01-01 12:34:56至2023-01-02 12:34:56之间的数据
mysqlbinlog --start-datetime="2023-01-01 12:34:56" --stop-datetime="2023-01-02 12:34:56" /path/to/binlog-file > /path/to/output.sql
~~~

* 2、使用幂等操作

如果可能，将数据库操作设计为幂等操作，这样即使重复执行也不会影响最终结果。这通常适用于INSERT ... ON DUPLICATE KEY UPDATE、REPLACE INTO等SQL语句。

* 3、先做数据校验

在重放binlog之前，先进行数据校验，比较数据库的当前状态和binlog开始记录时的状态。任何不一致都应引起警觉。

* 3、使用临时环境

在一个与生产环境隔离的临时环境中先进行binlog重放，这样即使有问题也不会影响生产数据。测试无误后，再在生产环境中进行操作。

* 4、谨慎操作

最后，但同样重要的是，应在数据库管理员或有经验的开发者的指导下进行这一操作，并确保有可靠的数据备份。
```

#### 8、NOT NULL属性和空串

> SQL设置了 `NOT NULL`还可以被空字符串（''）作为值插入数据
    
字段虽然确实设置了 NOT NULL，这意味着它不能接受 NULL 值。然而，如果在插入数据时提供了一个空字符串（''）作为字段的值，数据库将允许这个插入操作。因为在这种情况下，并没有尝试插入 NULL，而是插入了一个空字符串，它们是不同的概念。

空字符串（''）被认为是有效的字符串值，只是它的长度为零。当向字段插入数据时，如果提供了一个空字符串，这个操作是符合 NOT NULL 限制的。

如果想确保 nickname 列具有实际值，你可以在插入数据时对其进行验证。例如，可以在应用程序代码中检查 nickname 的长度是否大于零，或者使用数据库触发器在插入之前进行检查。
    
**后台检查**
    > ！=null 并不能检查空字符串（''）
    > 需要通过StringUtils.isEmpty()判断是否为空。
    
> StringUtils.isEmpty和!= null是两种用于检查字符串是否为空或为null的方法，但它们的检查方式略有不同。

1、StringUtils.isEmpty：这是Apache Commons Lang库中的一个方法，用于检查字符串是否为空或null。它会检查字符串是否为null，以及字符串长度是否为零（即空字符串）。这意味着，如果字符串为null或为空字符串（长度为零），StringUtils.isEmpty将返回true。
    
2、!= null：这是Java中的基本操作符，用于检查对象（如字符串）是否为null。它不会检查字符串的长度，只会检查字符串是否为null。因此，如果字符串为null，!= null将返回false。

#### 9、设计数据库索引

在MySQL中，索引是用来快速访问数据库表中特定信息的数据库结构。设计索引的目的是为了提高查询的速度，与之对应的是一些存储、管理和维护索引的开销。因此，在设计数据库表时，适当地设计和选择索引是非常关键的。

下面，我会从以下几个方面为你详细介绍设计索引的相关内容：

什么是索引：

索引可以看作是一本书的目录，通过目录，我们可以快速找到具体的内容，而不需要一页一页地翻查。在数据库中，索引帮助MySQL高效地查找数据。

索引的类型：

单列索引：一个索引只包含一个列。

复合索引：一个索引包含两个或多个列。

全文索引：用于全文搜索。

唯一索引：索引列的所有值都只能出现一次，即必须唯一。

主键索引：是唯一索引的一种特定类型，每个表只能有一个主键。

如何选择索引：

查询列：经常在WHERE子句中出现的列应该被考虑作为索引。

唯一性：具有唯一值的列是创建索引的好候选者。

选择性：具有较高唯一值的列（高选择性）是很好的索引候选。

查询性能：如果添加索引后查询性能有明显提升，则这个索引可能是合适的。

索引的开销：

虽然索引可以提高查询速度，但它们也会增加写操作（如INSERT、UPDATE、DELETE）的开销，因为索引结构需要被更新。
索引会占用磁盘空间。

注意事项：

不要过度索引：不是所有列都需要索引。只对经常用于查询条件或排序的列进行索引。

注意索引的维护：当表结构或查询模式发生变化时，确保对索引进行相应的调整。

```

设计索引的过程中需要考虑的主要因素和步骤如下：

选择索引列：

常用查询列：为经常出现在WHERE子句中的列创建索引。
高选择性的列：为那些包含大量唯一值的列创建索引。
避免全列索引：

如果只需要某个字段的部分数据，考虑创建前缀索引，这可以节省存储空间。
复合索引：

如果经常同时根据多个列进行查询，可以创建一个包含这些列的复合索引。但要注意列的顺序，经常作为过滤条件的列应放在复合索引的前面。
避免过度索引：

创建过多的索引会影响到INSERT、UPDATE和DELETE的性能，因为在进行这些操作时，索引也需要被更新。
过多的索引还会占用额外的存储空间。
主键和唯一索引：

主键是一个特殊的索引，它确保表中每行数据的唯一性。
唯一索引与主键类似，但一个表可以有多个唯一索引。
外键和参照完整性：

外键用于确保表之间数据的一致性和完整性。但在某些情况下，过多的外键约束可能会影响性能。
索引类型：

MySQL支持多种类型的索引，例如：B-Tree、Hash、FULLTEXT等。根据查询的需求和数据的类型选择合适的索引类型。
考虑读写比例：

如果数据库是读密集型的，那么应该重点考虑查询性能，创建更多的索引可能是有益的。
如果数据库是写密集型的，那么应当谨慎地创建索引，因为索引会增加写操作的成本。
定期评估和维护索引：

使用如EXPLAIN命令来评估查询的执行计划，确定索引是否得到了正确的使用。

```

#### 10、索引

> 我可以粗俗的认为设计索引就是在数据库表设计的时候指定某一列，然后在生成表数据的时候数据库引擎自然会根据该列生成一种便于检索的结构，这种结构就是索引吗

简单来说，当您在数据库表的某一列上创建索引时，数据库引擎会为该列生成一个特殊的数据结构。这个结构允许数据库引擎更快地查找到该列的值，而无需扫描整个表。这种特殊的数据结构，我们称之为“索引”。

这和我们在书籍后面看到的“索引”很相似。如果你想查找某个特定的主题，你可以直接查阅索引，找到该主题的页码，而不需要一页一页地翻阅整本书。

但要注意的是，虽然索引能提高查询速度，但它也会带来一定的开销，尤其是在数据写入时，因为索引结构也需要被更新。因此，设计索引时需要进行权衡，不应盲目为每个列创建索引。

```
当查询的列没有索引时，数据库引擎通常会进行全表扫描（Table Scan）。这意味着它会逐行检查表中的每一行，直到找到满足查询条件的数据。

具体来说：

全表扫描（Table Scan）：数据库引擎会读取整个表的所有数据，然后筛选出符合条件的行。这是一种非常低效的查询方式，特别是在数据量大的情况下。

索引扫描（Index Scan）：如果查询的列有索引，数据库引擎会使用这个索引来快速找到满足条件的数据。不需要检查表中的每一行，因为索引已经为引擎提供了快速查找的路径。

因此，如果一个查询经常被执行，并且每次都需要进行全表扫描，那么这个查询的性能可能会成为问题。在这种情况下，为相应的列添加索引可能会大大提高查询的效率。

但同样要注意，不是所有的查询都需要索引，过多的索引会增加写操作的开销并占用更多的存储空间。所以，在添加索引之前，应该根据实际的查询模式和数据量进行权衡。
```

#### 11、过多的索引会增加写操作的开销并占用更多的存储空间

```
当我们讨论索引导致写操作开销增加的原因时，需要从索引的数据结构层面进行分析。在MySQL中，常用的索引类型是B+树索引，我们以此为例进行说明：

索引结构的维护：当插入、删除或更新一条数据时，不仅要在实际的表中进行这些操作，还需要在索引结构中进行对应的操作。例如，对于B+树索引，插入一个新的键可能需要分裂一个节点；而删除一个键可能导致节点合并。这些操作都会增加写操作的开销。

数据页的分裂与合并：在B+树中，数据是按页（或称为块）来存储的。当一个页满时（例如，插入操作），可能需要分裂该页，并将一些键移到新的页中。同样，删除操作可能导致页中的数据太少，需要与其他页合并。这些页的分裂和合并操作也会导致额外的写开销。

物理存储的开销：索引结构占用额外的磁盘空间。因此，写入索引数据意味着需要写入更多的磁盘块，这也会增加写操作的延迟。

随机I/O：与顺序I/O相比，随机I/O通常更慢。由于B+树索引的性质，写操作可能在物理存储上导致随机I/O，尤其是当数据不断插入并导致树结构调整时。

多个索引的开销：如果一个表有多个索引，每次写操作都需要更新所有这些索引。这意味着写操作的开销会随着索引数量的增加而增加。

从上述分析中可以看出，索引虽然可以极大地提高查询性能，但它们在维护和存储方面带来了额外的开销。因此，在设计数据库时，需要根据实际需求和性能考虑来权衡是否以及如何添加索引。

```

#### 12、存储引擎

MySQL提供了多个存储引擎——`包括处理事务安全表的引擎`和`处理非事务安全表的引擎`，在MySQL中，`不需要在整个服务器中使用同一种存储引擎，针对具体的要求，可以对每一个表使用不同的存储引擎。`

存储引擎简介

MySQL中的数据用各种不同的技术存储在文件(或者内存)中。

这些技术中的每一种技术都使用不同的存储机制、索引技巧、锁定水平并且最终提供广泛的不同的功能和能力。

通过选择不同的技术，你能够获得额外的速度或者功能，从而改善你的应用的整体功能。

> 存储引擎说白了就是

* 如何存储数据

* 如何为存储的数据建立索引

* 如何更新、查询数据 等技术的实现方法。

例如，如果在研究大量的临时数据，也许需要使用内存存储引擎，因为内存存储引擎能够在内存中存储所有的表格数据。又或者，需要一个支持事务处理的数据库，以确保事务处理不成功时数据的回退能力，就要选择支持事务的存储引擎。

#### 13、在mysql使用where中使用子查询时需要保证select语句和where语句有相匹配的列

在 MySQL 中，当你使用子查询作为 WHERE 子句的一部分时，需要确保子查询的 SELECT 语句和外部查询的 WHERE 子句有相匹配的列，以便正确地检索所需的数据。这是因为子查询通常用于检索满足某些条件的行，而这些条件通常是基于外部查询中的列的值。

具体来说，子查询中的 SELECT 语句应该选择与外部查询中的列相关的数据，并且这些数据需要用于外部查询的 WHERE 子句中的条件。这确保了子查询返回的数据与外部查询的条件相匹配。

例如，假设你有两个表：orders 和 customers，你想查找所有购买某个产品的客户。你可以使用子查询来实现这个目标：

~~~sql
SELECT customer_name
FROM customers
WHERE customer_id IN (SELECT customer_id FROM orders WHERE product_name = 'YourProduct');
~~~

在这个示例中，子查询 (SELECT customer_id FROM orders WHERE product_name = 'YourProduct') 返回购买了特定产品的客户的 customer_id。然后，外部查询将使用这些 customer_id 来检索相应的客户名称，确保了子查询的结果与外部查询的条件相匹配。

总之，在使用子查询时，确保子查询的 SELECT 语句选择的列与外部查询的 WHERE 子句中的条件相关，以便获得正确的查询结果。这有助于确保子查询和外部查询之间的匹配。

-------------------------
子查询可以应用在哪些地方？

子查询（Subquery）是一个在 SQL 查询中嵌套的查询，它可以用于多个不同的地方以获取需要的数据或执行特定操作。子查询通常返回一个单一的值、一组值或一组记录，这些值可以在外部查询中使用。以下是一些常见的地方，可以应用子查询：

1、WHERE 子句中：你可以在 WHERE 子句中使用子查询来过滤结果集，例如，查找满足特定条件的行。

~~~sql
SELECT name, age
FROM employees
WHERE department_id = (SELECT department_id FROM departments WHERE department_name = 'HR');
~~~

2、FROM 子句中：子查询可以用作表，用于从中获取数据，并与其他表联接。

~~~sql
SELECT employees.name, departments.department_name
FROM employees
INNER JOIN (SELECT department_id, department_name FROM departments) AS departments
ON employees.department_id = departments.department_id;
~~~

3、SELECT 子句中：子查询可以用于选择数据并将其作为结果集中的一列。

~~~sql
SELECT name, (SELECT MAX(salary) FROM salaries WHERE employee_id = employees.id) AS max_salary
FROM employees;
~~~

4、HAVING 子句中：你可以在 HAVING 子句中使用子查询来过滤分组后的结果。

~~~sql
SELECT department_id, AVG(salary)
FROM employees
GROUP BY department_id
HAVING AVG(salary) > (SELECT AVG(salary) FROM employees);
~~~

5、INSERT 语句中：子查询可以用于插入数据，例如，将一张表的数据插入到另一张表中。

~~~sql
INSERT INTO new_table (column1, column2)
SELECT columnA, columnB FROM old_table WHERE condition;
~~~

6、UPDATE 语句中：子查询可以用于更新表中的数据。

~~~sql
UPDATE employees
SET salary = (SELECT MAX(salary) FROM salaries WHERE employee_id = employees.id)
WHERE department_id = (SELECT department_id FROM departments WHERE department_name = 'IT');
~~~

7、DELETE 语句中：子查询可以用于删除满足特定条件的行。

~~~sql
DELETE FROM employees
WHERE department_id = (SELECT department_id FROM departments WHERE department_name = 'Marketing');
~~~

子查询是强大的工具，可以帮助你在 SQL 查询中执行复杂的操作和筛选数据。你可以根据需要在不同的上下文中使用它们，以满足查询的需求。

#### 14、GROUP BY 的使用

GROUP BY 是 SQL 中的一个非常重要的子句，它用于`将查询结果按照一个或多个列的值进行分组`。这允许你对每个分组应用聚合函数，如 SUM、AVG、COUNT 等，以便获得分组级别的数据汇总。以下是关于 GROUP BY 子句的基本用法和示例：

基本语法：

~~~sql
SELECT column1, column2, aggregate_function(column3)
FROM table_name
GROUP BY column1, column2 DESC; --- 默认ASC
~~~

column1, column2 是你要选择的列。

aggregate_function 是聚合函数，用于对每个分组执行计算。

column3 是你要应用聚合函数的列。

table_name 是你要从中检索数据的表。

**示例 1：计算每个部门的平均工资（使用 AVG 聚合函数）：**

~~~sql
SELECT department, AVG(salary)
FROM employees
GROUP BY department;
~~~

> 在这个示例中，数据将按照部门分组，并对每个部门的工资应用平均值函数，从而获得每个部门的平均工资。

**示例 2：计算每个部门的员工数量（使用 COUNT 聚合函数）：**

~~~sql
SELECT department, COUNT(*) as employee_count
FROM employees
GROUP BY department;
~~~

> 在这个示例中，数据将按照部门分组，并对每个部门的员工数量应用计数函数，从而获得每个部门的员工数量。

**示例 3：计算每个部门的最高工资（使用 MAX 聚合函数）：**

~~~sql
SELECT department, MAX(salary) as max_salary
FROM employees
GROUP BY department;
~~~

> 在这个示例中，数据将按照部门分组，并对每个部门的工资应用最大值函数，从而获得每个部门的最高工资。

> GROUP BY 子句是在执行聚合操作之前用来分组数据的重要工具。它允许你以更高层次的抽象方式查看和分析数据，以便更好地理解数据的分布和趋势。

#### 15、内连接（INNER JOIN）、外连接（OUTER JOIN）、左连接（LEFT JOIN）和右连接（RIGHT JOIN）

```
内连接（INNER JOIN）、外连接（OUTER JOIN）、左连接（LEFT JOIN）和右连接（RIGHT JOIN）是 SQL 中用于联接多个表的不同类型的连接操作。它们之间的主要区别在于返回结果集中包含哪些行。

内连接 (INNER JOIN):

内连接返回满足连接条件的行，即两个表之间的交集。
结果集中包含那些在连接的两个表中都有匹配的行。
如果一个表中没有与另一个表匹配的行，该行将被排除在结果之外。

外连接 (OUTER JOIN):

外连接分为左外连接（LEFT OUTER JOIN）和右外连接（RIGHT OUTER JOIN）。
外连接返回满足连接条件的行以及不满足条件的行。
左外连接返回左表的所有行，以及与右表匹配的行。
右外连接返回右表的所有行，以及与左表匹配的行。
如果一个表中没有与另一个表匹配的行，它仍然会包括在结果中，但非匹配的列将会包含 NULL 值。

左连接 (LEFT JOIN):

左连接（LEFT JOIN）是外连接的一种特殊形式，通常指的是左外连接。
左连接返回左表的所有行以及与右表匹配的行。
如果一个表中没有与另一个表匹配的行，它仍然会包括在结果中，但非匹配的列将会包含 NULL 值。

右连接 (RIGHT JOIN):

右连接（RIGHT JOIN）是外连接的另一种特殊形式，通常指的是右外连接。
右连接返回右表的所有行以及与左表匹配的行。
如果一个表中没有与另一个表匹配的行，它仍然会包括在结果中，但非匹配的列将会包含 NULL 值。

总的来说，这些连接操作允许你以不同的方式联接表，根据查询需求获取不同的数据。内连接返回交集，外连接返回交集和不满足条件的行，左连接和右连接分别返回左表或右表的所有行以及与另一个表匹配的行。选择哪种连接类型取决于你想获得的结果集以及如何处理不匹配的行。
```
