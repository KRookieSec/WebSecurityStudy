# 7x11 HTB系列——rsync未授权访问

## 信息收集

1. 打开HTB，启动靶机，目标IP如下

   ![1.png](img/HTB/rsync/1.png)

2. 扫描端口

   ![2.png](img/HTB/rsync/2.png)

3. 发现开放了873端口，对应服务为rsync

## 漏洞利用

1. 尝试连接rsync服务

   ![3.png](img/HTB/rsync/3.png)

2. 发现rsync的public目录允许匿名访问，查看该目录下的文件

   ![4.png](img/HTB/rsync/4.png)

3. 发现flag.txt文件，获取文件内容

   ![5.png](img/HTB/rsync/5.png)

4. 成功获取flag