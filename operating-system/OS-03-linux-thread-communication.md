# 进程间通信方式

进程间通信(Inter-Process Communication)的方式有：管道与FIFO、信号与信号量、消息队列、共享内存、套接字。

-----
### 管道

管道分为两种：无名管道(简称管道)、命名管道(简称FIFO)。

**1、无名管道**

无名管道，简称**管道**。管道是一种特殊文件，由内核实现，它不依赖于任何文件系统，且只存在于内存。其大小一般为一页，即4KB，不能扩展，如果写满了再继续写的进程就会阻塞。管道是先进先出式的半双工容器，一般是用于单方向的通信，数据被读走后会自动删除管道内的数据，而双向通信则需要靠其他机制方式保证通信的正确性，不如考虑用两条管道。

众所周知，父子进程可以共享所有已打开的文件，包括每个文件中的位置指针。管道的读写两端可以单独关闭，这不会影响另一进程的读写，也就意味着一个进程能对其两端都进行读写，只有在所有打开的句柄都关闭后才是真的关闭。如果读写顺序不对，将得不到预期的结果。

**无名管道只能用于有亲缘关系的进程之间的通信**，比如父子进程之间。为什么？当某个进程创建了一个无名管道，由于父子进程是共享句柄`fd`的，管道也是以句柄形式进行操作的，所有通过fork产生的进程就可以共享这个管道。管道是由内核实现的，其他非亲缘进程无法找到这个管道。

管道的实现是借助了文件系统的file结构和VFS的索引节点inode，由于一般用于多进程的通信，内核实现管道时也会用锁来保证管道的操作，具体实现详见内核源文件`pipe.c`。


以下是一般用法：
```
    int fd[2];  // 句柄
    pid_t pid;
    pipe(fd);   // 创建管道
    if((pid=fork())<0) {
        return 0;           // fork失败
    } else if(pid>0) {
        close(fd[0]);       // 父进程：只写，关闭读
        write something to fd[1];
    } else {
        close(fd[1]);       // 子进程：只读，关闭写
        read something from fd[0];
    }
```



**2、命名管道**

命名管道，简称FIFO。它也是一种特殊的文件，存在于文件系统中，比无名管道要复杂一些，在使用之前需要先用mkfifo函数创建一个命名管道，然后才能用open、close、read、write函数对该管道进行操作。

Linux终端就能使用mkfifo命令，举个例子：
```
# 终端1
$mkfifo ~/test
$cat ~/test
```
敲完命令后会阻塞，这是正常的，因为管道里没有内容，所以读命令就会**阻塞**。此时在另一个终端中输入命令：
```
# 终端2
$echo 'this is message' > ~/test
```
终端1就可以看到输出了`this is message`，并且**不阻塞**了。这是命名管道的一种使用方式。在`~/`目录下，终端敲入`ls -l`可以看到多了个文件`test`，其第一列还写着`prw-rw-r--`，首字符`p`表示了这是个管道文件，这个文件会一直存在，直到你删除了它。要是在某个写操作阻塞的时候，直接`ctrl+c`终止它会怎样？不会有问题的，只是再对其读操作时，会发现之前所写内容并不在该管道中。其他详细信息可用`stat ~/test`查看。

FIFO以文件形式存在，但是它不支持lseek等这些违反其特性的函数，只能开、关、读、写这几种，而这些操作就需要考虑到是否以阻塞形式打开FIFO了，默认情况下是阻塞的，即调用open函数时会阻塞，直到该FIFO被写入了东西。不难想到其他的read、write函数也是这样的流程：读写操作在时间上有重合时才会阻塞返回。open函数也可以指定为非阻塞打开，之后就可以非阻塞读写了，比较方便，具体请参考其他资料。

FIFO中有两个数字需要关注，一个是`pipe capacity`，另一个是`PIPE_BUF`(或`pipe size`)。后者比较好查证，终端命令`ulimit -a | grep "pipe"`或者用C程序打出PIPE_BUF这个宏，Linux一般是4KB。而前者需要通过程序验证，在C中以非阻塞方式创建一个FIFO，一直写到返回错误`Resource temporarily unavailable`就是满了，我的机子测试结果为64KB。前者表明了管道的最大容量，而后者表明了保持原子性写操作的极限，超过PIPE_BUF大小就无法保证原子性。注：想彻底了解管道还需看内核源码。

关于PIPE_BUF有这样几个规定：

- POSIX.1-2001 says that write(2)s of less than PIPE_BUF bytes must be **atomic**. Writes of more  than  PIPE_BUF bytes may be nonatomic.
- POSIX.1-2001 requires PIPE_BUF to be at least 512 bytes(On Linux, PIPE_BUF is 4096 bytes).

具体参考`man 7 pipe`手册的PIPE_BUF项。

FIFO主要用于非亲缘关系的进程之间进行通信，它虽以文件形式存在于系统中，但是使用`stat ~/test`可以发现它的大小始终为0，其实它的缓冲区是存在于内存的，读写操作直接在内存中进行，所有读写都不经过磁盘。如果有多个读端，那么只能有一个读端读到数据，而且管道是基于进程的，没有进程它就相当于不存在，其中未处理完的数据也会丢失，无保存措施。



-----
### 信号

信号分为两种：signal(简称信号)、semaphore(简称信号量)。

**1、信号**

signal是进程间通信的一种常用方式，比如常用的`kill`命令就可以发送信号给指定进程，该进程能捕捉到这个信号从而进行处理。Linux支持的信号有几十种 ，而常见的信号只有十几种，如`SIGHUP`、`SIGPIPE`等，系统也规定了默认的处理方式，如`Term`、`CORE`等，可以使用signal函数为信号注册handler。

**2、信号量**

信号量，又称信号灯，它实质上是个自然数，一般用于进程或线程的同步，如经典的生产者与消费者模型。在Python中，信号量是这样使用的：
```
import threading
semaphore = threading.Semaphore(1)
semaphore.acquire()
...      # 使用信号量
semaphore.release()
```
看起来怎么有点像互斥量`mutex`？有点像，信号量一般可以用于表示资源的数量，在控制数量范围方面有较大用途，比如有5个厕所资源，为了防止冲突，可以使用初值为5的信号量，每当有一个厕所被使用时就acquire，信号量就自减，反之，release就自增，而当信号量为0时acquire就会会阻塞。可以看出，当初始化一个信号量值为1时就可以当互斥量来用，但是又和互斥量有些区别，比如互斥量取值只能是0或1且初始化为1，而信号量的取值可以是0～n任选；互斥量的使用与释放须成对出现，而信号量不用。这样看来，互斥量又很像锁，其实在功能上并没有什么区别，只是叫法不一样。

为什么信号量也属于进程通信的一部分？信号量看起来是不能进行通信的，但是它起码能传递消息，告诉其他进程或线程有关资源的信息，起到的就是通信的作用。

信号量有3种，分别是：POSIX有名信号量、POSIX基于内存的信号量、SYSTEM V信号量。它们的区别如下：

- POSIX有名信号量`Named semaphores`。利用虚拟文件系统实现信号量，一般挂在`/dev/shm`下面，类似于命名pipe。
- POSIX基于内存的信号量`Unnamed semaphores (memory-based semaphores)`。它是进程线程安全的，位于内存中的一个位置，由不同进程或线程共享，
- SYSTEM V信号量。年代久远，普遍支持，由内核维护，有二值信号量 、计数信号量、计数信号量集(多个计数信号量)。`$ipcs`就能看到系统的消息队列、信号量组、共享内存段。

关于POSIX信号量，用`$man sem_overview`可以查看有关信息。


-----
### 消息队列

为什么有了管道还有消息队列？由于管道的局限性：基于进程而存在(生命周期问题)、数据流格式(数据格式问题)、缓冲区有限(容量问题)等等，消息队列能够比较好的应付这些问题。**消息队列**由内核维护，存在于内存，不随某个用户进程的终止而消失(进程独立)，每条消息可以是不同类型，还没有先进先出的限制，支持随机查询，甚至按消息类型查询。消息队列的单位是消息，而非字节流。虽然消息队列与管道相比，容量大了很多，但是仍是有限的，每条消息和队列容量均有限，具体看`$ipcs -l`所打印出来的信息，这些限制都是可以更改的。

消息队列实际上就是一个链表，比管道稍微高级一点，支持的操作多一点。一般用于不同的进程之间的通信，顾名思义，消息队列很适合生产者与消费者模型，它们不管对方的存在，只关心数据。消息队列是有优先级的。消息队列分为POSIX和SYSTEM V两种版本，两者功能差不多，仅略微区别，POSIX支持mq_notify功能，而system V没有，关于区别可以参考 [what should be used SystemV Message queue or POSIX message queue?
](https://stackoverflow.com/questions/21304097/what-should-be-used-systemv-message-queue-or-posix-message-queue)

关于POSIX消息队列可参考[POSIX.4 Message Queues](http://users.pja.edu.pl/~jms/qnx/help/watcom/clibref/mq_overview.html)或者`$man mq_overview`。`$man svipc`可以看到system V的所有IPC介绍。


-----
### 共享内存

共享内存是最快捷的一种方式了，适合大块内存共享。因为内存可以由多方同时操作，所以常搭配锁或者信号量使用。共享内存是位于进程虚拟空间中的堆以上栈以下的部分，内核将两个进程的两块虚拟内存作映射到一起，进程操作起来跟操作自己的变量一样快捷。简单的说，A发有消息要给B，如果是管道方式，A需要将消息放进管道，B再从管道中取出，一共两个复制过程；如果是共享内存方式，A中的东西(位于共享内存块中)就可以直接被B所取到，完全无需拷贝。如果是A读一个文件的数据，再传给B处理后存到另一个文件，只需要让A读进文件，B就可以处理并输出了。总之比其他IPC方式要块。

共享内存的两种方式为mmap和shm。

**mmap**是一种内存映射方式，它的原型是这样的`void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);`，其中prot指定了创建的内存的保护方式，如可执行、可读、可写等等，fd指定了一个文件，offset指定了文件偏移，length指定了映射内存的大小，flags可以指定很多内存的属性以及如下两种映射方式：

- 私有映射。这是一种写时复制机制，每个进程在写操作后将独自拥有一份拷贝。
- 共享映射。这是普通的共享内存，所有共享者都操作同一份内存。

关于flags中的一种模式**匿名**，一般有如下两种情况：

- 文件映射。将文件映射到虚拟内存。
- 匿名映射(flags指定`MAP_ANONYMOUS`模式)。无需指定文件，申请的内存直接被初始化为0。

更多详细信息请参考`$man mmap`。



**shm**是另一内存映射方式，它可以将多个进程的不同虚拟地址内存块映射到同一物理地址内存块上从而实现共享内存，映射起来比较简单。它用如下结构体表示一块共享内存：
```
struct shmid_ds {
               struct ipc_perm shm_perm;
               size_t          shm_segsz;   /* size of segment */
               pid_t           shm_cpid;    /* PID of creator */
               pid_t           shm_lpid;    /* PID, last operation */
               shmatt_t        shm_nattch;  /* no. of current attaches */
               time_t          shm_atime;   /* time of last attach */
               time_t          shm_dtime;   /* time of last detach */
               time_t          shm_ctime;   /* time of last change */
           };
```
shm方式的两个关键操作：
- `int shmget(key_t key, size_t size, int flag);`
- `void *shmat(int shmid, const void *shmaddr, int shmflg);`

很明显，以上两种共享内存都是基于进程的，这一点不同于消息队列。可参考[shared memory](http://users.cs.cf.ac.uk/Dave.Marshall/C/node27.html)


-----
### 套接字

套接字常用于网络中不同机器之间的进程通信，但是它也可以用于单机的进程之间的通信，一个socket表示一个endpoint，它的唯一标识是`ip + port`，这样其实也就指定了正工作在该port上的一个进程。







