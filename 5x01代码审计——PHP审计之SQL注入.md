# 一、代码审计流程

![1.png](img/PHPCode/SQLInjection/1.png)

![2.png](img/PHPCode/SQLInjection/2.png)

![3.png](img/PHPCode/SQLInjection/3.png)

![4.png](img/PHPCode/SQLInjection/4.png)

# 二、PHP中的数据库操作函数
1. **mysqli_connect()**：用于连接MySQL数据库。 
	- 语法
	```PHP
	mysqli_connect(servername, username, password, dbname, port, socket)
	```
	- 参数说明
	```PHP
	- servername ：可选。要连接的MySQL服务器地址，默认为"localhost"。 
	- username ：可选。MySQL服务器的用户名，默认为当前用户。 
	- password ：可选。MySQL服务器的密码，默认为空。 
	- dbname ：可选。要连接的MySQL数据库名，默认为空。 
	- port ：可选。MySQL服务器的端口号，默认为3306。 
	- socket ：可选。MySQL服务器的socket文件路径，默认为ini配置文件中的mysql.default_socket。 
	- 返回值： 一个成功连接到MySQL服务器的mysqli对象。
	```
	- 示例
	```PHP
	$mysqli = new mysqli("localhost", "username", "password", "dbname", 3306);
	 // 面向过程风格
	$mysqli = mysqli_connect("localhost", "username", "password", "dbname", 3306);
	if (!$mysqli) {
	    die("连接失败：" . mysqli_connect_error());
	}
	```
2. **mysqli_query()**：用于执行SQL查询语句，查询语句可以是SELECT、INSERT、UPDATE、DELETE等类型。 
	- 语法
	```PHP
	mysqli_query(connection, query, resultmode)
	```
	- 参数说明
	```PHP
	- connection ：必须。连接到MySQL服务器的 `mysqli` 对象。 
	- query ：必须。要执行的SQL查询语句。 
	- resultmode ：可选。查询结果的返回类型，默认为 `MYSQLI_STORE_RESULT` 。其他可选值包括 `MYSQLI_USE_RESULT` 、 `MYSQLI_ASYNC` 等。 
	- 返回值：  如果查询成功，返回一个结果集对象。  如果查询失败，返回FALSE。
	```
	- 示例
	```PHP
	$conn=mysqli_connect("localhost","my_user","my_password","my_database");
	// 面向对象风格
	$result=$conn->query("SELECT * FROM my_table");
	if($result === false){
	    die('查询失败：'.$conn->error);
	}
	while($row=$result->fetch_assoc()){
	    echo $row['name']." ".$row['age']."<br>";
	}
	 // 面向过程风格
	$result=mysqli_query($conn,"SELECT * FROM my_table");
	if($result === false){
	    die('查询失败：'.mysqli_error($conn));
	}
	while($row=mysqli_fetch_assoc($result)){
	    echo $row['name']." ".$row['age']."<br>";
	}
	```
3. **mysqli_fetch_array()**：用于获取查询结果。 
	- 语法
	```PHP
	mysqli_fetch_array(result, resulttype)
	```
	- 参数说明
	```PHP
	- result ：必须。要获取行数据的结果集对象。 
	- resulttype ：可选。指定返回的数组类型，有 `MYSQLI_ASSOC` 、 `MYSQLI_NUM` 、 `MYSQLI_BOTH` 三种类型。默认为 `MYSQLI_BOTH` ，返回一个包含关联数组和索引数组的数组。 
	- 返回值： 如果结果集中还有行数据，返回一个包含行数据的数组。 如果结果集中所有数据都已经获取完毕，返回NULL。
	```
	- 示例
	```PHP
	$conn=mysqli_connect("localhost","my_user","my_password","my_database");
	$result=mysqli_query($conn,"SELECT * FROM my_table");
	while($row=mysqli_fetch_array($result,MYSQLI_ASSOC)){
	    echo $row['name']." ".$row['age']."<br>";
	}
	```
4. **mysqli_real_escape_string()**：用于转义查询语句中的特殊字符，以防止SQL注入攻击。 
	- 语法
	```PHP
	mysqli_real_escape_string(connection,escapestring)
	```
	- 参数说明
	```PHP
	- connection ：必须。连接到MySQL服务器的 mysqli对象。 
	- escapestring ：必须。需要转义的字符串。 
	- 返回值： 返回安全的SQL字符串，可以使用在SQL语句中
	```
	- 示例
	```PHP
	$conn=mysqli_connect("localhost","my_user","my_password","my_database");
	$str="It's a beautiful day.";
	$str=mysqli_real_escape_string($conn,$str);
	$sql="INSERT INTO my_table (name) VALUES ('$str')";
	$result=mysqli_query($conn,$sql);
	```
5. **mysqli_prepare()**：用于准备预处理语句，以防止SQL注入攻击。 
	- 语法
	```PHP
	mysqli_prepare(connection,query)
	```
	- 参数说明
	```PHP
	- connection ：必需。连接到MySQL服务器的 mysqli对象。 
	- query ：必需。需要进行预处理的SQL语句。 
	- 返回值： 如果预处理成功，返回一个 mysqli_stmt对象，否则返回FALSE。
	```
	- 示例
	```PHP
	$conn=mysqli_connect("localhost","my_user","my_password","my_database");
	$stmt=mysqli_prepare($conn,"INSERT INTO my_table (name,age) VALUES (?,?)");
	mysqli_stmt_bind_param($stmt,"si",$name,$age);
	$name="John";
	$age=25;
	mysqli_stmt_execute($stmt);
	```
6. **mysqli_bind_param()**：用于绑定参数，以防止SQL注入攻击，PHP中用于为预处理语句绑定参数的函数，它可以将PHP变量和MySQL参数进行绑定，将预处理语句中的占位符替换成具体的值。
	- 语法
	```PHP
	mysqli_stmt_bind_param(stmt,types,param1,param2,...)
	```
	- 参数说明
	```PHP
	- stmt ：必需。预处理语句的 mysqli_stmt对象。 
	- types ：必需。参数类型字符串，用于指定绑定参数的数据类型。 
	- param1 、 param2 、...：必需。预处理语句中需要绑定的参数值。 
	- 返回值： 如果绑定成功，返回TRUE，否则返回FALSE。 参数类型字符串说明： 参数类型字符串用于指定绑定参数的数据类型，有以下几种取值： 
		- i：整型。 
		- d：双精度型。 
		- s：字符串型。 
		- b：二进制型。 参数类型字符串中每个字符对应一个参数，字符的排序必须与参数的排序一致。
	```
	- 示例
	```PHP
	$conn=mysqli_connect("localhost","my_user","my_password","my_database");
	$stmt=mysqli_prepare($conn,"INSERT INTO my_table (name,age) VALUES (?,?)");
	mysqli_stmt_bind_param($stmt,"si",$name,$age);
	$name="John";
	$age=25;
	mysqli_stmt_execute($stmt);
	```
7. **mysqli_stmt_execute()**：PHP中用于执行预处理语句的函数，它可以执行由 `mysqli_prepare()` 函数准备的预处理语句。 
	- 语法
	```PHP
	mysqli_stmt_execute(stmt)
	```
	- 参数说明
	```PHP
	- stmt ：必需。预处理语句的 mysqli_stmt对象。 
	- 返回值： 如果执行成功，返回TRUE，否则返回FALSE。
	```
	- 示例
	```PHP
	$conn=mysqli_connect("localhost","my_user","my_password","my_database");
	$stmt=mysqli_prepare($conn,"INSERT INTO my_table (name,age) VALUES (?,?)");
	mysqli_stmt_bind_param($stmt,"si",$name,$age);
	$name="John";
	$age=25;
	mysqli_stmt_execute($stmt);
	```
8. **mysqli_close()**：用于关闭与数据库的连接。
9. **mysql_query()**：用于执行MySQL查询语句。已经过时被弃用。 
	- 语法
	```PHP
	mysql_query(query,result)
	```
	- 参数说明
	```PHP
	- query ：必需。要执行的SQL查询语句。 
	- result ：可选。连接标识符，如果省略，则使用上一次打开的连接。 
	- 返回值： 如果执行成功，返回一个资源类型的结果集对象（ resource ），否则返回FALSE。
	```
	- 示例
	```PHP
	$conn=mysql_connect("localhost","my_user","my_password");
	mysql_select_db("my_database",$conn);
	$result=mysql_query("SELECT * FROM my_table");
	while($row=mysql_fetch_array($result))
	{
	    echo $row['name']." ".$row['age'];
	}
	```
10. **mysql_db_query()**：用于执行MySQL查询语句。已经过时被弃用。 
	- 语法
	```PHP
	mysql_db_query(database,query,result)
	```
	- 参数说明
	```PHP
	- database ：必需。要连接的MySQL数据库名称。 
	- query ：必需。要执行的SQL查询语句。 
	- result ：可选。连接标识符，如果省略，则使用上一次打开的连接。 
	- 返回值： 如果执行成功，返回一个资源类型的结果集对象（ resource ），否则返回FALSE。
	```
	- 示例
	```PHP
	$conn=mysql_connect("localhost","my_user","my_password");
	mysql_select_db("my_database",$conn);
	$result=mysql_db_query("my_database","SELECT * FROM my_table");
	while($row=mysql_fetch_array($result))
	{
	    echo $row['name']." ".$row['age'];
	}
	```
11. **mysql_connect()**：用于连接MySQL数据库。已经过时被弃用。 
	- 语法
	```PHP
	mysql_connect(servername,username,password,new_link,client_flag)
	```
	- 参数说明
	```PHP
	- servername ：可选。MySQL数据库服务器的主机名或IP地址，如果省略，则使用默认值 localhost 。 
	- username ：可选。连接MySQL数据库使用的用户名，如果省略，则使用默认值root。 
	- password ：可选。连接MySQL数据库使用的密码，如果省略，则使用空密码。 
	- new_link ：可选。如果该参数为TRUE，则会打开一个新的数据库连接，否则会使用现有的连接（如果有）。默认值为FALSE。 
	- client_flag ：可选。MySQL连接的客户端标志。默认值为0。 
	- 返回值： 如果连接成功，则返回一个数据库连接标识符，如果连接失败，则返回FALSE。
	```
	- 示例
	```PHP
	$conn=mysql_connect("localhost","my_user","my_password");
	if(!$conn)
	{
	    die("Could not connect: " . mysql_error());
	}
	echo "Connected successfully";
	mysql_close($conn);
	```
12. **mysql_pconnect()**：用于创建持久化MySQL连接。已经过时被弃用。与 `mysql_connect()` 函数不同的是， `mysql_pconnect()` 函数打开一个到MySQL服务器的持久连接，如果该连接已经存在，则使用该连接。这个函数已经被废弃，在PHP7.0.0版本中被删除，建议使用 `mysqli` 或 `PDO` 扩展
	- 语法
	```PHP
	mysql_pconnect(servername,username,password,client_flag)
	```
	- 参数说明
	```PHP
	- servername ：可选。MySQL数据库服务器的主机名或IP地址，如果省略，则使用默认值 localhost 。 
	- username ：可选。连接MySQL数据库使用的用户名，如果省略，则使用默认值root。 
	- password ：可选。连接MySQL数据库使用的密码，如果省略，则使用空密码。 
	- client_flag ：可选。MySQL连接的客户端标志。默认值为0。 
	- 返回值： 如果连接成功，则返回一个数据库连接标识符，如果连接失败，则返回FALSE。
	```
	- 示例
	```PHP
	$conn=mysql_pconnect("localhost","my_user","my_password");
	if(!$conn)
	{
	    die("Could not connect: " . mysql_error());
	}
	echo "Connected successfully";
	mysql_close($conn);
	```
13. **mysql_unbuffered_query()**：用于执行MySQL查询语句，并将结果集存储在服务器上。已经过时被弃用。用于执行非缓存的MySQL查询的函数，它与 `mysql_query()` 函数的主要区别在于查询结果不会被缓存到PHP内存中，而是直接从MySQL服务器流式传输。这个函数已经被废弃，在PHP7.0.0版本中被删除
	- 语法
	```PHP
	mysql_unbuffered_query(query,connection)
	```
	- 参数说明
	```PHP
	- query ：必需。需要执行的SQL查询语句。 
	- connection ：可选。与MySQL服务器建立连接的标识符，如果省略，则使用上一个MySQL连接。 
	- 返回值： 如果查询成功，则 mysql_unbuffered_query() 函数返回一个结果集标示符，否则返回FALSE。
	```
	- 示例
	```PHP
	$result=mysql_unbuffered_query("SELECT * FROM my_table");
	if(!$result)
	{
	    die("Could not execute query: " . mysql_error());
	}
	while($row=mysql_fetch_array($result))
	{
	    echo $row['name']." ".$row['age']."<br/>";
	}
	```
# 三、PHP代码审计之SQL注入案例

## 审计发现方法

1. 使用正则表达式搜索`(update|select|insert|delete|).*?where.*=\`，或者直接全家搜索SQL操作语句，查看有哪些文件、函数进行了数据库交互操作
2. 跟踪找出来的函数，看有没有可控参数传入
3. 若存在可控参数传入，则跟踪查看是否对传入的参数进行了过滤

## bluecmsSQL注入

1. 使用PhpStorm载入cms，使用正则表达式在项目中搜索sql语句关键字，如下所示

   ``` sql
   (update|select|insert|delete|).*?where.*=
   ```

   ![5.png](img/PHPCode/SQLInjection/5.png)

2. 在匹配出来的结果中，逐个点击查看是否存在变量，优先查看变量写在where后的SQL语句，写在order by后的SQL可能注入不下去，选择第二个结果，存在变量`$ad_id`的语句，如上图所示，在下方可以直接看到具体代码，发现sql语句传递给了getone函数，转到getone()的声明，查看是否存在过滤

   ![6.png](img/PHPCode/SQLInjection/6.png)

3. 如上图，getone()中并没有进行过滤，sql语句被传递给了query()进行执行操作，再转到query()查看是否有过滤

   ![7.png](img/PHPCode/SQLInjection/7.png)

4. query()中也没有过滤，sql语句由query执行后，执行结果由dbshow()方法进行显示，转到dbshow方法查看是否有过滤

   ![8.png](img/PHPCode/SQLInjection/8.png)

5. 可以看到，整个链中所有调用的函数都没有对sql语句进行过滤，访问`ad_js.php?ad_id=`

   ![9.png](img/PHPCode/SQLInjection/9.png)

   ![10.png](img/PHPCode/SQLInjection/10.png)

6. 如上图，存在注入，但是没有回显，使用sqlmap进行测试，如下所示

   ```shell
   python3 sqlmap.py -u "http://www.bluecms.com:8080/ad_js.php?ad_id=1" --batch
   ```

   ![11.png](img/PHPCode/SQLInjection/11.png)

## 梦想CMS（lmxcms）后台SQL注入——CNVD-2020-59466

1. cnvd漏洞详情

   ![12.png](img/PHPCode/SQLInjection/12.png)

2. 该漏洞出现在后台，且该CMS使用MVC设计模式，先登录后台。可以看到后台的URL是admin.php?m=Index&a=index，这就是典型的MVC设计模式，即Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写，是一种软件设计典范，将M和V的实现代码分离，从而使同一个程序可以使用不同的表现形式。这里m是model，a就是方法的意思。

   ![13.png](img/PHPCode/SQLInjection/13.png)

3. 查看一下后台中文件名吻合的文件，只有BookAction.calss.php这一个文件符合要求

   ![14.png](img/PHPCode/SQLInjection/14.png)

4. 点击BookAction.calss.php，查看类文件中传入了变量的函数

   ![15.png](img/PHPCode/SQLInjection/15.png)

5. 只有这个回复留言的reply()函数中传入了变量id，但是reply()中没有sql语句，id变量被传入了getReply()中，转到getReply()中看一下源代码

   ![16.png](img/PHPCode/SQLInjection/16.png)

6. 同样没有sql语句，但是返回了一个selectModel()，id变量又被传入了selectModel()中，继续转到selectModel()

   ![17.png](img/PHPCode/SQLInjection/17.png)

7. selectModel()也没有sql语句，但是返回了一个selectDB()，转到selectDB()

   ![18.png](img/PHPCode/SQLInjection/18.png)

8. selectDB()中出现了sql语句，id变量最终被传递到了selectDB函数中，但selectModel()和selectDB都是被reply()方法所调用的，所以只需要访问reply方法，将id变量传递给reply方法，即可实现sql操作

   ![19.png](img/PHPCode/SQLInjection/19.png)

   ![20.png](img/PHPCode/SQLInjection/20.png)

9. 测试发现该注入点为`)`注入，为了方便测试，将selectDB中的sql语句输出

   ![21.png](img/PHPCode/SQLInjection/21.png)

10. 使用报错注入，查询数据库用户名，如下，注入成功

    ``` sql
    id=1)%20and%20updatexml(0,concat(0x7e,user()),1)%23
    ```

    ![22.png](img/PHPCode/SQLInjection/22.png)

## 梦想CMS（lmxcms）前台SQL注入——CNVD-2019-05674

1. 漏洞详情

   ![23.png](img/PHPCode/SQLInjection/23.png)

2. 根据描述，找到存在注入的文件，文件路径为`c/index/TagsAction.class.php`，寻找传入了变量的方法，刻意可以看到有一个data变量，data变量的值等于p()方法的值，下面还有一个urldecode转码的操作

   ![24.png](img/PHPCode/SQLInjection/24.png)

3. 转到p()看一下，如下，p()是一个表单验证，data变量get数据、有转义、验证非法字符，也就是说这里存在过滤，过滤了单双引号、反斜杠和null。sql语句被传入了filter_sql()方法，该方法使用正则表达式进行匹配，过滤了大小写、count、create、delete、select、update、use、drop、insert、info、from

   ![25.png](img/PHPCode/SQLInjection/25.png)

   ![26.png](img/PHPCode/SQLInjection/26.png)

   ![27.png](img/PHPCode/SQLInjection/27.png)

4. 常见的关键字都被过滤了，但是TagsAction类中有一个urldecode，可以对传入数据进行解码，攻击者将注入语句进行一次编码后，通过浏览器提交到服务器的时候，浏览器会自动将转码成URL编码数据进行一次解码，因此，将注入语句进行一重URL加密，并不能绕过，如果进行双重加密，则浏览器解码后的数据仍是经过了一重URL加密的语句，因此过滤函数无法匹配到非法字符，然后语句再经过urldecode解码后送入数据库，就造成了SQL注入

5. 寻找sql语句getNameData->oneModel->oneDB

   ![28.png](img/PHPCode/SQLInjection/28.png)

6. 将上图中的sql语句输出一下，构造注入语句，进行双重URL加密，

   ``` sql
   ?m=Tags&name=%25%33%31%25%32%37%25%32%30%25%36%31%25%36%65%25%36%34%25%32%30%25%37%35%25%37%30%25%36%34%25%36%31%25%37%34%25%36%35%25%37%38%25%36%64%25%36%63%25%32%38%25%33%30%25%32%63%25%36%33%25%36%66%25%36%65%25%36%33%25%36%31%25%37%34%25%32%38%25%33%30%25%37%38%25%33%37%25%36%35%25%32%63%25%37%35%25%37%33%25%36%35%25%37%32%25%32%38%25%32%39%25%32%39%25%32%63%25%33%31%25%32%39%25%32%33
   ```

7. 如下所示，注入成功

   ![29.png](img/PHPCode/SQLInjection/29.png)