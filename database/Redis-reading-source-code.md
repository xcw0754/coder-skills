# Redis部分源码简记

下面按序给出阅读源码的顺序。

### server.c server.h 服务端实例
server.c文件是服务器端的实现，接受着客户端发送来的命令，因此从这里开始看起也是不错的选择。

- 函数`int processCommand(client *c);` 客户端送来的命令在此接受处理。
- `client`定义于`server.h`中，56行左右。
- 命令表在server启动后用`dict.c`维护，每次有命令要执行也在里面找。打开`server.c`100行左右就能看到整个命令表。
- 整个服务端实例抽象为一个名为`server`的全局变量，类型是`struct redisServer`，在`server.h中`，3百多行。
- 数据库实例抽象为`redisDb`，定义于`server.h`，可以看到`watched_keys`在里边，就是有关watch命令的。
- 如果设置了`maxmemory`的话，服务端每收到一条命令都会判断是否需要释放过时的内存，超限了才会去释放。
- 函数`long long ustime()`用long long来保存时间，精度至微秒。实现方式一眼能看出来。

> 释放内存过程

释放操作过程中有个`latency`变量，它只是用来分析潜在事件问题的，可不看。`server.maxmemory_policy`记录了清理内存的策略。清理内存时是所有数据库一块清的，不是只清当前数据库。清理内存的过程需要的一些函数，写在`evict.c`。

> multi-exec事务块(★)

`multi.c / void queMultiCommand(client *c)`
每个连接到服务端的客户端都会在服务端中由一个client实例唯一标识，事务块的命令也记在client上。由multi和exec包起来的事务块中的每条命令都是在服务端进行排队的，等到exec一到才进行处理。排队的实现有点尴尬，由于服务端在未收到exec命令之前并不知道具体有多少条命令在块里面，它用来存放命令的内存空间是恰好大小的，也就是每来一条命令就需要将之前用来存放排队命令的那块连续内存全部拷贝到一块新的更大一点的内存中去。因此建议命令不要太多，可能有效率问题？

> client与server的auth过程

`server.c / void authCommand(client *c)`
密码是明文保存的，服务端收到客户端用auth命令发来的密码，直接进行字符串比对。密码长度上限是512B，也就是512个字符。

> config加载过程

`config.c / void loadServerConfig(char *filename, char *options);`  就是读配置文件到内存中而已。


### dict.c dict.h 字典

dict是字典，也就是类似于python中的dict，原理是哈希存储key-value对。

> dict预分配内存策略

`dict.h`中有一个宏`#DICT_HT_INITIAL_SIZE 4`，就是初始时哈希表的slot数量。
`dict.c`中有个函数`int dictExpand(dict *d, unsigned long size)`用于扩大哈希表预留的空间，每次扩大一倍，扩大后的表大小都是2的幂次大小，哈希表最大是`LONG_MAX+1LU`，通常相当于`1<<31`这样，具体大小和平台有关。

> dict的内部结构(★)

`static long _dictKeyIndex(dict *d, const void *key, uint64_t hash, dictEntry **existing)`函数中可以看到散列法是除留余数法，是通过挂链法解决散列冲突的，新元素挂在链头，因为越新越容易被访问。注：将key通过`SipHash`法得到hashkey，除留余数法用的是hashkey的。

`dictEntry *dictAddRaw(dict *d, void *key, dictEntry **existing)`函数是往dict中增加元素的过程，其中rehash的过程有趣，关键看`static void _dictRehashStep(dict *d)`中那一行代码调用了`dictRehash(d,1)`，每次才rehash已有元素中的10个桶(bucket)，以免rehash过多元素会影响到性能。

dict结构体中的一个成员是`dictht ht[2];`，两个哈希表，在rehash过程中，旧的元素放在ht[0]，新的则在ht[1]，然后陆陆续续将旧的搬到新的里面去，这个过程没有一次性完成，否则也无需定义两个哈希表。

> iterator 迭代器

迭代器还有safe和unsafe之分，unsafe迭代器在初始化和销毁都会检查是否修改了dict，这是非法的，只能调用`dictNext`。而safe迭代器可以往dict中添加元素等读写操作。


### t_zset.c 有序集合

这是sorted set的实现文件。

> sorted set 的内部结构

sorted set使用的是Skip List 跳跃表，而不是采用复杂的红黑树等平衡二叉树。`zskiplist *zslCreate(void)`函数用于创建跳跃表，跳跃表所用的结构体定义在server.h中。虽然Redis对跳跃表稍作修改，但是大致还是一样的，直接去看跳跃表相关论文即可。





















