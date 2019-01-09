# 三次握手 #
TCP的三次握手，之前的理解一直在于，这三次握手做了什么，没有对，为什么这么做有充分的理解。读到这篇文章（https://mp.weixin.qq.com/s/guP3jAE-C72H3ecEzXlHww）对这个动作的必要性做了解释。

先说三次握手确切的比喻
```
三次握手：
A -> B: 我们做下同步操作（SYN），我这边SEQ（sequence numbers 序列号）是100
B -> A: 我同步（SYN），我这边的SEQ是300，我确认（ACK），你下面应该发101号分段
A -> B: 我确认（ACK），我会发101号包，对应你那边301号包
A -> B: 我确认（ACK），这是我这边的101号包，对应你那边的301号包，<包>
```

```
TCP设计的描述
A fundamental notion in the design is that every octet of data sent
over a TCP connection has a sequence number. Since every octet is
sequenced, each of them can be acknowledged. 
The acknowledgment mechanism employed is cumulative so that an acknowledgment of sequence
number X indicates that all octets up to but not including X have been
received.
```
TPC的基本设定之一，是每个包，有一个序列号，接收端确认收到包的机制是累计的，如果对一个 SEQ为 X的包，确认收到，那么意味着，该连接中，序列号在X之前的包，已经全部被确认收到
序列号机制的详细步骤如下：
1. 连接建立
2. 生成一个初始序列号，32位；
3. 基于初始序列号，进行握手确认。

之所以需要三次握手，主要为了同步双方的SEQ,从而即使在双方生成SEQ方法不一致，没有全局时钟的情况下，仍然可以防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误。





PS
一种初始序列号生成方法
ISN = M + F(localhost, localport, remotehost, remoteport).
M是一个计时器，这个计时器每隔4毫秒加1。会循环，但是循环时间大于包在网络中的最大分段寿命，所以已经失效的请求，可以被它防止掉。及时循环，仍然是有效的
F是一个Hash算法，根据源IP、目的IP、源端口、目的端口生成一个随机数值。要保证hash算法不能被外部轻易推算得出，用MD5算法是一个比较好的选择。

