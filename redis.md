# redis

作用：数据库、缓存和消息中间件。

**redis 是单线程的**

redis基于内存操作，cpu不是redis的性能瓶颈，redis的瓶颈为服务器的内存和网络带宽

http://www.redis.cn/



## 安装：

最新稳定版本： https://download.redis.io/redis-stable.tar.gz



下载后解压：

```
tar xzf redis-stable.tar.gz
```

解压后的文件夹 移动到 /usr/local/redis 

```
sudo mv ./redis-stable /usr/local/redis
```

进入移动后的文件夹，编译redis

```
sudo make
```

编译结束后 测试编译是否成功

```
sudo make test
```

******

这一步可能出错：

 **原因是服务器上安装的tcl版本太低**

![image-20220531161528776](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220531161528776.png)

需要安装最新的tcl：

http://www.tcl.tk/software/tcltk/download.html

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220531161705235.png" alt="image-20220531161705235" style="zoom:80%;" />

解压后，[注意]进入`/usr/local/tcl8.6.12/unix`

依次执行：

```
sudo ./configure 
sudo make 
sudo make install 
sudo make test
```

*****

安装：

```
sudo make install
```



**修改配置文件：**

```
vim redis.conf 
```

![image-20220531165249856](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220531165249856.png)



### 启动redis：

查看是否启动

```
ps axu|grep redis
```

通过指定配置文件连接服务器

```shell
ubuntu@VM-24-7-ubuntu:/usr/local/bin/kconfig$ 
sudo redis-server kconfig/redis.conf
```

![image-20220531165524240](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220531165524240.png)

**连接redis**以及退出

```
sudo redis-cli -p 6379
```

![image-20220604233933198](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220604233933198.png)

``./redis-cli -p 端口号 -a 密码`` 使用密码登录

连接之后再输入密码
`auth 密码`





## 遇到的问题

**日志文件权限：**

日志文件权限只有root才能读写，因此`redis-cli shutdown`会出错。

```shell
sudo redis-server kconfig/redis.conf
```

![img](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/20210315105023112.png)

![img](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/20210315105053137.png)



![img](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/20210315105314904.png)







**未设置密码，**被黑客攻击导致出现``backup`脚本代码,通过在配置文件中添加密码解决





## 基础

默认使用0数据库

### 基本命令：

http://www.redis.cn/commands.html

set 设置key

get 获取该key的值

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220605005931163.png" alt="image-20220605005931163" style="zoom:67%;" />

select	切换数据库



dbsize	查看数据库大小

keys \*  查看所有key

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220605004324771.png" style="zoom:67%;" />

flushdb 清除当前数据库

FLUSHALL 清空所有数据库内容

exists name 判断某个键是否存在

MOVE name 1 （ 移除 库1 的 key

EXPIRE name 10 ( 设置 键 name 过期时间 为10s，可以用来存放用户cookie

ttl name （查看键 name 的过期时间

TYPE  name (查看key name的类型)

APPEND  key1 “hello” （往key1 这个键 后追加字符串



`config get requirepass` 查看密码

`config set requirepass 123`       设置123为密码

``./redis-cli -p 端口号 -a 密码`` 使用密码登录 连接之后再输入密码 `auth 密码`

配置文件中修改密码：

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220824002853249.png" alt="image-20220824002853249" style="zoom:67%;" />





使用`*`通配符模糊匹配：

![image-20220824123323778](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220824123323778.png)













### 五大数据类型：

#### string

计数器，统计多单位的数量，关注数，对象缓存存储

APPEND 关键字在键值后追加字符串，如果该键不存在相当于 set 该 key

STRLEN name 查看该键的字符串长度



**增减：**

INCR key （自增1

DECR key（自减1

![image-20220605010203445](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220605010203445.png)

自增、减指定步长

![image-20220605010347225](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220605010347225.png)





**截取**部分字符串：

GETRANGE name  left right

截取0 至 3 

![image-20220605010611863](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220605010611863.png)

0  至 -1 截取全部，和get key 相同

**替换**

SETRANGE name left string

将键name从left开始替换为指定字符串string

![image-20220605011111207](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220605011111207.png)

**过期时间：**

setex 设置过期时间

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220606000106796.png" alt="image-20220606000106796" style="zoom:80%;" />

setnx 不存在再设置（分布式锁常用）

返回1 修改成功

返回0 修改失败

![image-20220606000223884](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220606000223884.png)



**获取多个：**

mset 原子操作设置多个键值对 

mget 获取多个

msetnx 同时设置多个键值对（如果有一个已存在键，那么都不执行）（原子性操作要么一起成功，要么一起失败）

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220605235826361.png" alt="image-20220605235826361" style="zoom:80%;" />

**对象**

set user:1 {name:zhangsan, age:3} 设置一个	user:1 对象 值为json字符来保存一个对象



组合命令

getset ：先get再set，如果不存在，返回nil。如果存在，返回原来的值在设置新值



#### list

list可存在相同值

**插入：**

LPUSH list one  将值插入list列表**头部**

LRANGE list 0 -1  通过区间获取具体值（0 -1 ：获取该列表list 全部值

RPUSH list two 将值插入list列表的**尾部**





<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220606002223461.png" alt="image-20220606002223461" style="zoom:80%;" />

 **移除元素**

LPOP list 移除头部

RPOP list 移除尾部





<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220606002701473.png" alt="image-20220606002701473" style="zoom:80%;" />

移除指定值：

LREM user 1 4 ： 移除  1  个 user列表 中为 4 的值（可指定移除多少个，从头部开始

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220606003239696.png" alt="image-20220606003239696" style="zoom:67%;" />



RPOPLPUSH old new ：从old列表移除最后一个元素，将其添加进new列表

通过下标**获取值**

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220606002832603.png" alt="image-20220606002832603" style="zoom:80%;" />

LLEN list **获取列表长度**



LSET 	：将user列表指定下标index的值**更新**为 element（下标不存在会报错

![image-20220606004920347](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220606004920347.png)











## 事务

redis事务的本质：一组命令的集合！一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行。

redis事务没有隔离级别的概念：所有命令在事务中，并没有被直接执行，只有发起执行命令的时候才会执行

redis单条命令原子性，但是事务不保证原子性





事务：

* **开启事务**（multi）
* 命令入队
* **执行事务**（DISCARD）

开启，执行事务

```shell
127.0.0.1:6379> multi  #开启事务 下面的命令都会入队
OK
127.0.0.1:6379(TX)> set k1 v1
QUEUED
127.0.0.1:6379(TX)> set k2 v2
QUEUED
127.0.0.1:6379(TX)> get v2
QUEUED
127.0.0.1:6379(TX)> set k3 v3
QUEUED
127.0.0.1:6379(TX)> exec   #执行事务 按照顺序依次执行
1) OK
2) OK
3) (nil)
4) OK
```

取消事务：

```shell
127.0.0.1:6379> keys *
1) "k3"
2) "k1"
3) "k2"
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379(TX)> set k4 v4
QUEUED
127.0.0.1:6379(TX)> DISCARD  #取消事务，事务中的命令不会执行
OK
127.0.0.1:6379> keys *
1) "k3"
2) "k1"
3) "k2"
```



>编译型异常，所有命令都不会执行，如在输入命令的错误

> 运行时异常，事务队列中存在语法错误，执行命令时，其他命令可以正常执行，错误命令抛出异常



















## 数据持久化

RDB方式：根据配置规则将内存中的数据持久化到硬盘上（默认方式）

AOF方式：每次执行命令后将命令记录下来。采用日志的形式记录每个写操作，并追加到文件中。



### RDB

RDB 即快照模式，它是 Redis 默认的数据持久化方式，它会将数据库的快照保存在 dump.rdb 这个二进制文件中。

在指定的时间将内存中的数据集体写入磁盘，回复是直接将快照文件直接读入内存。

Redi会单独创建（fork）一个子进程来进行持久化，会先将数据写入一个临时RDB文件中，待持久化都结束了在用这个临时文件去替换上次持久化好正式RDB文件



只需要将rdb文件放在我们的redis启动目录，redis启动时会自动检查 dump.rdb恢复其中数据

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220822210852596.png" alt="image-20220822210852596" style="zoom:80%;" />

查看存放位置：

```shell
127.0.0.1:6379> config get dir
1) "dir"
2) "/usr/local/bin"
```

优点：适合大规模的数据恢复，对数据完整性要求不高

缺点：需要一定的时间间隔，如果redis意外宕机，最后一次修改数据就没有了。fork进程的时候会占用一定的内存空间

```
SAVE 手动保存RDB文件
```



**手动触发：**手动触发是通过`SAVAE`命令或者`BGSAVE`命令将内存数据保存到磁盘文件中

```
127.0.0.1:6379> SAVE
OK
127.0.0.1:6379> BGSAVE
Background saving started
127.0.0.1:6379>  LASTSAVE
(integer) 1611298430
```

**自动触发：**

![image-20220823210043378](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220823210043378.png)







#### 配置文件修改

在配置文件中搜索      `SNAPSHOTTING`

![image-20220823210043378](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220823210043378.png)

![image-20220823210029859](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220823210029859.png)

























### AOF







 





## Redis 和MySQL数据一致性

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/26257042-9cab9a6ceb17bcf1.png" alt="img" style="zoom:80%;" />

 当涉及到数据更新时，不管是先写MySQL数据库，再删除Redis缓存；还是先删除缓存，再写库，都有可能出现数据不一致的情况。 

 1.**先删除缓存**

 如果先删除Redis缓存数据，然而还没有来得及写入MySQL，另一个线程就来读取，这个时候发现缓存为空，则去Mysql数据库中读取旧数据写入缓存，此时缓存中为脏数据；然后数据库更新后发现Redis和Mysql出现了数据不一致的问题。
 2.**后删除缓存**

 **如果先写了库，然后再删除缓存，不幸的写库的线程挂了，导致了缓存没有删除，**这个时候就会直接读取旧缓存，最终也导致了数据不一致情况，因为写和读是并发的，没法保证顺序,就会出现缓存和数据库的数据不一致的问题。

四、解决方案：
 **方案一：延时双删策略+缓存超时设置：** 

 **1、先删除缓存 **

 **2、**再写数据库

 **3、**休眠500毫秒（休眠时间根据实际业务逻辑的耗时决定。）

 **4、**再次删除缓存

 方案缺点：

 **1、**在缓存过期时间内发生数据存在不一致 

 **2、**同时又增加了写请求的耗时。

方案二：**异步更新缓存（基于Mysql binlog的同步机制）：**

一旦mysql中产生了新的写入、更新和删除等操作，就可以把binlog相关的消息推送到redis，redis再根据binlog中的记录，对缓存数据进行更新，参考:mysql的主从数据库备份机制。

Mysql的数据操作都记录到binlog，通过消息队列及时更新到Redis上）















# go-redis



初始化连接：

```go
func init() {
	fmt.Println("init")
	Client = redis.NewClient(&redis.Options{
		Addr:     "127.0.0.1:6379",
		Password: "",
		DB:       0,
	})
	_, err := Client.Ping(ctx).Result()
	if err != nil {
		panic(err)
	}
}
```



下列所有方法前需加 **RedisClient.c.** 来调用，返回值全部都是各种格式的cmd，需要使用各种方法来从中取得我们需要的值

| 方法     | 作用                     | 参数                                              |
| -------- | ------------------------ | ------------------------------------------------- |
| Set      | 添加kv                   | 键 string, 值 interface{}, 过期时间 time.Duration |
| Get      | 获取值                   | 键 string                                         |
| Del      | 删除kv                   | 键 …string                                        |
| HSet     | 给散列添加kv             | 散列的键, 散列中的键 string, 值 interface{}       |
| HGet     | 获取散列kv的值           | 散列的键, 散列中的键 string                       |
| FlushAll | 删光                     | 无                                                |
| Ping     | 心跳验证                 | 无                                                |
| Exists   | 确认键是否存在           | 键 …string                                        |
| Expire   | 给key设置过期时间        | 键 string, 过期时间 time.Duration                 |
| Incr     | 增加键                   | 键 string                                         |
| Close    | 关闭连接                 | 无                                                |
| Watch    | 监听键，键出现时执行方法 | 方法 func(*Tx) error, 键 …string                  |
| Decr     | 值减一                   | 键 string                                         |

 值获取

result 获取值

| 方法      | 返回值      |
| --------- | ----------- |
| .Err()    | err         |
| .Result() | string，err |
| .Int64()  | int64，err  |







### 结构体

**redis.Cmd结构体**

```
type Cmd struct {
	baseCmd

	val interface{}
}
```



**redis.IntCmd结构体**

```go
type IntCmd struct {
	baseCmd
	val int64
}
```

```go
func (cmd *IntCmd) Result() (int64, error) {
	return cmd.val, cmd.err
}
```



**redis.StringCmd结构体**

```go
type StringCmd struct {
	baseCmd
	val string
}
```

Val方法

```go
func (cmd *StringCmd) Val() string {
	return cmd.val
}
```

Result方法

```go
func (cmd *StringCmd) Result() (string, error) {
	return cmd.Val(), cmd.err
}
```

**redis.StringSliceCmd结构体**

```go
type StringSliceCmd struct {
	baseCmd

	val []string
}
```

```go
func (cmd *StringSliceCmd) Result() ([]string, error) {
	return cmd.Val(), cmd.Err()
}
```

**redis.ScanCmd结构体**

```go
type ScanCmd struct {
	baseCmd

	page   []string
	cursor uint64

	process cmdable
}

```

```go
func (cmd *ScanCmd) Iterator() *ScanIterator {
	return &ScanIterator{
		cmd: cmd,
	}
}
```

**redis.IntCmd结构体**

```go
type IntCmd struct {
	baseCmd

	val int64
}
```



### 常用方法



**根据前缀和获取key**

```
vals, _ := rdb.Keys(ctx, "k*").Result()
```

**执行自定义命令**

```go
//Do是Client结构体的方法
	rdb.Do(ctx, "set", "key1", "value1").Result()
	val1, _ := rdb.Get(ctx, "key1").Result()
	val2, _ := rdb.Do(ctx, "get", "key1").Result()
```

**按通配符删除**

```go
vals1, _ := rdb.Keys(ctx, "k*").Result()
	fmt.Println(vals1) //输出:[key2 key1]
	iter := rdb.Scan(ctx, 0, "k*", 0).Iterator()
	for iter.Next(ctx) {
		err := rdb.Del(ctx, iter.Val()).Err()
		if err != nil {
			panic(err)
		}
	}
```

**连接集群**

```go
func initClient() (err error) {

	rdb := redis.NewClusterClient(&redis.ClusterOptions{
		Addrs: []string{":7000", ":7001", ":7002", ":7003", ":7004", ":7005"},
	})
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()
	//Ping返回的是：*StatusCmd ; Result方法是StatusCmd结构体的方法(StatusCmd的val, baseCmd的err)
	_, err = rdb.Ping(ctx).Result()
	return err
}


```

**哨兵模式**

```go
func initClient() (err error) {

	rdb := redis.NewFailoverClient(&redis.FailoverOptions{
		MasterName:    "master",
		SentinelAddrs: []string{"x.x.x.x:26379", "xx.xx.xx.xx:26379", "xxx.xxx.xxx.xxx:26379"},
	})
	ctx, cancel := context.WithTimeout(context.Background(), 5*time.Second)
	defer cancel()
	_, err = rdb.Ping(ctx).Result()
	return err
}

```

