---
title: Ubuntu云服务器如何安装MySQL
date: 2022-1-24 15:09:30
---

### 安装MySQL

首先验证下是否安装，键入mysql:

![Snipaste_2022-01-24_15-11-14](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-11-14.png)

像这样就是没有安装

然后下面我们就开始安装

1. 首先更新软件包(这个命令很常用)

   ```text
   sudo apt-get update
   ```

   如果当前系统已经是root用户则不用键入sudo，输入也是没关系的:

   ![Snipaste_2022-01-24_15-30-02](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-30-02.png)

2. 开始安装MySQL服务端和客户端(中间会有确认，输入y或者Y):

   ```text
   sudo apt-get install mysql-server mysql-client
   ```

   ![Snipaste_2022-01-24_15-31-35](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-31-35.png)

3. 然后我们查看下数据库版本，如果出现数据库版本则安装成功:

   ![Snipaste_2022-01-24_15-33-21](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-33-21.2tsy0k8c3wg0.webp)

4. 修改密码

   + 进入配置文件，在在mysqld.cnf中添加skip-grant-tables便可跳过登录认证(要记得保存):

     ```text
     sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
     ```

     ![Snipaste_2022-01-24_15-37-46](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-37-46.4qmshu9lsp00.webp)

   + 重启数据库服务，并登录数据库，不要输入密码，直接回车；登录数据库之后，进入mysql数据库，修改user表中user='root'的密码:

     ![Snipaste_2022-01-24_15-45-29](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-45-29.6bp8qx0apjc0.webp)

     来看看这上面出现的命令:

     ```text
     service mysql restart  # 重启mysql服务
     mysql -u root -p  # 以root用户在本地登录，这个时候我们没设置密码但在安装mysql的时候自动生成了一个，我们可以用这个自动生成的去登录，但是我们之前有个操作让我们跳过了登录认证，所以这里可以不输入密码直接登录
     use mysql;  # 使用mysql这个数据库
     select user,plugin from user;  # 选出user和plguin这两列
     update user set authentication_string=password("yourCode"),plugin='mysql_native_password' where user="root";  # 设置root用户登录密码
     flush privileges;  # 更新权限
     ```

   + 进入刚刚那个配置文件，将skip-grant-tables注释掉(在skip-grant-tables前添加#)，保存退出，这一步是关闭刚刚不输入密码就登录mysql的操作，确保登录数据库必须使用密码:

     ```text
     vi /etc/mysql/mysql.conf.d/mysqld.cnf
     ```

     ![Snipaste_2022-01-24_15-54-40](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-54-40.1j1paqeb25ls.webp)

### 开放远程连接权限

1. 进入刚刚的那个配置文件，将`bind-address          = 127.0.0.1`这一行注释掉，保存并退出:

   ```text
   vi /etc/mysql/mysql.conf.d/mysqld.cnf
   ```

   ![Snipaste_2022-01-24_15-58-55](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_15-58-55.3q30m3qywt60.webp)

2. 登录数据库，配置root用户远程权限

   ![Snipaste_2022-01-24_16-02-53](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_16-02-53.4h4j3qprai00.webp)

   重复的命令这里就不赘述了，讲一条关键的命令:

   ```text
   GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'yourCode' WITH GRANT OPTION;  # 让所有的地址都可以使用root用户，远程访问数据库
   ```

3. 云服务器控制台开放MySQL数据库3306端口，按下图操作点击确定即可:

   ![Snipaste_2022-01-24_16-10-05](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_16-10-05.3ud7mcfm31m0.webp)

   ![20200509230650615](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/20200509230650615.1ktpct6aln1c.webp)

4. 用客户端连接查看是否安装成功:

   因为我们刚配置完，我们最好在云服务器中重启下mysql的服务，使用命令:

   ```text
   sudo service mysql restart  # 这个命令一定要root权限
   ```

   上面这个命令一定要root权限，如果没有就会报如下错误:

   ![Snipaste_2022-01-24_16-33-39](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_16-33-39.6by085d5bhc0.webp)

   然后我们来测试下，这样成功开放远程链接了:
   ![Snipaste_2022-01-24_16-35-30](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_16-35-30.3v3eifkbtrc0.webp)

### 新增guest用户并设置访问权限

1. 登录数据库，进入mysql库
2. 创建guest用户，并设置登录密码
3. 为gest用户开放select、insert、update权限
4. 刷新生效

上面的命令对应如下:

```text
#创建用户
mysql> grant usage on *.* to 'guest'@'%'identified by 'yourguestcode' with grant option;
#设置权限
mysql> grant select,insert,update on *.* to 'guest'@'%' with grant option;
#刷新
mysql> flush privileges;
```

这上面我的密码搞错了，这里就不改了，密码直接是`yourguestcode`，修改密码可以使用以下语句:

```text
SET PASSWORD FOR 'guest'@'%' = PASSWORD('123456');  # 修改密码
FLUSH PRIVILEGES;  # 刷新权限
```

这里之所以是`@'%'`，因为上面设置了远程的访问权限

为验证guest用户设置是否生效，可以试试guest用户新建一个库，或者新建一个表，数据库会报错，因为我们没有给guest用户create权限:

![Snipaste_2022-01-24_17-19-38](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_17-19-38.30pnxs1a0m20.webp)

然后我们来验证下:

![Snipaste_2022-01-24_17-41-15](https://cdn.jsdelivr.net/gh/stormwasd/image-hosting@master/20220124/Snipaste_2022-01-24_17-41-15.1d1dkzqfg6ps.webp)







