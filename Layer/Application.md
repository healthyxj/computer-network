在IDEA中导入：选中Scratches and Consoles，选择左上角的file-new-modules from existing sources，选中要导入的iml文件

因为是在同一台计算机上访问服务器的，可以在wireshark中设置tcp.port == 8888，

拿完数据(数据交互完)就关闭的是短连接，拿完数据后还会等待的是长连接。



进程通过**socket(套接字接口)**传送和接收信息。

socket可以类比为门。而要接收信息，需要有验证。主机有32位的IP地址，还需要有端口号来与进程相连接。

# 0、应用层协议原理

应用程序体系结构(application architecture)是由应用程序研发者设计，规定了如何在各种端系统上阻止该应用程序。应用层的具体内容就是规定应用进程在通信时所遵循的协议。

## 1、应用程序体系结构

主要有CS(客户-服务器)结构和P2P(Peer-Peer)结构。

服务器有固定的IP地址，并且总是打开的。而P2P体系结构对位于数据中心的专用服务器依赖最小。P2P的自扩展性(self-scalability)

## 2、进程通信

进行通信的是**进程(process)**，一个进程可以认为是运行在端系统上的程序。

多个进程运行在同一台端系统上时，使用进程通信机制相互通信。运行在不同端系统上的进程通过交换**报文(message)**进行通信。

进程通过**套接字(Socket)**的软件向网络发送报文和从网络接收报文。

应用层定义了交换的**请求和响应**的信息。也定义了信息的**句法(syntax，定义了消息中的哪些字段，以及如何划定字段field)**。

# 一、DNS(Domain Name System)

人们有许多的标识(identifiers)。

IP地址不方便记忆，并且不能表达组织的名称和性质，人们设计出了域名（比如baidu.com）。但实际上，为了能够访问到具体的主机，**最终还是得知道目标主机的IP地址**。

获取IP地址的过程

~~~
1)同一台用户主机上运行着DNS应用的客户端
2)浏览器从URL抽取主机名，将主机名传递给DNS应用的客户端
3)DNS客户端向DNS服务器发送包含主机名的请求
4)DNS客户收到回答报文，包含含有对应于主机名的IP地址
5)浏览器收到IP地址，就能够向IP地址80端口的HTTP服务器进程发送一个TCP连接
~~~

DNS提供的重要服务

* **主机别名(host aliasing)**
  * 有的主机有多个别名；其中有**规范主机名(canonical hostname)**
* 邮件服务器别名(mail server aliasing)
* 负载分配(load distribution)
  * 繁忙的站点被冗余分布在多台服务器上，因此有着不同的IP地址。DNS服务器存储着这些IP地址集合

为什么不使用集中化的DNS

* 一旦出现错误，后果严重
* 流量的问题
* 
* 便于维护和管理

~~~
域名申请注册https://wanwang.aliyun.com/
~~~

如果全部使用域名，会增加路由器的负担，浪费流量。

现在多是**分布式**、层次数据库的DNS服务器。域名服务器实现名字到IP地址的解析， 一个服务器所负责管辖的（或有权限的）范围叫做区(zone)。每一个区设置相应的**权限域名服务器**，用来保存该区中的所有主机的域名到IP地址的映射。DNS 服务器的管辖范围不是以“域”为单位，而是**以“区”为单位**。

## 1、域名分类

根据级别不同，域名可以分为顶级域名(Top-level Domain,TLD)、二级域名、三级域名……在这些之前有**根域名服务器**。

所有的根域名服务器都知道所有的顶级域名服务器的域名和 IP 地址，不管是哪一个本地域名服务器，若要对因特网上任何一个域名进行解析，只要自己无法解析，就首先**求助于根域名服务器**。根域名服务器有13套装置。

### 顶级域名

* 通用顶级域名(**General Top-level Domain,gTLD**)
  * **.com（公司），.net（网络机构），.org（组织机构），.edu（教育）.gov（政府部门），.int（国际组织）**等
* 国家及地区顶级域名(Country Code Top-level Domain,ccTLD)
  * .cn(中国)、.jp(日本)、.uk(英国)，注意美国没有域名
* 新通用顶级域名(New Generic Top-level Domain,New gTLD)
  * .vip、.xyz、.top、.club、.shop等

### 二级域名

是顶级域名之下的域名。

* 在**通用顶级域名下，它一般指域名注册人的名称**，例如google、baidu、microsoft等
* 在国家及地区顶级域名下，它一般指**注册类别**的，例如com、edu、gov、net等

## 2、DNS流程

DNS的全称是：Domain Name System，译为：域名系统。利用DNS协议，可以将域名（比如baidu.com）解析成对应的IP地址（比如220.181.38.148）。DNS可以基于**UDP协议**，也可以基于TCP协议，**服务器占用53端口**。

客户端首先会访问最近的一台本地DNS服务器（也就是客户端自己配置的DNS服务器）。**所有的DNS服务器都记录了DNS根域名服务器的IP地址**。**上级DNS服务器记录了下一级DNS**服务器的IP地址。全球一共13台IPv4的DNS根域名服务器、25台IPv6的DNS根域名服务器。

主机向本地域名服务器的查询一般都是采用**递归**查询。如果主机所询问的本地域名服务器不知道被查询域名的 IP 地址，那么本地域名服务器就以 DNS 客户的身份，向其他根域名服务器继续发出查询请求报文。**本地域名服务器向根域名服务器的查询通常是采用迭代查询**。当根域名服务器收到本地域名服务器的迭代查询请求报文时，要么给出所要查询的 IP 地址，要么告诉本地域名服务器：“你下一步应当向哪一个域名服务器进行查询”。然后让本地域名服务器进行后续的查询。

因为缓存(存放最近用过的**名字**以及从何处**获得名字映射信息**的记录)，多数DNS查询都绕过了根服务器。为了保持正确的内容，域名服务器应为每项内容设置**定时器**，并处理超过合理时间的项。

## 3、报文格式

|           标识符           |             标志             |
| :------------------------: | :--------------------------: |
|           问题数           |           回答RR数           |
|          权威RR数          |           附加RR数           |
|     问题(问题的变量数)     |     查询的名字和类型字段     |
|   回答(资源记录的变量数)   |      对查询的响应中的RR      |
|   权威(资源记录的变量数)   |       权威服务器的记录       |
| 附加信息(资源记录的变量数) | 可被使用的附加"有帮助"的信息 |

前12个字节为首部字段

* 标识符
  * 用于标识该查询，会被复制到对查询的回答报文中以便让客户匹配发送的请求和接收到的回答
* 标志字段
  * 1比特的查询/回答标志位指出是查询报文还是回答报文：1表示回答，0表示查询



### 常见命令

ipconfig /displaydns：查看DNS缓存记录 

ipconfig /flushdns：清空DNS缓存记录 

ping 域名 

nslookup 域名 ：直接向某些DNS服务器发送一个DNS查询报文。

## 4、资源记录(RR)

共同实现DNS分布式数据库的所有DNS服务器存储了**资源记录(Resource record，RR)**。RR提供了主机到IP地址的映射。

resource records(RR)

RR format：**(Name, Value, Type, TTL)**。其中TTL是记录的生存时间，决定了资源记录从应当缓存中删除的时间。

### Type = A

name填主机名；value填**IP地址**

### Type = NS

name是域名；value是该域的**权威DNS服务器的主机名**

~~~
engr.sc.edu dns.engr.sc.edu NS ttl
~~~



### Type = CNAME

name是别名；value是**规范主机名**

~~~
www.engr.sc.edu dilbert.engr.sc.edu CNAME ttl
~~~



### Type = MX

value是mailbox的的规范主机名

~~~
engr.sc.edu hub0.engr.sc.edu MX ttl
~~~

## 5、DNS安全

单一的DNS服务器的问题

* 单点故障
  * 如果该服务器崩溃，整个因特网会随之瘫痪
* 通信容量(traffic volume)
* 远距离的集中式数据库
* 维护



* DDos攻击
  * 难以追溯
  * 流量过滤
  * 本地DNS服务器缓存的顶级域名的IP地址，允许根服务器绕过
* 重定向攻击(redirect attack)
  * 中间人攻击(干扰DNS请求)
  * DNS中毒(发送虚假的信息存入到DNS的缓存)



# 二、DHCP

IP地址按照分配方式，可以分为静态IP地址和动态IP地址。

静态IP地址需要手动设置，适用于不怎么挪动的台式机和服务器

动态IP地址从DHCP服务器自动获取IP地址，适用于**移动设备和无线设备**等，因为提供了**即插即用**(plug-and-play networking)的机制。

DHCP（Dynamic Host Configuration Protocol），译为：动态主机配置协议。DHCP协议基于**UDP协议**，**客户端是68端口，服务器是67端口**

DHCP服务器会从IP地址池中，挑选一个IP地址“**出租**“给客户端一段时间，称为**租用期(lease period)**，时间到期就回收它们。平时家里上网的路由器就可以充当DHCP服务器。

现在是每一个网络至少有一个 DHCP **中继代理**，它配置了 DHCP 服务器的 IP 地址信息。当 DHCP 中继代理收到主机发送的发现报文后， 就以单播方式向DHCP 服务器转发此报文，并等待其回答。收到 DHCP 服务器回答的提供报文后 ，DHCP 中继代理再将此提供报文发回给主机。

凡是受到**DHCP发现报文(DHCPDISCOVER)**的DHCP服务器都会发出**DHCP提供报文(DHCPOFFER)**。客户受到提供报文，就会发送**请求报文(DHCPREQUEST)**，DHCP服务器再发送回**确认报文(DHCPACK)**，表示可以使用临时IP地址了。DHCP客户需要根据租用期设置两个计时器T1和T2，超时时间分别是0.5T和0.875T，超时时间到了就请求更新租用期。T1时间到，DHCP就会发送请求报文要求更新租用期。若服务器同意，就发送DHCPACK，客户重新设置计时器。否则**重新发送发现报文**。服务器不响应，在T2时间到了，还是重新发送请求报文。客户可以随时终止租用期，发送DHCPRELEASE.

## 四个阶段

* DISCOVER：主机发现DHCP服务器
  * 发DHCP发现报文(DHCP discover message)，也就是广播包（源IP是0.0.0.0，目标IP是255.255.255.255，目标MAC是FF:FF:FF:FF:FF:FF）
* OFFER：提供租约
  * 服务器返回可以租用的IP地址，以及租用期限、子网掩码、网关、DNS等信息
  * 注意：这里可能会有**多个服务器提供租约**
* REQUEST：选择IP地址
  * 客户端选择一个OFFER，发送广播包进行回应，回显配置的参数。
* ACKNOWLEDGE：确认
  * 被选中的服务器发送ACK数据包给客户端。至此，IP地址分配完毕

## 细节

* DHCP服务器可以跨网段分配IP地址么？（DHCP服务器、客户端不在同一个网段） 
  * 可以借助DHCP中继代理（DHCP Relay Agent）实现跨网段分配IP地址。
* 自动续约 
  * 客户端会在租期不足的时候，自动向DHCP服务器发送REQUEST信息申请续约
* 常用命令
  * ipconfig /all：可以看到DHCP相关的详细信息，比如租约过期时间、DHCP服务器地址等 
  * ipconfig /release：释放租约
  * ipconfig /renew：重新申请IP地址、申请续约（延长租期）
* DHCP能够返回客户的路由器的第一跳的地址；DNS服务器的名字和IP地址；子网掩码

# 三、HTTP(超文本传输协议)

html/login.html是一个URI，可能是很多路径都有的。

http://localhost:8080/html/login.html是一个URL，唯一访问。前面的是主机名，后面的是路径名称。

HTTP是**无连接的，面向事务**的，只是使用了TCP向上提供的服务，**无状态的(stateless)**，不保存客户的相关信息。HTTP分为**非持久HTTP(non-persistent connection)和持久HTTP**，前者是一次性的，建立了**TCP连接**后，发送完消息，就会断开TCP连接。

~~~
非持续连接
HTTP客户发起TCP连接，连接确认。
HTTP客户经套接字发送HTTP请求报文，请求报文包含了路径名。
HTTP服务器接收报文，将内容封装在响应报文中。
HTTP服务器通知TCP准备断开连接。
HTTP客户接收响应报文，断开连接。
~~~

可以用**往返时间(Round-Trip Time,RTT)**衡量整个过程花费的时间.

**DASH(Dynamic, Adaptive Streaming over HTTP，经HTTP的动态适应性流)**，视频能够编码成不同的版本，每个版本有不同的比特率，对于不同的质量水平客户能够动态地请求来自不同版本且长度为几秒的视频段数据块。客户端能够决定什么时候请求chunk(使得溢出不会发生)，DASH(经HTTP的动态适应性流)主要功能是实现对应不同的浏览器提供不同的服务。

流式媒体=encoding + DASH + playout + buffering。



## 1、版本

1991年，HTTP/0.9 

只支持GET请求方法获取文本数据（比如HTML文档），且不支持请求头、响应头等，无法向服务器传递太多信息 

1996年，HTTP/1.0 

支持POST、HEAD等请求方法，支持请求头、响应头等，支持更多种数据类型（不再局限于文本数据） 

浏览器的每次请求都需要与服务器建立一个TCP连接，请求处理完成后立即断开TCP连接 

1997年，**HTTP/1.1（最经典、使用最广泛的版本）**

支持PUT、DELETE等请求方法 

采用**持久连接（Connection: keep-alive），多个请求可以共用同一个TCP连接**。对于非持久连接，操作系统会打开平行的TCP连接来抓取引用的对象，or流水线方式。

## 2、标准

由万维网协会（W3C）、互联网工程任务组（IETF）协调制定，最终发布了一系列的RFC(Request For Comments,请求意见稿)

HTTP/1.1最早是在1997年的RFC 2068中记录的。该规范在1999年的RFC 2616中已作废。2014年又由RFC 7230系列的RFC取代。HTTP/2标准于2015年5月以RFC 7540正式发表，取代HTTP/1.1成为HTTP的实现标准

## 3、报文格式

不严谨，提供大致认识

HTTP信息只有两种：request和response

### 请求报文

![](F:\壁纸及图\temp images\HTTP请求报文.jpg)

请求行有三个字段：方法、URI、版本

* 方法中有GET和POST请求，GET请求无请求体(**如要输入username&password只能附加在URI后**)，而**POST有请求体，故POST更常用**。

* URI是请求的地址名。

URI后跟的是HTTP的版本号。**版本号是两个字节的回车换行CRLF**

然后就是请求头，字段(field)

* host 主机名
* User-agent 服务器的代理
* Connection 连接
* Accept-language 接受的语言

具体如下所示

~~~HTTP
GET /somedir/page.html HTTP/1.1
Host: www.someschool.edu 
User-agent: Mozilla/4.0
Connection: close	//表示不需要建立持续连接 
Accept-language:fr
~~~

当请求方法为post时，实体段中的内容就是用户在表单中输入的内容。

**CRLF分别是CR(回车)、LF(换行)**

然后是首部字段名，例如HOST: 192.168.1.10

注意**最后还有一个CRLF**

### 响应报文

![](F:\壁纸及图\temp images\HTTP响应报文.jpg)

HTTP响应报文是服务器发过来的，因此没有方法名称。首部行也成为响应头，实体**主体部分是返回的HTML**，也被称为响应体。

状态码：如果是正常的状态则为200，短语一般为OK。

以下为响应报文的示例

~~~http
HTTP/1.1 200 OK 
Connection: close	//表示发送完报文后将会关闭TCP连接
Date: Thu, 06 Aug 1998 12:00:15 GMT	//首部行指示服务器产生并发送响应报文的时间，也就是服务器检索到对象，将对象放入响应报文并发送的时刻 
Server: Apache/1.3.0 (Unix) 
Last-Modified: Mon, 22 Jun 1998 …... //对象创建or最后修改的时间
Content-Length: 6821 
Content-Type: text/html
~~~

ABNF（Augmented BNF），是BNF（Backus-Naur Form，译为：巴科斯-瑙尔范式）的修改、增强版。ABNF是最严谨的HTTP报文格式描述形式，脱离ABNF谈论HTTP报文格式，往往都是片面、不严谨的

### ABNF的核心规则

| 规则   | 形式              | 意义                          |
| ------ | ----------------- | ----------------------------- |
| ALPHA  | %x41-5A / %x61-7A | 大写和小写ASCII字母           |
| DIGIT  | %x30-39           | 数字0-9                       |
| HEXDIG |                   | 十六进制数字                  |
| SP     | %x20              | 空格                          |
| HTAB   | %x09              | 横向制表符                    |
| WSP    | SP/HTAB           | 空格或横向制表符              |
| CHAR   | %x01-7F           | 任何7-位ASCII字符，不包括NULL |
| OCTET  | %x00-FF           | 8位数据                       |
| CR     | %x0D              | 回车                          |
| LF     | %x0A              | 换行                          |
| CRLF   | CRLF              | 互联网标准换行(%x0D%x0A)      |

## 4、ABNF下的报文格式-整体

HTTP-message = **start-line *( header-field CRLF )  CRLF [ message-body ]**

start-line = request-line / status-line	开始行是请求行或者状态行

| /    | 任选一个                   |
| ---- | -------------------------- |
| *    | 0个或多个；2*表示至少2个， |
| ()   | 组成一个整体               |
| []   | 可选，可有可无             |

### 开始行 request-line / status-line

request-line = **method SP request-target SP HTTP-version CRLF** 

HTTP-version = HTTP-name "/" DIGIT "." DIGIT ;'""表示固定，所以才会看到HTTP/1.1

HTTP-name = %x48.54.54.50 ; HTTP 



status-line = HTTP-version SP status-code SP reason-phrase CRLF 

status-code = 3DIGIT 

reason-phrase = *( HTAB / SP / VCHAR / obs-text )

HTTP/1.1 200

### 请求头 header-filled与请求体message-body

header-field = field-name ":" OWS field-value OWS

field-name = token

field-value = * (field-content / obs-fold)

**OWS = %(SP / HTAB)**



message-body = *OCTET

## 5、URL编码

统一资源定位符URL是对可以从因特网上得到的资源的位置和访问方法的一种简洁的表示，相当于一个文件名在网络范围内的扩展。

URL的形式

由以冒号隔开的两大部分组成，并且在URL中的字符**对大小写没有要求**。

<协议>://<主机>:<端口>/<路径>

* 协议
  * ftp——文件传送协议
  * http——超文本传输协议
  * News——usenet新闻
* 主机
  * 存放资源的主机在因特网中的域名
* 端口和路径有时可以省略，例如**HTTP的默认端口号为80，通常可以省略**，若省略路径，则访问的是某个**主页(home page)**



URL中一旦出现了一些特殊字符（比如中文、空格），需要进行编码。在**浏览器地址栏输入URL时，是采用UTF-8进行编码**

如编码前：https://www.baidu.com/s?wd=百度 ，编码后：https://www.baidu.com/s?wd=%E5%8D%8E%E4%B8%BA



## 6、请求方法

GET、HEAD、POST、PUT、DELETE、CONNECT、OPTIONS、TRACE 

RFC 5789, section 2: Patch method：描述了PATCH方法 

GET：常用于**读取**的操作，请求参数直接拼接在URL的后面（浏览器对URL是有长度限制的） 

POST：常用于**添加、修改、删除**的操作，请求参数可以放到请求体中（没有大小限制） 

**HEAD：请求得到与GET请求相同的响应，但没有响应体**。使用场景举例：在下载一个大文件前，先获取其大小，再决定是否要下载。以此可以节约带宽资源

CONNECT： 用于代理服务器

## 7、状态码

1XX表示通知信息，例如请求接收到了或正在进行处理

### 正常

200 OK

表示请求成功，请求的目标随后会通过message发送过来



### 重定向

301 Moved Permanently

请求的目标被移动了， 随后新的地址会发送过来。**新的URI会在响应报文的Location中**。

304 Not Modified

### 客户端

400 Bad Request

通用差错代码，表示服务器不能理解请求信息

404 Not Found

在服务器上找不到请求的文件

### 服务器

505 HTTP Version Not Supported

HTTP版本不被支持





#  四、cookie

在客户端存储一些数据，**服务器可以返回cookie交给客户端去存储**，一般是浏览器的本地硬盘，只能存储在某个浏览器，**不同的浏览器之间不互通**，浏览器关闭cookie就会消失.

cookie能够保持状态。

**cookie的作用过程**：当用户在浏览器上第一次登录某个网站，就会发送一个请求。当服务器接收到请求，就会发送一个**包含Set-cookie**的响应，使得主机上的浏览器被标记某个特殊的ID。下次用户通过该浏览器访问同一个网站时，就会把Set-cookie上的ID发送个服务器，服务器就能够找到数据库中对应的表格，同意用户的连接。

## cookie携带的信息

* authorization 授权
* shopping carts  购物车
* recommendations 推荐
* user session state 用户会话状态

session

在服务器存储一些数据，一般存储在内存中。session在服务器的默认生存时间为30min

会话跟踪技术



# 五、web caches(Proxy server)

代理服务器能够不麻烦源服务器。

用户能够设置浏览器通过缓存接入web。浏览器首先发送所有的HTTP请求到缓存。在缓存中的对象就会被缓存返回。**如果缓存中没有该对象，就会通过代理服务器向源服务器发送请求来获取对象**。

~~~
1)浏览器创建到Web缓存器的TCP连接，并向web缓存器中的对象发送一个HTTP请求。
2)Web缓存查看是否存储了该对象的副本，如果有，就用HTTP响应报文返回该对象；若没有就打开与该对象的初始服务器的TCP连接，重复HTTP请求和响应。
3)web缓存器接收到对象，就存储一份副本。并利用已有的TCP连接将HTTP响应报文的副本发送回。
~~~

使用web cache有许多的的优点

* 减少响应时间
* 减少机构访问链接上的流量
* 使“贫困”的内容提供商可以有效地交付内容（但P2P文件共享也是如此）

版本的新旧问题，使用**条件GET方法(conditional GET)**(请求报文使用GET方法，并且请求报文中包括一个"If-Modified-Since")。

对于缓存服务器，如果有最新的缓存版本，就不会发送目标请求。

HTTP中缓存的格式：If-modified-since: <date>

服务器的响应中也会包含相关的内容，来表征缓存的版本是否是最新的：HTTP/1.0 304 Not  Modified

## CDN(Contents Distribution Nextworks)

将内容分发到不同的网络中，需要用CDN形成服务器深入形成各种网络。

CDN采用两种不同的服务器安置原则

* 深入
  * 是通过在遍及全球的接入ISP中部署服务器集群来深入到ISP的接入网中
* 邀请做客
  * 通过在少量（例如 10 个）关键位置建造大集群来邀请到 ISP 做客；通常将集群建在IXP

### CDN操作

当用户主机用浏览器检索某个特定的视频时，CDN必须截获该请求，来实现

* 确定此时适合用于该客户的CDN服务器集群
* 将用户的请求重定向到该集群的某台CDN服务器

~~~
1)用户访问位于Netcinema的web网页
2)用户点击链接时，该用户主机就会发送一个对该URL的DNS请求
3)该用户的本地DNS服务器将DNS请求中继到一台用户Netcinema的权威DNS服务器，观察到请求的类型；返回一个CDN服务器的主机名
4)DNS进入了kingDNS的专用DNS基础设施；用户的LDNS发送第二个请求，最终指向返回CDN内容服务器的IP地址。在该内容服务器的DNS系统中，指定了DNS服务器，客户能够将这台服务器接收到内容
5)LDNS向用户主机转发内容服务CDN节点的IP地址
6)一旦客户接收到kingDNS的IP地址，就与该IP地址创建直接的连接，并发出HTTP GET请求
~~~

CDN能够存储CDN结点上的内容的拷贝。因此，用户向CDN 请求文件时，CDN 就能够利用最近的网络向用户发送数据。

### 集群选择策略

任何CDN的部署，核心是**集群选择策略(cluster selection strategy)**。动态的将客户定向到CDN中的某个服务器集群或数据中心的机制。

一般的策略是指派客户到地理上最为临近的集群。使用商用地理位置数据库，每个LDNSIP地址映射到一个地理位置。

CDN能够对集群和客户之间的时延和丢包性执行周期性的实时测量。

# 六、FTP(file transfer protocol)

TCP控制连接端口: 21,TCP数据连接端口: 20

客户端通过控制连接授权。客户通过控制连接发送请求浏览遥远的目录。当**服务器接收到文件传输请求，服务器会打开第二个端口为20的TCP连接**。但是在传输完文件后，服务器会关闭数据连接。



# 七、邮件

电子邮件是一种异步通信媒介。因特网电子邮件系统的**主要组成部分**：用户代理(user agent)、邮件服务器(mail server)、**简单邮件传输协议(SMTP)**.

邮件服务器主要包括mailbox、message queue、SMTP protocol。SMTP的端口号为25.

## 用户代理UA

用户代理 UA 就是**用户与电子邮件系统的接口**， 是电子邮件客户端软件。用户代理实现的功能有撰写、显示、处理和通信。

邮件服务器的功能是发送和接收邮件，同时向发信人报告邮件传送的情况。

## SMTP的过程

![]()

用户通过user agent**传递信息**，user agent（UA）**发送message**到自己的mail server，信息被放入**等待队列**中。mail server建立与另一台mail server的**TCP连接**。message就会通过TCP协议发送到另一台mail server上。另一台mail server就会将message放入到mailbox中，等待另一个用户打开mailbox读取message。最终是用POP3和IMAP协议进行读取的。

**SMTP不使用中间邮件服务器发送邮件**。

SMTP示例(其中s代表server，c代表client)

~~~
S: 220 hamburger.edu 	//服务器主机
C: HELO crepes.fr 	//客户主机
S: 250 Hello crepes.fr, pleased to meet you 
C: MAIL FROM: <alice@crepes.fr> 
S: 250 alice@crepes.fr... Sender ok 
C: RCPT TO: <bob@hamburger.edu> 
S: 250 bob@hamburger.edu ... Recipient ok 
C: DATA
S: 354 Enter mail, end with "." on a line by itself 
C: Do you like ketchup? 
C: How about pickles? 
C: . 
S: 250 Message accepted for delivery 
C: QUIT
S: 221 hamburger.edu closing connection
~~~

## SMTP与HTTP比较

SMTP的过程相当于推，而HTTP的过程相当于**拉**。

SMTP需要有7-bit的ASCII，而且用的是**持久的连接**。**CRLF.CRLF表示结束**

## 邮件形式

信封

* Mail FROM:
* RCPT TO:

内容

* To:
* From:
* Subject:
* Cc： 抄送，应给某人发送一个邮件副本

message 部分是7-bits的ASCII。

## POP3(第三版的邮局协议)

POP3(Post Office Protocol -Version3)是**无状态连接**。

在接收邮件的用户 **PC 机中必须运行 POP 客户程序**，而在用户所连接的 ISP 的**邮件服务器中则运行 POP 服务器程序**。

POP3按照三个阶段进行工作：特许(Authorization)、事务处理及更新。

* 特许
  * 用户代理以明文形式发送用户名和口令以鉴别用户
* 事务处理
  * 用户代理取回报文；用户可做对报文做删除标记，取消报文删除标记，以及获取邮件的统计信息
* 更新
  * 在客户发出quit命令后，结束POP3会话；此时邮件服务器删除被标记为删除的报文

分为两个阶段

认证内容

* client command
  * user: decalre username
  * password
* server responses
  * +OK
  * -ERR

交易阶段(transaction phase)

* list: lis t mesage numbers
* retr:retrieve message by number
* dele: delete
* quit



~~~
S: +OK POP3 server ready 
C: user bob 
S: +OK 
C: pass hungry 
S: +OK user successfully logged on

C: list
S: 1 498 
S: 2 912 
S: . 
C: retr 1 
S: <message 1 contents>
S: . 
C: dele 1 
C: retr 2 
S: <message 1 contents>
S: . 
C: dele 2 
C: quit
S: +OK POP3 server signing off
~~~



## IMAP(因特网邮件访问协议)

将所有的信息存储在服务器上。用户在自己的 PC 机上就可以操纵 ISP 的邮件服务器的邮箱，就像在本地操纵一样。IMAP是一个**联机协议**，当用户 PC 机上的IMAP 客户程序打开 IMAP 服务器的邮箱时，用户就可看到邮件的首部。若用户需要打开某个邮件，则该邮件才传到用户的计算机上。

* 允许用户组织信息。

将每个报文与一个文件夹练习起来：当报文第一次到达服务器时，与收件人的INBOX文件夹相关联。收件人就能够把邮件移动到一个新的、用户创建的文件夹中，进行阅读邮件、删除邮件等操作。

* 允许用户代理获取报文某些部分的内容

用户代理可以只读取一个报文的报文首部

## 基于web的mail

信息还是通过**HTTP传输到mail server**，但是在mail server之间是通过**SMTP**传输的。

# 八、P2P

每个洪流有一个基础设施节点，称为**追踪器(tracer)**。

当一个对等方加入洪流时，就向追踪器注册自己，并周期性地通知追踪器它仍在该洪流中。

在任何给定的时间，每个对等方有来自该文件的块的子集，并且不同的对等方有不同的子集。用户周期性地向临近对等方询问所具有的块列表。通过这个信息，可以向还没有的块发出请求。

使用**最稀缺优先(rarest first)**的技术。请求邻居中拥有的副本数量最少的块。最终得以均衡每个块在洪流中的副本的数量。



# 九、Socket Proramming

学习如何搭建使用套接字的客户端/服务器应用程序。套接字就相当于在传输层和应用层之间的门。

两种套接字对应两种传输层的协议

## TCP协议

TCP是可靠的，面向连接的传输层协议。



## UDP协议

UDP是不可靠的传输协议，UDP没有建立连接。也没有握手。发送端只需要附上目的地址的IP地址和端口号。服务器只需要分离出目的IP地址和端口号。UDP连接也许会丢包、顺序也不对。

顺序是服务器先创建套接字接口,端口号为x。同时客户端这边也建立套接字接口。然后客户端这里建立用户数据报，并且发送给服务器。服务器收到用户发来的数据报，就回应给用户以特定的套接字端口。客户读取数据报，然后就关闭客户端的套接字。





CDN边缘结点一般是离用户比较近的

