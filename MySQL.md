# MySQL

root密码 `mysql811613151`(811613151)

![image-20220101221249089](MySQL.assets/image-20220101221249089.png)

### 问题：

**查看命令历史记录：**

![image-20220526105721922](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220526105721922.png)

**密码错误，忘记密码：**

```
cd /etc/mysql

```



![image-20220427010230283](MySQL.assets/image-20220427010230283.png)

```
[client]
host     = localhost
user     = debian-sys-maint
password = s5AmU7wSOIrsUWJ2
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = s5AmU7wSOIrsUWJ2
socket   = /var/run/mysqld/mysqld.sock

```

打开`debian.cnf`，使用里面的密码登录mysql。 输入

```
mysql -u debian-sys-maint -p password
```

然后输入以下命令修改密码为123456：

```
use mysql;

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';

flush privileges;
quit;
```

**linux下登录到mysql：**

```
mysql -u root -p
```



2022/5/25:root密码：`zx,.123qwe`





查看mysql用户被访问权限：

```
select * from mysql.user where user='root' \G;
```



![image-20220516203529712](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220516203529712.png)

修改配置：

`vim /etc/mysql/mysql.conf.d/mysqld.cnf`

![image-20220516204758476](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220516204758476.png)

注释掉了：`bind-address            = 127.0.0.1`

重启mysql服务器：`service mysql restart`

![image-20220516205539025](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220516205539025.png)











### 基本信息：

#### 数据库用户名

查看mysql的用户名（进入数据库后：

`select user from mysql.user;`

![image-20220516110457115](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220516110457115.png)

其中：

- mysql.infoschema：是MySQL自带的，它提供了访问数据库元数据的方式 
- mysql.sys：用于 sys schema中对象的定义。使用 mysql.sys 用户可避免DBA重命名或者删除root用户时发生的问题。该用户已被锁定，客户端无法连接。
- mysql.sessio：插件内部使用来访问服务器。该用户已被锁定，客户端无法连接



开启mysql远程访问：

1. 登录mysql：

mysql -u root -p+(root用户密码)

2. 切换到mysql数据库：

use mysql

3. 修改root用户host权限值'localhost'-->'%'

update user set host='%' where user='root';

\* 4. 开启远程访问：

grant all privileges on *.* to 'root'@'%' identified by '(root用户密码)' with grant option;

5. 刷新权限：

flush privileges;

\* 6. 关闭mysql：

exit

\* 7. 运行serives.msc重启mysql服务.





### 使用MySQL

#### 选择数据库

#### 连接

**1.** **启动**mysql**：**用命令行进到MySQL中，管理员身份打开cmd输入`net start MySQL80`

**2.** **进入**mysql**：cd**进入安装bin**文件夹，**输入`mysql -hlocalhost -uroot –p`，再输入密码

```
生成样例数据库crashcourse：

下载.sql文件，共两个， create.sql和populate.sql

下载网址：http://www.forta.com/books/0672327120/

CREATE database crashcourse; (与《MySQL必知必会》书中一致，生成数据库)
```

**USE crashcourse;** (打开数据库，使后续操作都执行在这个数据库中)

```
SOURCE F:/MySQL/mysql_scripts/create.sql;(注意斜杠方向，先调用create，再调用populate)

SOURCE F:/MySQL/mysql_scripts/populate.sql;

刚刚连接进数据库时，需要选择一个数据库，为此使用`USE`关键字。

必须先使用 USE 打开数据库，才能读取到其中的数据
```

#### 了解数据和表

数据库、表、列、用户、权限等的信息被存储在数据库和表中（MySQL 使用MySQL来存储这些信息）。不过，内部的表一般不直接访问。可用 MySQL的SHOW命令来显示这些信息（MySQL从内部表中提取这些信息）。



**1.**输入：`SHOW DATABASES;`

输出：

![image-20220102161045556](MySQL.assets/image-20220102161045556.png)

`SHOW DATABASES;`返回可用数据库的一个列表。包含在这个列 表中的可能是MySQL内部使用的数据库（如例子中的mysql和 information_schema）。



**2.**使用`SHOW TABLES;`,为了获得一个数据库内的表的列表

输入：SHOW TABLES;

![image-20220102161229617](MySQL.assets/image-20220102161229617.png)

`SHOW TABLES;`返回当前选择的数据库内可用表的列表。



**3.**SHOW 也可以用来**显示表列：**

输入：`SHOW COLUMNS FROM customers;`

​			`DESC 表名`

输出：

![image-20220102161514199](MySQL.assets/image-20220102161514199.png)

`SHOW COLUMNS `要求给出一个表名（这个例子中的 FROM customers），它对每个字段返回一行，行中包含字段名、数据类型、是否允许NULL、键信息、默认值以及其他信息（如字段cust_id 的auto_increment）。

* `DESCRIBE`   MySQL支持用DESCRIBE作为SHOW COLUMNS FROM的一种快捷方式，`DESCRIBE customers;`是`SHOW COLUMNS FROM customers;`的一种快捷方式。

  支持的SHOW语句还有：

**4.**`SHOW STATUS`，用于显示广泛的服务器状态信息； 

**5.**`SHOW CREATE DATABASE`和`SHOW CREATE TABLE`，分别用来显示,创建特定数据库或表的MySQL语句；  

**6.**`SHOW GRANTS`，用来显示授予用户（所有用户或特定用户)的安全权限；

**7.**`SHOW ERRORS`和`SHOW WARNINGS`，用来显示服务器错误或警告消息。

可在命令行输入 HELP SHOW 查看允许的SHOW语句。

### 检索数据

在本章中可以学习到使用SELECT语句从表中检索一个或多个数据列。





 **倒序查找前n条数据：**

```
select * from videos order by id desc limit 2\G
```

#### SELECT语句

为了使用SELECT检索表数据，必须至少给出两条信息-想选择什么，以及从什么地方选择

```
select * from videos\G;
每一行的值垂直输出
```

![image-20220525130438422](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220525130438422.png)

**检索单个列**

输入：SELECT prod_name

​			FROM products;

利用**SELECT**语句从**products**表中检索一个名为**prod_name**的列。所需的列名在**SELECT**关键字之后给出，**FROM **关键字指出从哪个表名中检索数据。此语句的输出如下所示：

![image-20220102171600245](MySQL.assets/image-20220102171600245.png)

* 显示出的数据顺序可能不同，因为没有明确排序查询结构。则返回的数据的顺序没有特殊意义。
* **结束SQL语句**。多条SQL语句必须以分号`；`为分隔，使用mysql命令行,必须加上分号来结束SQL语句。
* **SQL语句不区分大小写**，许多开发人员习惯对所有SQL关键字使用大写，而对所有列和表名使用小写（建议使用）,这样做使代码更易于阅读和调试。
* **使用空格**处理SQL语句时，其中所有空格都会被忽略，SQL语句可以在上一行给出，也可以分为多行，多数SQL开发人员认为将SQL语句分为多行共容易阅读。

**检索多个列**

要想从一个表中检索多个列，使用相同的SELECT语句。唯一的不同 是必须在SELECT关键字后给出多个列名，列名之间必须以逗号分隔。

从表 `products`中寻找三个列名。

![image-20220310165710647](MySQL.assets/image-20220310165710647.png)

**检索所有列**

`SELECT * from 表名;`

### 插入数据

INSERT是用来插入（或添加）行到数据库表的。插入可 以用几种方式使用： 插入完整的行； 插入行的一部分； 插入多行； 插入某些查询的结果

#### 插入完整的行

不安全的插入：

如果不提供列名，则必须给每个表列提供 一个值。如果提供列名，则必须对每个列出的列给出一个值。 如果不这样，将产生一条错误消息，相应的行插入不成功。

![image-20220516092916201](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220516092916201.png)



安全的插入：

​	因为提供了列名，VALUES必须以其指定的次序匹配指定的列名，不 一定按各个列出现在实际表中的次序。其优点是，即使表的结构改变， 此INSERT语句仍然能正确工作。你会发现cust_id的NULL值是不必要的， cust_id列并没有出现在列表中，所以不需要任何值。

​	使用这种语法，还可以省略列。这表示可以只给某些列提供值，给 其他列不提供值。

![image-20220516093425231](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220516093425231.png)



​	省略的列必须满足以下某个条件。 该列定义为允许NULL值（无值或空值）。 在表定义中给出默认值。这表示如果不给出值，将使用默 认值。

### 更新数据

```
UPDATE videos
SET  release_time = '1653551477' 		//什么 改成-> 什么
WHERE 	id = 2;//根据什么搜索（一般主键）
```





### 删除

从表中删除 id为1 的行。

```
DELETE FROM user WHERE id = 1;
```

删除表：

```
 DROP TABLE videos;
```













### 创建和更新表

**创建表**

利用CREATE TABLE创建表，必须给出下列信息： 新表的名字，在关键字**CREATE TABLE**之后给出；表列的名字和定义，用逗号分隔。

**更新表**



**为表插入列**

为某表插入一个叫做myid的列，存储字符类型

```
ALTER TABLE tableName
ADD myid CHAR(20);
```

**删除列**

```
ALTER TABLE tableName DROP COLUMN  myid ;
```





**更新表名**

```
RENAME TABLE name1 TO name2;
```













### 连结表

外键为某个表的一列，它包含另一个表的主键

可伸缩性： 能够适应不断增加的工作量而不失败。设 计良好的数据库或应用程序称之为可伸缩性好







### 用户管理

MySQL用户账号和信息存储在名为mysql的MySQL数据库中。

```
//查看用户
USE mysql;
select user from user;

//创建用户
create user root identified by '..123456'; //用户名 user 密码为123456

```

删除用户

```
DROP USER 用户名;
```

提升用户权限

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;
```

![image-20220525022313522](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220525022313522.png)

更改用户密码：

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
```







# MySQL_hsp

### 创建数据库

`CREATE DATABASE 表名`

![image-20220312151431029](MySQL.assets/image-20220312151431029.png)

### 查看删除数据库

`SHOW DATABASE`



查看某数据库被创建时所使用的语句：使用SHOW CREATE DATABASE 库名



![image-20220312152513191](MySQL.assets/image-20220312152513191.png)

### 备份数据库

![image-20220312152917622](MySQL.assets/image-20220312152917622.png)



`mysqldump -u root -p -B db_1 > d:\\bak.sql`

**dos下输入指令：**

![image-20220312154330132](MySQL.assets/image-20220312154330132.png)

**d盘下生成sql备份文件**：

![image-20220312154215484](MySQL.assets/image-20220312154215484.png)

**进入MySQL命令行输入指令**，进行恢复：

`source d:\\back.sql`

![image-20220312154726128](MySQL.assets/image-20220312154726128.png)

**单独备份库内某表：**

`mysqldumo -u root -p 数据库 表1 表2 表n > d:\\文件名.sql`

* 跟备份库指令相比，少了 	-B 







# 小林mysql

## select语句发生过程

### 连接器

MySQL定义了空闲连接的最大空闲时间，由`wait_timeout`控制，默认8小时（2888）秒，如果空闲连接超过了时间，连接器会自动断开。

```sql
mysql> show variables like 'wait_timeout';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| wait_timeout  | 28800 |
+---------------+-------+
```

一个处于空闲状态的连接被服务端主动断开后，这个客户端并不会马上知道，等到客户端在发起下一个请求的时候



MySQL的最大连接数由 max_connections 参数控制

```sql
mysql> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+
```

连接也分为长连接和短连接

```c
// 短连接
连接 mysql 服务（TCP 三次握手）
执行sql
断开 mysql 服务（TCP 四次挥手）

// 长连接
连接 mysql 服务（TCP 三次握手）
执行sql
执行sql
执行sql
....
断开 mysql 服务（TCP 四次挥手）
```

长连接可以减少建立连接和断开连接的过程，但长连接累计过多，将导致**MySQL服务占用内存**过大，被系统强制杀死，发生服务重启异常。



解决长连接占用内存的方法：

1. 定期断开长连接
2. 客户端主动重置连接。



至此，连接器的工作做完了，简单总结一下：

- 与客户端进行 TCP 三次握手建立连接；
- 校验客户端的用户名和密码，如果用户名或密码不对，则会报错；
- 如果用户名和密码都对了，会读取该用户的权限，然后后面的权限逻辑判断都基于此时读取到的权限；

### 查询缓存

连接器工作完成后，客户端可以向MySQL发送查询语句
