# 0、概述

**运输层主要是为应用进程之间**提供逻辑通信(logical communication)。而**网络层是为主机之间**提供逻辑通信。

运行在计算机中的进程是用**进程标识符**来标志的，解决进程标识符的变动的问题，可以使用**端口号(port)**。

将主机间交付扩展到进程间交付称为运输层的多路复用(transport-layer multiplexing)与多路分解(demultiplexing)。UCP和TCP还可以通过在报文段首部增加差错检查字段而提供完整性检查。最底程度的运输层服务有**端到端的数据交付和差错检查**。

将运输层报文段中的数据**交付**到正确的套接字上称为**多路分解(demultiplex)**。从源主机上的不同的套接字中收集数据块，并为每个数据块封装上首部信息从而生成报文段，然后将报文段传递到网络层，这些工作称为**多路复用(multiplex)**。

# 一、TCP和UDP

传输层有2个协议，分别是**TCP(Transmission Control Protocol，传输控制协议)和UDP(User Datagram Protocol，用户数据报协议)**。中文名为传输控制协议和用户数据报协议。两个对等运输实体在通信时传送的数据单位叫做**运输协议数据单元TPDU**(Transport Protocol Data Unit).

UDP使用场景

**DNS**、SNMP、HTTP/3

发送到套接字的**明文密码**以明文遍历Internet

TLS(Transport Layer Security)：能提供加密的TCP连接；数据完整性；以及端点认证。

服务器一般是80端口。

|     性质     |             TCP             |                 UDP                  |
| :----------: | :-------------------------: | :----------------------------------: |
|    连接性    |          面向连接           |                无连接                |
|    可靠性    |      可靠传输，不丢包       | 不可靠传输，尽最大努力传输，可能丢包 |
| 首部占用空间 |             大              |                  小                  |
|   传输速率   |             慢              |                  快                  |
|   资源消耗   |             大              |                  小                  |
|   应用场景   | 浏览器、文件传输、邮件发送  |             音视频等实时             |
|  应用层协议  | HTTP、HTTPS、FTP、SMTP、DNS |                 DNS                  |

TCP三次握手，发送请求，发送响应，发送确认。一旦形成了连接可以看成是形成了一个管道。

![TCP与UDP格式](img\TCP与UDP格式.jpg)

## 1、UDP

只在IP数据报上增加了**端口**的功能和**差错检测**功能。

UDP是**无连接**的，面向报文的。减少了建立和释放连接的开销。UDP尽最大能力交付，不保证可靠交付。首部只有8个字节(TCP首部至少2个字节)。常用的使用UDP的应用有NFS(文件传输)、SNMP(网络管理)、DNS(名字转换)还有各种流式传输的应用。

UDP首部有**16位源端口号，16位目的端口号**，16位UDP长度以及16位UDP检验和。合计8个字节。

UDP的套接字是由**二元组**进行标识的，即目的IP地址和目的的端口号。

### UDP长度(Length)

占16位，**首部的长度+数据的长度**

首部字段有8个字节。由4个字段组成，即源端口、目的端口、长度、检验和。

### UDP检验和(CheckSum)

**检验和的计算内容：伪首部+首部+数据**

发送方的UDP对报文段中的所有16比特字的和进行**反码**运算，求和时遇到任何溢出都会被**回卷(最高位回到最低位)**最终得到的结果放在UDP报文段中的检验和字段。

**伪首部**的内容有源IP地址、目的IP地址、0、17(代表是UDP)、UDP长度。分别是4、4、1、1、2个字节。如果**所有16个比特字加起来(包括检验和)**不是16个1，就说明出错了。

### 端口(Port)

占2个字节，取值范围为0-65535。其中**熟知端口**，数值一般为0~1023；**登记端口号**，数值为1024~49151，为没有熟知端口号的应用程序使用的。客户端口号or短暂端口号，留给客户进程选择暂时使用。

客户端的源端口是临时开启的随机端口。**防火墙可以设置开启或关闭端口来提高安全性**。通信结束后，这个端口号可供其他客户进程以后使用

| 协议   | HTTP   | HTTPS   | FTP    | Mysql    | DNS        | SMTP   | POP3    |
| ------ | ------ | ------- | ------ | -------- | ---------- | ------ | ------- |
| 端口号 | TCP+80 | TCP+443 | TCP+21 | TCP+3306 | TCP\UDP+53 | TCP+25 | TCP+110 |

~~~
netstat –an：查看被占用的端口
netstat –anb：查看被占用的端口、占用端口的应用程序
telnet 主机 端口：查看是否可以访问主机的某个端口

安装telnet：控制面板 – 程序 – 启用或关闭Windows功能 – 勾选“Telnet Client” – 确定
~~~



## 2、TCP

![TCP伪首部](img\TCP伪首部.jpg)

TCP是保证应用程序之间端到端的可靠通信。

**TCP连接的端点是套接字(Socket)**

套接字 socket = (IP地址 ： 端口号)

每一条TCP连接唯一地被通信两端的端点确定。

**TCP连接** :: = {socket1, socket2} = {(IP地址：port)， (IP地址：port)}

TCP服务器应用程序有一个欢迎套接字，在12000号端口号上等待来自TCP客户的连接建立请求。服务器主机可以支持很多并行的TCP套接字，每个套接字与一个进程相联系。

当今的高性能的Web服务器只使用一个进程，每与一个新的客户建立连接，就会为新的客户创建套接字的新线程(线程可以看成是轻量级的子进程)。

源端口和目的端口字段各占两个字节。端口是运输层与应用层的服务接口。

### 序号(Sequence Number)

占4字节.首先，在传输过程的每一个字节都会有一个编号.在**建立连接后**，序号代表：这一次传给对方的TCP**数据部分的第一个字节的编号**

### 确认号(Acknowledgement Number)

占4字节。在**建立连接后**，确认号代表：**期望对方**下一次传过来的TCP**数据部分的第一个字节的编号**，只有在ACK=1的时候，确认号才有效。

### 数据偏移(Data Offset)

也就是**首部长度**，占4位，取值范围是0x0101~0x1111.乘以4表示首部的长度，因此首部长度范围为20-60字节，原理上等同于网络层的片偏移

### 保留(Reserved)

占6位，目前无大功能

~~~
UDP的首部中有个16位的字段记录了整个UDP报文段的长度（首部+数据）
但是，TCP的首部中仅仅有个4位的字段记录了TCP报文段的首部长度，并没有字段记录TCP报文段的数据长度

分析
UDP首部中占16位的长度字段是冗余的，纯粹是为了保证首部是32bit对齐
TCP\UDP的数据长度，完全可以由IP数据包的首部推测出来
传输层的数据长度 = 网络层的总长度 – 网络层的首部长度 – 传输层的首部长度
~~~

### 标志位(flags)

保留(Reserved)的后三位没有用处，可以划分为标志位。标志位有6个

* URG(urgent)
  * 当**URG=1时，紧急指针字段才有效**。表明当前报文段中有紧急数据，应优先尽快传送
* ACK（Acknowledgment） 
  * 当ACK=1时，确认号字段才**有效**
* PSH(push)
  * 接收 TCP 收到 PSH = 1 的报文段，就尽快地交付接收应用进程，而**不再等到整个缓存都填满**了后再向上交付
* RST（Reset）
  * 当RST=1时，表明连接中出现严重差错，必须**释放连接**，然后再重新建立连接
* SYN（Synchronization） 
  * 当**SYN=1、ACK=0时，表明这是一个建立连接的请求** 
  * 若对方**同意建立连接，则回复SYN=1、ACK=1**
* FIN(Finish)
  * 当FIN=1时，表明数据已经发送完毕，要求**释放连接**

### 窗口

占2字节。这个字段有流量控制功能，用以**告知对方下一次允许发送**的数据大小（字节为单位）

### 检验和(CheckSum)

与UDP相似，TCP检验和的计算内容：伪首部+首部+数据

伪首部：占用12个字节，仅在计算检验和时起作用，并不会传递给网络层

### 紧急指针

 16 位，指出在本报文段中紧急数据共有多少个字节（紧急数据放在本报文段数据的最前面）。

### 选项

**最大报文段长度 MSS**(Maximum Segment Size)是TCP报文段中的数据字段的最大长度。

# 二、TCP要点

## 可靠传输(rdt)

重要的机制有**检验和、序号、定时器和肯定和否定确认分组**。

简单的有**ARQ（Automatic Repeat–reQuest）**，自动重传请求。

* 差错检测，需要一种机制检测何时出现了差错。
* 接收方反馈，接收方将会返回ACK和NAK报文
* 重传

**超时重传**，未收到确认信号就会一直等待直到超时进行重传。

确认丢失，（确认信号中途丢失）会发送重复的请求数据。

确认迟到，则会收下迟到的确认，但什么也不做

~~~
若有个包重传了N次还是失败，会一直持续重传到成功为止么？

这个取决于系统的设置，比如有些系统，重传5次还未成功就会发送reset报文（RST）断开TCP连接
~~~

### 停等协议

**停止等待(stop-and-wait)协议**，当发送端收到一个NAK分组时，就会重传上一个分组，并且等待接收方为响应分组发送的ACK和NAK。这个状态下，发送方是不能发送数据的，因此叫停止等待协议。



但是遇到ACK和NAK受损时，发送方就无法知道接收方是否准确的接收到了上一次发送的分组。

解决方法有

* 不理解->重新发送ACK和NAK，不是好方法
* 增加足够的比特进行检测和比特，这种方式不仅能够检测差错，还可以对数据进行恢复。
* 直接重新传输数据分组，这种方式会造成数据的冗余，产生冗余分组(duplicate packet)
  * 解决方法：在数据中添加一个字段，用于记录发送方对数据分组的编号

从rdt1.0完美版本到rdt2.1(接收方必须发送会一个ACK报文以确定分组序号)，rdt2.2(接收方不必要发送一个ACK报文)，rdt3.0(要考虑吧底层信道丢包的问题)rdt3.0(为重传增加一个倒计数定时器)

### 连续ARQ协议

更有效的有连续ARQ(**自动重传请求，Automatic Repeat reQuest**)协议与滑动窗口协议。

自动重传请求(Auto Repeat Quest)，再发送完一个分组后，必须暂时保留已发送的分组的副本。**分组和确认分组都必须进行编号**。超时计时器的**重传时间**应当比数据在分组传输的平均往返时间更长一些.

与ARQ协议，并列的有**停止等待(stop-and-wait)协议**。但是有效发送时间为Td，总的时间为Td + RTT + Ta(确认的时间)，导致总的信道利用率就低了。**接收方采取累计确认的方式，只对按序到达的最后一个分组发送确认**，表示到这个分组为止所有分组都已经正确收到了。

停止等待协议的改进是使用**流水线(pipelining)传输**，发送方连续发送多个分组，不必没发完一个分组就停下来等待对方的确认。

流水线技术带来的影像有

* 增加序号范围，在流水线中传输的数据，必须有唯一的**编号**，因此在也存在多个输送中的未确认的报文。
* 协议的接收方和发送方不得不**缓存多个分组**，发送方应最低限度地能够缓存已经发送但没有确认的分组。
* 解决流水线传输的差错恢复问题的方法主要有**回退N步和选择重传**

![TCP01](img\TCP01_序号_确认号.png)

### Go-back-N(回退N)

如果发送方发送了前 5 个分组，而中间的第 3 个分组丢失了。这时接收方只能对前两个分组发出确认。发送方无法知道后面三个分组的下落，而只好把后面的三个分组都再重传一次。这就叫做 Go-back-N（回退 N），表示需要再退回来重传已发送过的 N 个分组。可见当通信线路质量不好时，连续 ARQ 协议会带来负面的影响。

将**基序号(base)**定为最早未确认分组的序号，将**下一个序号(nextseqnum)**定义为最小的未使用序号。依据这两个可以将整个数据报划分为4段。确认、发送的二二组合。已经发送但是还未确认的分组可以看成是序号范围长度为N的窗口，随着分组被确认，该窗口将会向前移动，因此GBN协议也称为滑动窗口(sliding-window-protocol)协议。N称为窗口长度(window size)

GBN的发送方必须响应三种类型的事件

* 上层的调用
  * 上层调用rdt_send()时，需要先检查发送窗口是否已满。

累积确认

接收方对**按序到达的最后一个分组**发送确认，表示到这个分组为止所有分组都已经收到了。

~~~
可靠传输的具体实现
1)发送端和接收端都要有一个发送窗口和接收窗口。
2)利用字节的序号进行控制，TCP的所有的确认都基于序号。
3)TCP连接的RTT不是固定不变的。
~~~

### 选择性确认(SACK)

在TCP通信过程中，如果发送序列中间某个数据包丢失（比如1、2、3、4、5中的3丢失了）,TCP会通过重传最后确认的分组后续的分组（最后确认的是2，会重传3、4、5）.这样原先已经正确传输的分组也可能重复发送（比如4、5），降低了TCP性能。

为改善上述情况，发展出了**SACK（Selective acknowledgment，选择性确认）**技术，告诉发送方哪些数据丢失，哪些数据已经提前收到。使TCP只重新发送丢失的包（比如3），不用发送后续所有的分组（比如4、5）

**SACK信息会放在TCP首部的选项部分，最多有40个字节**。一对边界信息占8个字节，因此**SACK最多携带4组边界信息**。

Kind：占1字节。值为5代表这是SACK选项 

Length：占1字节。表明SACK选项一共占用多少字节 

Left Edge：占4字节，左边界 

Right Edge：占4字节，右边界



~~~
为什么选择在传输层就将数据“大卸八块”分成多个段，而不是等到网络层再分片传递给数据链路层？

因为可以提高重传的性能，需要明确的是：可靠传输是在传输层进行控制的
✓ 如果在传输层不分段，一旦出现数据丢失，整个传输层的数据都得重传
✓ 如果在传输层分了段，一旦出现数据丢失，只需要重传丢失的那些段即可
~~~

## 流量控制

让发送方的发送速率不要太快，让接收方来得及接收处理。

原理 

通过**确认报文中窗口字段来控制发送方的发送速率**，发送方的发送窗口大小不能超过接收方给出窗口大小。当发送方收到接收窗口的大小为0时，发送方就会停止发送数据

~~~
特殊情况
一开始，接收方给发送方发送了0窗口的报文段。后面，接收方又有了一些存储空间，给发送方发送的非0窗口的报文段丢失了。发送方的发送窗口一直为零，双方陷入僵局
~~~

当**发送方收到0窗口通知**时，这时发送方停止发送报文。并且同时**开启一个定时器**，隔一段时间就发个测试报文去询问接收方最新的窗口大小。如果接收的窗口大小还是为0，则发送方再次刷新启动定时器

![](img\TCP02_流量控制.png)

## 拥塞控制

防止过多的数据注入到网络中，避免网络中的路由器或链路过载

拥塞控制是一个**全局性的过程**，涉及到所有的主机、路由器，以及与降低网络传输性能有关的所有因素，是大家共同努力的结果，相比而言，**流量控制是点对点通信的控制**

~~~
MSS（Maximum Segment Size）：每个段最大的数据部分大小
在建立连接时确定

cwnd（congestion window）：拥塞窗口

rwnd（receive window）：接收窗口

swnd（send window）：发送窗口
swnd = min(cwnd, rwnd)
~~~

### 慢开始(slow start)

![](img\TCP拥塞避免.jpg)

cwnd的初始值比较小，然后随着**数据包被接收方确认（收到一个ACK）**,cwnd就成倍增长（**指数级**)。

**传输轮次(transmission round)**使用慢开始算法后，每经过一个传输伦茨(实际上就是往返时间)，拥塞窗口cwnd就加倍。

###  拥塞避免（congestion avoidance）

**ssthresh（slow start threshold）：慢开始阈值**，cwnd达到阈值后，以线性方式增加

**拥塞避免（加法增大**）：拥塞窗口缓慢(**线性**)增大，以防止网络过早出现拥塞 

**乘法减小**：只要网络出现拥塞，把ssthresh减为拥塞峰值的一半，同时执行慢开始算法（cwnd又恢复到初始值） 

当网络出现频繁拥塞时，ssthresh值就下降的很快

### 快速重传（fast retransmit)

接收方 

**每收到一个失序的分组后就立即发出重复确认** ,使发送方及时知道有分组没有到达,而不要等待自己发送数据时才进行确认

发送方 

只要**连续收到三个重复确认（总共4个相同的确认**），就应当立即重传对方尚未收到的报文段,而不必继续等待重传计时器到期后再重传



### 快速恢复（fast recovery）

![](img\TCP拥塞避免快恢复.jpg)

与慢开始不同之处是现在不执行慢开始算法，即cwnd现在不恢复到初始值,而是**把cwnd值设置为新的ssthresh值（减小后的值）**,然后开始执行拥塞避免算法（“加法增大”），使拥塞窗口缓慢地线性增大

**发送窗口的最大值：swnd = min(cwnd, rwnd) **

当rwnd < cwnd时，是**接收方的接收能力限制发送窗口的最大值** 

当cwnd < rwnd时，则是网络的拥塞限制发送窗口的最大值

## 序号、确认号

![](img\TCP序号与确认号1.jpg)

建立连接时，TCP数据部分是没有内容的。

![](img\TCP中的原生raw seq.jpg)

## 建立连接

![](img\TCP三次握手.jpg)

CLOSED：client处于关闭状态 

 LISTEN：server处于监听状态，等待client连接 

 SYN-RCVD：表示server接受到了SYN报文，当收到client的ACK报文后，它会进入到ESTABLISHED状态 

 SYN-SENT：表示client已发送SYN报文，等待server的第2次握手 

 ESTABLISHED：表示连接已经建立

前两次握手的特点

* SYN都设置为1 

* **数据部分的长度都为0** 

* TCP**头部的长度一般是32字节**(第三次也是) 
  * 固定头部：20字节
  * 选项部分：12字节
* 双方会交换确认一些信息
  * 比如MSS、是否支持SACK、Window scale（窗口缩放系数）等
  * 这些数据都放在了TCP头部的选项部分中（12字节

往返时间(RTT)：一个分组从客户端到服务器再回到客户端的时间。

一个RTT来初始化TCP连接。一个RTT来HTTP请求和第一个HTTP响应返回的时间。还有就是文件传输的时间。

**总时间就是2*RTT + 文件传输的时间**，这是对应非持久的HTTP连接。



### 3次握手的疑问

为什么建立连接的时候，要进行3次握手？2次不行么？ 

主要目的：**防止server端一直等待，浪费资源**。

如果建立连接只需要2次握手，可能会出现的情况 

* 假设client发出的第一个连接请求报文段，因为网络延迟，在连接释放以后的某个时间才到达server。本来这是一个早已失效的连接请求，但server收到此失效的请求后，误认为是client再次发出的一个新的连接请求。于是server就向client发出确认报文段，同意建立连接。
* 如果不采用“3次握手”，那么只要server发出确认，新的连接就建立了。由于现在client并没有真正想连接服务器的意愿，因此不会理睬server的确认，也不会向server发送数据。但server却以为新的连接已经建立，并一直等待client发来数据，这样，server的很多资源就白白浪费掉了

采用“三次握手”的办法可以防止上述现象发生。例如上述情况，client没有向server的确认发出确认，server由于收不到确认，就知道client并没有要求建立连接

第3次握手失败了，会怎么处理？

此时server的状态为SYN-RCVD，若等不到client的ACK，server会重新发送SYN+ACK包。如果server**多次重发SYN+ACK**都等不到client的ACK，就会**发送RST包，强制关闭连接**

## 释放连接-4次挥手

![](img\TCP08_释放连接.png)

* FIN-WAIT-1：表示想**主动关闭连接**

向对方发送了FIN报文，此时进入到FIN-WAIT-1状态

* CLOSE-WAIT：表示在等待关闭 

当对方发送FIN给自己，自己会**回应一个ACK报文**给对方，此时则进入到CLOSE-WAIT状态.在此状态下，**需要考虑自己是否还有数据要发送给对方**，如果没有，发送FIN报文给对方 

* FIN-WAIT-2：只要对方发送ACK确认后，主动方就会处于FIN-WAIT-2状态，然后等待对方发送FIN报文 

* CLOSING：一种比较罕见的例外状态 

表示你发送FIN报文后，并没有收到对方的ACK报文，反而却也收到了对方的FIN报文 

如果双方几乎在同时准备关闭连接的话，那么就出现了双方同时发送FIN报文的情况，也即会出现CLOSING状态,表示双方都正在关闭连接

* LAST-ACK：被动关闭一方在发送FIN报文后，最后等待对方的ACK报文,当收到ACK报文后，即可进入CLOSED状态了 

* TIME-WAIT：表示收到了对方的FIN报文，并发送出了ACK报文，就等2MSL后即可进入CLOSED状态了.

如果FIN-WAIT-1状态下，收到了对方**同时带FIN标志和ACK标志的报文时，可以直接进入到TIME-WAIT状态**，而无须经过FIN-WAIT-2状态 

* CLOSED：关闭状态.由于有些状态的时间比较短暂，所以很难用netstat命令看到，比如SYN-RCVD、FIN-WAIT-1等

~~~
允许任何一方先发起断开请求。

client发送ACK后，需要有个TIME-WAIT阶段，等待一段时间后，再真正关闭连接。一般是等待2倍的MSL（Maximum Segment Lifetime，最大分段生存期）
✓ MSL是TCP报文在Internet上的最长生存时间
✓ 每个具体的TCP实现都必须选择一个确定的MSL值，RFC 1122建议是2分钟
✓ 可以防止本次连接中产生的数据包误传到下一次连接中（因为本次连接中的数据包都会在2MSL时间内消失了）

如果client发送ACK后马上释放了，然后又因为网络原因，server没有收到client的ACK，server就会重发FIN
这时可能出现的情况是
① client没有任何响应，服务器那边会干等，甚至多次重发FIN，浪费资源
② client有个新的应用程序刚好分配了同一个端口号，新的应用程序收到FIN后马上开始执行断开连接的操作，本来它可能是想跟server建立连接的
~~~

### 4次挥手的疑问

TCP是全双工模式，第一次挥手后，主机1不发送任何数据了，但可以接收主机2的数据。第二次是确认，第三次，是主机2发送不再发送数据的信息了。第四次，是主机1的确认。

有时只看到**三次握手**，其实是**将第2、三次握手合并**了。
