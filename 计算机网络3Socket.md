### I/O模型

输入操作：
1.等待数据准备好
2.从内核向进程复制数据

套接字上的输入操作：
1.等待数据从网络中到达，被等待数据到达时，被复制到内核中某缓冲区
2.把数据从内核缓冲区复制到应用进程缓冲区

UNIX五种I/O模型：
1.阻塞式I/O
2.非阻塞式I/O
3.I/O复用(select和poll)
4.信号驱动式I/O(SIGIO)
5.异步I/O(AIO)

#### 阻塞式

应用进程被阻塞，直到数据从内核缓冲区复制到应用进程缓冲区才返回
阻塞过程，其他应用进程可执行，不消耗CPU时间，CPU利用率效率较高

![image-20200225191452802](/Users/jacyn/Documents/study/Learning-materials/image/image-20200225191452802.png)

#### 非阻塞式

应用进程执行系统调用--->内核返回错误码--->应用进程继续执行，不断执行系统调用来获知I/O是否完成-------轮询(polling)
CPU要处理很多系统调用，利用率低
![image-20200225194124946](/Users/jacyn/Documents/study/Learning-materials/image/image-20200225194124946.png)

#### I/O复用

使用select或poll等待数据，等待多个套接字的任何一个变为可读。
这一过程阻塞，某一套接字可读时返回，再使用recvfrom把数据从内核复制到进程。
可让单个进程具有处理多个I/O事件的能录，又称event Driven I/O，即事件驱动I/O
若一个web服务器没有I/O复用，每一个Socket连接需要创建一个线程处理，进程线程创建和切换开销大。
![image-20200225194913257](/Users/jacyn/Documents/study/Learning-materials/image/image-20200225194913257.png)

#### 信号驱动I/O

应用进程使用Sigaction系统调用，内核立即返回，应用程序可继续执行，等待数据阶段非阻塞。
数据到达时，内核向应用进程发送SIGIO信号--->应用程序收到后再信号处理程序中调用revfrom将数据从内核复制到应用程序中。
CPU利用率更高
![image-20200225195239772](/Users/jacyn/Documents/study/Learning-materials/image/image-20200225195239772.png)

#### 异步I/O

应用进程执行aio_read系统调用后立即返回，不阻塞，内核在所有操作完成后向应用进程发送信号。

![image-20200225195412021](/Users/jacyn/Library/Application Support/typora-user-images/image-20200225195412021.png)

异步I/O 通知应用进程I/O完成
信号驱动I/O通知可以开始I/O

#### 五大I/O模型比较

同步I/O：数据从内核缓冲区复制到应用进程缓冲区阶段，应用进程会阻塞，如：阻塞式I/O，非阻塞式I/O，I/O复用，信号驱动I/O
异步I/O：不会阻塞
非阻塞式I/O，信号驱动I/O，异步I/O在第一阶段不会阻塞

![image-20200225195936123](/Users/jacyn/Documents/study/Learning-materials/image/image-20200225195936123.png)

### I/O复用

select/poll/epoll

#### select

```
int select(int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

readset、writeset、exceptset，分别对应读、写、异常条件的描述符集合。
fd_set 使用 数组实现，数组大小使用 FD_SETSIZE 定义。
timeout 为超时参数，调用 select 会一直阻塞直到有描述符的事件到达或者等待的时间超过 timeout。 
成功调用返回结果大于 0，出错返回结果为 -1，超时返回结果为 0。

#### poll

```
int poll(struct pollfd *fds, unsigned int nfds, int timeout);
```

pollfd 使用链表实现

#### 比较

##### 功能

1.select会修改描述符，而poll不会
2.select的描述符类型用数组实现，FD_SETSIZE大小默认为1024.要监听更多描述符，修改FD_SELECT之后重新编译：poll用链表，无描述符数量限制
3.poll提供更多事件类型，且对描述符的重复利用上比select高
4.如果一个线程对某个描述符调用了select或者poll，另一个线程关闭了该描述符，会导致结果不确定

##### 速度

select < poll
1.select和poll每次调用需将全部描述符从应用进程缓冲区复制到内核缓冲区
2.返回结果没有声明哪些描述符准备好，返回值大于0，应用程序需要使用轮询的方式找到I/O完成的描述符

##### 可移植性

所有的系统都支持sekect，只有新的系统支持poll

##### epoll

```
int epoll_create(int size); 
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)；用于向内核注册新的描述符或改变某个文件描述符的状态，已注册的描述符维护在红黑树，通过回调函数内核将I/O准备好的描述符加入到链表中管理
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);可得到事件完成的描述符
```

只需将描述符从进程缓冲区向内核缓冲区拷贝一次，且进程不需通过轮询获取事件完成的描述符
仅适用于linux
比select和poll更灵活，没有描述符数量限制
对多线程编程更友好，一个线程点用了epoll_wait()，另一个线程关闭了描述符，不会产生类似select和poll的不确定情况

###### 工作模式

1.LT(level trigger)模式
epoll_wait() 检测到描述符事件到达--->通知进程(进程可不立即处理该事件)--->再次调用epoll_wait() 再次通知进程。同时支持 Blocking 和 No-Blocking。
2.ET(edge trigger) 模式
通知进程必须立即处理事件，下次再调用时不会得到事件达到的通知
减少了epoll事件被重复触发的次数，效率比LT高，只支持No-Blocking
避免由于文件句柄的阻塞读/阻塞写--->饿死多个文件描述符的任务

##### 应用场景

1.select
timeout参数精度为1ns，poll和epoll为1ms。
select适用于实时性要求高的场景，如核反应堆的控制
可移植性好，几乎被所有主流平台支持
2.poll
没有最大描述符数量的限制，平台支持且对实时性要求不高，用poll
3.epoll
只需允许再LInux，有大量的描述符需要同时轮询，连接最好是长连接
需同时监控1000个描述符时没必要用epoll
需监控的描述符状态变化多，且短暂，没必要用epoll
因epoll的所有描述符都存储在内核中，不容易调试--->每次需要对描述符状态的改变需要通过epoll_ctl()进行系统调用，频繁系统调用降低频率。
