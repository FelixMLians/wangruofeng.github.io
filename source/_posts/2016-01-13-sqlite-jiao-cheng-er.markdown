---
layout: post
title: "SQLite_教程(二)"
date: 2016-01-13 23:21:34 +0800
comments: true
tags: [iOS, SQLite, Database, Octopress]
keywords: octopress, iOS, Database, Swift, Objective-c, SQLite
categories: Database
description: SQLite_教程(二)
---

#### SQLite 数据类型

SQLite 数据类型是一个用来指定任何对象的数据类型的属性。SQLite 中的每一列，每个变量和表达式都有相关的数据类型。

您可以在创建表的同时使用这些数据类型。SQLite 使用一个更普遍的动态类型系统。在 SQLite 中，值的数据类型与值本身是相关的，而不是与它的容器相关。

#### SQLite 存储类

每个存储在 SQLite 数据库中的值都具有以下存储类之一：

|存储类  |描述      			     |
|-------|-----------------------|
|NULL   |值是一个 NULL 值。		 |
|INTEGER|	值是一个带符号的整数，根据值的大小存储在 1、2、3、4、6 或 8 字节中。|
|REAL	|值是一个浮点值，存储为 8 字节的 IEEE 浮点数字。|
|TEXT	|值是一个文本字符串，使用数据库编码（UTF-8、UTF-16BE 或 |UTF-16LE）存储。|
|BLOB	|值是一个 blob 数据，完全根据它的输入存储。|

SQLite 的存储类稍微比数据类型更普遍。INTEGER 存储类，例如，包含 6 种不同的不同长度的整数数据类型。

---

#### SQLite Affinity 类型
SQLite 支持列上的类型 affinity 概念。任何列仍然可以存储任何类型的数据，但列的首选存储类是它的 **affinity**。在 SQLite3 数据库中，每个表的列分配为以下类型的 affinity 之一：

|Affinity |描述      			     |
|---------|-------------------------|
|TEXT	  |该列使用存储类 NULL、TEXT 或 BLOB 存储所有数据。
|NUMERIC  |该列可以包含使用所有五个存储类的值。
|INTEGER  |与带有 NUMERIC affinity 的列相同，在 CAST 表达式中带有异常。
|REAL	  |与带有 NUMERIC affinity 的列相似，不同的是，它会强制把整数值转换为浮点表示。
|NONE	  |带有 affinity NONE 的列，不会优先使用哪个存储类，也不会尝试把数据从一个存储类强制转换为另一个存储类。

#### SQLite Affinity 及类型名称

下表列出了当创建 SQLite3 表时可使用的各种数据类型名称，同时也显示了相应的应用 Affinity：

|数据类型  |Affinity      			     |
|---------|-----------------------------|
|INT <br> INTEGERTINYINT <br> SMALLINT <br> MEDIUMINT <br> BIGINT <br> UNSIGNED BIG INT <br> INT2 <br> INT8  <br> | INTEGER  |            
|CHARACTER(20) <br> VARCHAR(255) <br> VARYING CHARACTER(255) <br> NCHAR(55) <br> NATIVE CHARACTER(70) <br> NVARCHAR(100) <br> TEXT <br> CLOB                   							  | TEXT    |
|BLOB <br> no datatype specified                 | NONE    |
|REAL <br> DOUBLE <br> DOUBLE PRECISION <br> FLOAT | REAL  |
|NUMERIC <br> DECIMAL(10,5) <br> BOOLEAN <br> DATE <br> DATETIME | NUMERIC |

---

#### Boolean 数据类型
SQLite 没有单独的 Boolean 存储类。相反，布尔值被存储为整数 0（false）和 1（true）。

---

#### Date 与 Time 数据类型

SQLite 没有一个单独的用于存储日期和/或时间的存储类，但 SQLite 能够把日期和时间存储为 TEXT、REAL 或 INTEGER 值。

|存储类	  |日期格式      			     |
|---------|-----------------------------|
|TEXT	  |格式为 "YYYY-MM-DD HH:MM:SS.SSS" 的日期。
|REAL	  |从公元前 4714 年 11 月 24 日格林尼治时间的正午开始算起的天数。
|INTEGER  |	从 1970-01-01 00:00:00 UTC 算起的秒数。

您可以以任何上述格式来存储日期和时间，并且可以使用内置的日期和时间函数来自由转换不同格式。

#### SQLite 创建数据库

SQLite 的 sqlite3 命令被用来创建新的 SQLite 数据库。您不需要任何特殊的权限即可创建一个数据。

---

**语法**

sqlite3 命令的基本语法如下：

	$sqlite3 DatabaseName.db

通常情况下，数据库名称在 RDBMS 内应该是唯一的。

**实例**

如果您想创建一个新的数据库 <testDB.db>，SQLITE3 语句如下所示：

	$sqlite3 testDB.db
	SQLite version 3.8.10.2 2015-05-20 18:17:19
	Enter ".help" for usage hints.
	sqlite>

上面的命令将在当前目录下创建一个文件 testDB.db。该文件将被 SQLite 引擎用作数据库。如果您已经注意到 sqlite3 命令在成功创建数据库文件之后，将提供一个 sqlite> 提示符。
一旦数据库被创建，您就可以使用 SQLite 的 .databases 命令来检查它是否在数据库列表中，如下所示：

	sqlite>.databases
	seq  name             file                                                      
	---- ---------------- --------------------------------------------
	0    main             /Users/wangruofeng/Documents/SQLitePractice/testDB.db

您可以使用 SQLite .quit 命令退出 sqlite 提示符，如下所示：

	sqlite>.quit
	$

#### .dump 命令

您可以在命令提示符中使用 SQLite .dump 点命令来导出完整的数据库在一个文本文件中，如下所示：

	$sqlite3 testDB.db .dump > testDB.sql

上面的命令将转换整个 testDB.db 数据库的内容到 SQLite 的语句中，并将其转储到 ASCII 文本文件 testDB.sql 中。您可以通过简单的方式从生成的 testDB.sql 恢复，如下所示：

	$sqlite3 testDB.db < testDB.sql

此时的数据库是空的，一旦数据库中有表和数据，您可以尝试上述两个程序。现在，让我们继续学习下一章。

#### SQLite 附加数据库

假设这样一种情况，当在同一时间有多个数据库可用，您想使用其中的任何一个。SQLite 的 ATTACH DTABASE 语句是用来选择一个特定的数据库，使用该命令后，所有的 SQLite 语句将在附加的数据库下执行。相当于给数据库取一个别名

---

**语法**

SQLite 的 ATTACH DATABASE 语句的基本语法如下：

	ATTACH DATABASE 'DatabaseName' As 'Alias-Name';

如果数据库尚未被创建，上面的命令将创建一个数据库，如果数据库已存在，则把数据库文件名称与逻辑数据库 'Alias-Name' 绑定在一起。

---

**实例**
如果想附加一个现有的数据库 testDB.db，则 ATTACH DATABASE 语句将如下所示：

	sqlite> ATTACH DATABASE 'testDB.db' as 'TEST';

使用 SQLite .database 命令来显示附加的数据库。

	sqlite> .database
	seq  name             file
	---  ---------------  ----------------------
	0    main             /home/sqlite/testDB.db
	2    test             /home/sqlite/testDB.db

数据库名称 main 和 temp 被保留用于主数据库和存储临时表及其他临时数据对象的数据库。这两个数据库名称可用于每个数据库连接，且不应该被用于附加，否则将得到一个警告消息，如下所示：

	sqlite>  ATTACH DATABASE 'testDB.db' as 'TEMP';
	Error: database TEMP is already in use
	sqlite>  ATTACH DATABASE 'testDB.db' as 'main';
	Error: database main is already in use

---

#### SQLite 分离数据库

SQLite的 **DETACH DTABASE** 语句是用来把命名数据库从一个数据库连接分离和游离出来，连接是之前使用 ATTACH 语句附加的。如果同一个数据库文件已经被附加上多个别名，DETACH 命令将只断开给定名称的连接，而其余的仍然有效。您无法分离 main 或 temp 数据库。

> 如果数据库是在内存中或者是临时数据库，则该数据库将被摧毁，且内容将会丢失。

**语法**

SQLite 的 DETACH DATABASE 'Alias-Name' 语句的基本语法如下：

	DETACH DATABASE 'Alias-Name';

在这里，'Alias-Name' 与您之前使用 ATTACH 语句附加数据库时所用到的别名相同。

**实例**

假设在前面的章节中您已经创建了一个数据库，并给它附加了 'test' 和 'currentDB'，使用 .database/.databases 命令，我们可以看到：

	sqlite> .databases
	seq  name             file
	---  ---------------  ----------------------
	0    main             /home/sqlite/testDB.db
	2    test             /home/sqlite/testDB.db
	3    currentDB        /home/sqlite/testDB.db

现在，让我们尝试把 'currentDB' 从 testDB.db 中分离出来，如下所示：

	sqlite> DETACH DATABASE 'currentDB';

现在，如果检查当前附加的数据库，您会发现，testDB.db 仍与 'test' 和 'main' 保持连接。

	sqlite> .databases
	seq  name             file
	---  ---------------  ----------------------
	0    main             /home/sqlite/testDB.db
	2    test             /home/sqlite/testDB.db

---

#### SQLite 创建表

SQLite 的 CREATE TABLE 语句用于在任何给定的数据库创建一个新表。创建基本表，涉及到命名表、定义列及每一列的数据类型。

---

**语法**

CREATE TABLE 语句的基本语法如下：

	CREATE TABLE database_name.table_name(
	   column1 datatype  PRIMARY KEY(one or more columns),
	   column2 datatype,
	   column3 datatype,
	   .....
	   columnN datatype,
	);

CREATE TABLE 是告诉数据库系统创建一个新表的关键字。CREATE TABLE 语句后跟着表的唯一的名称或标识。您也可以选择指定带有 table_name 的 *database_name*。

**实例**

下面是一个实例，它创建了一个 COMPANY 表，ID 作为主键，NOT NULL 的约束表示在表中创建纪录时这些字段不能为 NULL：

	sqlite> CREATE TABLE COMPANY(
	   ID INT PRIMARY KEY     NOT NULL,
	   NAME           TEXT    NOT NULL,
	   AGE            INT     NOT NULL,
	   ADDRESS        CHAR(50),
	   SALARY         REAL
	);

让我们再创建一个表，我们将在随后章节的练习中使用：

	sqlite> CREATE TABLE DEPARTMENT(
	   ID INT PRIMARY KEY      NOT NULL,
	   DEPT           CHAR(50) NOT NULL,
	   EMP_ID         INT      NOT NULL
	);

您可以使用 SQLIte 命令中的 .tables/.table 命令来验证表是否已成功创建，该命令用于列出附加数据库中的所有表。

	sqlite> .tables
	COMPANY          DEPARTMENT       TEST.COMPANY     TEST.DEPARTMENT

在这里，可以看到 COMPANY 表出现两次，一个是主数据库的 COMPANY 表，一个是为 testDB.db 创建的 'test' 别名的 test.COMPANY 表。您可以使用 SQLite .schema 命令得到表的完整信息，如下所示：

	sqlite>.schema COMPANY
	CREATE TABLE COMPANY(
	   ID INT PRIMARY KEY     NOT NULL,
	   NAME           TEXT    NOT NULL,
	   AGE            INT     NOT NULL,
	   ADDRESS        CHAR(50),
	   SALARY         REAL
	);

#### SQLite 删除表

SQLite 的 DROP TABLE 语句用来删除表定义及其所有相关数据、索引、触发器、约束和该表的权限规范。

> 使用此命令时要特别注意，因为一旦一个表被删除，表中所有信息也将永远丢失。

---

**语法**

DROP TABLE 语句的基本语法如下。您可以选择指定带有表名的数据库名称，如下所示：

	DROP TABLE database_name.table_name;

**实例**

让我们先确认 COMPANY 表已经存在，然后我们将其从数据库中删除。

	sqlite> .tables
	COMPANY       test.COMPANY

这意味着 COMPANY 表已存在数据库中，接下来让我们把它从数据库中删除，如下：

	sqlite> DROP TABLE COMPANY;
	sqlite>

现在，如果尝试 .TABLES 命令，那么将无法找到 COMPANY 表了：

	sqlite> .tables
	sqlite>

显示结果为空，意味着已经成功从数据库删除表。

资料整理于：[RUNOOB.COM-SQLite教程](http://www.runoob.com/sqlite/sqlite-tutorial.html)
转载请注明出处。
