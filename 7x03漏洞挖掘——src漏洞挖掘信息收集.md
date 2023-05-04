# 一、模糊搜索

1. 使用Fofa、Hunter等测绘平台搜索

	- 搜索域名，domain="xxx.com"

	- 搜索图标
	```shell
	#fofa
	ico_hash="xxxx"

	#hunter
	web.icon:"xxxx"

```

	- 搜索js，搜索特征比较明显的js

	- 搜索网站特征，如thinkphp的特征、特有图片

	- 搜索title

	- 搜索body

2. 使用谷歌语法

3. 在github上搜索域名关键字

	- 搜索域名前缀，如四川大学scu.edu.cn，搜索scu

	- 搜索域名

4. 使用微步情报社区、360威胁情报中心、奇安信威胁情报中心等平台搜索

# 二、准确搜索

1. 使用灯塔、水泽等集成工具收集

	- [魔改灯塔](https://github.com/ki9mu/ARL-plus-docker)

2. 使用小蓝本xiaolanben.com等平台搜索企业资产子域名

3. 使用微信搜索小程序、公众号

	- 有的资产必须搜索子公司全称才可以搜到

4. 使用抖音、快手、B站等平台搜索后台等，可以找到一些后台和帐号密码

# 三、通用系统搜索

1. 先CNVD上搜索系统名或公司名，找历史漏洞

2. 然后Fofa、hunter上搜公司名、系统名有的会自动提示语法

# 四、EDU通用搜索

1. 教育漏洞报告平台上找edu开发商

2. 根据开发商寻找是否有系统

3. 测试开发商系统漏洞

4. 根据开发商系统通用漏洞批量挖掘使用该系统的学校漏洞

# 五、抓包过滤

1. 以下站点抓包时可以过滤掉

```

*.chrome.*

*.mozilla.*

*.google-analytics.*

*.google.*

*.googleadservices.*

*.googleadsserving.*

*.googleapis.*

*.googlesyndication.*

*.googletagmanager.*

*.googleusercontent.*

*.gstatic.*

*.baidustatic.*

*.bdstatic.*

*.sogoucdn.*

*.microsoftonline.*

*.microsoft.*

*.bing.*

*.csdnimg.*

*.51cto.*

*.zhihu.*

*.freebuf.*

*.huoxian.*

*.alicdn.*

*.butian.*

*.anquanke.*

*.geetest.*

1.12.247.13:5003

*.alipay.com

*.huoxian.cn

*.fofa.info

*.qianxin.com

*.yuque.com

*.bugcrowd.com

*.weixin.qq.com

```