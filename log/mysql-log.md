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
