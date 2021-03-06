
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->
<!-- code_chunk_output -->

* [TCP连接的状态](#tcp连接的状态)
* [三次握手](#三次握手)
* [四次挥手](#四次挥手)
* [如何保障TCP连接的可靠性](#如何保障tcp连接的可靠性)
* [拥塞控制算法](#拥塞控制算法)
	* [慢开始与拥塞避免](#慢开始与拥塞避免)
	* [快重传与快恢复](#快重传与快恢复)

<!-- /code_chunk_output -->

# TCP连接的状态

<img src = assets/markdown-img-paste-20181009173627542.png width = 300>

```
LISTEN - 侦听来自远方TCP端口的连接请求；
SYN-SENT -在发送连接请求后等待匹配的连接请求；
SYN-RECEIVED - 在收到和发送一个连接请求后等待对连接请求的确认；
ESTABLISHED- 代表一个打开的连接，数据可以传送给用户；
FIN-WAIT-1 - 等待远程TCP的连接中断请求，或先前的连接中断请求的确认；
FIN-WAIT-2 - 从远程TCP等待连接中断请求；
CLOSE-WAIT - 等待从本地用户发来的连接中断请求；
CLOSING -等待远程TCP对连接中断的确认；
LAST-ACK - 等待原来发向远程TCP的连接中断请求的确认；
TIME-WAIT -等待足够的时间以确保远程TCP接收到连接中断请求的确认；
CLOSED - 没有任何连接状态；
```

1. 客户端独有的：（1）SYN_SENT （2）FIN_WAIT1 （3）FIN_WAIT2 （4）CLOSING （5）TIME_WAIT 。
2. 服务器独有的：（1）LISTEN （2）SYN_RCVD （3）CLOSE_WAIT （4）LAST_ACK 。
3. 共有的：（1）CLOSED （2）ESTABLISHED 。

# 三次握手
1. 首先由Client发出请求连接即，SYN=1，ACK=0(请看头字段的介绍)，TCP规定SYN=1时不能携带数据，但要消耗一个序号，因此声明自己的序号是 seq=x。
2. 然后 Server 进行回复确认，即 SYN=1 ACK=1 seq=y，ack=x+1。
3. 再然后 Client 再进行一次确认，但不用SYN 了，这时即为 ACK=1, seq=x+1，ack=y+1。

# 四次挥手
1. Client发送一个FIN，用来关闭Client到Server的数据传送，Client进入FIN_WAIT_1状态。
2. Server收到FIN后，发送一个ACK给Client，确认序号为收到序号+1（与SYN相同，一个FIN占用一个序号），Server进入CLOSE_WAIT状态。
3. Server发送一个FIN，用来关闭Server到Client的数据传送，Server进入LAST_ACK状态。
4. Client收到FIN后，Client进入TIME_WAIT状态，接着发送一个ACK给Server，确认序号为收到序号+1，Server进入CLOSED状态，完成四次挥手。

# 如何保障TCP连接的可靠性
- 分割数据块
- 超时重传：开启一个计时器，如果没有得到发送的数据报的ACK报文，那么就重新发送数据，直到发送成功为止
	- 主机A发送数据给B之后, 可能因为网络拥堵等原因, 数据无法到达主机B;
	- 主机A在一个特定时间间隔内没有收到B发来的确认应答, 会进行重发。
- 发送包有错，直接丢弃，等待发送端重新发送
- 流量控制，保障缓冲区正常

# 拥塞控制算法
TCP连接维护了一个拥塞窗口（cwnd）

## 慢开始与拥塞避免
1. 慢开始：通过改变拥塞窗口cwnd，指数倍的增长1->2->4，先探测一下网络的拥塞程度，也就是说由小到大逐渐增加拥塞窗口的大小
2. 拥塞避免：每次增加1，线性增长，增长很慢，发现拥塞，就会将慢开始门限值设置当前cwnd的一半，cwnd设置为1，重新开始慢开始算法ssthresh门限：避免拥塞窗口增长过快，导致网络拥塞
> cwnd<ssthresh：慢开始算法(指数增长)
> cwnd>ssthresh：拥塞避免算法（线性增长，每次+1）
> cwnd=ssthresh：都可以

3. 到达拥塞窗口之后，门限值将为cwnd的一半，重新执行慢开始算法，重复1，2的操作

## 快重传与快恢复
前面不变，重要的是拥塞之后  
快重传的意思是，在发送三个重复确认之后，会重新发送丢失报文  
> 慢开始门限值设置当前cwnd的一半
> 执行拥塞避免算法（加法算法）
