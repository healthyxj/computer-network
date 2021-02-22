# 网络协议

## 一、基础知识

路由器、交换机

静态路由、动态路由

局域网、以太网、无线局域网

DNS、CDN、VPN、NAT

MAC、IPv4、IPv6、端口

子网划分、子网掩码



学习中需要的环境

* 客户端-服务器开发环境
  * 客户端：浏览器(html+css+js)
  * 服务器：Java
* 网络抓包
  * 浏览器(chrome、firefox)、fiddler、wireshark
* 模拟工具
  * Xshell(只有windows版本)、Packet Tracer、GNS3(考证和考研时会用到)



### 1、客户端-服务器

服务器为后台开发(Java、c++、PHP、.NET、python、Go)，而移动开发(Android、ios)、嵌入式开发(c、c++、汇编)、前端开发(HTML、css、js)均属于客户端开发。

c、c++具有跨平台性，即使用平台相关的编译器生成对应平台的可执行文件。在windows上是PE格式，在MAC上是mach-o格式，在linux上是ELF格式。

Java编译后的是.class，字节码文件，对于操作系统来说，不是可执行文件。需要在操作系统上安装JVM(java virtual machine)，Java虚拟机。最后由JVM翻译成0101的机器指令。如果代码有语法错误，将编译失败，不会生成字节码文件。Oracle公司能提供JDK，JDK中包括了JRE(Java runtime environment),有了JRE，就有JVM。

服务器要运行开发人员的Java代码，就需要服务器软件。开源的服务器软件有tomcat，而tomcat本身就是由Java开发的。 流程：客户端发送请求，对应端口的服务器软件就调用对应的开发人员的Java程序。

tomcat可以在bin目录下找到startup.bat进行启动。双击后过一段时间，看到Server startup in [560] milliseconnds表示已经启动了。

http地址格式：http://IP地址:端口号/context path，context path也是项目。IP地址用于找到服务器，端口号用于找到对应的服务器软件，context path用于找项目名称。在startup.bat里可以看到http-nio-8080，表示端口号为8080，只需要在浏览器地址栏里输入http://localhost:8080，或者http://IP地址：端口号,就能访问tomcat服务器。

再打开webapps文件夹，可以看到有许多项目文件夹。可以看到里面有docs文件夹，因此在浏览器地址栏输入http://localhost:8080/docs就会跳转到docs项目的目录下。

### 2、协议

为更好地促进互联网络的研究和发展，国际标准化组织ISO在1985年制定了网络互联模型，OSI参考模型：物理层、数据链路层、网络层、运输层、会话层、表示层、应用层。由于过于理论，目前都是4层的TCP/IP协议，网络接口层(network access)、网际层(internet)、运输层(transport)、应用层(application)。用于学习研究的是五层的模型：物理层(physical)、数据链路层(datalink)、网络层、运输层、应用层。



## 二、参考模型(重点)

7层

4层

5层





## 三、主要协议

UDP

TCP

HTTP

HTTPS



~~~
常见面试题
1、TCP和UDP的区别？报文格式是怎么样的
2、TCP的流量控制和拥塞控制？TCP如何实现可靠性传输？
3、为什么连接是3次握手？关闭是4次握手
4、7层模型和4层模型的区别？每一层的作用是什么？
5、交换机和路由器的区别？
……
~~~



## 四、实战工具





## 五、其他协议
