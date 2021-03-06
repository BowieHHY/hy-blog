### TCP

> 传输控制协议（Transition Control Protocol）是一种面向连接的、可靠的、基于字节流的传输层通信协议。

> *应用层次在传输层*

“ 应用层向TCP层发送用于网间传输的、用8位字节表示的数据流，然后TCP把数据流分区成适当长度的报文段（通常受该计算机连接的网络的数据链路层的最大传输单元（MTU）的限制）。之后TCP把结果包传给IP层，由它来通过网络将包传送给接收端实体的TCP层。TCP为了保证不发生丢包，就给每个包一个序号，同时序号也保证了传送到接收端实体的包的按序接收。然后接收端实体对已成功收到的包发回一个相应的确认（ACK）；如果发送端实体在合理的往返时延（[RTT](https://baike.baidu.com/item/RTT)）内未收到确认，那么对应的数据包就被假设为已丢失将会被进行重传。TCP用一个校验和函数来检验数据是否有错误；在发送和接收时都要计算校验和。 ”

TCP 负责既要足够快地发送数据报，以便使用网络容量，但又不能引起网络拥塞

TCP 必须把接受到的数据报重新装配成正确的顺序

#### 头部

![TCP/IP 协议| Colynn.Liu](https://user-images.githubusercontent.com/5203608/89868541-8c88f100-dbe5-11ea-87df-01f5c0577201.png)

- 序号：保证了 TCP 传输的报文都是有序的，对端可以通过序号顺序的拼接
- 确认号：表示数据接收端期望接收到的下一个字节的编号是多少，同时也表示上一个序号的数据已经收到
- 窗口 : 表示还能接受多少字节的数据，流量控制
- 标识符：
  - URG =1，为1表示本数据包的数据部分包含紧急信息，是一个高优先级数据报文，此时紧急指针有效。紧急数据一定位于当前数据包数据部分的最前面，紧急指针标明了紧急数据的尾部。
  - ACK =1：该字段为一表示确认号字段有效。此外，TCP 还规定在连接建立后传送的所有报文段都必须把 ACK 置为一。
  - PSH =1：该字段为一表示接收端应该立即将数据 push 给应用层，而不是等到缓冲区满后再提交。
  - RST =1：该字段为一表示当前 TCP 连接出现严重问题，可能需要重新建立 TCP 连接，也可以用于拒绝非法的报文段和拒绝连接请求。
  - SYN =1：当SYN=1，ACK=0时，表示当前报文段是一个连接请求报文。当SYN=1，ACK=1时，表示当前报文段是一个同意建立连接的应答报文。
  - FIN =1：该字段为一表示此报文段是一个释放连接的请求报文。

#### 工作方式

**建立连接**

- 三次握手协议

  ![图2 TCP的三次握手](https://bkimg.cdn.bcebos.com/pic/29381f30e924b899cb32f6316e061d950a7bf6a9?x-bce-process=image/resize,m_lfit,w_440,limit_1/format,f_auto)

  TCP三次握手的过程如下：

  1. 客户端发送SYN（SEQ=x）报文给服务器端，进入SYN_SEND状态。
  2. 服务器端收到SYN报文，回应一个SYN （SEQ=y）ACK（ACK=x+1）报文，进入[SYN_RECV](https://baike.baidu.com/item/SYN_RECV)状态。
  3. 客户端收到服务器端的SYN报文，回应一个ACK（ACK=y+1）报文，进入Established状态。

三次握手完成，TCP客户端和服务器端成功地建立连接，可以开始传输数据了。



> 常考面试题：为什么 TCP 建立连接需要三次握手，明明两次就可以建立起连接

因为这是为了防止出现失效的连接请求报文段被服务端接收的情况，从而产生错误。

可以想象如下场景。客户端发送了一个连接请求 A，但是因为网络原因造成了超时，这时 TCP 会启动超时重传的机制再次发送一个连接请求 B。此时请求顺利到达服务端，服务端应答完就建立了请求，然后接收数据后释放了连接。

假设这时候连接请求 A 在两端关闭后终于抵达了服务端，那么此时服务端会认为客户端又需要建立 TCP 连接，从而应答了该请求并进入 ESTABLISHED 状态。但是客户端其实是 CLOSED 的状态，那么就会导致服务端一直等待，造成资源的浪费。

PS：在建立连接中，任意一端掉线，TCP 都会重发 SYN 包，一般会重试五次，在建立连接中可能会遇到 SYN Flood 攻击。遇到这种情况你可以选择调低重试次数或者干脆在不能处理的情况下拒绝请求。

**连接终止**

TCP 是全双工的，在断开连接时两端都需要发送 FIN 和 ACK。

1. 若客户端 A 认为数据发送完成，则它需要向服务端 B 发送连接释放请求。

2. B 收到连接释放请求后，会告诉应用层要释放 TCP 链接。然后会发送 ACK 包，并进入 CLOSE_WAIT 状态，此时表明 A 到 B 的连接已经释放，不再接收 A 发的数据了。但是因为 TCP 连接是双向的，所以 B 仍旧可以发送数据给 A。

3. B 如果此时还有没发完的数据会继续发送，完毕后会向 A 发送连接释放请求，然后 B 便进入 LAST-ACK 状态。

> PS：通过延迟确认的技术（通常有时间限制，否则对方会误认为需要重传），可以将第二次和第三次握手合并，延迟 ACK 包的发送。

4. A 收到释放请求后，向 B 发送确认应答，此时 A 进入 TIME-WAIT 状态。该状态会持续 2MSL（最大段生存期，指报文段在网络中生存的时间，超时会被抛弃） 时间，若该时间段内没有 B 的重发请求的话，就进入 CLOSED 状态。当 B 收到确认应答后，也便进入 CLOSED 状态。

**为什么 A 要进入 TIME-WAIT 状态，等待 2MSL 时间后才进入 CLOSED 状态？**

为了保证 B 能收到 A 的确认应答。若 A 发完确认应答后直接进入 CLOSED 状态，如果确认应答因为网络问题一直没有到达，那么会造成 B 不能正常关闭。

#### 可靠性

广播和[多播](https://baike.baidu.com/item/多播)不能用于TCP。

TCP 能提供流量控制和拥塞处理

#### 协议对比

TCP 是面向连接的传输控制协议，而UDP 提供了无连接的数据报服务；

TCP 具有高可靠性，确保传输数据的正确性，不出现丢失或乱序；UDP 在传输数据前不建立连接，不对数据报进行检查与修改，无须等待对方的应答，所以会出现分组丢失、重复、乱序，应用程序需要负责传输可靠性方面的所有工作；

UDP 具有较好的实时性，工作效率较 TCP 协议高；UDP 段结构比 TCP 的段结构简单，因此网络开销也小。

TCP 协议可以保证接收端毫无差错地接收到发送端发出的字节流，为应用程序提供可靠的通信服务。对可靠性要求高的通信系统往往使用 TCP 传输数据。比如 HTTP 运用 TCP 进行数据的传输。

#### 术语

最大传输段大小（[MSS](https://baike.baidu.com/item/MSS/3567770)）

拥塞控制算法：（具体看[百科](https://baike.baidu.com/item/TCP/33012#3)）

