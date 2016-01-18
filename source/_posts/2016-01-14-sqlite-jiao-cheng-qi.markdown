---
layout: post
title: "SQLite_教程(七)"
date: 2016-01-14 01:46:57 +0800
comments: true
tags: [iOS, SQLite, Database, Octopress]
keywords: Octopress, iOS, Database, Swift, Objective-c, SQLite
categories: Database
description: SQLite_教程(七)
---

#### SQLite Order By

SQLite 的 **ORDER BY** 子句是用来基于一个或多个列按升序或降序顺序排列数据。

---

**语法**

ORDER BY 子句的基本语法如下：

	SELECT column-list
	FROM table_name
	[WHERE condition]
	[ORDER BY column1, column2, .. columnN] [ASC | DESC];

您可以在 ORDER BY 子句中使用多个列。确保您使用的排序列在列清单中。

**实例**

假设 COMPANY 表有以下记录：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	3           Teddy       23          Norway      20000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0
	6           Kim         22          South-Hall  45000.0
	7           James       24          Houston     10000.0

下面是一个实例，它会将结果按 SALARY 升序排序：

	sqlite> SELECT * FROM COMPANY ORDER BY SALARY ASC;

这将产生以下结果：

		ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	7           James       24          Houston     10000.0
	2           Allen       25          Texas       15000.0
	1           Paul        32          California  20000.0
	3           Teddy       23          Norway      20000.0
	6           Kim         22          South-Hall  45000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0

下面是一个实例，它会将结果按 NAME 和 SALARY 升序排序：

	sqlite> SELECT * FROM COMPANY ORDER BY NAME, SALARY ASC;

这将产生以下结果：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	2           Allen       25          Texas       15000.0
	5           David       27          Texas       85000.0
	7           James       24          Houston     10000.0
	6           Kim         22          South-Hall  45000.0
	4           Mark        25          Rich-Mond   65000.0
	1           Paul        32          California  20000.0
	3           Teddy       23          Norway      20000.0

下面是一个实例，它会将结果按 NAME 降序排序：

	sqlite> SELECT * FROM COMPANY ORDER BY NAME DESC;

这将产生以下结果：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	3           Teddy       23          Norway      20000.0
	1           Paul        32          California  20000.0
	4           Mark        25          Rich-Mond   65000.0
	6           Kim         22          South-Hall  45000.0
	7           James       24          Houston     10000.0
	5           David       27          Texas       85000.0
	2           Allen       25          Texas       15000.0


#### SQLite Group By

SQLite 的 **GROUP BY** 子句用于与 SELECT 语句一起使用，来对相同的数据进行分组。
在 SELECT 语句中，GROUP BY 子句放在 WHERE 子句之后，放在 ORDER BY 子句之前。

---

**语法**


下面给出了 GROUP BY 子句的基本语法。GROUP BY 子句必须放在 WHERE 子句中的条件之后，必须放在 ORDER BY 子句之前。

	SELECT column-list
	FROM table_name
	WHERE [ conditions ]
	GROUP BY column1, column2....columnN
	ORDER BY column1, column2....columnN

您可以在 GROUP BY 子句中使用多个列。确保您使用的分组列在列清单中。

**实例**

假设 COMPANY 表有以下记录：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	3           Teddy       23          Norway      20000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0
	6           Kim         22          South-Hall  45000.0
	7           James       24          Houston     10000.0

如果您想了解每个客户的工资总额，则可使用 GROUP BY 查询，如下所示：

	sqlite> SELECT NAME, SUM(SALARY) FROM COMPANY GROUP BY NAME;

这将产生以下结果：

	NAME        SUM(SALARY)
	----------  -----------
	Allen       15000
	David       85000
	James       20000
	Kim         45000
	Mark        65000
	Paul        40000
	Teddy       20000

让我们把 ORDER BY 子句与 GROUP BY 子句一起使用，如下所示：

	sqlite>  SELECT NAME, SUM(SALARY)
	         FROM COMPANY GROUP BY NAME ORDER BY NAME DESC;

这将产生以下结果：

	NAME        SUM(SALARY)
	----------  -----------
	Teddy       20000
	Paul        40000
	Mark        65000
	Kim         45000
	James       20000
	David       85000
	Allen       15000

#### SQLite Having 子句
HAVING 子句允许指定条件来过滤将出现在最终结果中的分组结果。

WHERE 子句在所选列上设置条件，而 HAVING 子句则在由 GROUP BY 子句创建的分组上设置条件。

---

**语法**

下面是 HAVING 子句在 SELECT 查询中的位置：

	SELECT
	FROM
	WHERE
	GROUP BY
	HAVING
	ORDER BY

在一个查询中，HAVING 子句必须放在 GROUP BY 子句之后，必须放在 ORDER BY 子句之前。下面是包含 HAVING 子句的 SELECT 语句的语法：

	SELECT column1, column2
	FROM table1, table2
	WHERE [ conditions ]
	GROUP BY column1, column2
	HAVING [ conditions ]
	ORDER BY column1, column2

**实例**

假设 COMPANY 表有以下记录：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	3           Teddy       23          Norway      20000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0
	6           Kim         22          South-Hall  45000.0
	7           James       24          Houston     10000.0
	8           Paul        24          Houston     20000.0
	9           James       44          Norway      5000.0
	10          James       45          Texas       5000.0

下面是一个实例，它将显示名称计数小于 2 的所有记录：

	sqlite > SELECT * FROM COMPANY GROUP BY name HAVING count(name) < 2;

这将产生以下结果：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	2           Allen       25          Texas       15000
	5           David       27          Texas       85000
	6           Kim         22          South-Hall  45000
	4           Mark        25          Rich-Mond   65000
	3           Teddy       23          Norway      20000

下面是一个实例，它将显示名称计数大于 2 的所有记录,注意其实这里只返回最后一条记录：

	sqlite > SELECT * FROM COMPANY GROUP BY name HAVING count(name) > 2;

这将产生以下结果：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	10          James       45          Texas       5000


#### SQLite Distinct 关键字
SQLite 的 **DISTINCT** 关键字与 SELECT 语句一起使用，来消除所有重复的记录，并只获取唯一一次记录。
有可能出现一种情况，在一个表中有多个重复的记录。当提取这样的记录时，DISTINCT 关键字就显得特别有意义，它只获取唯一一次记录，而不是获取重复记录。

---
**语法**

用于消除重复记录的 DISTINCT 关键字的基本语法如下：

	SELECT DISTINCT column1, column2,.....columnN
	FROM table_name
	WHERE [condition]

**实例**

假设 COMPANY 表有以下记录：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	3           Teddy       23          Norway      20000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0
	6           Kim         22          South-Hall  45000.0
	7           James       24          Houston     10000.0
	8           Paul        24          Houston     20000.0
	9           James       44          Norway      5000.0
	10          James       45          Texas       5000.0

首先，让我们来看看下面的 SELECT 查询，它将返回重复的工资记录：

	sqlite> SELECT name FROM COMPANY;

这将产生以下结果：

	NAME
	----------
	Paul
	Allen
	Teddy
	Mark
	David
	Kim
	James
	Paul
	James
	James

现在，让我们在上述的 SELECT 查询中使用 DISTINCT 关键字：

	sqlite> SELECT DISTINCT name FROM COMPANY;

这将产生以下结果，没有任何重复的条目：

	NAME
	----------
	Paul
	Allen
	Teddy
	Mark
	David
	Kim
	James

资料整理于：[RUNOOB.COM-SQLite教程](http://www.runoob.com/sqlite/sqlite-tutorial.html)
转载请注明出处。
