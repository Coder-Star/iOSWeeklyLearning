************************************************
整理编辑：[反向抽烟](opooc.com)、[师大小海腾](https://juejin.cn/user/782508012091645)

面试解析是新出的模块，我们会按照主题讲解一些高频面试题，本期主题是**计算机网络**，以下题目均来自真实面试场景。

### 输入网址进入网页按回车刷新网页都发生了什么？URL 输入到显示的过程？

1. **DNS 解析**：当用户输入一个网址并按下回车键的时候，浏览器获得一个域名，而在实际通信过程中，我们需要的是一个 IP 地址，因此我们需要先把域名转换成相应 IP 地址；
2. **TCP 连接**：浏览器通过 DNS 获取到 Web 服务器真正的 IP 地址后，便向 Web 服务器发起 TCP 连接请求，通过 TCP 三次握手建立好连接后，浏览器便可以将 HTTP 请求数据发送给服务器了；
3. **发送 HTTP 请求**：浏览器向 Web 服务器发起一个 HTTP 请求，HTTP 协议是建立在 TCP 协议之上的应用层协议，其本质是在建立起的 TCP 连接中，按照 HTTP 协议标准发送一个索要网页的请求。在这一过程中，会涉及到负载均衡等操作；
4. **处理请求并返回**：服务器获取到客户端的 HTTP 请求后，会根据 HTTP 请求中的内容来决定如何获取相应的文件，并将文件发送给浏览器；
5. **浏览器渲染**：浏览器根据响应开始显示页面，首先解析 HTML 文件构建 DOM 树，然后解析 CSS 文件构建渲染树。如果页面有 JavaScript 脚本文件，那么 JavaScript 文件下载完成并加载后，通过 DOM API 和 CSSOM API 来操作渲染树，等到渲染树构建完成后，浏览器开始布局渲染树并将其绘制到屏幕上；
6. **断开连接**：客户端和服务器通过四次挥手终止 TCP 连接。

### 拥塞控制有哪些阶段？如何实现拥塞控制？TCP 的拥塞控制解释一下？

1. 拥塞控制考虑整个网络，是全局性的考虑；
2. **慢启动算法**：由小到大逐渐增加发送数据量，每收到一个报文确认就加 1 倍的报文数量，以指数规律增长，增长到慢启动阈值后就不增了；
3. **拥塞避免算法**：维护一个拥塞窗口的变量，只要网络不拥塞，就试探着拥塞窗口调大，以加法规律增长，该算法可以保证在网络不拥塞的情况下，发送更多的数据；
4. **快速重传**：接收端收到的序列号不连续时，连发 3 个重复的确认报文给发送方；
5. **快速恢复**：拥塞窗口变为原来的一半，阈值也变为发生拥塞时大小的一半，继续拥塞避免算法。

### TCP 怎么保证可靠传输？TCP 怎样实现可靠传输的？TCP 为什么可以保证可靠传输？怎么理解 TCP 的连接，可靠和字节流？

1. **数据分块**：应用数据被分割成 TCP 认为最适合发送的数据块；
2. **序列号和确认应答**：TCP 给发送的每一个包进行编号，在传输的过程中，每次接收方收到数据后，都会对传输方进行确认应答，即发送 ACK 报文，这个 ACK 报文当中带有对应的确认序列号，告诉发送方成功接收了哪些数据以及下一次的数据从哪里开始发。除此之外，接收方可以根据序列号对数据包进行排序，把有序数据传送给应用层，并丢弃重复的数据；
3. **校验和**：TCP 将保持它首部和数据部分的检验和。这是一个端到端的检验和，目的是检测数据在传输过程中的任何变化。如果收到报文段的检验和有差错，TCP 将丢弃这个报文段并且不确认收到此报文段；
4. **流量控制**：TCP 连接的双方都有一个固定大小的缓冲空间，发送方发送的数据量不能超过接收端缓冲区的大小。当接收方来不及处理发送方的数据，会提示发送方降低发送的速率，防止产生丢包。TCP 通过滑动窗口协议来支持流量控制机制；
5. **拥塞控制**：当网络某个节点发生拥塞时，减少数据的发送；
6. **ARQ 协议**：也是为了实现可靠传输的，它的基本原理就是每发完一个分组就停止发送，等待对方确认。在收到确认后再发下一个分组；
7. **超时重传**：当 TCP 发出一个报文段后，它启动一个定时器，等待目的端确认收到这个报文段。如果超过某个时间还没有收到确认，将重发这个报文段。

***
整理编辑：[反向抽烟](opooc.com)、[师大小海腾](https://juejin.cn/user/782508012091645)

面试解析是新出的模块，我们会按照主题讲解一些高频面试题，本期主题是**计算机网络**，以下题目均来自真实面试场景。计算机网络是面试必考的知识点，最好比较系统的去学习了解，推荐书籍：《图解 TCP/IP》、《网络是怎样连接的》；推荐付费课程：[计算机网络通关 29 讲](https://t7.lagounews.com/RR7FRYRDsi3B1 "计算机网络通关 29 讲")，大家可以根据自己喜欢的学习方式进行选择。


### 什么是 TCP 的三次握手和四次挥手？

我们先来看一下 TCP 报文头部结构：

![](https://gitee.com/zhangferry/Images/raw/master/iOSWeeklyLearning/20210704165732.png)

握手阶段主要依靠以下几个标志位：
* SYN：在建立连接时使用，用来同步序号。SYN=1 代表这是一个请求建立连接或同意建立连接的报文，只有前两次握手中 SYN 才为 1，带 SYN 标志的 TCP 报文段称为同步报文段；
  * 当 SYN=1，ACK=0 时，表示这是一个请求建立连接的报文段
  * 当 SYN=1，ACK=1 时，表示对方同意建立连接
* ACK：表示前面确认号字段是否有效。ACK=1 代表有效。带 ACK 标志的 TCP 报文段称为确认报文段；
* FIN：表示通知对方本端数据已发送完毕，要关闭连接了。带 FIN 标志的 TCP 报文段称为终止报文段。

**三次握手是指建立一个 TCP 连接时，需要客户端和服务端总共发送 3 个包，需要三次握手才能确认双方的接收与发送能力是否正常。**

![](https://gitee.com/zhangferry/Images/raw/master/iOSWeeklyLearning/20210703051424.png)

1. 客户端向服务端发起连接请求，需要发送一个 SYN 报文到服务端。
2. 当服务端收到客户端发过来的 SYN 报文后，返回给客户端 SYN、ACK 报文。`这时候服务端可以确认客户端的发送能力和自己的接收能力正常`。
3. 客户端收到该报文。`这时候客户端可以确认双方的发送和接收能力都正常`。然后客户端再回复 ACK 报文给服务端，服务端收到该报文。`这时候服务端可以确认客户端的接收能力和自己的发送能力正常。所以这时候双方都可以确认自己和对方的接收与发送能力都正常`。就这样客户端和服务端通过 TCP 建立了连接。

**四次挥手的目的是关闭一个 TCP 连接。**

![](https://gitee.com/zhangferry/Images/raw/master/iOSWeeklyLearning/20210703051443.png)

1. 客户端主动发起连接断开，发送一个 FIN 报文到服务端；
2. 服务端返回给客户端 ACK 报文。此时服务端处于关闭等待状态，而不是立马给客户端发 FIN 报文，这个状态还要持续一段时间，因为服务端可能还有数据没发完。`此时客户端到服务端的连接已经断开。但客户端和服务端之间所建立的 TCP 连接通道是全双工的，此时只是处于半关闭状态，所以服务端到客户端可能还会传递数据`；
3. 当服务端的数据都发送完毕后，给客户端发送一个 FIN，ACK 报文；
4. 客户端回应一个 ACK 报文。注意客户端发出 ACK 报文后不是立马释放 TCP 连接，而是要经过 2MSL（最长报文段寿命的 2 倍时长）后才释放 TCP 连接。而服务端一旦收到客户端发出的确认报文就会立马释放 TCP 连接，所以服务端结束 TCP 连接的时间要比客户端早一些。`此时服务端到客户端的连接也已经断开，整个 TCP 连接关闭`。


### 为什么 TCP 连接是三次握手？两次不可以吗？

TCP 是一个全双工协议，它要保证双方都具有接收与发送的能力。

因为需要考虑连接时丢包的问题，如果只握手两次，第二次握手时如果服务端发给客户端的确认报文段丢失，此时服务端已经准备好了收发数据（可以理解为服务端已经连接成功），而客户端一直没收到服务端的确认报文，所以客户端就不知道服务端是否已经准备好了（可以理解为客户端未连接成功），这种情况下客户端不会给服务端发数据，也会忽略服务端发过来的数据。

如果是三次握手，即便发生丢包也不会有问题，比如如果第三次握手客户端发的确认报文丢失，服务端在一段时间内没有收到确认报文的话就会重新进行第二次握手，也就是服务端会重发 SYN 报文段，客户端收到重发的报文段后会再次给服务端发送确认报文。


### 为什么 TCP 连接是三次握手，关闭的时候却要四次挥手？

主要是建立连接时接收者的 SYN-ACK 一同发送了，而关闭是 FIN 和 ACK 却不能同时发送，因为断开连接要处理的情况比较多，比如服务器端可能还有发送出的消息没有得到 ACK，也可能服务器资源需要释放等。所以先发一个 ACK 表示已经收到了发送方的请求，等上述情况都有了确定的处理，再发 FIN 表示接收方已经完成了后续工作。

类比现实世界中，你收到了一个 Offer，出于礼貌你先回复一下，然后思考一段时间再回复 HR 最终的结果。


### 为什么客户端发出第四次挥手的确认报文后要等 2MSL 的时间才能释放 TCP 连接？

这里同样是要考虑丢包的问题，如果第四次挥手的报文丢失，服务端没收到确认报文就会重发第三次挥手的报文，这样报文一去一回最长时间就是 2MSL，所以需要等这么长时间来确认服务端确实已经收到了。

参考：[https://zhuanlan.zhihu.com/p/141396896](https://zhuanlan.zhihu.com/p/141396896 "https://zhuanlan.zhihu.com/p/141396896")
