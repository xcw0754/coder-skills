# 套接字socket

socket频繁用于网络中的进程通信，可以是本地与本地进程通信，也可以是本地与远端进程通信。如下是基于TCP协议的客户端与服务端的使用socket流程：

- 客户端：`socket()` => `connect()` => `read()/write()` => `close()`
- 服务端：`socket()` => `bind()` => `listen()` => `accept()` => `read()/write()` => `close()`


-----
### 服务端

#### socket()

原型：`int socket(int domain, int type, int protocol);`
功能：为本次通信创建一个endpoint。
参数：

- **domain**: 指定协议族，有如下选择
	`AF_UNIX, AF_LOCAL`   本地通信
	`AF_INET`             IPv4
	`AF_INET6`            IPv6
	`AF_IPX`              IPX
	`AF_NETLINK`          Kernel user interface device
	`AF_X25`              ITU-T X.25 / ISO-8208 protocol
	`AF_AX25`             Amateur radio AX.25 protocol
	`AF_ATMPVC`           Access to raw ATM PVCs
	`AF_APPLETALK`        Appletalk
	`AF_PACKET`			  Low level packet interface
其中常见的就`AF_INET`、`AF_INET6`、`AF_LOCAL`等。

- **type**: 指定通信方式，有如下选择
	- `SOCK_STREAM`		数据流(按字节)
	- `SOCK_DGRAM`		数据报(无连接，不可靠，有长度上限)
	- `SOCK_SEQPACKET`	数据报(可靠，有序，有连接，有长度上限)
	- `SOCK_RAW`		裸存取(适合ICMP)
	- `SOCK_RDM`		数据报(可靠数据报，但不保证顺序)
其中`SOCK_STREAM`和`SOCK_DGRAM`分别就是TCP和UDP所用的，要小心type并不是和任何domain可以乱搭配的，比如`AF_INET`和`SOCK_SEQPACKET`就不能搭配，得根据协议族选择合适的type。
还有两个选择可以和上面进行搭配：
	- `SOCK_NONBLOCK`		效果同`fcntl(sockfd, F_SETFL, oldflag | O_NONBLOCK)`。
	- `SOCK_CLOEXEC`		功能是当执行`exec()`时关闭此文件，防止文件还一直打开着不用。
使用的时候像这样就行了`SOCKET_STREAM | SOCK_NONBLOCK`，两个都可以用fcntl来单独设置。

- **protocol**: 指定协议，常见的有`TCP`、`UDP`等，在文件`/etc/protocols`中能找到所有协议的编号及宏。如要使用默认协议，写0即可，比如`SOCK_STREAM`就是TCP，`SOCK_DGRAM`就是UDP。


**返回值**: 若参数搭配合法，且系统条件允许，就会返回一个socket句柄，否则返回-1,错误原因需要检查errno，有如下可能：

- `EACCES`	指定的type或者protocol被拒绝。
- `EAFNOSUPPORT`	地址族不支持。
- `EINVAL`	protocol不明或者不可用。
- `EINVAL`	type无效。
- `EMFILE`	每个进程的句柄数量达到上限。
- `ENFILE`	系统句柄数量达到上限。
- `ENOBUFS or ENOMEM`	内存不足。
- `EPROTONOSUPPORT`	domain域不支持这种type或者protocol。




#### bind()

原型：`int bind(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`
功能： 将socket与某个地址绑定在一起，在多网卡情况下可以自己指定用哪个地址。
参数：

- **sockfd**	socket句柄。
- **addr**	指定地址，将相关的结构体地址传进去。
其中sockaddr是为了取各个项比较方便，其定义如下：
```
struct sockaddr {
	sa_family_t sa_family;
	char        sa_data[14];
}
```
不同的协议族对应不同的结构体，比如IPv4：
```
struct sockaddr_in {
   sa_family_t    sin_family; /* AF_INET */
   in_port_t      sin_port;   /* 端口号 */
   struct in_addr sin_addr;   /* 地址 */
};
struct in_addr {
   uint32_t       s_addr;		/* 4字节IPv4地址 */
};
```
比如IPv6：
```
struct sockaddr_in6 {
    sa_family_t     sin6_family;   /* AF_INET6 */
    in_port_t       sin6_port;     /* 端口 */
    uint32_t        sin6_flowinfo; /* flow information */
    struct in6_addr sin6_addr;     /* 地址 */
    uint32_t        sin6_scope_id; /* Scope ID */
};
struct in6_addr {
    unsigned char   s6_addr[16];   /* 16字节IPv6地址 */
};
```
- **addrlen**	说明第2个参数的长度，单位是字节，所以用sizeof即可检测出来。

**返回值**： 若正常则返回0，否则返回-1，并置errno。错误原因检查errno即可，`man 2 bind`可以查看错误码。
**注意**： 机器字节序和网络字节序可能会不同，为了保证连接有效，涉及到1个字节以上的均需要转换字节序，相关函数是`htonl()`、`htons()`，即host to network long(32位的)，host to network short(16位的)，反过来还有`nltoh()`和`nstoh()`两个函数。


#### listen()

原型：`int listen(int sockfd, int backlog);`
功能： 开始监听端口。
参数：

- **sockfd**	socket句柄，仅限type为`SOCK_STREAM` or `SOCK_SEQPACKET`的socket，其他类型的无需bind。
- **backlog**	指定等待accept的队列长度，超过了这个长度的连接请求会被拒绝(收到`ECONNREFUSED`错误码)或者被忽略(这样client就可能会重连)，具体还是看协议而定，不过这都是客户端需要关心的事了。

**返回值**： 若正常则返回0，否则返回-1，并置errno。错误原因检查errno即可，`man listen`可以查看错误码。

**注意**，多线程的errno怎么检查？C++11起，errno已经多线程安全，无需担心。


#### accept()

原型：`int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);`
功能：从队列中接受一个来自客户端的连接。
参数：

- **sockfd**	socket句柄。
- **addr**		指示了客户端相关的信息，如地址、端口等，取决于sockfd的协议所用地址类型。
- **addrlen**	传一个socklen_t型的地址进去，函数会填充一个数字指示了addr的长度，单位字节。

**返回值**： 若正常则返回一个新的socket句柄，用于和客户端进行读写。首个参数sockfd是服务端用socket()函数创建的，用途是监听某个端口。返回值这个新句柄不同于sockfd，它是自动产生的，而且只是用于同该刚接受的客户端沟通，不会影响首个参数sockfd，sockfd仍继续监听着。
如果队列中没有客户端的连接在pending，出现的情况就跟socket所设的flag有关了，比如阻塞式就会阻塞到连接到达，非阻塞式就会设置错误码`EAGAIN`或`EWOULDBLOCK`。其他错误码不列了。

**注意**，还有个功能一样的费标准的函数accept4()，它就多了个参数flag，用来指定accept的模式`SOCK_NONBLOCK`和`SOCK_CLOEXEC`，只是一次性的指定。



#### read/write

这两个函数就跟读写文件一样，没啥区别。但是还有其他几个函数也是用于socket句柄的读写，总结起来可以使用的操作如下：

- `write / read`	抽象程度最高
- `send / recv`
- `sendto / recvfrom`
- `sendmsg / recvmsg`
- `writev / readv`
- `sendfile`	发送文件，直接在内核帮搞定。好处是无需分两步操作：先将文件load进用户内存，再发送。

这些函数都可以操作sockfd是因为socket可以认为是个文件，这些函数的原理都差不多，从低层到高层的顺序是`sendmsg => sendto => send => write`，其实就是封装得越来越抽象，越抽象越容易操作，但是同时也失去了一些功能，看一下下面这3个函数原型：

- `ssize_t send(int sockfd, const void *buf, size_t len, int flags);`
- `ssize_t sendto(int sockfd, const void *buf, size_t len, int flags, const struct sockaddr *dest_addr, socklen_t addrlen);`
- `ssize_t sendmsg(int sockfd, const struct msghdr *msg, int flags);`
- `ssize_t readv(int fd, const struct iovec *iov, int iovcnt);` 分散读。将要读的数据分别写在几个不同的内存位置，比如一个存于buffer1,一个存于buffer2,两个buffer无需在地址上连续。
- `ssize_t writev(int fd, const struct iovec *iov, int iovcnt);` 聚集写。要发送的数据在内存中是分散的，如两次new出来的结构体，用这个函数一次性写，就可以减少多次write所带来的开销。


**注意**，send只用于有连接的协议如TCP，因为没有参数可以指定地址。而sendmsg和sendto则有无连接的协议均可。比较常用的就是send和recv两个函数。



-----
### 客户端

客户端与服务端不同的只是多了个connect函数，其他的功能基本一样。

#### connect()

原型：`int connect(int sockfd, const struct sockaddr *addr, socklen_t addrlen);`
功能：客户端请求连接服务端。
参数：

- **sockfd**	socket句柄。
- **addr**		指定服务端地址。
- **addrlen**	sizeof(addr)

**返回值**： 若正常则返回0，否则返回-1，并置errno。错误原因检查errno即可，`man connect`可以查看错误码。

**注意**，这个函数比较简单，一般都是阻塞式调用，从它被调用之后直到被服务端接受后return，此后sockfd就可以用于读写了。




