# io多路复用

常见的io多路复用模型有select、pselect、poll、epoll、kqueue等。

### select

先看看select接口的原型：
`int select(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);`

第2、3、4个参数就是三个句柄集合，里面放着需要监听的io事件，分别表示读、写、异常事件。而第1个参数nfds是这三个句柄集合中的最大的句柄号码加1，这是为了效率而设计的参数，提供的准确一些会有效率提升。第5个参数`timeout`用于指定监听时长，可以是
- null，无论是否有事件，只检查一遍就返回
- 0，阻塞式监听，没有事件发生就不会返回
- 大于0，监听指定时长后无论是否有事件发生，必定返回；若有事件提前发生则立刻返回

挺灵活的监听方式，它的检查过程就是线性扫描0到nfds之间的句柄，如果有事件发生就会返回，如果select调用之前已经有准备好的事件，那么一调用之后就会多个事件就绪了待处理。其实第2～4参数主要就是个整型数组，每个元素有32位(long型)，可以用于表示32个句柄，说白了就是bitset啦，glibc中定义的大小是1024bit大小的数组，表示0～1023的句柄号。这样一来，select能监听到的句柄号小于1024，否则得到的结果不是预期的那样，这是select接口的限制，可以通过修改头文件中的`FD_SETSIZE`这个宏来增大它(可能要重新编译)，在部分系统上可以直接在你的程序中重新定义这个宏，就不用去修改头文件了，可惜linux不行。

select做的是轮询，可以监听的句柄有限，适用于句柄不多的情况，否则效率也不高，所以如果监听量比较大的话应该选择其他io模型比较好。在select返回之后检查一下返回值，若大于0，则表示有事件就绪，此时需要自己逐个遍历三个集合中的句柄检测哪个就绪了。

- 修改FD_SETSIZE可参考 [dealing with FD_SETSIZE on select
](https://stackoverflow.com/questions/12355927/dealing-with-fd-setsize-on-select)。
- glibc的/include/sys/select.h文件中有select和pselect的定义。


### pselect

`int pselect(int nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, const struct timespec *timeout, const sigset_t *sigmask);`

功能就跟select一样，多了最后一个参数而已。所以ppoll同样也不列出来了，看poll即可。


### poll

`int poll(struct pollfd *fds, nfds_t nfds, int timeout);`

poll与select非常相似，只有部分细节不同。最大的区别是poll未设连接数上限，这就比select的1024好很多了。poll的普及性虽然不如select，但是现在的主流系统都有支持，所以问题不大。poll可以突破连接数上限是因为第1个参数是一个数组，由我们自己定义并填充的，想要多大就多大，而select是需要通过设置select里面的三个数组的，这三个数组并不归我们管理，大小不可控(即不容易修改)。第2个参数需要告诉poll第1个参数究竟给了多少个结构体，第3个参数是超时项，同select一样。

- 关于poll和select的区别可参考 [What are the differences between poll and select?
](https://stackoverflow.com/questions/970979/what-are-the-differences-between-poll-and-select)


### epoll

epoll主要提供了三个函数(忽略其他变种)：
`int epoll_create(int size);`
`int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout);`
`int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);`

epoll是poll和select的改进版本，它同样不限制连接数(其实不可能没有限制，只是它允许的量很大，数量上限就是`/proc/sys/fs/file-max`，我的机器中的值约80万)。关于LT和ET模式，阻塞与非阻塞，暂时不讲了，后面再补充(TODO)。


epoll的高效之处在于它的机制，当使用epoll_create时就会创建一棵红黑树和一个链表，红黑树用于存放待监听的句柄，而链表存放已触发事件的句柄，返回值代表一个epoll实例。当使用ctl往epoll实例中添加待监听句柄时，内核在epoll_wait之前就已经开始监听了，当事件发生时就会往链表中放待处理的句柄，但是内核没有通知我们，直到我们调用了epoll_wait之后，如果链表非空，则返回，如果链表为空，则等待timeout时间后或者有事件时才返回。epoll没有使用轮询，是以回调的方式在句柄事件发生时将句柄添加到链表的，所以效率高。而且添加和删除待监听的句柄是用红黑树维护的，效率也高。还有一个细节，当我们使用epoll_ctl往里边添加待监听句柄时，会将句柄(一个整型大小)拷贝到内核内存区，等到调用epoll_wait后才会将就绪的句柄拷贝回用户内存区(量很少)，之后再监听的话就无需再次添加进去了，因为红黑树(位于内核内存区)还没有被销毁，结构也没有被破坏。而select每次返回之后，如果需要再次监听，都要重新拷贝到内核内存区，相比起来epoll省去了多次拷贝的过程(反复监听的情况下)。

综上，epoll高效之处就是：
- 减少内存拷贝次数
- 减少句柄遍历，以中断的方式来回调，从而代替轮询

参考文章[epoll 或者 kqueue 的原理是什么？](https://www.zhihu.com/question/20122137)

### kqueue

待补充

















