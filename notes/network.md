# 感谢
- [CyC2018/CS-Notes](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C.md)

---
# 目录
#### [1.基础知识](#1-基础知识)
#### [2.IP](#2-IP)
#### [3.TCP](#3-TCP)
#### [4.UDP](#4-UDP)
#### [5.SCTP](#4-SCTP)

---


---
# 1. 基础知识
[^_^]:
	以下是引用
	[OSI_tcp_five]: https://github.com/CyC2018/CS-Notes/blob/master/pics/426df589-6f97-4622-b74d-4a81fcb1da8e.png
	[osi]: /pics/osi7.png
	[tcp_ip]: /pics/tcpip.png
	[tcp_ip_protocol]: https://github.com/CyC2018/CS-Notes/raw/master/pics/d4eef1e2-5703-4ca4-82ab-8dda93d6b81f.png


## 1.1 计算机网络体系结构
![OSI_tcp_five]


各层次含义为：

![osi]

- TCP/IP 协议层的主要协议

![tcp_ip_protocol]

![tcp_ip]


## 1.2 物理层

通信方式：

- 单工通信：单向传输
- 半双工通信：双向交替传输
- 全双工通信：双向同时传输


## 1.3 数据链路层
[^_^]:
	以下是引用
	[数据链路层详解]: https://blog.csdn.net/xiongqiaochu/article/details/65653132
	[数据链路层]: https://blog.csdn.net/xiongqiaochu/article/details/65653132
	[frame]: /pics/frame.png
	[pppframe]: /pics/pppframe.png
	[macFrame]: /pics/macframe.png
	
- 实现网卡接口的网络驱动程序，处理数据在物理媒介上的传输。
- 两个常用的协议：
	- ARP(Address Resolve Protocol): 地址解析协议
	- RARP(Reverse Address Resolve Protocol): 逆地址解析协议
	- 主要实现IP地址和及机器物理地址（MAC地址）之间的转换
	
#### 主要功能
- 链路管理
- 帧同步
- 流量控制
- 差错控制
- 寻址
- 透明传输


#### 信道
1. 点对点信道，一对一的通信方式 
2. 广播信道，一对多的广播通信方式

- 通常使用的多是点对点的信道
	- HDLC协议： 面向比特流的
	- PPP协议： 面向字节流的
	- HDLC在控制字段中提供了可靠的确认机制，因此它可以实现可靠传输，而PPP则不提供可靠传输
	- 全世界使用得最多的数据链路层协议是PPP协议


#### 帧
- [数据链路层]

![frame]

- 以太网帧
![macFrame]
- 帧部：18字节
- MTU: 46-1500字节；

- ppp 帧

![pppframe]

- ppp属于广域网范畴，MAC是局域网范畴;
- 按实际情况和环境就选用不同的协议，ppp支持的网络结构只能是点对点，mac支持多点对多点。
- 以太网中用mac，远程的话就用ppp（如ADSL拨号，就是基于ppp的）。



#### 信道复用
- 频分复用
频分复用的所有主机在相同的时间占用不同的频率带宽资源。

- 时分复用
时分复用的所有主机在不同的时间占用相同的频率带宽资源。

使用频分复用和时分复用进行通信，在通信的过程中主机会一直占用一部分信道资源。但是由于计算机数据的突发性质，通信过程没必要一直占用信道资源而不让出给其它用户使用，因此这两种方式对信道的利用率都不高。

- 统计时分复用

是对时分复用的一种改进，不固定每个用户在时分复用帧中的位置，只要有数据就集中起来组成统计时分复用帧然后发送。

- 波分复用

光的频分复用。由于光的频率很高，因此习惯上用波长而不是频率来表示所使用的光载波。


- 码分复用


#### CSMA/CD 协议
CSMA/CD 表示载波监听多点接入 / 碰撞检测。

- **多点接入** ：说明这是总线型网络，许多主机以多点的方式连接到总线上。
- **载波监听** ：每个主机都必须不停地监听信道。在发送前，如果监听到信道正在使用，就必须等待。
- **碰撞检测** ：在发送中，如果监听到信道已有其它主机正在发送数据，就表示发生了碰撞。虽然每个主机在发送数据之前都已经监听到信道为空闲，但是由于电磁波的传播时延的存在，还是有可能会发生碰撞。
记端到端的传播时延为 τ，最先发送的站点最多经过 2τ 就可以知道是否发生了碰撞，称 2τ 为**争用期** 。只有经过争用期之后还没有检测到碰撞，才能肯定这次发送不会发生碰撞。

当发生碰撞时，站点要停止发送，等待一段时间再发送。这个时间采用 **截断二进制指数退避算法** 来确定。从离散的整数集合 {0, 1, .., (2k-1)} 中随机取出一个数，记作 r，然后取 r 倍的争用期作为重传等待时间。

![](https://github.com/CyC2018/CS-Notes/raw/master/pics/5aa82b89-f266-44da-887d-18f31f01d8ef.png)



## 1.4 网络层
[^_^]:
	[icmp]: /pics/icmp.png
	[icmp详解]: https://blog.csdn.net/jxch____/article/details/78876995

- 实现数据包的选路和转发。
- 网络层的主要协议：
	- IP协议
	- ICMP协议（Internet Control Message Protocol, 因特网控制报文协议）


#### ICMP 
- [icmp详解]

- 格式

![icmp]


- 分类
	- ICMP差错报告报文
	- ICMP询问报文
	
![](https://github.com/CyC2018/CS-Notes/raw/master/pics/aa29cc88-7256-4399-8c7f-3cf4a6489559.png)






# 2. IP

# 3. TCP

# 4. UDP

# 5. SCTP




