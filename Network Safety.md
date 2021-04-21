# 一、网络安全问题概述

## 1、威胁

计算机网络上的通信主要面临着以下四种威胁

* **截获**：从网络上窃听他人的通信内容
* **中断**：有意中断他人在网络上的通信
* **篡改**：故意篡改网络上传送的报文
* **伪造**：伪造信息在网络上传送

其中，截获信息的攻击称为被动攻击，更改信息和拒绝用户使用资源的攻击为主动攻击。



## 2、目标

计算机网络通信安全的目标为

* 防止析出**报文**内容
* 防止**通信量**分析
* 检测更改报文流
* 检测拒绝报文服务
* 检测伪造初始化连接。



## 3、恶意程序

计算机病毒会“传染”其他程序的程序， “传染”是通过修改其他程序来把自身或其变种复制进去完成的。

计算机蠕虫——通过网络的通信功能将自身从一个结点发送到另一个结点并启动运行的程序。 

特洛伊木马——一种程序，它执行的功能超出所声称的功能（伪装）。 

逻辑炸弹——一种当运行环境满足某种特定条件时执行其他特殊功能的程序。

## 4、计算机网络安全的内容

保密性



安全协议的设计



访问控制



# 二、密码体制

**密码编码学(cryptography)**是密码体制的设计学，而**密码分析学(cryptanalysis)**则是在未知密钥的情况下从密文推演出明文或密钥的技术。密码编码学与密码分析学合起来即为密码学 (cryptology)。

如果不论截取者获得了多少密文，但在密文中都没有足够的信息来唯一地确定出对应的明文，则这一密码体制称为**无条件安全**的，或称为理论上是不可破的。如果密码体制中的密码不能被可使用的计算资源破译，则这一密码体制称为**在计算上是安全的**。

## 1、对称密钥密码体制

常规密钥密码体制，即加密密钥与解密密钥是相同的密码体制。

### 数据加密标准

数据加密标准DES，是一种分组密码。在加密前，先对整个明文进行**分组**。每一个**组长为 64 位**。然后对每一个 64 位二进制数据进行**加密处理**， 产生一组 64 位密文数据。最后**将各组密文串接起来**，即得出整个的密文。最后将各组密文串接起来，即得出整个的密文。 使用的密钥为 64 位（实际密钥长度为 56 位，有 8 位用于奇偶校验)。

### DES的保密性

DES 是世界上第一个公认的实用密码算法标准， 它曾对密码学的发展做出了重大贡献。 目前较为严重的问题是 DES 的密钥的长度。

## 2、公钥密码体制

公钥密码体制使用**不同的加密密钥与解密密钥**， 是一种“由已知加密密钥推导出解密密钥在计算上是不可行的”密码体制。

公钥密码体制的产生主要是因为两个方面的原因，一是由于常规密钥密码体制的密钥分配问题， 另一是由于对数字签名的需求。

### 加密密钥与解密密钥

在公钥密码体制中，**加密密钥(即公钥)** PK 是公开信息，而解密密钥(即私钥或秘钥) SK 是**需要保密**的。加密算法和解密**算法都是公开**的。



~~~
	任何加密方法的安全性取决于密钥的长度，以及攻破密文所需的计算量。在这方面，公钥密码体制并不具有比传统加密体制更加优越之处。
	由于目前公钥加密算法的开销较大，在可见的将来还看不出来要放弃传统的加密方法。公钥还需要密钥分配协议，具体的分配过程并不比采用传统加密方法时更简单。
~~~

### 公钥算法的特点

1)发送者 A 用 B 的公钥 PKB 对明文 X 加密（E 运算）后，在接收者 B 用自己的私钥 SKB 解密（D 运算），即可恢复出明文：

DskB(Y) = DskB(EpkB(X)) = X.

2)加密和解密的运算可以对调。从已知的 PK 实际上不可能推导出 SK，即从 PK 到 SK 是“计算上不可能的”。

## 3、数字签名

数字签名需要保证三点：

* **报文鉴别**——接收者能够核实发送者对报文的签名；
* **报文的完整性**——发送者事后不能抵赖对报文的签名；
* **不可否认**——接收者不能伪造对报文的签名

### 数字签名的实现

A的明文X通过A的私钥SKA，进行加密，然后到达B，B通过A的公钥PKA核实签名就能够获得A的明文X。

### 具有保密性的数字签名

A的明文X通过A的私钥和B的公钥加密，通过因特网传输到B处。B通过B的私钥和A的公钥解密，就能够获取明文了。

## 4、鉴别

在信息的安全领域中，对付**被动攻击**的重要措施是**加密**，而对付**主动攻击**中的篡改和伪造则要用**鉴别(authentication)**。鉴别与授权(authorization)是不同的概念。授权涉及到的问题是：所进行的过程是否被**允许**（如是否可以对某文件进行读或写）。 而鉴别则是判断真伪。

### 报文鉴别

许多报文并不需要加密但却需要数字签名，以便让报文的接收者能够鉴别报文的真伪。然而对很长的报文进行数字签名会使计算机增加很大的负担（需要进行很长时间的运算）。

### 报文摘要MD

Message Digest,

A 将报文 X 经过**报文摘要算法**运算后得出很短的**报文摘要 H**。然后用自己的**私钥**对 H 进行 D 运算，即进行数字签名。得出已签名的报文摘要D(H)后，并**将其追加在报文 X 后面**发送给 B。

B收到报文后，做两件事。

* 用A的公钥对 D(H) 进行**E运算**，得出报文摘要 H 。 
* (**重复检验**)对报文 X 进行报文摘要运算，看是否能够得出同样的报文摘要 H。如一样，就能以极高的概率断定收到的报文是 A 产生的。否则就不是。

MD的优点

仅对短得多的定长报文摘要 H 进行数字签名要比对整个长报文进行数字签名要简单得多。

### 报文摘要算法

报文摘要算法就是一种**散列函数**。这种散列函数也叫做密码编码的检验和。也是一种精心选择的**单向函数**。可以很容易地计算出一个长报文 X 的报文摘要H，但要想从报文摘要 H 反过来找到原始的报文 X，则实际上是不可能的。

### 实体鉴别

实体鉴别是在系统接入的全部持续时间内对和自己通信的对方实体只需验证一次。

最简单的实体鉴别过程 

A 发送给 B 的报文的被加密，使用的是对称密钥KAB。 B 收到此报文后，用**共享对称密钥** KAB 进行解密，因而鉴别了实体 A 的身份。

漏洞

入侵者 C 可以从网络上截获 A 发给 B 的报文。C并不需要破译这个报文（因为这可能很花很多时间）而可以直接把这个由 A 加密的报文发送给 B，使 B 误认为 C 就是 A。然后 B 就向伪装是 A的 C 发送应发给 A 的报文。

**重放攻击(replay attack)**， C 甚至还可以截获 A 的 IP 地址，然后把 A 的 IP 地址冒充为自己的 IP 地址（这叫做 **IP 欺骗**），使 B 更加容易受骗。一种解决方案为不重数(nonce)，不重数就是一个不重复使用的大随机数，即一次一数。

其他的有中间人攻击。

## 5、密钥分配

目前常用的密钥分配方式是设立**密钥分配中心KDC (Key Distribution)**。

用户 A 和 B 都是 KDC 的登记用户，并已经在KDC 的服务器上安装了各自和 KDC 进行通信的**主密钥（master key）KA 和 KB**。 “主密钥”可 简称为“密钥”。

### 公钥的分配

需要有一个值得信赖的机构——即**认证中心CA(Certification Authority)**，来将公钥与其对应的实体（人或机器）进行绑定(binding)。

### 链路加密

在采用链路加密的网络中，每条通信链路上的加密是**独立实现**的。通常对每条链路使用不同的加密密钥。所有的中间结点(包括可能经过的路由器)未必都是安全的。链路加密的最大缺点是**在中间结点暴露了信息的内容**。在网络互连的情况下，仅采用链路加密是不能实现通信安全的。

### 端到端加密

端到端加密是在源结点和目的结点中对传送的PDU 进行加密和解密，报文的安全性不会因中间结点的不可靠而受到影响。

# 三、防火墙(firewall)

防火墙是由软件、硬件构成的系统，是一种**特殊编程的路由器**，用来在两个网络之间实施接入控制策略。**接入控制策略**是由使用防火墙的单位自行制订的，为的是可以最适合本单位的需要。

防火墙内的网络称为**“可信赖的网络”(trusted network)**，而将外部的因特网称为**“不可信赖的网络”(untrusted network)**。

## 1、防火墙的功能

阻止和允许。大多数服防火墙的**主要功能是阻止**。

## 2、防火墙技术

网络级防火墙——用来防止整个**网络**出现外来非法的入侵。属于这类的有**分组过滤和授权服务器**。前者检查所有流入本网络的信息，然后拒绝不符合事先制订好的一套准则的数据，而后者则是检查**用户的登录是否合法**。

应用级防火墙——从应用程序来进行接入控制。 通常使用**应用网关或代理服务器**来区分各种应用。例如，可以只允许通过访问万维网的应用，而阻止 FTP 应用的通过。

# 四、无线局域网

一个**基本服务集 BSS **包括一个**基站和若干个移动站**，所有的站在本 BSS 以内都可以直接通信，但在和本 BSS 以外的站通信时，都要通过本 BSS 的基站。

## 1、AP

基本服务器内的基站叫做**接入点AP(Access Point)**。当网络管理员安装AP时，必须为该AP分配一个不超过32字节的**服务集标识符SSID和一个信道**。

一个移动站若要加入到一个基本服务集 BSS，就必须先选择一个接入点 AP，并**与此接入点建立关联**。建立关联就表示这个移动站加入了选定的 AP 所属的子网，并和这个 AP 之间创建了一个虚拟线路。只有关联的 AP 才向这个移动站发送数据帧，而这个移动站也只有通过关联的 AP 才能向其他站点发送数据帧。

**被动扫描**，即移动站等待接收接入站周期性发出的**信标帧(beacon frame)**。 信标帧中包含有若干系统参数（如服务集标识符 SSID 以及支持的速率等）。 

主动扫描，即**移动站**主动发出**探测请求帧(probe request frame)**，然后等待从 AP 发回的探测响应帧(probe response frame)。

## 2、热点(hot spot)

能够向公众提供有偿或无偿接入Wi-Fi 的服务。这样的地点就叫做热点。



## 3、移动自组网络(ad hoc network)

自组网络是没有固定基础设施（即没有 AP）的无线局域网。这种网络由一些处于平等状态的移动站之间相互通信组成的临时网络。

几种不同的接入

* 固定接入(fixed access)——在作为网络用户期间，用户设置的地理位置保持不变。 
* **移动接入(mobility access)**——用户设置能够以车辆速度移动时进行网络通信。当发生切换时， 通信仍然是连续的。 
* 便携接入(portable access)——在受限的网络覆盖面积中，用户设备能够在以步行速度移动时进行网络通信，提供有限的切换能力。 
* 游牧接入(nomadic access)——用户设备的地理位置至少在进行网络通信时保持不变。如用户设备移动了位置，则再次进行通信时可能还要寻找最佳的基站 。



# 五、IPv6

下一代网际协议ipng

最主要的问题就是 32 位的 IP 地址不够用。

要解决 IP 地址耗尽的问题的措施：

* 采用**无类别域间路由CIDR**， IP地址分配更合理
* **网络地址转换NAT方法**节省全球的IP地址
* 采用具有更大地址空间的新版本的IP地址IPv6

## 1、基本首部

IPv6 仍支持无连接的传送所引进的主要变化如下 

* 更大的地址空间。IPv6 将地址从 IPv4 的 **32 位 增大到了 128 位**。 
* 扩展的地址层次结构。 
* 灵活的**首部格式**。 
* 改进的选项。 
* 允许协议继续扩充。 
* 支持**即插即用（即自动配置） **
* 支持资源的预分配

IPv6 将**首部长度变为固定的 40 字节**，称为**基本首部(base header)**。 将不必要的功能取消了，首部的字段数减少到只有 8 个。 **取消了首部的检验和字段**，加快了路由器处理数据报的速度。 在基本首部的后面允许有零个或多个扩展首部。 所有的**扩展首部和数据**合起来叫做数据报的**有效载荷(payload)或净负荷**。

![](C:\Users\ASUS\Downloads\Compressed\computer-network-main\img\IPv6格式.jpg)

通信量类(traffic class)，有8位，为了区分不同IPv6数据报的类别和优先级。

流标号(flow label)，有20位，流是互联网上从特定源点到特定终点的一系列数据报，**所有属于同一个流的数据报有同样的流标号**。

**跳数限制(hop limit)**，有8位，源站在数据报发出时即设定跳数限制，路由器在转发数据包时就会将跳数限制字段中的值减1.**当跳数限制的值为0时，路由器就将该数据报丢弃**。TTL

## 2、扩展首部

IPv6 把原来 IPv4 首部中选项的功能都放在**扩展首部**中，并将扩展首部留给路径**两端的源站和目的站**的主机来处理。

数据报途中经过的路由器都不处理这些扩展首部 （只有一个首部例外，即逐跳选项扩展首部），大大提高了路由器的效率。

 RFC 2460 中定义了六种扩展首部： 

* 逐跳选项 
* 路由选择 
* 分片 
* 鉴别 
* 封装安全有效载荷 
* 目的站选项

IPv6的地址空间，可以使用单播(unicast)、多播(multicast)、任播(anycast)。

## 3、冒号十六进制记法

colon hexadecimal notation

每个 16 位的值用十六进制值表示，各值之间用冒号分隔。**零压缩(zero compression)**，即一连串**连续的零可以为一对冒号所取代**。FF05:0:0:0:0:0:0:B3 -> FF05::B3

## 4、点分十进制记法的后缀

60 位的前缀 12AB00000000CD3 可记为： 12AB:0000:0000:CD30:0000:0000:0000:0000/60

或12AB::CD30:0:0:0:0/60

或12AB:0:0:CD30::/60

## 5、地址空间的分配

IPv6 将 128 位地址空间分为两大部分。

* 第一部分是**可变长度的类型前缀**，定义了地址的目的地
* 第二部分是地址的其余部分，长度也是可变的。

特殊地址

* 未指明地址，这是 16 字节的全 0 地址，可缩写为两个冒号“::” 。这个地址只能为还没有配置到一个标准的 IP 地址的主机当作源地址使用
* 环回地址，0:0:0:0:0:0:0:1,记为::1
* 基于IPv4的地址，前缀为0000 0000保留一小部分地址为IPv4兼容

## 6、地址的转换

向 IPv6 过渡只能采用逐步演进的办法，同时，还必须使新安装的 IPv6 系统能够**向后兼容**。IPv6 系统必须能够接收和转发 IPv4 分组，并且能够为 IPv4 分组选择路由。

### 双协议栈

**双协议栈(dual stack)**是指在完全过渡到 IPv6 之前，使一部分主机（或路由器）装有两个协议栈， 一个 IPv4 和一个 IPv6。

IPv6转IPv4，去掉流标号。IPv4转IPv6，增加一个**空的流标号**

### 隧道

将IPv6数据报封装在IPv4数据报中。

ICMPv6 的报文格式和 IPv4 使用的 ICMP 的相似，即前 4 个字节的字段名称都是一样的。

ICMPv6 将第 5 个字节起的后面部分作为报文主体。

ICMPv6的报文划分为四大类

* 差错报告报文
* 提供信息的报文
* 多播听众发现的报文
* 邻站发现报文