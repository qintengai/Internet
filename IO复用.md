#### I/O复用

##### 1、I/O多路复用

本质是通过一种机制(系统内核缓冲I/O数据)，让单个进程可以监视多个文件描述符，一旦某个描述符就绪(一般是都就绪或写就绪)，能够通知程序进行相应的读写操作。

指内核一旦发现进程指定的一个或者多个IO条件准备读取，它就通知该进程。IO 多路复用适用如下场合：

- 当客户处理多个描述字时(一般是交互式输入和网络套接口)，必须使用I/O复用。
- 当一个客户同时处理多个套接口时，而这种情况时可能的，但很少出现。
- 如果一个TCP服务器既要处理监听套接口，又要处理已连接套接口，一般也要用到I/O复用。
- 如果一个服务器既要处理TCP，又要处理UDP，一般要使用I/O复用。
- 如果一个服务器要处理多个服务或多个协议，一般要使用I/O复用。

与多进程和多线程技术相比，I/O多路复用技术的最大优势是系统开销小，系统不必创建进程/线程，也不必维护这些进程/线程。从而大大减小了系统的开销。

Linux中基于socket的通信本质也是一种I/O，使用socket() 函数创建的套接字默认都是阻塞的，这意味着当sockets API的调用不能立即完成时，线程一直处于等待状态，知道操作完成获得结果或者超时出错。会引起阻塞的socket API分为以下四种：

- 输入操作：recv()、recvfrom()。以阻塞套接字为参数调用该函数接收数据时，如果套接字缓冲区内没有数据可读，则调用线程在数据到来前一直睡眠
- 输出操作：send()、sendto()。以阻塞套接字为参数调用该函数发送数据时，如果套接字缓冲区没有可用空间，线程会一直睡眠，直到有空间。
- 接收连接：accept()。以阻塞套接字为参数调用该函数，等待接受对方的连接请求。如果此时没有连接请求，线程就会进入睡眠状态。
- 外出连接：connect()。对于TCP连接，客户端以阻塞套接字为参数，调用该函数向服务器发起连接。该函数在收到服务器的应答前，不会返回。这意味着TCP连接总会等待至少服务器的一次往返时间。



##### 2、select  poll  epoll

区别：

- select，poll，epoll都是IO[多路复用机制](https://www.zhihu.com/search?q=多路复用机制&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A272891398})，即可以监视多个描述符，一旦某个描述符就绪（读或写就绪），能够通知程序进行相应读写操作。 但select，poll，epoll本质上都是**同步I/O**，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。

- select，poll实现需要自己不断轮询所有fd集合，直到设备就绪，期间可能要睡眠和唤醒多次交替。而epoll其实也需要调用epoll_wait不断轮询就绪链表，期间也可能多次睡眠和唤醒交替，但是它是设备就绪时，调用回调函数，把就绪fd放入就绪链表中，并唤醒在epoll_wait中进入睡眠的进程。虽然都要睡眠和交替，但是select和poll在“醒着”的时候要遍历整个fd集合，而epoll在“醒着”的时候只要判断一下就绪链表是否为空就行了，这节省了大量的CPU时间。这就是回调机制带来的性能提升。
- select，poll每次调用都要把fd集合从用户态往内核态拷贝一次，并且要把current往设备等待队列中挂一次，而epoll只要一次拷贝，而且把current往等待队列上挂也只挂一次（在epoll_wait的开始，注意这里的等待队列并不是设备等待队列，只是一个epoll内部定义的等待队列）。这也能节省不少的开销。

select和poll的区别：

- select使用的是定长数组，而poll是通过用户自定义数组长度的形式(pollfd[])

- select只支持最大fd < 1024,如果单个进程的文件句柄数超过1024，select就不能用了。poll在接口上无限制，考虑到每次都要拷贝到内核，一般文件句柄多的情况下建议用epoll

- select由于使用的是位运算，所以select需要分别设置read/write/error fds的掩码。而poll是通过设置数据结构中fd和event参数来实现read/write，比如读为POLLIN,写为POLLOUT，出错为POLLERR：

  ```
  struct pollfd pfd;
  pfd.fd = fd;
  pfd.events = POLLIN;
  pfd.revents = 0;
  ```

- select中fd_set是被内核和用户共同修改的，所以要么每次FD_CLR再FD_SET，要么备份一份memcpy进去。而poll中用户修改的是events，系统修改的是revents。所以参考muduo的代码，都不需要自己去清除revents，从而使得代码更加简洁。

- select的timeout使用的是struct timeval *timeout，poll的timeout单位是int。

- select使用的是绝对时间，poll使用的是相对时间。

- select的精度是微秒（timeval的分度），poll的精度是毫秒。

- select的timeout为NULL时表示无限等待，否则是指定的超时目标时间；poll的timeout为-1表示无限等待。所以有用select来实现usleep的。

- 

select  poll 和epoll的区别：

- select poll 每次都需要将用户空间的数据 拷贝到内核空间
- 内核检查就绪描述符时，轮询的方式 O(n)
- 遍历所有描述符，检索就绪描述符，O(n)

在大量描述符的情况下，select poll 性能就降低 使用epoll能解决

- epoll每个描述符只需要向内核空间拷贝一次
- epoll 内核实现：通过注册回调函数完成的
- epoll返回后，直接提供就绪描述符，应用程序检索就绪描述符的时间复杂度O(1)





















