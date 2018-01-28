# 简单操作Redis

此文的目的是提供一种方案，最快上手，免去各种折腾的开销，不提供各种折腾方案，只提供我试过的方案。有点数据库使用基础，而且对Redis有大致的了解，可以进入到实操阶段。


### 下载及使用

> 下载

途径一(没试过)：
先从github上clone整个仓库，点击直达[Redis仓库地址](https://github.com/antirez/redis)
途径二(荐)：
从官网上下载源文件，进去后选择一个stable下载即可，点击直达[官网下载页面](https://redis.io/download)

> 编译

我用上面途径二下载的，终端切到刚下载的`Redis/src`文件夹下，输入`make`后等，过程中应该没有错误。编译完后就可以使用了。

> 使用

打开终端切到`Redis/src`文件夹下，输入`./redis-server`即可启动server端。

接下来你需要一个客户端来操作这个server，打开新终端切到上面同样文件夹下，输入`./redis-cli`就启动了一个客户端，用它可操作server中的数据。服务端开一个，客户端随你开几个，到达上限了会通知你的。

-----
### 简单操作

> 指定数据库

刚连接客户端时是在0号数据库下工作。默认配置下，数据库有16个，分别是0～15号，用`SELECT 2`来切换到2号数据库。

> 插入与查询

插入格式如`SET key value`，重复则替换。查询格式如`GET key`。如你所看到的，返回的是字符串，因为Redis中啥都是字符串。

> 数据加工

简单的数据加工是自增，如`INCR key`，然后就会将value自增1，即使未曾插入过该key。这是原子操作，同你自己取出来再自增再插回去不一样。

> 设置key的生存时间

设置生存时间是为了让冗余的数据自动删除，不然数据会一直在里面不会自己消失。格式`EXPIRE key 20`，单位是秒。设完可以用`TTL key`来看看剩多长时间会消失，如果返回的是-2，那么就是已经消失了，不信你GET一下。如果你得到-1，表示此key永不消失。

-----
### 使用数据结构

你可能会需要这个站点[<参考命令>](http://doc.redisfans.com/)查找命令

> 从List入手

List顾名思义，列表，支持头插尾插，可以访问任一元素。
`RPUSH`：尾插
`LPUSH`:头插
`LLEN`：求长度
`LRANGE`：查询。如`LRANGE listname 1 -1`，后两个数字是下标范围，-1是末端下标。
`RPOP`:尾删
`LPOP`：头删

> 接着Set

Set故名思义，集合，无序，无重复。
`SADD` 增加元素。`SADD Setname value`
`SREM` 删除元素。`SREM Setname value`
`SISMEMBER` 判断元素是否已存在。`SISMEMBER Setname value`，返回的是0否1是。
`SMEMBERS` 取出整个集合。`SMEMBERS Setname`
`SUNION` 集合操作的一种：求并集。`SUNION Setname1 Setname2`，返回并集。
`SCARD`
`SDIFF`
`SDIFFSTORE`
`SINTER`
`SINTERSTORE`
`SMOVE`
`SPOP`
`SRANDMEMBER`
`SUNIONSTORE`
`SSCAN`


> Set进化版: Sorted Set

Sorted Set就是有序的集合，和Set有点不同，现在每次插入都需要指定一个score用于排序用的。基本操作符差不多。

`ZADD` 增加元素。`ZADD ZSetname score value`
`ZREM` 删除元素。`ZREM ZSetname value`
`ZRANK` 返回下标。`ZRANK ZSetname value`，最小返回值为0。
`ZRANGE` 查找元素。`	ZRANGE ZSetname 0 -1`，按排序后的下标取范围内元素。
`ZCARD`
`SCAN`
`SCORE`
`ZCOUNT`
`ZINCRBY`
`REVRANGE`
`REVRANK`
`UNIONSTORE`
`INTERSTORE`
`REVRANGEBYSCORE`
`ZRANGEBYSCORE`
`REMRANGEBYRANK`
`REMRANGEBYSCORE`

> Hash表

哈希表。

`HSET` 插入元素。`HSET HSetname key value`
`HDEL` 删除元素。
`HEXISTS`
`HGET` 获取元素。
`HGETALL` 获取整个表。
`HINCRBY`
`HINCRBYFLOAT`
`HKEYS`
`HLEN` 表长。
`HMGET`
`HMSET`
`HSETNX`
`HVALS`
`HSCAN`










