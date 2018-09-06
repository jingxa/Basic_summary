

# 一、 IO模型

[^_^]:
	图片
	[pic_block_io]: /pics/io/block_io.png
	[pic_nonblock_io]: /pics/io/nonblock_io.png
	[pic_multiplex_io]: /pics/io/multiplex_io.png
	[pic_sig_io]: /pics/io/sig_io.png
	[pic_aio]: /pics/io/aio.png
	[pic_five_conclude]: /pics/io/five_conclude.png
	
	
## 1.1 阻塞式IO模型

![pic_block_io]

## 1.2 非阻塞式IO模型

![pic_nonblock_io]

## 1.3 IO复用

![pic_multiplex_io]

## 1.4 信号驱动式IO

![pic_sig_io]

## 1.5 异步I

![pic_aio]


## 1.6 比较

![pic_five_conclude]


- 前四种都是同步IO模型
- 异步IO模型才与POSIX定义的异步IO相匹配；

---

# 二、 IO复用 函数

[^_^]:
	图片
	[pic_select]: /pics/io/select_con.png


## 2.1 select函数

```C++
#include <sys/seclect.h>
#include <sys/time.h>

int select(int maxfdp1, fd_set* readset , fd_set* writeset, fd_set* exceptset,
		const struct timeval *timeout);
			// 返回： 若有就绪描述符则为器数目，若超时为0， 若出错为-1

			
void FD_ZERO(fd_set* fdset); // 清空fdset所有位			
void FD_SET(int fd, fd_set* fdset);  // 打开fd对应的位
void FD_CLR(int fd, fd_set* fdset);	 // 关闭fd对应的位
int FD_ISSET(int fd, fd_set* fdset);   // 判断fd是否在fdset中
			
			
```

- select函数的设定和返回都在同一个列表中，因此每回调用，都必须重置
- 返回： 若有就绪描述符则为器数目，若超时为0， 若出错为-1

### (1) 描述符就绪条件

#### 读就绪
四个条件之一满足就select 返回套接字“就绪”：

1. 该套接字**接收缓冲区**的数据字节数大于等于接收缓冲区的**低水位标记**的当前大小。可以使用`SO_RCVLOWAT`套接字选项来设置低水位标记，TCP,UDP默认为1；

2. 该链接的读半关闭（接收了FIN的TCP链接）。套接字的读操作将不阻塞并返回0

3. 该套接字是一个监听套接字并且已完成的连接数不为0。这样的套接字accept将不阻塞并返回0(返回EOF）

4. 其上有一个套接字错误待处理。这样的套接字的读操作不阻塞并返回-1(也就是返回一个错误，同时设置errno),这些待处理错误可以用`SO_ERROR`套接字选项使用`getsockopt`获取消除；


#### 写就绪

1. 该套接字**发送缓冲区**的**可用空间字节数**大于等于发送缓冲区的**低水位标记**的当前大小。可以使用`SO_SNDLOWAT`套接字选项来设置低水位标记，TCP,UDP默认为2048；

2. 该链接的写半关闭。套接字的写操作将产生**SIGPIPE**信号；

3. 使用非阻塞connect的套接字已建立连接，或者connect已经失败告终

4. 其上有一个套接字错误待处理。这样的套接字的读操作不阻塞并返回-1(也就是返回一个错误，同时设置errno),这些待处理错误可以用`SO_ERROR`套接字选项使用`getsockopt`获取消除；

#### 异常处理

- 如果一个套接字存在带外数据或者处于带外标记，那么有异常条件处理；


【注意】
- 某个套接字发生错误，select标记即可读也可写；
- 水位标记： 运行应用进程控制selecet返回可读或可写前有多少数据可读或多大空间用于写；
	
- 任何UDP只要发送水位小于等于发送区大小就可以写，因为UDP不需要连接。


![pic_select]


### (2) select 的最大描述符总数

- 1024或者2048



---

## 2.2 pselect函数

```c++

#include <sys/seclect.h>
#include <signal.h>
#include <time.h>

int pselect(int maxfd1, fd_set* readset, fd_set* writeset, fd_set* exceptset,
		const struct timespec *timeout, const sigset_t * sigmask);
```

- 返回： 有就绪描述符返回数目，超时返回0， 出错返回-1

### 与 select 不同之处

1. pselect 使用 timespec 结构，而不使用 timeval 结构；

```
struct timespec{
	time_t tv_sec;		// 秒
	long tv_nsec;		// 纳秒
};
```

2. pselect 增加了参数： 指向信号掩码的指针。该参数运行程序禁止递交某些信号，在测试由这些当前被制止信号的信号处理函数设置的全局变量，然后调用pselect，好俗他重新设置信号掩码。




---

## 2.3 poll函数

- 没有最大描述符限制

```C++
#include <poll.h>
int poll(struct pollfd *fdarray, unsigned long nfds, int timeout);

```
- 返回： 若有就绪描述符则为器数目，若超时为0，出错返回-1

**pollfd**:

```C++
struct pollfd {
	int fd;
	short events;    // 兴趣事件
	short revents;   // 返回事件
};
```

**nfds**:

- 指定 pollfd数组的个数








## 2.3 shutdown函数

终止网络连接close的限制：
1. close把描述符的引用减去1，仅在计数变为0的时候才关闭套接字。shutdown可以不管计数就激发TCP的正常连接终止序列。

2. close终止读和写两个方向的数据传送。
	
	
```c++

#include <sys/socket.h>
int shutdown(int sockfd, int howto);

```
	
- 返回： 若成功则0，出错返回-1

**howto**:

- SHUT_RD（0）: 关闭连接的读的这一半---套接字中不再有数据可接收，接收缓冲区中的数据全被丢弃。进程不能对套接字调用任何读函授。

- SHUT_WR（1）:	关闭连接写的这一半---**半关闭**，当前套接字发送缓冲中的数据全部被丢弃。后跟TCP的正常连接终止序列。进程不能对套接字调用任何写函数。

- SHUT_RDWR（2）： 连接读写都关闭。等效两次shutdown不同的调用；
	
### (1)
