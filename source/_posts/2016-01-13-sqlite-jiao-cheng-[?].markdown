---
layout: post
title: "SQLite_教程(一)"
date: 2016-01-13 23:18:15 +0800
comments: true
tags: [iOS, SQLite, Database, Octopress]
keywords: octopress, iOS, Database, Swift, Objective-c, SQLite
categories: Database
description: SQLite_教程(一)
---

#### 简介：
SQLite 是一个软件库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。SQLite 是在世界上最广泛部署的 SQL 数据库引擎。SQLite 源代码不受版权限制。
本教程将告诉您如何使用 SQLite 编程，并让你迅速上手。

#### 什么是 SQLite？
SQLite是一个进程内的库，实现了自给自足的、无服务器的、零配置的、事务性的 SQL 数据库引擎。它是一个零配置的数据库，这意味着与其他数据库一样，您不需要在系统中配置。
就像其他数据库，SQLite 引擎不是一个独立的进程，可以按应用程序需求进行静态或动态连接。SQLite 直接访问其存储文件

#### 为什么要用 SQLite？
* 不需要一个单独的服务器进程或操作的系统（无服务器的）。
* SQLite 不需要配置，这意味着不需要安装或管理。
* 一个完整的 SQLite 数据库是存储在一个单一的跨平台的磁盘文件。
* SQLite 是非常小的，是轻量级的，完全配置时小于 400KiB，省略可选功能配置时小于250KiB。
* SQLite 是自给自足的，这意味着不需要任何外部的依赖。
* SQLite 事务是完全兼容 ACID 的，允许从多个进程或线程安全访问。
* SQLite 支持 SQL92（SQL2）标准的大多数查询语言的功能。
* SQLite 使用 ANSI-C 编写的，并提供了简单和易于使用的 API。
* SQLite 可在 UNIX（Linux, Mac OS-X, Android, iOS）和 Windows（Win32, WinCE, WinRT）中运行。

#### SQLite 命令
与关系数据库进行交互的标准 SQLite 命令类似于 SQL。命令包括 CREATE、SELECT、INSERT、UPDATE、DELETE 和 DROP。这些命令基于它们的操作性质可分为以下几种：

#### DDL - 数据定义语言
| 命令   | 描述 |
|-------|-----|
|CREATE	|创建一个新的表，一个表的视图，或者数据库中的其他对象。|
|ALTER	|修改数据库中的某个已有的数据库对象，比如一个表。     |
|DROP	|删除整个表，或者表的视图，或者数据库中的其他对象。   |

#### DML - 数据操作语言
| 命令   | 描述 |
|-------| -----|
|INSERT |创建一条记录。|
|UPDATE	|修改记录。      |
|DELETE	|删除记录。      |

#### DQL - 数据查询语言
| 命令   | 描述 |
|-------| -----|
|SELECT |	从一个或多个表中检索某些记录。|

#### 在 Mac OS X 上安装 SQLite
最新版本的 Mac OS X 会预安装 SQLite，但是如果没有可用的安装，只需按照如下步骤进行：
请访问 [SQLite 下载页面](http://www.sqlite.org/download.html)，从源代码区下载 sqlite-autoconf-*.tar.gz。
步骤如下：

	$tar xvfz sqlite-autoconf-3071502.tar.gz
	$cd sqlite-autoconf-3071502
	$./configure --prefix=/usr/local
	$make
	$make install


上述步骤将在 Mac OS X 机器上安装 SQLite，您可以使用下列命令进行验证：

	$sqlite3
	SQLite version 3.7.15.2 2013-01-09 11:53:05
	Enter ".help" for instructions
	Enter SQL statements terminated with a ";"
	sqlite>

最后，在 SQLite 命令提示符下，使用 SQLite 命令做练习。

#### SQLite语法

**大小写敏感性**

有个重要的点值得注意，SQLite 是不区分大小写的，但也有一些命令是大小写敏感的，比如 GLOB 和 glob 在 SQLite 的语句中有不同的含义。

**注释**

SQLite 注释是附加的注释，可以在 SQLite 代码中添加注释以增加其可读性，他们可以出现在任何空白处，包括在表达式内和其他 SQL 语句的中间，但它们不能嵌套。
SQL 注释以两个连续的 "-" 字符（ASCII 0x2d）开始，并扩展至下一个换行符（ASCII 0x0a）或直到输入结束，以先到者为准。
您也可以使用 C 风格的注释，以 "/\*" 开始，并扩展至下一个 "*/" 字符对或直到输入结束，以先到者为准。SQLite的注释可以跨越多行。

#### SQLite 语句
所有的 SQLite 语句可以以任何关键字开始，如 SELECT、INSERT、UPDATE、DELETE、ALTER、DROP 等，所有的语句以分号（;）结束。

SQLite ANALYZE 语句：

	ANALYZE;
	or
	ANALYZE database_name;
	or
	ANALYZE database_name.table_name;

SQLite AND/OR 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  CONDITION-1 {AND|OR} CONDITION-2;

SQLite ALTER TABLE 语句：

	ALTER TABLE table_name ADD COLUMN column_def...;
	SQLite ALTER TABLE 语句（Rename）：
	ALTER TABLE table_name RENAME TO new_table_name;

SQLite ATTACH DATABASE 语句：

	ATTACH DATABASE 'DatabaseName' As 'Alias-Name';
	SQLite BEGIN TRANSACTION 语句：
	BEGIN;
	or
	BEGIN EXCLUSIVE TRANSACTION;

SQLite BETWEEN 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  column_name BETWEEN val-1 AND val-2;

SQLite COMMIT 语句：

	COMMIT;

SQLite CREATE INDEX 语句：

	CREATE INDEX index_name
	ON table_name ( column_name COLLATE NOCASE );

SQLite CREATE UNIQUE INDEX 语句：

	CREATE UNIQUE INDEX index_name
	ON table_name ( column1, column2,...columnN);

SQLite CREATE TABLE 语句：

	CREATE TABLE table_name(
	   column1 datatype,
	   column2 datatype,
	   column3 datatype,
	   .....
	   columnN datatype,
	   PRIMARY KEY( one or more columns )
	);

SQLite CREATE TRIGGER 语句：

	CREATE TRIGGER database_name.trigger_name
	BEFORE INSERT ON table_name FOR EACH ROW
	BEGIN
	   stmt1;
	   stmt2;
	   ....
	END;

SQLite CREATE VIEW 语句：

	CREATE VIEW database_name.view_name  AS
	SELECT statement....;

SQLite CREATE VIRTUAL TABLE 语句：

	CREATE VIRTUAL TABLE database_name.table_name USING weblog( access.log );
	or
	CREATE VIRTUAL TABLE database_name.table_name USING fts3( );

SQLite COMMIT TRANSACTION 语句：

	COMMIT;
	SQLite COUNT 子句：
	SELECT COUNT(column_name)
	FROM   table_name
	WHERE  CONDITION;

SQLite DELETE 语句：

	DELETE FROM table_name
	WHERE  {CONDITION};

SQLite DETACH DATABASE 语句：

	DETACH DATABASE 'Alias-Name';

SQLite DISTINCT 子句：

	SELECT DISTINCT column1, column2....columnN
	FROM   table_name;

SQLite DROP INDEX 语句：

	DROP INDEX database_name.index_name;

SQLite DROP TABLE 语句：

	DROP TABLE database_name.table_name;

SQLite DROP VIEW 语句：

	DROP INDEX database_name.view_name;

SQLite DROP TRIGGER 语句：

	DROP INDEX database_name.trigger_name;

SQLite EXISTS 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  column_name EXISTS (SELECT * FROM   table_name );

SQLite EXPLAIN 语句：

	EXPLAIN INSERT statement...;
	or
	EXPLAIN QUERY PLAN SELECT statement...;

SQLite GLOB 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  column_name GLOB { PATTERN };

SQLite GROUP BY 子句：

	SELECT SUM(column_name)
	FROM   table_name
	WHERE  CONDITION
	GROUP BY column_name;

SQLite HAVING 子句：

	SELECT SUM(column_name)
	FROM   table_name
	WHERE  CONDITION
	GROUP BY column_name
	HAVING (arithematic function condition);

SQLite INSERT INTO 语句：

	INSERT INTO table_name( column1, column2....columnN)
	VALUES ( value1, value2....valueN);

SQLite IN 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  column_name IN (val-1, val-2,...val-N);

SQLite Like 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  column_name LIKE { PATTERN };

SQLite NOT IN 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  column_name NOT IN (val-1, val-2,...val-N);

SQLite ORDER BY 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  CONDITION
	ORDER BY column_name {ASC|DESC};

SQLite PRAGMA 语句：

	PRAGMA pragma_name;

	For example:

	PRAGMA page_size;
	PRAGMA cache_size = 1024;
	PRAGMA table_info(table_name);

SQLite RELEASE SAVEPOINT 语句：

	RELEASE savepoint_name;

SQLite REINDEX 语句：

	REINDEX collation_name;
	REINDEX database_name.index_name;
	REINDEX database_name.table_name;

SQLite ROLLBACK 语句：

	ROLLBACK;
	or
	ROLLBACK TO SAVEPOINT savepoint_name;

SQLite SAVEPOINT 语句：

	SAVEPOINT savepoint_name;
	SQLite SELECT 语句：
	SELECT column1, column2....columnN
	FROM   table_name;

SQLite UPDATE 语句：

	UPDATE table_name
	SET column1 = value1, column2 = value2....columnN=valueN
	[ WHERE  CONDITION ];

SQLite VACUUM 语句：

	VACUUM;

SQLite WHERE 子句：

	SELECT column1, column2....columnN
	FROM   table_name
	WHERE  CONDITION;

资料整理于：[RUNOOB.COM-SQLite教程](http://www.runoob.com/sqlite/sqlite-tutorial.html)
转载请注明出处。
