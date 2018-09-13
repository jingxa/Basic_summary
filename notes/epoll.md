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

事件：

```c++
//保存触发事件的某个文件描述符相关的数据（与具体使用方式有关）
  
typedef union epoll_data {
     void *ptr;
     int fd;
     __uint32_t u32;
     __uint64_t u64;
 } epoll_data_t;
 
  //感兴趣的事件和被触发的事件
 struct epoll_event {
     __uint32_t events; /* Epoll events */
     epoll_data_t data; /* User data variable */
 };


```

#### events可以是以下几个宏的集合：
 
EPOLLIN ：表示对应的文件描述符可以读（包括对端SOCKET正常关闭）； 
EPOLLOUT：表示对应的文件描述符可以写； 
EPOLLPRI：表示对应的文件描述符有紧急的数据可读（这里应该表示有带外数据到来）； 
EPOLLERR：表示对应的文件描述符发生错误； 
EPOLLHUP：表示对应的文件描述符被挂断； 
EPOLLET： 将EPOLL设为边缘触发(Edge Triggered)模式，这是相对于水平触发(Level Triggered)来说的。 
EPOLLONESHOT：只监听一次事件，当监听完这次事件之后，如果还需要继续监听这个socket的话，需要再次把这个socket加入到EPOLL队列里

#### 工作模式

- LT模式：Level Triggered水平触发

这个是缺省的工作模式。同时支持block socket和non-block socket。内核会告诉程序员一个文件描述符是否就绪了。如果程序员不作任何操作，内核仍会通知。
 
- ET模式：Edge Triggered 边缘触发

是一种高速模式。仅当状态发生变化的时候才获得通知。这种模式假定程序员在收到一次通知后能够完整地处理事件，于是内核不再通知这一事件。注意：缓冲区中还有未处理的数据不算状态变化，所以ET模式下程序员只读取了一部分数据就再也得不到通知了，正确的用法是程序员自己确认读完了所有的字节（一直调用read/write直到出错EAGAIN为止）。





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

### (2) eppoll_entry


```c++
/* poll所用到的钩子Wait structure used by the poll hooks */
struct eppoll_entry {

    struct list_head llink;  // 双向连接，连接到struct epitem

    struct epitem *base;	// 指向 本结构体的Container --- struct epitem

    wait_queue_t wait;		// 连接到 wait queue 头部的 目标文件

    wait_queue_head_t *whead;  //连接“wait” wait epitem 的等待队列头
};


```



### (3) ep_pqueue: epoll_ctl中的使用

```c++

/* Wrapper struct used by poll queueing */
struct ep_pqueue {
    poll_table pt;
    struct epitem *epi;
};

```

### (4) ep_send_events_data 

```c++

/* Used by the ep_send_events() function as callback private data */
struct ep_send_events_data {
    int maxevents;
    struct epoll_event __user *events;
};

```


### (5) eventpoll

而每个epoll fd对应的主要数据结构为:

```c++

struct eventpoll {
    spin_lock_t lock;//对本数据结构的访问
    struct mutex mtx;//防止使用时被删除
    wait_queue_head_t wq;//sys_epoll_wait() 使用的等待队列
    wait_queue_head_t poll_wait;//file->poll()使用的等待队列
    struct list_head rdllist;//事件满足条件的链表
    struct rb_root rbr;//用于管理所有fd的红黑树
    struct epitem *ovflist;//将事件到达的fd进行链接起来发送至用户空间
}


```
	
## 4. epoll 的初始化

```c++

static int __init eventpoll_init(void)
{
   mutex_init(&pmutex);

   ep_poll_safewake_init(&psw);

   epi_cache = kmem_cache_create("eventpoll_epi", sizeof(struct epitem), 0, SLAB_HWCACHE_ALIGN|EPI_SLAB_DEBUG|SLAB_PANIC, NULL);

   pwq_cache = kmem_cache_create("eventpoll_pwq", sizeof(struct eppoll_entry), 0, EPI_SLAB_DEBUG|SLAB_PANIC, NULL);

   return 0;

}



```


# 二、 源码分析整理

## 1.epoll 的创建

// 你没看错, 这就是epoll_create()的真身, 基本啥也不干直接调用epoll_create1了

```c++

SYSCALL_DEFINE1(epoll_create, int, size)
{
        if (size <= 0)
                return -EINVAL;
        return sys_epoll_create1(0);
}

```

- epoll_create1

<details><summmary>SYSCALL_DEFINE1</summary>

```c++

SYSCALL_DEFINE1(epoll_create1, int, flags)
{
    int error;
    struct eventpoll *ep = NULL;	//主描述符
    /* 这句没啥用处... */
    BUILD_BUG_ON(EPOLL_CLOEXEC != O_CLOEXEC);
	
    /* 对于epoll来讲, 目前唯一有效的FLAG就是CLOEXEC */
    if (flags & ~EPOLL_CLOEXEC)
        return -EINVAL;
		

    /* 分配一个struct eventpoll, 分配和初始化细节我们随后深聊~ */
    error = ep_alloc(&ep);
    if (error < 0)
        return error;
		

    /* 这里是创建一个匿名fd, 说起来就话长了...长话短说:
     * epollfd本身并不存在一个真正的文件与之对应, 所以内核需要创建一个
     * "虚拟"的文件, 并为之分配真正的struct file结构, 而且有真正的fd.
     * 这里2个参数比较关键:
     * eventpoll_fops, fops就是file operations, 就是当你对这个文件(这里是虚拟的)进行操作(比如读)时,
     * fops里面的函数指针指向真正的操作实现, 类似C++里面虚函数和子类的概念.
     * epoll只实现了poll和release(就是close)操作, 其它文件系统操作都有VFS全权处理了.
     * ep, ep就是struct epollevent, 它会作为一个私有数据保存在struct file的private指针里面.
     * 其实说白了, 就是为了能通过fd找到struct file, 通过struct file能找到eventpoll结构.
     */
    error = anon_inode_getfd("[eventpoll]", &eventpoll_fops, ep,
                 O_RDWR | (flags & O_CLOEXEC));
    if (error < 0)
        ep_free(ep);
    return error;
}

```
</details>

#### epoll_alloc : 初始化结构


1. 通过`get_cunrent_user`获得用户信息
2. 通过`kzalloc()`分配空间
3. 失败, 释放获得的用户信息并退出
4. 初始化自旋锁
5. 加锁
6. 初始化等待队列, 就绪队列, 红黑树根
7. ep 保存用户信息, 红黑树头, 等待队列和就绪队列


<details><summmary>ep_alloc</summary>

```c++
static int ep_alloc(struct eventpoll **pep)
{
    int error;
    struct user_struct *user;
    struct eventpoll *ep;
    /* 获取当前用户的一些信息, 比如是不是root啦, 最大监听fd数目啦 */
    user = get_current_user();
    error = -ENOMEM;
    ep = kzalloc(sizeof(*ep), GFP_KERNEL);  // 申请空间, kzalloc()与kalloc基本一样, 只是会在申请空间之后将其清零, 避免以前的数据影响.
    
	if (unlikely(!ep))
        goto free_uid;
		
    /* 这些都是初始化啦 */
    spin_lock_init(&ep->lock);
    mutex_init(&ep->mtx);
    init_waitqueue_head(&ep->wq);//初始化自己睡在的等待队列
    init_waitqueue_head(&ep->poll_wait);//初始化
    INIT_LIST_HEAD(&ep->rdllist);//初始化就绪链表
	
	
    ep->rbr = RB_ROOT;
    ep->ovflist = EP_UNACTIVE_PTR;
    ep->user = user;
    *pep = ep;
    return 0;
	
	
free_uid:
    free_uid(user);
    return error;
}

```



其中： init_waitqueue_head

```c++
// kernel/wait.c
void init_waitqueue_head(wwait_queue_head_t *q)
{
    spin_lock_init(&q->lock);
    INIT_LIST_HEAD(&q->task_list);
}
```
</details>
	
好了, epoll_create重要的分配空间已经讲了, 剩下还有一个重点是创建匿名文件, 返回匿名文件的文件描述符.
	
	
####  anon_inode_getfd: 创建匿名文件

<details><summmary>anon_inode_getfd</summary>

```c++
// 创建文件描述符, inode 指针的指向
int anon_inode_getfd(const char *name, const struct file_operations *fops,
             void *priv, int flags)
{
    struct qstr this;
    struct dentry *dentry;
    struct file *file;
    int error, fd;

    // 完成文件描述符的分配
    error = get_unused_fd_flags(flags);
    if (error < 0)
        return error;
    fd = error;

    error = -ENOMEM;
    // 初始化即将放入目录项中的文件名
    this.name = name;
    this.len = strlen(name);
    this.hash = 0;
	
    // 文件名信息加入到目录项中
    dentry = d_alloc(anon_inode_mnt->mnt_sb->s_root, &this);
    if (!dentry)
        goto err_put_unused_fd;
    // 为文件分配空间, 映射地址
    ...

    return fd;
err_dput:
    ...
}


```

</details>


其中,定义了一个别名：

<details><summmary>alloc_fd</summary>

```c++

// include/linux/file.h
#define get_used_fd_flags(flags) alloc_fd(0, (flags))

```
	
	
	
```c++

// 创建文件, 文件, 返回文件描述符
// 这里传入的 start的值为 0
int alloc_fd(unsigned start, unsigned flags)
{
    // currnet指向当前进程的指针, 通过current获得进程的文件表
    struct files_struct *files = current->files;
    unsigned int fd;
    int error;
    struct fdtable *fdt;

    spin_lock(&files->file_lock);
repeat:
    // 通过进程的打开文件列表获得文件描述符位图结构, 说白了就是打开进程的文件描述符表
    fdt = files_fdtable(files);
    fd = start;
    if (fd < files->next_fd)
        fd = files->next_fd;
    if (fd < fdt->max_fds)
        // 在文件描述符表中找到一个空闲的bit位, 找到空闲的bit位就相当于找到一个文件描述符
        fd = find_next_zero_bit(fdt->open_fds->fds_bits,fdt->max_fds, fd);
    // 通过fd值, 判断是否对文件描述符表进行扩展
    error = expand_files(files, fd);
    // 错误判断
    ...
    if (start <= files->next_fd)
        files->next_fd = fd + 1;

    FD_SET(fd, fdt->open_fds);
    if (flags & O_CLOEXEC)
        FD_SET(fd, fdt->close_on_exec);
    else
        FD_CLR(fd, fdt->close_on_exec);
    ...
}

```	
</details>	
	
#### 总结
1. 函数从`SYSCALL_DEFINE1(epoll_create)`开始, 调用`ep_alloc`函数对`eventpoll`分配内存空间, 然后再初始化红黑树, 就绪队列链表等, 
2. 最重要的是alloc_fd函数的调用, 为epoll创建一个存放数据的匿名文件, 并为文件设置好inode索引号, 文件名存放在目录项中
3. 最后, 返回文件打开的文件描述符, 将文件的信息放入进程的task_struct结构中的文件描述符表中.
	
	
	
--- 

## 2. epoll_ctl 

1. 调用`copy_from_user`将数据从用户空间拷贝到内核空间
2. 通过`fget()`获得获得`epoll_create()`创建的匿名文件的文件指针.
3. 进行 `epoll_ctl()` 传入的 op方法的判断.
	
	
<details><summmary>SYSCALL_DEFINE4</summary>


```c++

SYSCALL_DEFINE4(epoll_ctl, int, epfd, int, op, int, fd,
        struct epoll_event __user *, event)
{
    int error;
    struct file *file, *tfile;
    struct eventpoll *ep;
    struct epitem *epi;
    struct epoll_event epds;
    error = -EFAULT;
	
    /*
     * 错误处理以及从用户空间将epoll_event结构copy到内核空间.
     */
    if (ep_op_has_event(op) &&
        copy_from_user(&epds, event, sizeof(struct epoll_event)))
        goto error_return;
		
    /* 取得struct file结构, epfd既然是真正的fd, 那么内核空间
     * 就会有与之对于的一个struct file结构
     * 这个结构在epoll_create1()中, 由函数anon_inode_getfd()分配 */
	 
    error = -EBADF;
    file = fget(epfd);
    if (!file)
        goto error_return;
		

    /* 我们需要监听的fd, 它当然也有个struct file结构, 上下2个不要搞混了哦 */
    tfile = fget(fd);
    if (!tfile)
        goto error_fput;
		
    /* The target file descriptor must support poll */
    error = -EPERM;
    /* 如果监听的文件不支持poll, 那就没辙了.
     * 你知道什么情况下, 文件会不支持poll吗?
     */
    if (!tfile->f_op || !tfile->f_op->poll)
        goto error_tgt_fput;
		
    /*
     * We have to check that the file structure underneath the file descriptor
     * the user passed to us _is_ an eventpoll file. And also we do not permit
     * adding an epoll file descriptor inside itself.
     */
	 
    error = -EINVAL;
    /* epoll不能自己监听自己... */
    if (file == tfile || !is_file_epoll(file))
        goto error_tgt_fput;
		

    /* 取到我们的eventpoll结构, 来自与epoll_create1()中的分配 */
    ep = file->private_data;
	
    /* 接下来的操作有可能修改数据结构内容, 锁之~ */
    mutex_lock(&ep->mtx);

	
    /* 对于每一个监听的fd, 内核都有分配一个epitem结构,
     * 而且我们也知道, epoll是不允许重复添加fd的,
     * 所以我们首先查找该fd是不是已经存在了.
     * ep_find()其实就是RBTREE查找, 跟C++STL的map差不多一回事, O(lgn)的时间复杂度.
	 
	   ep_find : 二叉查找文件
        1. 通过将ep_set_ffd()将文件描述符和文件指针加入到一个结构体中
        2. 调用ep_cmp_ffd()进行节点与要查找的文件进行比较, 二叉搜索
     */
	 
    epi = ep_find(ep, tfile, fd);
    error = -EINVAL;
	
    switch (op) {
	
    case EPOLL_CTL_ADD:  // 添加
        if (!epi) {
            /* 之前的find没有找到有效的epitem, 证明是第一次插入, 接受!
             * 这里我们可以知道, POLLERR和POLLHUP事件内核总是会关心的
             * */
            epds.events |= POLLERR | POLLHUP;
			
            /* rbtree插入, 详情见ep_insert()的分析
             * 其实我觉得这里有insert的话, 之前的find应该
             * 是可以省掉的... */
			 
            error = ep_insert(ep, &epds, tfile, fd);
        } else
            /* 找到了!? 重复添加! */
            error = -EEXIST;
        break;
		
        /* 删除和修改操作都比较简单 */
    case EPOLL_CTL_DEL:
        if (epi)
            error = ep_remove(ep, epi);
        else
            error = -ENOENT;
        break;
    case EPOLL_CTL_MOD:
        if (epi) {
            epds.events |= POLLERR | POLLHUP;
            error = ep_modify(ep, epi, &epds);
        } else
            error = -ENOENT;
        break;
    }
    mutex_unlock(&ep->mtx);
error_tgt_fput:
    fput(tfile);
error_fput:
    fput(file);
error_return:
    return error;
}

```

</details>	


#### ep_insert
	

<details><summmary>ep_insert</summary>
	
```c++

/*
 * ep_insert()在epoll_ctl()中被调用, 完成往epollfd里面添加一个监听fd的工作
 * tfile是fd在内核态的struct file结构
 */
static int ep_insert(struct eventpoll *ep, struct epoll_event *event,
             struct file *tfile, int fd)
{
    int error, revents, pwake = 0;
    unsigned long flags;
    struct epitem *epi;
    struct ep_pqueue epq;
	
	
    /* 查看是否达到当前用户的最大监听数 */
    if (unlikely(atomic_read(&ep->user->epoll_watches) >=
             max_user_watches))
        return -ENOSPC;
		
	
    /* 从著名的slab中分配一个epitem */
    if (!(epi = kmem_cache_alloc(epi_cache, GFP_KERNEL)))
        return -ENOMEM;
		
	
    /* Item initialization follow here ... */
    /* 这些都是相关成员的初始化... */
    INIT_LIST_HEAD(&epi->rdllink);
    INIT_LIST_HEAD(&epi->fllink);
    INIT_LIST_HEAD(&epi->pwqlist);
    epi->ep = ep;
	
	
    /* 这里保存了我们需要监听的文件fd和它的file结构 */
    ep_set_ffd(&epi->ffd, tfile, fd);
    epi->event = *event;
    epi->nwait = 0;
	
    /* 这个指针的初值不是NULL哦... */
    epi->next = EP_UNACTIVE_PTR;
	
	
    /* Initialize the poll table using the queue callback */
    /* 好, 我们终于要进入到poll的正题了 */
    epq.epi = epi;
	
    /* 初始化一个poll_table
     * 其实就是指定调用poll_wait(注意不是epoll_wait!!!)时的回调函数,和我们关心哪些events,
     * ep_ptable_queue_proc()就是我们的回调啦, 初值是所有event都关心 
	 */
	 
    init_poll_funcptr(&epq.pt, ep_ptable_queue_proc);
	

    /* 这一部很关键, 也比较难懂, 完全是内核的poll机制导致的...
     * 首先, f_op->poll()一般来说只是个wrapper, 它会调用真正的poll实现,
     * 拿UDP的socket来举例, 这里就是这样的调用流程: f_op->poll(), sock_poll(),
     * udp_poll(), datagram_poll(), sock_poll_wait(), 最后调用到我们上面指定的
     * ep_ptable_queue_proc()这个回调函数...(好深的调用路径...).
     * 完成这一步, 我们的epitem就跟这个socket关联起来了, 当它有状态变化时,
     * 会通过ep_poll_callback()来通知.
     * 最后, 这个函数还会查询当前的fd是不是已经有啥event已经ready了, 有的话
     * 会将event返回. */
	 
    revents = tfile->f_op->poll(tfile, &epq.pt);
	
    /*
     * We have to check if something went wrong during the poll wait queue
     * install process. Namely an allocation for a wait queue failed due
     * high memory pressure.
     */
    error = -ENOMEM;
    if (epi->nwait < 0)
        goto error_unregister;
		
    /* Add the current item to the list of active epoll hook for this file */
    /* 这个就是每个文件会将所有监听自己的epitem链起来 */
	
    spin_lock(&tfile->f_lock);
    list_add_tail(&epi->fllink, &tfile->f_ep_links);
    spin_unlock(&tfile->f_lock);
	

    /* 都搞定后, 将epitem插入到对应的eventpoll中去 */
    ep_rbtree_insert(ep, epi);
	
    spin_lock_irqsave(&ep->lock, flags);
	
	
    /* 到达这里后, 如果我们监听的fd已经有事件发生, 那就要处理一下 */
	
    if ((revents & event->events) && !ep_is_linked(&epi->rdllink)) {
	
        /* 将当前的epitem加入到ready list中去 */
        list_add_tail(&epi->rdllink, &ep->rdllist);
		
		
        /*
		 * 如果有应用程序在等待事件发生，那么就唤醒上层等待事件发生的应用程序，
		 * 那ep->wq这个成员是在什么时候被添加了等待epoll事件发生的应用程序信息
		 * 的呢?这个就是通过epoll_wait()系统调用来感知的。当有应用程序调用了
		 * epoll_wait()的时候，在ep_poll()函数中就会把当前应用程序current对象
		 * 加入到ep->wq成员中。
		 */
        if (waitqueue_active(&ep->wq))
            wake_up_locked(&ep->wq);
			
        /* 谁在epoll当前的epollfd, 也唤醒它... */
        if (waitqueue_active(&ep->poll_wait))
            pwake++;
    }
	
    spin_unlock_irqrestore(&ep->lock, flags);
	
	/* 增加当前用户加入epoll中的文件描述符个数 */
    atomic_inc(&ep->user->epoll_watches);
	
    /* We have to call this outside the lock */
    if (pwake)
        ep_poll_safewake(&ep->poll_wait);
    return 0;
error_unregister:
    ep_unregister_pollwait(ep, epi);
    /*
     * We need to do this because an event could have been arrived on some
     * allocated wait queue. Note that we don't care about the ep->ovflist
     * list, since that is used/cleaned only inside a section bound by "mtx".
     * And ep_insert() is called with "mtx" held.
     */
    spin_lock_irqsave(&ep->lock, flags);
    if (ep_is_linked(&epi->rdllink))
        list_del_init(&epi->rdllink);
    spin_unlock_irqrestore(&ep->lock, flags);
    kmem_cache_free(epi_cache, epi);
    return error;
}


```	

</details>

ep_insert()函数主要做了如下三件事：
1. 创建并初始化一个strut epitem类型的对象，完成该对象和被监控事件（包括fd、struct epoll_event类型的对象）以及epoll对象eventpoll的关联。
2. 将struct epitem类型的对象加入到epoll对象eventpoll的红黑树中管理起来。
3. 将struct epitem类型的对象加入到被监控事件对应的目标文件的等待列表中，并注册事件就绪时会调用的回调函数，在epoll中该回调函数就是ep_poll_callback()。
	
	
具体的调用如下：
	
1. 调用`kmem_cache_alloc`为 epitem 从 slab 申请缓存空间
2. 调用`ep_set_ffd`将文件描述符和文件指针加入ffd结构体中
3. 调用`init_poll_funcptr()`设置回调函数为`ep_ptable_queue_proc`
4. 掉用`ep_rbtree_insert()` 将epi, 加入到ep中

	
#### ep_rbtree_insert

- `init_poll_funcptr `将函数 `poll_queue_proc()`来设置为回调函数

```c++

// 将qproc注册到 poll_table_struct 中
// 因为执行 f_op->poll() 时. XXX_poll() 函数会执行 poll_wait() 回调函数, 而 poll_wait()又会调用 poll_table_struct 中的 qproc

static inline void init_poll_funcptr(poll_table *pt, poll_queue_proc qproc)
{
    pt->qproc = qproc;
}

```

#### ep_ptable_queue_proc函数


```c++
/*
 * 该函数在调用f_op->poll()时会被调用.
 * 也就是epoll主动poll某个fd时, 用来将epitem与指定的fd关联起来的.
 * 关联的办法就是使用等待队列(waitqueue)
 */
static void ep_ptable_queue_proc(struct file *file, wait_queue_head_t *whead,
                 poll_table *pt)
{

	// 从注册的结构中struct ep_pqueue中获取项epi
    struct epitem *epi = ep_item_from_epqueue(pt);
	
	// eppoll_entry主要完成epitem和epitem事件发生时的callback（ep_poll_callback）函数之间的关联
    struct eppoll_entry *pwq;
	
	
    if (epi->nwait >= 0 && (pwq = kmem_cache_alloc(pwq_cache, GFP_KERNEL))) {
        /* 初始化等待队列, 指定ep_poll_callback为唤醒时的回调函数,
         * 当我们监听的fd发生状态改变时, 也就是队列头被唤醒时,
         * 指定的回调函数将会被调用. */
		 
        init_waitqueue_func_entry(&pwq->wait, ep_poll_callback);
        pwq->whead = whead;
        pwq->base = epi;
		
        /* 将刚分配的等待队列成员加入到头中, 头是由fd持有的 */
        add_wait_queue(whead, &pwq->wait);
        list_add_tail(&pwq->llink, &epi->pwqlist);
        /* nwait记录了当前epitem加入到了多少个等待队列中,
         * 我认为这个值最大也只会是1... */
        epi->nwait++;
    } else {
        /* We have to signal that an error occurred */
        epi->nwait = -1;
    }
}

```	


#### ep_poll_callback :

最后一次的调用, ep_poll_callback主要就只是将要回调事件的文件描述符(fd)加入到 epoll的监听队列中.

1. 加锁
2. 进行事件状态的判断, 没有事件, `goto out_unlink`
3. event_poll是否加入就绪队列中, `goto is_list`
4. 调用`list_add_tail` 加入链表
5. 将epi结构体加入就绪队列中
6. `is_list`段 : 调用waitqueue_active 判断就绪队列是否为空, 不为空计数值增加,
7. `out_unlock`段 :解除所有的锁
8. 退出


<details><summary>ep_poll_callback</summary>
	
```c++
/*
 * 这个是关键性的回调函数, 当我们监听的fd发生状态改变时, 它会被调用.
 * 参数key被当作一个unsigned long整数使用, 携带的是events.
 */
static int ep_poll_callback(wait_queue_t *wait, unsigned mode, int sync, void *key)
{
    int pwake = 0;
    unsigned long flags;
    struct epitem *epi = ep_item_from_wait(wait);//从等待队列获取epitem.需要知道哪个进程挂载到这个设备
    
	struct eventpoll *ep = epi->ep;//获取
    spin_lock_irqsave(&ep->lock, flags);
	


    if (!(epi->event.events & ~EP_PRIVATE_BITS))
        goto out_unlock;

		
    /* 没有我们关心的event... */
    if (key && !((unsigned long) key & epi->event.events))
        goto out_unlock;

		
    /*
     * 这里看起来可能有点费解, 其实干的事情比较简单:
     * 如果该callback被调用的同时, epoll_wait()已经返回了,
     * 也就是说, 此刻应用程序有可能已经在循环获取events,
     * 这种情况下, 内核将此刻发生event的epitem用一个单独的链表
     * 链起来, 不发给应用程序, 也不丢弃, 而是在下一次epoll_wait
     * 时返回给用户.
     */
    if (unlikely(ep->ovflist != EP_UNACTIVE_PTR)) {
        if (epi->next == EP_UNACTIVE_PTR) {
            epi->next = ep->ovflist;
            ep->ovflist = epi;
        }
		
        goto out_unlock;
    }
	
	
    /* 将当前的epitem放入ready list */
    if (!ep_is_linked(&epi->rdllink))
        list_add_tail(&epi->rdllink, &ep->rdllist);

    /* 唤醒epoll_wait... */
    if (waitqueue_active(&ep->wq))
        wake_up_locked(&ep->wq);
    /* 如果epollfd也在被poll, 那就唤醒队列里面的所有成员. */
    if (waitqueue_active(&ep->poll_wait))
        pwake++;
		
out_unlock:
    spin_unlock_irqrestore(&ep->lock, flags);
    /* We have to call this outside the lock */
    if (pwake)
        ep_poll_safewake(&ep->poll_wait);
    return 1;
}


```	

</details>	


### 总结

#### (1) SYSCALL_DEFINE4(epoll_create, int, epfd, int, op, int, fd, struct epoll_event __user*, event)

1. 调用`copy_from_user`将数据从用户空间拷贝到内核空间
2. 通过`fget()`获得获得`epoll_create()`创建的匿名文件的文件指针,被监控的文件描述符fd对应的struct fd类型的对象.
3. 通过`ep_find`寻找fd对应的 epitem；
4. 进行 `epoll_ctl()` 传入的 op方法的判断.


#### (2) ep_insert

- 向epoll中添加事件的时候，使用`ep_insert`函数

1. 判断监控描述符的数量是否达到最大
2. 调用`kmem_cache_alloc` 从slab cache中申请一个struct epitem类型的对象
3. 调用`ep_set_ffd`将文件描述符和文件指针加入ffd结构体中
4. 调用`init_poll_funcptr()`设置回调函数为`ep_ptable_queue_proc`
5. 掉用`ep_rbtree_insert()` 将struct epitem epi插入到红黑树中


#### (3) ep_ptable_queue_proc 

1. 从注册的结构中`struct ep_pqueue`中获取项epitem
2. 为`eppoll_entry`将申请缓存空间,`eppoll_entry`主要完成epitem和epitem事件发生时的callback（ep_poll_callback）函数之间的关联
3. `init_waitqueue_func_entry`设置`ep_poll_callback`函数为 poll 被唤醒时要调用的回调函数
4. 将其加入到等待队列中

#### (4) ep_poll_callback

函数主要的功能是将被监视文件的等待事件就绪时，将文件对应的epitem实例添加到就绪队列中，当用户调用epoll_wait()时，内核会将就绪队列中的事件报告给用户.


>这里主要讲的是EPOLL_CTL_ADD, 所以当是选择加入时, 就调用ep_insert函数, 将回调函数设置为ep_ptable_queue_proc函数, 也就是将消息到达后, 需要自动启动ep_ptable_proc函数, 进而调用ep_poll_callback函数, 该函数就是把来的消息所对应的结构和文件信息加入到就绪链表中, 以便之后调用 epoll_wait 可以直接从就绪队列链表中夺得就绪的文件. **也正是这样, epoll的回调函数使epoll不用每次都轮询遍历数据, 而是自动唤醒回调, 更加的高效. 并且回调函数也只是在进程加入的时侯才设置, 而且只设置一次.**




---


## 3. epoll_wait

epoll_wait()系统调用主要是用于收集在epoll中监控的就绪事件


### SYSCALL_DEFINE4(epoll_wait,..)

1. 判断最大值合法性
2. 获取匿名文件即epoll的文件指针, 取得文件信息中的`eventpoll`结构
3. 调用ep_poll阻塞自己, 等待有消息的到来


<details><summary>SYSCALL_DEFINE4</summary>

```c++

SYSCALL_DEFINE4(epoll_wait, int, epfd, struct epoll_event __user *, events,
        int, maxevents, int, timeout)
{
    int error;
    struct file *file;
    struct eventpoll *ep;
	
	
	// 判断最大值没有超过能承受的范围   
    if (maxevents <= 0 || maxevents > EP_MAX_EVENTS)
        return -EINVAL;
		
		
    /* 这个地方有必要说明一下:
     * 内核对应用程序采取的策略是"绝对不信任",
     * 所以内核跟应用程序之间的数据交互大都是copy, 不允许(也时候也是不能...)指针引用.
     * epoll_wait()需要内核返回数据给用户空间, 内存由用户程序提供,
     * 所以内核会用一些手段来验证这一段内存空间是不是有效的.
     */
	 
    if (!access_ok(VERIFY_WRITE, events, maxevents * sizeof(struct epoll_event))) {
        error = -EFAULT;
        goto error_return;
    }
	

	
    error = -EBADF;
    /* 获取epollfd的struct file, epollfd也是文件嘛 */
	
    file = fget(epfd);
    if (!file)
        goto error_return;
		
 
    error = -EINVAL;
	
    /* 检查一下它是不是一个真正的epollfd... */
    if (!is_file_epoll(file))
        goto error_fput;

		
    /* 获取eventpoll结构 */
    ep = file->private_data;

    /* OK, 睡觉, 等待事件到来~~ */
    error = ep_poll(ep, events, maxevents, timeout);
error_fput:
    fput(file);
error_return:
    return error;
}

```

</details>


epoll_wait()中最重要的函数其实是ep_poll, 它会完成等待消息到来, 执行, 没有消息到来就阻塞自己. 可以看出来这是epoll_wait的核心.


### ep_poll

<details><summary>ep_poll</summary>

```c++

/* 这个函数真正将执行epoll_wait的进程带入睡眠状态... */
static int ep_poll(struct eventpoll *ep, struct epoll_event __user *events,
           int maxevents, long timeout)
{
    int res, eavail;
    unsigned long flags;
    long jtimeout;
    wait_queue_t wait;//等待队列
	
	
    /* 计算睡觉时间, 毫秒要转换为HZ */
    jtimeout = (timeout < 0 || timeout >= EP_MAX_MSTIMEO) ?
        MAX_SCHEDULE_TIMEOUT : (timeout * HZ + 999) / 1000;
		
		
retry:
    spin_lock_irqsave(&ep->lock, flags);
    res = 0;
	
    /* 如果ready list不为空, 就不睡了, 直接干活... */
    if (list_empty(&ep->rdllist)) {

	
        /* OK, 初始化一个等待队列, 准备直接把自己挂起,
         * 注意current是一个宏, 代表当前进程 */
		 
        init_waitqueue_entry(&wait, current);//初始化等待队列,wait表示当前进程
        __add_wait_queue_exclusive(&ep->wq, &wait);//挂载到ep结构的等待队列
        for (;;) {

            /* 将当前进程设置位睡眠, 但是可以被信号唤醒的状态,
             * 注意这个设置是"将来时", 我们此刻还没睡! */
			 
            set_current_state(TASK_INTERRUPTIBLE);
			
            /* 如果这个时候, ready list里面有成员了,
             * 或者睡眠时间已经过了, 就直接不睡了... */
			 
            if (!list_empty(&ep->rdllist) || !jtimeout)
                break;
				
            /* 如果有信号产生, 也起床... */
            if (signal_pending(current)) {
                res = -EINTR;
                break;
            }
            /* 啥事都没有,解锁, 睡觉... */
            spin_unlock_irqrestore(&ep->lock, flags);
			
			
            /* jtimeout这个时间后, 会被唤醒,
             * ep_poll_callback()如果此时被调用,
             * 那么我们就会直接被唤醒, 不用等时间了...
             * 再次强调一下ep_poll_callback()的调用时机是由被监听的fd
             * 的具体实现, 比如socket或者某个设备驱动来决定的,
             * 因为等待队列头是他们持有的, epoll和当前进程
             * 只是单纯的等待...
             **/
			 
            jtimeout = schedule_timeout(jtimeout);//睡觉
            spin_lock_irqsave(&ep->lock, flags);
        }
		
		/* 从ep->wq等待队列中将调用了epoll_wait()的进程对应的节点移除 */
        __remove_wait_queue(&ep->wq, &wait);
		
        
		/* 设置当前进程的状态为RUNNING */
        set_current_state(TASK_RUNNING);
    }
	

	/* 
	 * 判断epoll对象的rdllist链表和ovflist链表是否为空，如果不为空，说明有就绪
	 * 事件发生，那么该函数返回1，否则返回0
	 */
	
    eavail = !list_empty(&ep->rdllist) || ep->ovflist != EP_UNACTIVE_PTR;
    spin_unlock_irqrestore(&ep->lock, flags);

	 
    /* 如果一切正常, 有event发生, 就开始准备数据copy给用户空间了... */
	
    if (!res && eavail &&
        !(res = ep_send_events(ep, events, maxevents)) && jtimeout)
        goto retry;
    return res;
}

```


</details>


1. 先设置好时间长度, 如果传过来的已经设置好的时间, 就设置为传入 的时间, 没有的话就默认一个初始时间.
2. 初始化就绪的等待队列
3. 对有没有进程准备好, 有消息传来或者对时间是否到达进行判断, 如果满足就退出循环, 执行进程.
4. 如果没有满足就执行进程调度, 让出cpu, 让其他的进程执行, 等待一定的时间后返回该进程继续进行判断, 有准备好的执行, 没有继续让出cpu.
5. 调用ep_send_evnets将就绪链表中的数据返回给用户空间.


在ep_poll 等待消息到来, 当消息来到后执行ep_send_events函数, 实现将链表从内核态复制到用户态.


### ep_send_events



```c++
static int ep_send_events(struct eventpoll *ep,
              struct epoll_event __user *events, int maxevents)
{
    struct ep_send_events_data esed;
    esed.maxevents = maxevents;
    esed.events = events;
    return ep_scan_ready_list(ep, ep_send_events_proc, &esed);
}



```


### ep_scan_ready_list

<details><summary>ep_scan_ready_list</summary>

```c++

static int ep_scan_ready_list(struct eventpoll *ep,
                  int (*sproc)(struct eventpoll *,
                       struct list_head *, void *),
                  void *priv)
{
    int error, pwake = 0;
    unsigned long flags;
    struct epitem *epi, *nepi;
    LIST_HEAD(txlist);
	
	
    mutex_lock(&ep->mtx);

    spin_lock_irqsave(&ep->lock, flags);
	
	
    /* 这一步要注意, 首先, 所有监听到events的epitem都链到rdllist上了,
     * 但是这一步之后, 所有的epitem都转移到了txlist上, 而rdllist被清空了,
     * 要注意哦, rdllist已经被清空了! */
	 
    list_splice_init(&ep->rdllist, &txlist);
	
    /* ovflist, 在ep_poll_callback()里面我解释过, 此时此刻我们不希望
     * 有新的event加入到ready list中了, 保存后下次再处理... */
	 
    ep->ovflist = NULL;
    spin_unlock_irqrestore(&ep->lock, flags);
	
 
	 
    /* 在这个回调函数里面处理每个epitem
     * sproc 就是 ep_send_events_proc, 下面会注释到. */
    error = (*sproc)(ep, &txlist, priv);
    spin_lock_irqsave(&ep->lock, flags);
	
    /* 现在我们来处理ovflist, 这些epitem都是我们在传递数据给用户空间时
     * 监听到了事件. */
	 
    for (nepi = ep->ovflist; (epi = nepi) != NULL;
         nepi = epi->next, epi->next = EP_UNACTIVE_PTR) {

		 
        /* 将这些直接放入readylist */
        if (!ep_is_linked(&epi->rdllink))
            list_add_tail(&epi->rdllink, &ep->rdllist);
    }

	
    ep->ovflist = EP_UNACTIVE_PTR;

	
    /* 上一次没有处理完的epitem, 重新插入到ready list */
    list_splice(&txlist, &ep->rdllist);
    /* ready list不为空, 直接唤醒... */
    if (!list_empty(&ep->rdllist)) {

        if (waitqueue_active(&ep->wq))
            wake_up_locked(&ep->wq);
        if (waitqueue_active(&ep->poll_wait))
            pwake++;
    }
	
    spin_unlock_irqrestore(&ep->lock, flags);
    mutex_unlock(&ep->mtx);
    /* We have to call this outside the lock */
    if (pwake)
        ep_poll_safewake(&ep->poll_wait);
    return error;
}

```

</details>


1. 将就绪队列的链表复制到另一个新的链表中, 并且清空就绪队列.
2. 将后面到来的一些新的event加入到ovflist中，交给下一次处理
3. `error = (*sproc)(ep, &txlist, priv);`处理前面的epitem 集合
4. 对于新的event，从ovflist中加入到rdlist中，再将txlist的未处理完的epitem加入到rdlist中




### ep_send_events_proc

<details><summary>ep_send_events_proc</summary>

```c++

/* 该函数作为callbakc在ep_scan_ready_list()中被调用
 * head是一个链表, 包含了已经ready的epitem,
 * 这个不是eventpoll里面的ready list, 而是上面函数中的txlist.
 */
static int ep_send_events_proc(struct eventpoll *ep, struct list_head *head,
                   void *priv)
{
    struct ep_send_events_data *esed = priv;
    int eventcnt;
    unsigned int revents;
    struct epitem *epi;
    struct epoll_event __user *uevent;

	
    /* 扫描整个链表... */
    for (eventcnt = 0, uevent = esed->events;
         !list_empty(head) && eventcnt < esed->maxevents;) {
		 
        /* 取出第一个成员 */
        epi = list_first_entry(head, struct epitem, rdllink);
		
        /* 然后从链表里面移除 */
        list_del_init(&epi->rdllink);
		
		
        /* 读取events,
         * 注意events我们ep_poll_callback()里面已经取过一次了, 为啥还要再取?
         * 1. 我们当然希望能拿到此刻的最新数据, events是会变的~
         * 2. 不是所有的poll实现, 都通过等待队列传递了events, 有可能某些驱动压根没传
         * 必须主动去读取. */
		 
        revents = epi->ffd.file->f_op->poll(epi->ffd.file, NULL) &
            epi->event.events;

			
        if (revents) {
		
            /* 将当前的事件和用户传入的数据都copy给用户空间,
             * 就是epoll_wait()后应用程序能读到的那一堆数据. */
            if (__put_user(revents, &uevent->events) ||
                __put_user(epi->event.data, &uevent->data)) {
                list_add(&epi->rdllink, head);
                return eventcnt ? eventcnt : -EFAULT;
            }
			
            eventcnt++;
            uevent++;
            if (epi->event.events & EPOLLONESHOT)
                epi->event.events &= EP_PRIVATE_BITS;
				
            else if (!(epi->event.events & EPOLLET)) {
                /* 嘿嘿, EPOLLET和非ET的区别就在这一步之差呀~
                 * 如果是ET, epitem是不会再进入到readly list,
                 * 除非fd再次发生了状态改变, ep_poll_callback被调用.
                 * 如果是非ET, 不管你还有没有有效的事件或者数据,
                 * 都会被重新插入到ready list, 再下一次epoll_wait
                 * 时, 会立即返回, 并通知给用户空间. 当然如果这个
                 * 被监听的fds确实没事件也没数据了, epoll_wait会返回一个0,
                 * 空转一次.
                 */
				 
                list_add_tail(&epi->rdllink, &ep->rdllist);
            }
        }
    }
    return eventcnt;
}

```


</details>


1. 从链表中取出第一个epitem并且移除，然后取出epitem的events
2. `__put_user` 将就绪事件拷贝到用户态内存中
3. 根据ET或者LT模式，如果是LT模式，将事件再次加入到ep->rdllist链表中





