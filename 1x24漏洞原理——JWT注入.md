# 一、JWT概述
1. JWT，全称为JSON Web Token，是一种基于JSON格式的轻量级的安全身份验证机制。它使用标准的JSON对象进行安全传输声明信息。JWT由三部分组成：头部、载荷和签名。头部标识出该JWT使用的加密算法，载荷则存放着该JWT所表达的声明信息，签名则是对头部和载荷进行签名后的结果，以确保JWT没有被篡改。 
2. JWT通常用于客户端和服务端之间的认证和授权，比如一个用户登录了一个Web应用程序，应用程序会返回给客户端一个JWT，客户端在后续的每个请求中都携带这个JWT，以保证服务端对请求的认证和授权。 
3. JWT的主要优点是，它具有可扩展性、跨语言性、标准化和自包含性等优点，可以被广泛应用于各种Web应用程序和服务中，特别是基于微服务架构的应用程序。
# 二、常见的JWT漏洞及危害
1. 信息泄露：如果加密算法不正确或密钥被泄露，攻击者可以解码JWT并获得载荷中的信息。 
2. 过期Token：JWT默认包含一个过期时间，但攻击者可以使用重放攻击来重复使用过期的Token，绕过身份验证。 
3. 无效签名：如果签名算法不正确或密钥被泄露，攻击者可以更改载荷并重新签名，从而绕过身份验证。 
4. 算法选择错误：JWT支持多种算法，但不同的算法具有不同的安全性。如果选择不安全的算法，攻击者可以轻松地破解签名并获取载荷中的信息。 
5. XSS攻击：如果应用程序没有正确转义JWT中的载荷数据，攻击者可以通过注入脚本来实施XSS攻击。 
6. 重放攻击：攻击者可以在有效期内重复使用JWT，绕过身份验证。
# 三、代码示例
1. 使用PHP编写一个存在JWT的代码示例
	- 创建JWT
	```PHP
	// Require the JWT library
	require_once 'vendor/autoload.php';
	 // Set the secret key
	$secret = 'mySecretKey';
	 // Set the payload data
	$payload = array(
	    'user_id' => 123,
	    'email' => 'example@gmail.com',
	    'exp' => time() + 3600 // Set the expiration time
	);
	 // Create the JWT
	$jwt = \Firebase\JWT\JWT::encode($payload, $secret);
	echo $jwt; // Output the JWT
	```
	- 验证JWT
	```PHP
	// Require the JWT library
	require_once 'vendor/autoload.php';
	 // Set the secret key
	$secret = 'mySecretKey';
	 // Set the JWT
	$jwt = 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxMjMsImVtYWlsIjoiZXhhbXBsZUBnbWFpbC5jb20iLCJleHAiOjE2MzI0NzA2NzB9.-9A9J8-3Smm5SvCF4hZqL0QolRBMRfK8yfZgtE8zCuw';
	 try {
	    // Decode the JWT and validate the signature
	    $decoded = \Firebase\JWT\JWT::decode($jwt, $secret, array('HS256'));
	     // Access the payload data
	    echo $decoded->user_id;
	    echo $decoded->email;
	} catch (Exception $e) {
	    // JWT is invalid
	    echo $e->getMessage();
	}
	```
# 四、BurpSuite靶场示例
1. 打开BurpSuite靶场[通过未验证签名绕过 JWT 身份验证](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-unverified-signature)，同时打开BurpSuite抓包，进入靶场后点击右上角的My Account进行登录，查看数据包
![1.png](./img/JWT/1.png)
2. 发现cookie中有三个点，格式符合JWT特征，选中cookie，右键发送到BurpSuite插件"Json Web Token"，如下，发现有秘钥及算法信息
![2.png](./img/JWT/2.png)
3. 修改数据包尝试直接访问admin，发现需要管理员身份
![3.png](./img/JWT/3.png)
4. 修改JWT，将用户名wiener改为administrator，确定修改后点击发送，如下。成功绕过验证进入管理后台
![4.png](./img/JWT/4.png)
5. 在管理后台发现删除用户的功能，尝试删除用户carlos
![5.png](./img/JWT/5.png)
6. 重新进入管理员后台，发现用户carlos已被成功删除
![6.png](./img/JWT/6.png)