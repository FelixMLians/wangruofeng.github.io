---
layout: post
title: "SQLite_教程(五)"
date: 2016-01-14 01:35:50 +0800
comments: true
tags: [iOS, SQLite, Database, Octopress]
keywords: Octopress, iOS, Database, Swift, Objective-c, SQLite
categories: Database
description: SQLite_教程(五)
---

#### SQL表达式
表达式是一个或多个值、运算符和计算值的SQL函数的组合。

SQL 表达式与公式类似，都写在查询语言中。您还可以使用特定的数据集来查询数据库。

---

**语法**

假设 SELECT 语句的基本语法如下：

	SELECT column1, column2, columnN
	FROM table_name
	WHERE [CONTION | EXPRESSION];

有不同类型的 SQLite 表达式，具体讲解如下：

#### SQLite - 布尔表达式
SQLite 的布尔表达式在匹配单个值的基础上获取数据。语法如下：

	SELECT column1, column2, columnN
	FROM table_name
	WHERE SINGLE VALUE MATCHTING EXPRESSION;

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

下面的实例演示了 SQLite 布尔表达式的用法：

	sqlite> SELECT * FROM COMPANY WHERE SALARY = 10000;
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	4           James        24          Houston   10000.0

#### SQLite - 数值表达式
这些表达式用来执行查询中的任何数学运算。语法如下：

	SELECT numerical_expression as  OPERATION_NAME
	[FROM table_name WHERE CONDITION] ;

在这里，numerical_expression 用于数学表达式或任何公式。下面的实例演示了 SQLite 数值表达式的用法：

	sqlite> SELECT (15 + 6) AS ADDITION
	ADDITION = 21

有几个内置的函数，比如 avg()、sum()、count()，等等，执行被称为对一个表或一个特定的表列的汇总数据计算。

	sqlite> SELECT COUNT(*) AS "RECORDS" FROM COMPANY;
	RECORDS = 7

---

#### SQLite - 日期表达式
日期表达式返回当前系统日期和时间值，这些表达式将被用于各种数据操作。

	sqlite>  SELECT CURRENT_TIMESTAMP;
	CURRENT_TIMESTAMP = 2016-01-13 16:29:56

#### SQLite Where 子句
SQLite的 WHERE 子句用于指定从一个表或多个表中获取数据的条件。

如果满足给定的条件，即为真（true）时，则从表中返回特定的值。您可以使用 WHERE 子句来过滤记录，只获取需要的记录。

WHERE 子句不仅可用在 SELECT 语句中，它也可用在 UPDATE、DELETE 语句中，等等，这些我们将在随后的章节中学习到。

---

**语法**

SQLite 的带有 WHERE 子句的 SELECT 语句的基本语法如下：

	SELECT column1, column2, columnN
	FROM table_name
	WHERE [condition]

**实例**

您还可以使用比较或逻辑运算符指定条件，比如 >、<、=、LIKE、NOT，等等。假设 COMPANY 表有以下记录：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	3           Teddy       23          Norway      20000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0
	6           Kim         22          South-Hall  45000.0
	7           James       24          Houston     10000.0

下面的实例演示了 SQLite 逻辑运算符的用法。下面的 SELECT 语句列出了 AGE 大于等于 25 且工资大于等于 65000.00 的所有记录：

	sqlite> SELECT * FROM COMPANY WHERE AGE >= 25 AND SALARY >= 65000;
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0

下面的 SELECT 语句列出了 AGE 大于等于 25 或工资大于等于 65000.00 的所有记录：

sqlite> SELECT * FROM COMPANY WHERE AGE >= 25 OR SALARY >= 65000;

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0

下面的 SELECT 语句列出了 AGE 不为 NULL 的所有记录，结果显示所有的记录，意味着没有一个记录的 AGE 等于 NULL：

	sqlite>  SELECT * FROM COMPANY WHERE AGE IS NOT NULL;
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	3           Teddy       23          Norway      20000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0
	6           Kim         22          South-Hall  45000.0
	7           James       24          Houston     10000.0

下面的 SELECT 语句列出了 NAME 以 'Ki' 开始的所有记录，'Ki' 之后的字符不做限制（不区分大小写）：

	sqlite> SELECT * FROM COMPANY WHERE NAME LIKE 'Ki%';
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	6           Kim         22          South-Hall  45000.0

下面的 SELECT 语句列出了 NAME 以 'Ki' 开始的所有记录，'Ki' 之后的字符不做限制（区分大小写）：

	sqlite> SELECT * FROM COMPANY WHERE NAME GLOB 'Ki*';
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	6           Kim         22          South-Hall  45000.0

下面的 SELECT 语句列出了 AGE 的值为 25 或 27 的所有记录：

	sqlite> SELECT * FROM COMPANY WHERE AGE IN ( 25, 27 );
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	2           Allen       25          Texas       15000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0


#### SQLite AND/OR 运算符
SQLite 的 AND 和 OR 运算符用于编译多个条件来缩小在 SQLite 语句中所选的数据。这两个运算符被称为连接运算符。

这些运算符为同一个 SQLite 语句中不同的运算符之间的多个比较提供了可能。

---

#### AND 运算符
AND 运算符允许在一个 SQL 语句的 WHERE 子句中的多个条件的存在。使用 AND 运算符时，只有当所有条件都为真（true）时，整个条件为真（true）。例如，只有当 condition1 和 condition2 都为真（true）时，[condition1] AND [condition2] 为真（true）。

**语法**

带有 WHERE 子句的 AND 运算符的基本语法如下：

	SELECT column1, column2, columnN
	FROM table_name
	WHERE [condition1] AND [condition2]...AND [conditionN];

您可以使用 AND 运算符来结合 N 个数量的条件。SQLite 语句需要执行的动作是，无论是事务或查询，所有由 AND 分隔的条件都必须为真（TRUE）。

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

下面的 SELECT 语句列出了 AGE 大于等于 25 且工资大于等于 65000.00 的所有记录：


	sqlite> SELECT * FROM COMPANY WHERE AGE >= 25 AND SALARY >= 65000;
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0

#### OR 运算符
OR 运算符也用于结合一个 SQL 语句的 WHERE 子句中的多个条件。使用 OR 运算符时，只要当条件中任何一个为真（true）时，整个条件为真（true）。例如，只要当 condition1 或 condition2 有一个为真（true）时，[condition1] OR [condition2] 为真（true）

**语法**

带有 WHERE 子句的 OR 运算符的基本语法如下：

	SELECT column1, column2, columnN
	FROM table_name
	WHERE [condition1] OR [condition2]...OR [conditionN]

您可以使用 OR 运算符来结合 N 个数量的条件。SQLite 语句需要执行的动作是，无论是事务或查询，只要任何一个由 OR 分隔的条件为真（TRUE）即可。

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

下面的 SELECT 语句列出了 AGE 大于等于 25 或工资大于等于 65000.00 的所有记录：

	sqlite> SELECT * FROM COMPANY WHERE AGE >= 25 OR SALARY >= 65000;
	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0


#### SQLite Update 语句
SQLite 的 **UPDATE **查询用于修改表中已有的记录。可以使用带有 WHERE 子句的 UPDATE 查询来更新选定行，否则所有的行都会被更新。

---

**语法**

带有 WHERE 子句的 UPDATE 查询的基本语法如下：

	UPDATE table_name
	SET column1 = value1, column2 = value2...., columnN = valueN
	WHERE [condition];

您可以使用 AND 或 OR 运算符来结合 N 个数量的条件。

---

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

下面是一个实例，它会更新 ID 为 6 的客户地址：

	sqlite> UPDATE COMPANY SET ADDRESS = 'Texas' WHERE ID = 6;

现在，COMPANY 表有以下记录：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          California  20000.0
	2           Allen       25          Texas       15000.0
	3           Teddy       23          Norway      20000.0
	4           Mark        25          Rich-Mond   65000.0
	5           David       27          Texas       85000.0
	6           Kim         22          Texas       45000.0
	7           James       24          Houston     10000.0

如果您想修改 COMPANY 表中 ADDRESS 和 SALARY 列的所有值，则不需要使用 WHERE 子句，UPDATE 查询如下：

	sqlite> UPDATE COMPANY SET ADDRESS = 'Texas', SALARY = 20000.00;

现在，COMPANY 表有以下记录：

	ID          NAME        AGE         ADDRESS     SALARY
	----------  ----------  ----------  ----------  ----------
	1           Paul        32          Texas       20000.0
	2           Allen       25          Texas       20000.0
	3           Teddy       23          Texas       20000.0
	4           Mark        25          Texas       20000.0
	5           David       27          Texas       20000.0
	6           Kim         22          Texas       20000.0
	7           James       24          Texas       20000.0
资料整理于：[RUNOOB.COM-SQLite教程](http://www.runoob.com/sqlite/sqlite-tutorial.html)
转载请注明出处。
