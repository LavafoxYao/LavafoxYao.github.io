---
title: Socket端口复用的作用
date: 2020-09-03 10:36:07
tags: ['网络编程']
categories: 网络编程
---

解释了`TIME_WAIT`的作用以及`socket`端口复用的作用.

<!--more-->

## Socket端口复用

#### TIME_WAIT

主动关闭的`Socket`端会进入`TIME_WAIT`状态，并且持续`2MSL`时间长度，`MSL`就是`maximum segment lifetime(最大分节生命期）`，这是一个`IP数据包`能在互联网上生存的最长时间，超过这个时间将在网络中消失。`MSL`在RFC 1122上建议是2分钟，而源自`berkeley`的TCP实现传统上使用30秒，因而，`TIME_WAIT`状态一般维持在1-4分钟。 

`TIME_WAIT`状态有**两个存在的理由**:

1. 可靠地实现`TCP`全双工的链接终止.

   > TIME_WAIT的存在以我自己的理解是为了接收被动断开方发来的`FIN`,并且给被动关闭方一个`ACK`应答.能够使得全双工的TCP连接正常的实现完全断开.
   >
   > UNP中给的解释:  在进行关闭连接四次挥手协议时，最后的ACK是由主动关闭端发出的。如果这个最终的ACK丢失，服务器将重发最终的FIN，因此客户端必须维护状态信息允 许它重发最终的ACK。如果不维持这个状态信息，那么客户端将响应RST分节，服务器将此分节解释成一个错误。因而，要实现TCP全双工连接的正常终止，必须处理终止序列四个分节中任何一个分节的丢失情况，主动关闭 的客户端必须维持状态信息进入TIME_WAIT状态。

   ![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/09/Snipaste_2020-09-03_10-06-29.png)

2. 允许老的重复分解在网络中消逝.

   > 假设在IP1：Port1（客户端）和IP2：Port2（服务端）之间有一个TCP连接，我们关闭这个连接，过一段时间仍在相同的IP和端口建立另一个连接，后一个连接称之为前一个连接的化身。TCP必须阻止老的重复分组在该连接终止后再出现，为做到这一点，TCP不给处于TIME_WAIT状态的分组发起新的化身。 既然TIME_WAIT状态的持续时间是2MSL，这足以人任一方向上的TCP数据报被丢弃，我们就能保证没成功建立一个TCP连接时，来自该连接先前的化身的老的重复分组都已经在网络中消逝了。



#### socket端口复用

```C
int optval = 1;
setsockopt(listen_fd, SOL_SOCKET,  SO_REUSEADDR, &optval, sizeof(optval));
```

**一般来说，一个端口释放后会等待两分钟之后才能再被使用，`SO_REUSEADDR`是让端口释放后立即就可以被再次使用。** 

