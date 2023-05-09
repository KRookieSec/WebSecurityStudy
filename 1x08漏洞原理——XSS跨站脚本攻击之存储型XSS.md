# 一、存储型XSS概述
1. 存储型XSS是一种XSS攻击类型，攻击者将恶意脚本注入到Web应用程序的数据库中，并在其他用户尝试访问相同页面时将其注入到页面中。当用户访问被注入脚本的页面时，该脚本将在其浏览器中执行。
2. 存储型XSS攻击最常见的目标是表单和评论字段，因为它们允许用户输入数据，并且这些数据通常没有得到充分的验证和清理。
3. 为了防止存储型XSS攻击，重要的是要对所有用户输入进行验证和清理，并对其进行适当的编码，以确保任何恶意脚本都被禁止执行。
# 二、代码示例
1. 使用PHP编写一个存在存储型XSS漏洞的代码示例
```PHP
/ 获取用户评论
$comment = $_POST['comment'];
// 存储评论到数据库
$servername = 'localhost';
$username = 'root';
$password = '';
$dbname = 'test';
$conn = mysqli_connect($servername, $username, $password, $dbname);
if (!$conn) {
	die("连接失败: " . mysqli_connect_error());
}
$sql = "INSERT INTO comments (comment) VALUES ('$comment')";
mysqli_query($conn, $sql);
mysqli_close($conn);
// 将评论显示在页面上
echo "<h1>评论</h1>";
echo $comment;
```
2. 在这个示例中，攻击者可以通过提交一个包含恶意脚本的评论来注入存储型XSS。为了防止这种攻击，应该对用户输入进行验证和清理，并对其进行适当的编码，以确保任何恶意脚本都被禁止执行。例如，可以使用以下代码来修复上面的示例：
```PHP
// 获取用户评论
$comment = $_POST['comment'];
// 存储评论到数据库
$servername = 'localhost';
$username = 'root';
$password = '';
$dbname = 'test';
$conn = mysqli_connect($servername, $username, $password, $dbname);
if (!$conn) {
	die("连接失败: " . mysqli_connect_error());
}
$comment = htmlspecialchars($comment); //编码用户评论
$sql = "INSERT INTO comments (comment) VALUES ('$comment')";
mysqli_query($conn, $sql);
mysqli_close($conn);
// 将评论显示在页面上
echo "<h1>评论</h1>";
echo htmlspecialchars($comment); //编码用户评论
```
# 三、BurpSuite靶场示例
1. 访问靶场[BurpSuite靶场stored XSS](https://portswigger.net/web-security/cross-site-scripting/stored)。
![1.png](./img/XSS/stored/1.png)
2. 进入靶场后如下所示，是一些博客文章的页面，如果有存储型XSS的话，那么只有可能存在于评论、留言或者反馈这种功能点。
![2.png](./img/XSS/stored/2.png)
3. 点击查看帖子，如下，发现底部有一个 评论功能，在评论中插入一段xss代码，然后点击提交后返回博客
![3.png](./img/XSS/stored/3.png)
4. 再次打开刚刚的博客，发现页面弹窗并显示了cookie
![4.png](./img/XSS/stored/4.png)
