# 7x12HTB系列——funnel

## 信息收集

1. 启动靶机

   ![1.png](img/HTB/funnel/1.png)

2. 扫描端口

   ![2.png](img/HTB/funnel/2.png)

3. 开放了21端口和22端口，其中21端口允许ftp匿名访问

   ![3.png](img/HTB/funnel/3.png)


## 漏洞利用

1. 尝试匿名登录ftp成功，存在ftp未授权访问，dir发现ftp存在mail_backup目录，查看目录文件

   ![4.png](img/HTB/funnel/4.png)

2. 该目录下存在password_policy.pdf和welcome_28112022两个文件，get到本地，查看文件内容

   ![5.png](img/HTB/funnel/5.png)

   ![6.png](img/HTB/funnel/6.png)

3. 发现几个账户和一个密码，直接尝试ssh连接，发现christine用户可以使用密码funnel123#!#登录ssh，登录后查看一下端口进程

   ![7.png](img/HTB/funnel/7.png)

4. 发现目标主机本地端口5432由postgresql服务，但是在christine用户不能直接访问，且服务仅开放本地访问，使用ssh将postgresql转发到外部1234端口，注意，靶机和攻击机器都需要执行一次

   ```ssh
   ssh -L 1234:localhost:5432 christine@10.129.131.44
   ```

5. 攻击机器访问目标主机1234端口postgresql服务

   ```ssh
   psql -U christine -h localhost -p 1234
   ```

   ![8.png](img/HTB/funnel/8.png)

6. 成功登录postgresql，`\l`查看数据库列表，发现一个secrets

   ![9.png](img/HTB/funnel/9.png)

7. 使用`\c secrets`连接到secrets，使用`\dt`查看表列表，发现flag

   ![10.png](img/HTB/funnel/10.png)