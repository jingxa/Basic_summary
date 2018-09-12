# 参考资料

- [epoll源码实现分析[整理]](https://www.cnblogs.com/apprentice89/p/3234677.html)

- [epoll内核源码详解+自己总结的流程 精](https://www.nowcoder.com/discuss/26226?type=0&order=0&pos=27&page=1)




# epoll源码分析整理


## 1. epoll的API

```c++
#include<sys/epoll.h>
int epoll_create(int size);

int epoll_clt(int epfd, int op, int fd, struct epoll_event* event);

int epoll_wait(int epfd, struct epoll_event* events, int maxevents, int timeout);


```

## 2 epoll 的高效

1. select/poll每次调用都要传递所要监控的所有fd给select/poll系统调用
	- 这意味着每次调用都要将fd列表从用户态拷贝到内核态，当fd数目很多时，这会造成低效。
	- 而每次调用epoll_wait时（作用相当于调用select/poll），不需要再传递fd列表给内核，因为已经在epoll_ctl中将需要监控的fd告诉了内核（epoll_ctl不需要每次都拷贝所有的fd，只需要进行增量式操作）。所以，在调用epoll_create之后，内核已经在内核态开始准备数据结构存放要监控的fd了。
	- 每次epoll_ctl只是对这个数据结构进行简单的维护。

2. 内核使用了slab机制，为epoll提供了快速的数据结构：
	- 在内核里，一切皆文件。所以，epoll向内核注册了一个文件系统，用于存储上述的被监控的fd。
	- 当你调用epoll_create时，就会在这个虚拟的epoll文件系统里创建一个file结点。当然这个file不是普通文件，它只服务于epoll。
	- epoll在被内核初始化时（操作系统启动），同时会开辟出epoll自己的内核高速cache区，用于安置每一个我们想监控的fd，这些fd会以红黑树的形式保存在内核cache里，以支持快速的查找、插入、删除。
	- 这个内核高速cache区，就是建立连续的物理内存页，然后在之上建立slab层，简单的说，就是物理上分配好你想要的size的内存对象，每次使用时都是使用空闲的已分配好的对象。

3.  epoll的第三个优势在于：当我们调用epoll_ctl往里塞入百万个fd时，epoll_wait仍然可以飞快的返回，并有效的将发生事件的fd给我们用户。
	- 这是由于我们在调用epoll_create时，内核除了帮我们在epoll文件系统里建了个file结点，在内核cache里建了个红黑树用于存储以后epoll_ctl传来的fd外，还会再建立一个list链表，用于存储准备就绪的事件
	- 当epoll_wait调用时，仅仅观察这个list链表里有没有数据即可。有数据就返回，没有数据就sleep，等到timeout时间到后即使链表没数据也返回。所以，epoll_wait非常高效。
	- 而且，通常情况下即使我们要监控百万计的fd，大多一次也只返回很少量的准备就绪fd而已，所以，epoll_wait仅需要从内核态copy少量的fd到用户态而已。
	- 那么，这个准备就绪list链表是怎么维护的呢？当我们执行epoll_ctl时，除了把fd放到epoll文件系统里file对象对应的红黑树上之外，还会给内核中断处理程序注册一个回调函数，告诉内核，如果这个fd的中断到了，就把它放到准备就绪list链表里。
	- 所以，当一个fd（例如socket）上有数据到了，内核在把设备（例如网卡）上的数据copy到内核中后就来把fd（socket）插入到准备就绪list链表里了。
	
	
	
	
	
如此，一颗红黑树，一张准备就绪fd链表，少量的内核cache，就帮我们解决了大并发下的fd（socket）处理问题。
	
	
1.执行epoll_create时，创建了红黑树和就绪list链表。

2.执行epoll_ctl时，如果增加fd（socket），则检查在红黑树中是否存在，存在立即返回，不存在则添加到红黑树上，然后向内核注册回调函数，用于当中断事件来临时向准备就绪list链表中插入数据。

3.执行epoll_wait时立刻返回准备就绪链表里的数据即可。

 
	
	

	
## 3. epoll相关数据结构

### (1) epitem : 监听的fd

- 当向系统中添加一个fd时，就创建一个epitem结构体，这是内核管理epoll的基本数据结构：

```c++

struct epoitem{
    struct rb_node  rbn;        //用于 eventpoll 主结构管理的红黑树

    struct list_head  rdllink;  //事件就绪队列,被链接到 eventpoll 的rdlist中

    struct epitem  *next;       //用于 eventpoll 主结构体中的链表

	struct epoll_filefd  ffd;   //这个结构体对应的被监听的文件描述符信息，即epitem对应的fd 和 struct file

	int  nwait;                 //poll操作中事件的个数

    struct list_head  pwqlist;  //双向链表，保存着被监视文件的等待队列，功能类似于select/poll中的poll_table

    struct eventpoll  *ep;      //该项属于哪个主结构体（多个epitm从属于一个eventpoll）

    struct list_head  fllink;   //双向链表，用来链接被监视的文件描述符对应的struct file。因为file里有f_ep_link,用来保存所有监视这个文件的epoll节点

    struct epoll_event  event;  //注册的感兴趣的事件,也就是用户空间的epoll_event



};
```


```c++
struct epoll_filefd {
    struct file *file;
    int fd;
};

```





### (2) eventpoll



	
	
	
	
	
	
	
	
	
	
	
	
	
	
	