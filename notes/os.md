# 目录

#### [1. 基本概念](#1-基本概念)

#### [2. 进程和线程](#2-进程和线程)

#### [3. 内存管理](#3-内存管理)

#### [4. 设备管理](#4-设备管理)


---

# 1. 基本概念

## 1.1 操作系统的特征

#### 并发

- 并发是指宏观上在一段时间内能同时运行多个程序，而并行则指同一时刻能运行多个指令。

- 并行需要硬件支持，如多流水线或者多处理器。

- 操作系统通过引入进程和线程，使得程序能够并发运行。

#### 共享

- 共享是指系统中的资源可以被多个并发进程共同使用。

- 有两种共享方式：互斥共享和同时共享。

- 互斥共享的资源称为临界资源，例如打印机等，在同一时间只允许一个进程访问，需要用同步机制来实现对临界资源的访问。


#### 虚拟
- 虚拟技术把一个物理实体转换为多个逻辑实体。

- 主要有两种虚拟技术：时分复用技术和空分复用技术。例如多个进程能在同一个处理器上并发执行使用了时分复用技术，让每个进程轮流占有处理器，每次只执行一小个时间片并快速切换。

#### 异步

- 异步指进程不是一次性执行完毕，而是走走停停，以不可知的速度向前推进。


---

## 1.2 操作系统主要功能
1. 进程管理
2. 内存管理
3. 文件管理
4. 设备管理

---

## 1.3 中断分类

#### 外中断

由 CPU 执行指令以外的事件引起，如 I/O 完成中断，表示设备输入/输出处理已经完成，处理器能够发送下一个输入/输出请求。此外还有时钟中断、控制台中断等。

#### 异常

由 CPU 执行指令的内部事件引起，如非法操作码、地址越界、算术溢出等。

#### 陷入

在用户程序中使用系统调用。


---

## 1.3 系统调用

#### 定义和特点
- Linux内核中设置了一组用于实现各种系统功能的子程序，称为系统调用。
- 用户可以通过系统调用命令在自己的应用程序中调用它们。
- 从某种角度来看，系统调用和普通的函数调用非常相似。
	- 区别仅仅在于，系统调用由操作系统核心提供，运行于核心态；
	- 而普通的函数调用由函数库或用户自己提供，运行于用户态。


---
	
#### 用户空间的使用
linux下三种发生系统调用的方法

1. 通过 glibc 提供的库函数： 封装了操作系统提供的系统服务，即系统调用的封装。
2. 使用 syscall 直接调用
3. 通过 int 指令陷入：用户态程序通过软中断指令`int 0x80` 来陷入内核态，参数的传递是通过寄存器，eax 传递的是系统调用号，ebx、ecx、edx、esi和edi 来依次传递最多五个参数，当系统调用返回时，返回值存放在 eax 中。

---	
	
#### 系统调用过程

在设置了系统调用功能号和参数后，执行系统调用指令便开始进入系统调用，其一般处理过程如下：

1. 系统产生软中断（或陷入），由中断硬件完成部分现场信息保护： PSW 的值和 PC 寄存器的值；并通过中断向量转向系统总控中断处理程序完成其他 CPU 现场信息的保存：如陷入类型、参数表指针、其他 CPU 寄存器的值等

2. 然后转向执行系统调用处理程序：使用系统调用功能号查找系统调用入口表，找到相应系统调用的处理子程序的入口地址；

3. 执行系统调用处理子程序，完毕后返回执行成功与否以及成功时的执行结果给调用者；

4. 恢复被中断进程或新调度进程的 CPU 现场，返回被中断进程或新调度进程执行。





---

#### 常用的系统调用
- 具体的参考 [原Linux系统调用详解（实现机制分析）--linux内核剖析（六）](https://blog.csdn.net/gatieme/article/details/50779184)

##### 进程控制
- `fork,vfork,clone`
- `execve`
- `exit,_exit`
- `getpid, getppid,getpgid`
- `wait,wait3,wait4`

##### 文件系统控制
- `fcntl`
- `open,creat, close`
- `read,write, readv, writev`
- `lseek`
- `dup,dup2`

##### 文件系统操作
- `access,chdir,mkdir`
- `chmod,chown,chroot,stat,fstat`
- `link,unlink,mount`


##### 系统控制
- `ioctl, _sysctl, reboot`
- `alarm, time `

##### 内存管理
- `brk,sbrk`
- `mmap,nunmap,mprotect`

##### 网络管理
- `gethostid,sethostid, gethostname, sethostname`

##### 用户管理
- `getuid, getgid,setuid, setgid`

##### socket控制
- `socket, bind, connect, accept, send, recv, sednto,recvfrom.listen`

##### 进程通信

- `ipc`
- `sigaction, signal,kill `
- `msgctl,msgget,msgsnd,msgrcv`
-` pipe`
- `semctl,semget, semop`
- `shmctl, shmget`



---

# 2. 进程和线程

[^_^]:
	进程和线程
	[pic_process_state]: /pics/os/process_state.png
	[pic_thread_state]: /pics/os/thread_state.jpg


## 2.1 进程 和 线程

#### 定义

##### 进程
- 进程是具有独立功能的**程序**关于某个**数据集合**上的一次**运行活动**，是系统进行**资源分配和调度**（若不支持线程机制，进程的系统调度的单位。否则，线程是系统调度的单位）的独立单位。

- 引入进程是为了使多个程序可以并发的执行，以提高系统的资源利用率和吞吐量。

##### 线程
- 线程是进程的实体，是**CPU调度和分派的基本单位**，它是比进程更小的能独立运行的基本单位。

---

#### 进程和线程区别

##### (1)拥有资源

- 进程是资源分配的基本单位，但是线程不拥有资源，线程可以访问隶属进程的资源。

##### (2)调度方面
- 线程是独立调度的基本单位，在同一进程中，线程的切换不会引起进程切换；
- 从一个进程内的线程切换到另一个进程中的线程时，会引起进程切换。

##### (3)系统开销
- 由于创建或撤销进程时，系统都要为之分配或回收资源，如内存空间、I/O 设备等，所付出的开销远大于创建或撤销线程时的开销。
- 在进行进程切换时，涉及当前执行进程 CPU 环境的保存及新调度进程 CPU 环境的设置，而线程切换时只需保存和设置少量寄存器内容，开销很小。

##### (4)通信方面
- 进程间通信 (IPC) 需要进程同步和互斥手段的辅助，以保证数据的一致性。而线程间可以通过直接读/写同一进程中的数据段（如全局变量）来进行通信。

---

## 2.2 进程和线程状态转换图 

#### 进程

![pic_process_state]

#### 线程

![pic_thread_state]


---

## 2.3 进程同步和互斥

#### 定义

**临界区**：
- 对临界资源进行访问的那段代码称为临界区。为了互斥访问临界资源，每个进程在进入临界区之前，需要先进行检查。


**互斥**：

- 是指某一资源同时只允许一个访问者对其进行访问，具有唯一性和排它性。但互斥无法限制访问者对资源的访问顺序，即访问是无序的。

**同步**:

- 是指在互斥的基础上（大多数情况），通过其它机制实现访问者对资源的有序访问。

**简单地说**：同步体现的是一种协作性，互斥体现的是一种排他性。

---
#### 进程同步方式
- 原子操作
- 信号量机制
- 自旋锁管程
- 会合
- 分布式系统（暂时不懂。。。这几种）

#### 线程同步方式
- 互斥量
- 信号量
- 事件(信号)

---

#### linux同步方式

- 线程同步和进程同步的本质区别在于**锁放在哪**，放在私有的进程空间还是放在多进程共享的空间，并且看锁是否具备进程共享的属性，

##### 线程同步：

1. 互斥锁

2. 读写锁

3. 条件变量

4. 信号量

5. 自旋锁

6. 屏障（barrier）


##### 进程同步：

1. 互斥锁

2. 读写锁

3. 条件变量

4. 记录锁(record locking)

5. 信号量

6. 屏障（barrier）

---

#### 经典同步问题

- [经典同步问题](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F.md#经典同步问题)

---

## 2.4 进程间的通信

#### (1)管道

##### 无名管道

```c++
include <unistd.h>
int pipe(int fd[2]);
```

- fd[0]: 读取端;
- fd[1]: 写入端；
- 只支持半双工通信（单向交替传输）；
- 只能在父子进程中使用。
- 缓冲区有限

##### 有名管道 FIFO

```c++
#include <sys/stat.h>
int mkfifo(const char *path, mode_t mode);
int mkfifoat(int fd, const char *path, mode_t mode);
```
有名管道特点：

- 是FIFO文件，存在于文件系统中，可以通过文件路径名来指出。
- 可以在不具有亲缘关系的进程间进行通信。
- 长期存于系统中，使用不当容易出错
- 缓冲区有限


#### (2)信号量
- 信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。
- 多线程同步的信号量是POSIX信号量， 而在进程里使用SYSTEM V信号量。

- 优点：可以同步进程
- 缺点：信号量有限

#### (3)消息队列
- 消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。
- 消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。

- 优点：可以实现任意进程间的通信，并通过系统调用函数来实现消息发送和接收之间的同步，无需考虑同步问题，方便
- 缺点：信息的复制需要额外消耗 CPU 的时间，不适宜于信息量大或操作频繁的场合

#### (4)信号

- 信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

#### (5)共享内存
- 共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。
- 共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。
- 它往往与其他通信机制，如信号量，配合使用，来实现进程间的同步和通信。
- 缺点：
	- 通信是通过将共享空间缓冲区直接附加到进程的虚拟地址空间中来实现的，因此进程间的读写操作的同步问题
	- 利用内存缓冲区直接交换信息，内存的实体存在于计算机中，只能同一个计算机系统中的诸多进程共享，不方便网络通信

#### (6)套接字

- 套解字也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信。
- 优点：
	- 传输数据为字节级，传输数据可自定义，数据量小效率高
	- 传输数据时间短，性能高
	- 适合于客户端和服务器端之间信息实时交互
	- 可以加密,数据安全性强
- 缺点：需对传输的数据进行解析，转化成应用级的数据。


---

## 2.5 进程调度算法 

#### (1)先来先服务（FCFS，First-Come-First-Served
- 此算法的原则是按照作业到达后备作业队列（或进程进入就绪队列）的先后次序来选择作业（或进程）。

#### (2)短作业优先（SJF,Shortest Process Next
- 这种调度算法主要用于作业调度，它从作业后备队列中挑选所需运行时间（估计值）最短的作业进入主存运行。

#### (3)时间片轮转调度算法（RR，Round-Robin）
- 当某个进程执行的时间片用完时，调度程序便停止该进程的执行，并将它送就绪队列的末尾，等待分配下一时间片再执行。然后把处理机分配给就绪队列中新的队首进程，同时也让它执行一个时间片。这样就可以保证就绪队列中的所有进程，在一给定的时间内，均能获得一时间片处理机执行时间。

#### (4)高响应比优先（HRRN，Highest Response Ratio Next）
- 按照高响应比（（已等待时间＋要求运行时间）/ 要求运行时间）优先的原则，在每次选择作业投入运行时，先计算此时后备作业队列中每个作业的响应比RP然后选择其值最大的作业投入运行。

#### (5)优先权(Priority)调度算法
- 按照进程的优先权大小来调度，使高优先权进程得到优先处理的调度策略称为优先权调度算法。注意：优先数越多，优先权越小。

#### (6)多级队列调度算法
- 多队列调度是根据作业的性质和类型的不同，将就绪队列再分为若干个子队列，所有的作业（或进程）按其性质排入相应的队列中，而不同的就绪队列采用不同的调度算法。


---

## 2.6 死锁
死锁是指多个进程在运行过程中，因为争夺资源而造成的一种僵局，如果没有外力推进，处于僵局中的进程就无法继续执行。

#### 原因
1. 竞争资源
2. 进程推进顺序不当

#### 死锁的必要条件

1. **互斥条件**：一个资源一次只能被一个进程所使用，即是排它性使用。
2. **不可剥夺条件**：一个资源仅能被占有它的进程所释放，而不能被别的进程强占。
3. **请求与保持条件**：进程已经保持了至少一个资源，但又提出了新的资源要求，而该资源又已被其它进程占有，此时请求进程阻塞，但又对已经获得的其它资源保持不放。
4. **环路等待条件**：当每类资源只有一个时，在发生死锁时，必然存在一个进程-资源的环形链。

#### 预防死锁
- 破坏四个必要条件之一；
1. 打破互斥条件：改造独占性资源为虚拟资源，大部分资源已无法改造。
2. 打破不可抢占条件：当一进程占有一独占性资源后又申请一独占性资源而无法满足，则退出原占有的资源。
3. 打破占有且申请条件：采用资源预先分配策略，即进程运行前申请全部资源，满足则运行，不然就等待，这样就不会占有且申请。
4. 打破循环等待条件：实现资源有序分配策略，对所有设备实现分类编号，所有进程只能采用按序号递增的形式申请资源。

#### 死锁避免
- 银行家算法：该方法允许进程动态地申请资源，系统在进行资源分配之前，先计算资源分配的安全性。若此次分配不会导致系统从安全状态向不安全状态转换，便可将资源分配给进程；否则不分配资源，进程必须阻塞等待。从而避免发生死锁。
- 有序资源分配法

#### 死锁定理
- S为死锁状态的充分条件是：尚且仅当S状态的资源分配图是不可完全简化的，该充分条件称为死锁定理。

#### 死锁的删除
- 方法1
	- 强制性地从系统中撤消一个或多个死锁的进程以断开循环等待链，并收回分配给终止进程的全部资源供剩下的进程使用。
	
- 方法2
	- 使用一个有效的挂起和解除机构来挂起一些死锁的进程，其实质是从被挂起的进程那里抢占资源以解除死锁。


---

# 3. 内存管理

[^_^]:
	内存管理
	[pic_virtual_mem]: /pics/os/virtual_mem.png
	[pic_page_mem]: /pics/os/page_mem.png
	[pic_seg_mem]: /pics/os/seg_mem.png
	[pic_dynamic_mem]: /pics/os/dynamic_mem.png
	
## 3.1 虚拟内存

#### 局部性原理

1. 时间上的局部性：最近被访问的页在不久的将来还会被访问；

2. 空间上的局部性：内存中被访问的页周围的页也很可能被访问。


#### 内存的发展历程
1. 没有内存抽象(单进程，除去操作系统所用的内存之外，全部给用户程序使用)
2. 有内存抽象（多进程，进程独立的地址空间，交换技术(内存大小不可能容纳下所有并发执行的进程) 
3. 连续内存分配(固定大小分区(多道程序的程度受限)，可变分区(首次适应，最佳适应，最差适应)，碎片)
4. 不连续内存分配（分段，分页，段页式，虚拟内存）

#### 虚拟内存的思想
1. 每个进程拥有独立的地址空间，这个空间被分为大小相等的多个块，称为页(Page)，每个页都是一段连续的地址。
2. 这些页被映射到物理内存，但并不是所有的页都必须在内存中才能运行程序。
3. 当程序引用到一部分在物理内存中的地址空间时，由硬件立刻进行必要的映射；当程序引用到一部分不在物理内存中的地址空间时，由操作系统负责将缺失的部分装入物理内存并重新执行失败的命令

于进程而言，逻辑上似乎有很大的内存空间，实际上其中一部分对应物理内存上的一块(称为帧，通常页和帧大小相等)，还有一些没加载在内存中的对应在硬盘上，

![pic_virtual_mem]

---

## 3.2 分页和分段

### (1)分页式管理
- 把主存空间划分为大小相等且固定的块，块相对较小，作为主存的基本单位。

#### 页表
- 来记录逻辑地址和实际存储地址之间的映射关系，以实现从页号到物理块号的映射。

![pic_page_mem]

**快表**：

- 包含快表机制的内存管理中，当要访问内存数据的时候，首先将页号在快表中查询，如果查找到说明要访问的页表项在快表中，那么直接从快表中读取相应的物理块号；
- 如果没有找到，那么访问内存中的页表，从页表中得到物理地址，同时将页表中的该映射表项添加到快表中

### (2)分段式管理
- 分页是为了提高内存利用率，而分段是为了满足程序员在编写代码的时候的一些逻辑需求(比如数据共享，数据保护，动态链接等)。
- 地址是二维的，一维是段号，一维是段内地址；其中每个段的长度是不一样的，而且每个段内部都是从0开始编址的。

#### 段表
- 每个段内部是连续内存分配，但是段和段之间是离散分配的，因此也存在一个逻辑地址到物理地址的映射关系，相应的就是段表机制
- 段表中的每一个表项记录了该段在内存中的起始地址和该段的长度。段表可以放在内存中也可以放在寄存器中

![pic_seg_mem]

---

#### 区别

1. 目的：段是信息的逻辑单位，它是根据用户的需要划分的，因此段对用户是可见的 ；页是信息的物理单位，是为了管理主存的方便而划分的，对用户是透明的。
2. 大小：段的大小不固定，有它所完成的功能决定；页大大小固定，由系统决定（一般为4k）
3. 地址空间：段向用户提供二维地址空间；页向用户提供的是一维地址空间
4. 信息共享：段是信息的逻辑单位，便于存储保护和信息的共享，页的保护和共享受到限制。
5. 内存碎片： 页式存储管理的优点是没有外碎片（因为页的大小固定），但会产生内碎片（一个页可能填充不满）；而段式管理的优点是没有内碎片（因为段大小可变，改变段大小来消除内碎片）。但段换入换出时，会产生外碎片（比如4k的段换5k的段，会产生1k的外碎片）。

---

## 3.3 内存连续分配
- 主要是指动态分区分配时所采用的几种算法。

![pic_dynamic_mem]

#### (1)首次适应(First Fit)算法
- 空闲分区以地址递增的次序链接。分配内存时顺序查找，找到大小能满足要求的第一个空闲分区。

#### (2)最佳适应(Best Fit)算法
- 空闲分区按容量递增形成分区链，找到第一个能满足要求的空闲分区。

#### (3)最坏适应(Worst Fit)算法
- 又称最大适应(Largest Fit)算法，空闲分区以容量递减的次序链接。找到第一个能满足要求的空闲分区，也就是挑选出最大的分区


---

## 3.4 页面置换算法

#### (1)最佳置换算法（Optimal）

- 即选择那些永不使用的，或者是在最长时间内不再被访问的页面置换出去。（它是一种理想化的算法，性能最好，但在实际上难于实现）

#### (2)先进先出置换算法FIFO

- 该算法总是淘汰最先进入内存的页面，即选择在内存中驻留时间最久的页面予以淘汰。

#### (3)最近最久未使用置换算法LRU（Least Recently Used）

- 该算法是选择最近最久未使用的页面予以淘汰，系统在每个页面设置一个访问字段，用以记录这个页面自上次被访问以来所经历的时间T，当要淘汰一个页面时，选择T最大的页面。

#### (4)Clock置换算法(最近未用算法NRU（Not RecentlyUsed）)

- 页面设置一个访问位，并将页面链接为一个环形队列，页面被访问的时候访问位设置为1。
- 页面置换的时候，如果当前指针所指页面访问为为0，那么置换，否则将其置为0，
- 循环直到遇到一个访问为位0的页面。


#### (5)改进型CLOCK算法
每个页面都有两个状态位：R 与 M，当页面被访问时设置页面的 R=1，当页面被修改时设置 M=1。其中 R 位会定时被清零。可以将页面分成以下四类：

- R=0，M=0
- R=0，M=1
- R=1，M=0
- R=1，M=1

当发生缺页中断时，NRU 算法随机地从类编号最小的非空类中挑选一个页面将它换出。

优先换出已经被修改的脏页面（R=0，M=1），而不是被频繁使用的干净页面（R=1，M=0）。



#### (5)最少使用置换算法LFU

- 设置寄存器记录页面被访问次数，每次置换的时候置换当前访问次数最少的。

--- 

## 3.5 内存抖动现象 和Belady现象

#### 抖动
- 页面的频繁更换，导致整个系统效率急剧下降，这个现象称为内存抖动（或颠簸）。抖动一般是内存分配算法不好，内存太小引或者程序的算法不佳引起的。

**解决办法**：
1. 如果是因为页面替换策略失误，可以修改替换算法来解决这个问题；
2. 如果是因为运行的程序太多，造成程序无法同时将所有频繁访问的页面调入内存，则要降低多道程序的数量；
3. 否则，还剩下两个办法：终止该进程或增加物理内存容量。


#### Belady现象
- 对有的页面置换算法，页错误率可能会随着分配帧数增加而增加。
- FIFO会产生Belady异常。
- 栈式算法无Belady异常，LRU，LFU（最不经常使用），OPT都属于栈式算法。



# 4. 设备管理




# 参考文献

1. [CyC2018/CS-Notes](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F.md#%E5%9F%BA%E6%9C%AC%E7%89%B9%E5%BE%81)

2. [huihut/interview](https://github.com/huihut/interview#操作系统)

3. [linw7/Skill-Tree](https://github.com/linw7/Skill-Tree/blob/master/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F.md)

4. [面试/笔试第二弹 —— 操作系统面试问题集锦](https://blog.csdn.net/justloveyou_/article/details/78304294)
 
5. [操作系统面试重难点总结](https://www.jianshu.com/p/d254b138de03)

6. [常见面试题整理（操作系统）](https://blog.csdn.net/gui951753/article/details/79489748)

7. [操作系统与编译面试题](https://www.jianshu.com/p/b2ff3117faf1)