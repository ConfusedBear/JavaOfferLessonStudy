

# 一、课程导读

## java基础考点

JavaEE基础 + 内部/内嵌类 + 访问权限修饰词 + APIs + OOPs + 线程 + 集合 + JVM + 异常



# 二、计算机网络面试核心

## 1.网络基础知识讲解

### 1.1 OSI开放式互联参考模型

#### 1.1.1 物理层（网卡、网线）

​	机械、电子、定时接口通信信道上的原始比特流传输

#### 1.1.2 数据链路层（提供错误监测、纠正）

​	物理寻址，同时将原始比特流转变为逻辑传输线路

#### 1.1.3 网络层（路由器）

​	控制子网的运行，如逻辑编制、分组传输、路由选择

#### 1.1.4 传输层（TCP、UDP）

​	接受上一层的数据，在必要的时候把数据进行分割，并将这些数据交给网络层，且保证这些数据段有效到达对端

#### 1.1.5 会话层

​	不同机器上的用户之间建立及管理会话

#### 1.1.6 表示层

​	信息的语法语义以及它们的关联，入加密解密、转换翻译、压缩解压缩

#### 1.1.7应用层（HTTP协议）

![OSI开放式互联参考模型](C:\Users\31711\Documents\assets\1551185520827.png)



### 1.2 TCP/IP概念层模型

> OSI的“实现”：TCP/IP

![1551185637359](C:\Users\31711\Documents\assets\1551185637359.png)

> 先自上而下，后自下而上处理数据头部

![1551185927311](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551185927311.png)

## 2 *TCP的三次握手

### 2.1 传输控制协议TCP简介

​	面向连接的、可靠的、基于字节流的传输层通信协议

​	将应用层的数据流分割成报文段并发送给目标节点的TCP层

​	数据包都有序号，对方收到则发送ACK确认，未收到则重传

​	使用奇偶校验和函数来检验数据在传输过程中是否有误

![1551186892501](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551186892501.png)



> IP地址 + 协议 + 端口号 = Socket

源端口					目的端口

​	 		  序列号

​	    		 ACK

数据偏移 保留域 TCPFlags       滑动窗口

检验和					紧急指针（指出紧急字节数）

​			   TCP可选



### 2.2 TCP Flags

URG：紧急指针标志	1有效0忽略

ACK：确认需要标志

PSH：push标志	报文段应尽快交给应用程序

RST：重置链接标志

SYN：同步序号，用于建立连接过程	SYN=1，ACK=0 ——> 该确认段没有使用捎带的

确认域						          SYN=1，ACK=1 ——> 捎带确认域

FIN：finish标志，用于释放连接	1：没有数据了，可关闭数据流

### 2.3 TCP三次握手的流程图

![1551187873241](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551187873241.png)

![1551189904033](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551189904033.png)

### 2.4 首次握手的隐患——SYN超时

> 问题起因分析

​	Server收到Client的SYN，回复SYN-ACK的时候未收到ACK确认

​	Server不断重试直至超时，Linux默认等待63秒才断开连接

> 针对SYN Flood的防护措施

​	SYN队列满后，通过tcp_syncookies参数回发SYN Cookie

​	若为正常连接则Clinet会回发SYN Cookie，直接建立链接

### 2.5 建立连接后，Clinet出现故障怎么办

> 保活机制

​	向对方发送保活探测报文，如果未收到响应则继续发送

​	尝试次数达到保活探测数仍未收到响应则终端连接

## 3 *TCP的四次挥手

### 3.1 TCP四次挥手流程图

![1551190645947](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551190645947.png)

> MSL：最长报文段寿命
>
> IFC793规定为2分钟，Linus规定为30秒

![1551190919801](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551190919801.png)

### 3.2 为什么会有TIME_WAIT状态

​	确保有足够的时间让对方收到ACK包

​	避免新旧连接混淆

### 3.3 为什么需要四次握手才能断开连接

​	因为全双工，发送方和接收方都需要FIN报文和ACK报文

### 3.4 服务器出现大量CLOSE_WAIT状态的原因

​	对方关闭socket连接，我方忙于读或写，没有及时关闭连接

> ​	检查代码，特别是释放资源的代码
>
> ​	检查配置，特别是处理请求的线程配置

## 4 TCP和UDP的区别

### 4.1 UDP简介

![1551191346115](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551191346115.png)

源端口		目的端口

数据包长度	奇偶校验值

​		用户数据

### 4.2 UDP的特点

​	面向非连接

​	不维护连接状态，支持同时向多个客户端传输相同的信息

​	数据包报头只有8个字节，额外开销较小

​	吞吐量只受限于数据生成速率、传输速率以及机器性能

​	尽最大努力交付，不保证可靠交付，不需要维持复杂的连接状态表

​	面向报文，不对应用程序提交的报文信息进行拆分或者合并

### 4.3 TCP和UDP的区别

​	面向连接 VS 无连接

​	可靠性	TCP可靠	   UDP不可靠

​	有序性	TCP有序	   UDP无序

​	速度	    TCP速度慢       UDP适用速度敏感应用（在线视频等）

​	量级	    TCP_20字节     UDP_8字节

## 5 TCP的滑窗

### 5.1 RTT和RTO

​	RTT：发送一个数据包到收到对应的ACK，所花费的时间

​	RTO：重传时间间隔

### 5.2 TCP的滑动窗口

> TCP使用滑动窗口做流量控制与乱序重排

​	保证TCP的可靠性

​	保证TCP的流控特性

### 5.3 窗口数据的计算过程

![1551193271019](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551193271019.png)

​	**发送端**

​	LastByteAcked：已经被ACK确认的最大位置

​	LastByteSent：已经被发送的最后一个字节的位置

​	LastByteWritten：上层应用已写完的最后一个字节的位置

​	**接收端**

​	LastByteRead：上层应用已经读完的最后一个字节的位置

​	NextByteExpected：已经收到但还没有发送回执的最后一个位置

​	LastByteRcvd：已收到的最后一个字节的位置

> AdvertisedWindow = MaxRcvBuffer - (LastByteRcvd - LastByteRead)
>
> EffectiveWindow = AdvertisedWindow - (LastByteSent - LastByteAcked)

### 5.4 TCP会话的发送方

![1551193875997](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551193875997.png)



> 发送窗口长度 = Category #2 + Category #3



![1551194005818](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551194005818.png)



### 5.5 TCP会话的接收方

![1551194063868](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551194063868.png)

> 接受窗口长度 = Category #3

## 6 HTTP相关

### 6.1 HTTP简介

超文本传输协议HTTP主要特点：

​	支持客户、服务器模式



![1551231296338](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551231296338.png)

​	简单快速 + 灵活 + 无连接 + 无状态

### 6.2 HTTP请求结构

![1551231414760](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551231414760.png)

![1551231531718](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551231531718.png)

> 最后一行空一行是必须的，用来告诉服务器已经请求完毕
>

### 6.3 HTTP相应请求

![1551231683799](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551231683799.png)

![1551231707030](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551231707030.png)

### 6.4 请求/相应的步骤

​	· 客户端连接到Web服务器

​		默认端口号80，建立一个套接字Socket连接

​	· 发送HTTP请求

​	· 服务器接受请求并返回HTTP相应

​		服务器将定位资源放进套接字Socket，给客户端读取

​	· 释放连接TCP连接

​		连接模式为短连接：服务器主动关闭TCP连接，客户端被动关闭连接

​		连接模式为长连接（keep-alive)：保持一段时间

> ​		HTTP1.1开始默认是长连接

​	· 客户端浏览器解析HTML内容

### 6.5 *在浏览器地址栏键入URL，按下回车之后经历的流程

​	· DNS解析（浏览器缓存 -> 系统缓存 -> 路由器缓存 -> IPS服务器缓存 -> 根域名服务器缓存 -> 顶级域名服务器缓存）

​	· TCP连接（三次握手等）

​	· 发送HTTP请求

​	· 服务器处理请求并返回HTTP报文

​	· 浏览器解析渲染页面

​	· 浏览器释放TCP连接（四次挥手），连接结束

### 6.6 *HTTP状态码

​	1xx：指示信息——表示请求已接收，继续处理

​	2xx：成功——表示请求已经被成功接收、理解、接收

​	3xx：重定向——要完成请求必须进行更进一步的操作

​	4xx：客户端错误——请求有语法错误或请求无法实现

​	5xx：服务器端错误——服务器未能实现合法的请求

![1551233304624](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551233304624.png)

### 6.7 *GET请求和POST请求的区别

​	· HTTP报文层面：GET将请求信息放在URL，POST放在报文体（请求正文）中

> ​		URL有长度限制，请求正文没有长度限制

​	· 数据库层面：CET符合幂等性和安全性，POST不符合

> ​		幂等性：对数据库的一次操作和多次操作，获得结果是一致的
>
> ​		安全性：对数据库的操作没有改变其中的数据

​	· 其他层面：GET可以被缓存、被存储，而POST不行

### 6.8 *Cookie和Session的区别

**Cookie简介**

​	是由服务器发给客户端的特殊信息（存放在HTTP响应头和ResponseHead），以文	本的形式存放在客户端

​	客户端再次请求的时候，会把Cookie回发

​	服务器接收到后，会解析Cookie生成与客户单相对应的内容

**Cookie的设置以及发送过程**	![1551236379650](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551236379650.png)

**Session简介**

​	服务器端的机制，在服务器上保存的信息

​	解析客户单请求并操作session id，按需保存状态信息

**Session的实现方式**

​	· 使用Cookie来实现

​	· 使用URL回写来实现

![1551237084649](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551237084649.png)

***Cookie和Session的区别**

​	· Cookie数据存放在客户的浏览器上，Session数据放在服务器上

​	· Session相对于Cookie更安全

​	· 若考虑减轻服务器负担，应当使用Cookie

## 7 HTTP和HTTPS的区别

### 7.1 HTTPS简介

![1551237283144](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551237283144.png)

### 7.2 SSL(Security Sockets Layer，安全套接层)

​	· 为网络通信提供安全及数据完整性的一种安全协议

​	· 是操作系统对外的API，SSL3.0后更名为TLS

​	· 采用身份验证和数据加密保证网络通信的安全和数据的完整性

### 7.3 加密的方式

​	· 对称加密：加密和解密都是用同一个密钥（容易泄露）

​	· 非对称加密：加密适用的密钥和解密适用的密钥是不相同的（性能过低）

​	· 哈希算法：将任意长度的信息转换为固定长度的值，算法不可逆（如MD5）

​	· 数字签名：证明某个信息或者文件是某人发出/认同的

### 7.4 HTTPS数据传输流程

​	· 浏览器将支持的加密算法信息发送给服务器

​	· 服务器选择一套浏览器支持的加密算法，以证书的形式回发浏览器

​	· 浏览器验证证书合法性，并结合证书公钥加密信息发送给服务器

​	· 浏览器使用私钥解密信息，验证哈希，加密相应信息回发浏览器

​	· 浏览器解密相应信息，并对消息进行验真，之后进行加密交互数据

### 7.5 *HTTP和HTTPS的区别

​	· HTTPS需要到CA申请证书，HTTP不需要

​	· HTTPS密文传输，HTTP明文传输

​	· 链接方法不同，HTTPS默认使用443端口，HTTP使用80端口

​	· HTTPS = HTTP + 加密 + 认证 + 完整性保护，较HTTP安全

### 7.6 HTTPS真的安全吗

​	· 浏览器默认填充http://，请求需要进行跳转，有被劫持的风险

​	· 可以使用HSTS（HTTP Strict Transport Security）优化

## 8 Socket相关

### 8.1 Socket简介

> Socket是对TCP/IP协议的抽象，是操作系统对外开放的端口

![1551237989993](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551237989993.png)

### 8.2 Socket通信流程

![1551238052576](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551238052576.png)

### 8.3 *Socket相关的面试题

![1551238154703](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551238154703.png)

**TCPServer.java**

![1551238350265](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551238350265.png)



**LengthCalculator.java**

![1551238420341](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551238420341.png)



**TCPClient.java**

![1551238601393](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551238601393.png)



**UDPServer.java**

![1551238800779](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551238800779.png)



**UDPClient.java**

![1551238914941](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551238914941.png)

# 三、数据库

## 1 关系型数据库主要考点

### 1.1 考点思维导图

![1551239693658](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551239693658.png)

### 1.2 一个面试题

> **如何设计一个关系型数据库？**

![1551240013297](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551240013297.png)

### 1.3 索引模块

#### 1.3.1 为什么要使用索引

​	避免全表查询，快速查询数据

#### 1.3.2 什么样的信息能成为索引

​	主键、唯一健以及普通键等

#### 1.3.3 索引的数据结构

​	· 二叉查找树进行二分查找

​	· B-Tree结构进行查找

​	· B+Tree结构进行查找（MySql）

​	· Hash结构进行查找

## 2 优化索引——二叉查找树

![1551247554543](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551247554543.png)

## 3 优化索引——B树

![1551247740774](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551247740774.png)

**定义**

​	· 根节点至少包括两个孩子

​	· 树中每个节点最多含有m个孩子（m>=2）

​	· 出根节点和叶节点外，其他每个节点至少有ceil(m/2)个孩子 //ceil为取上限函数

​	· 所有叶子结点都位于同一层

**假设每个非终端节点中包含有n个关键字信息，其中**

​	· Ki(i=1...n)为关键字，且关键字按顺序升序排序K(i-1)<Ki

​	· 关键字的字数n必须满足：[ceil(m/2)-1] <= n <= m-1

​	· 非叶子节点的指针：P[1]、P[2].....P[M]；其中P[1]指向关键字小于K[1]的子树，P[M]指向关键字大于K[M-1]的子树，其他P[i]指向关键字属于(K[i-1],K[i])的子树

## 4 *优化索引——B+树

![1551249243752](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551249243752.png)

**B+树是B树的变体，其定义基本与B树相同，除了：**

​	· 非叶子节点的子树指针与关键字个数相同

​	· 飞叶子节点的子树指针P[i]，指向关键字值[K[i]，K[i+1])(左闭右开)的子树

​	· 非叶子节点仅用来索引，数据都保存在叶子节点中

​	· 所有叶子节点均有一个链指针指向下一个叶子节点

## 5 结论

​	**B+Tree更适合用来做存储索引**

​		· B+树的磁盘读写代价更低

​		· B+树的查询效率更加稳定

​		· B+树更有利于对数据库的扫描（范围查询）

## 6 优化索引——Hash和BitMap

![1551249747350](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551249747350.png)

**Hash索引缺点：**

​	· 仅仅能满足“=”、“IN”，不能使用范围查询

​	· 无法被用来避免数据的的排序操作

​	· 不能利用部分索引键查询

​	· 不能避免表扫描

​	· 遇到大量Hash值相等的情况后性能并不一定就比B-Tree索引高

![1551250007950](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551250007950.png)

> 锁的力度非常大，不适用高并发的联机事务处理系统，即OLPP系统；适合并发较小，且统计运算较多的OPAP系统

## 7 密集索引和稀疏索引的区别

​	· 密集索引文件中的每个搜索码值都对应一个索引值

​	· 稀疏索引文件只为索引码的某些值建立索引项

![1551250301797](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551250301797.png)

> **InnoDB**
>
> ​	· 若一个主键被定义，则该主键为密集索引
>
> ​	· 若没有主键被定义，该表的第一个唯一非空索引则作为密集索引
>
> ​	· 若不满足以上条件，innoDB内部会生成一个隐藏主键（密集索引）
>
> ​	· 非主键索引存储相关键位和其对应的主键值，包含两次查找

![1551254935926](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551254935926.png)

## 8 优化慢查询SQL

​	· 根据慢日志定位慢查询SQL

​	· 使用explain等工具分析SQL

​	· 修改SQL或者尽量让SQL走索引

**Explain关键字段**

​	· Type

![1551255795501](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551255795501.png)

​	· extra

![1551255834066](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551255834066.png)

![1551256098918](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551256098918.png)

> 通过select account代替select name 改进效率
>
> 再通过增加name的索引再次提升效率

## 9 联合索引的最左匹配原则的成因

​	· 最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配知道遇到范围查询（>、<、between、like）就停止匹配**比如a = 3 and b = 4 and c > 5 and d = 6 如果建立（a,b,c,d)顺序的索引，d是用不到索引的，如果减哪里(a,b,d,c)的索引则都可以用到**，a,b,d的顺序可以任意调整

​	· =和in可以乱序，比如a = 1 and b = 2 and c = 3建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

> 建立联合索引时，相当于order by第一个，再order by第二个。。。

![1551270969391](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551270969391.png)

## 10 索引是建立的越多越好吗

​	· 数据量小的表不需要建立索引，建立会增加额外的索引开销

​	· 数据变更需要维护索引，因此更多的索引意味着更多的维护成本

​	· 更多的索引也意味着需要更多的空间

## 11 锁模块之MyISAM与InooDB关于锁方面的区别

> 注：MyISAM和InooDB为MySql的两种数据库引擎

### 11.1 锁模块常见问题

​	· MyISAM与InooDB关于锁方面的区别是什么

​	· 数据库事务的四大特性

​	· 事务隔离界别以及各级别下的并发访问问题

​	· InnoDB可重复读隔离级别下如何避免幻读

​	· RC，RR级别下的InnoDB的非阻塞读如何实现

### 11.2 MyISAM与InooDB关于锁方面的区别是什么

​	· MyISAM默认用的是表级锁，不支持行级锁

> 对表进行SELECT时，会自动加上表级读锁，修改数据则需要加上写锁，但需要等待SELECT完成后才可以
>
> ![1551321940707](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551321940707.png)
>
> 普通SELECT语句后面加 for update即可上写锁，即排他锁
>
> 读锁为共享锁，可以有多个读锁；写锁为排他锁，只允许有一个

 	

​	· InnoDB默认用的是行级锁，也支持表级锁

> ![1551322251255](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551322251255.png)
>
> 默认不会被排他锁影响，除非加上lock in share mode，这才会加上排他锁
>
> 不走索引时，整张表就会被锁住，走的是表级锁
>
> IS：共享读锁 IX：排他写锁

![1551322595613](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551322595613.png)

> 表级锁是在表头上锁，行级锁需要对行对齐上锁，开销更大

### 11.3 MyISAM适合的场景

​	· 频繁执行全表count语句（MyISAM用一个变量保存了行数，InnoDB不保存行数）

​	· 对数据及逆行增删改的频率不高，查询非常频繁

​	· 没有事务

![1551324484424](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551324484424.png)

### 11.4 InnoDB适合的场景

​	· 数据增删改查都相当频繁

​	· 可靠性要求比较高，要求支持事务

### 11.5 *数据库锁的分类

​	· 按锁的粒度划分，可分为表级锁、行级锁、页级锁（介于表级锁和行级锁之间，定位于同一个存储页的相邻行之间的锁）

​	· 按锁级别划分，可分为共享锁、排它锁

​	· 按加锁方式划分，可分为自动锁（表锁，insert，update，delete等），显式锁（Select，lock share mode）

​	· 按操作划分，可分为DML锁（对数据进行操作，增删改查），DDL锁（对表结构进行变更，autotable）

​	· 按使用方式划分，可分为乐观锁（数据提交更新时才会看是否锁冲突，实现方式：版本号、时间戳）、悲观锁（全程用排它锁锁定，保守策略，效率大，增加死锁概率）

### 11.6 乐观锁的实现——版本号

程序一：

![1551324677714](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551324677714.png)

程序二：

![1551324711214](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551324711214.png)



## 12 锁模块之数据库事务的四大特性

**ACID**

​	· 原子性（Atomic）：事务包含的所有操作，要不全部执行，要么全部失败回滚

​	· 一致性（Consistency）：事务应确保数据库的状态从一致状态转向另外一个一致状态，一致状态指数据库的数据应满足完整性约束

​	· *隔离性（Isolation）：多个事务并发执行时，一个事务不应该影响其他事务

​	·  持久性（Durability）：一个事务一旦提交，他对数据库的修改应该永久保存在数据库中，在于DBMS的恢复性能

## 13 事务隔离级别以及各级别下的并发访问问题

### 13.1 事务并发访问引起的问题以及如何避免

> 隔离级别越高越安全，但串行化越严重，会降低并发级别
>
> Oracle默认ReadComitted已提交读，Mysql默认RepeatableRead可重复读

![1551333034685](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551333034685.png)

#### 13.1.1 更新丢失——MySQL所有事务隔离级别在数据库层面上均可避免

![1551325880897](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551325880897.png)

#### 13.1.2 ***脏读**——Read-Comitted事务隔离级别以上可避免

查看事务隔离性：

![1551326067402](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551326067402.png)

**设置隔离级别为“未提交读”：**规定事务可以读其他事务未提交的数据

![1551326173225](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551326173225.png)

**脏读示例：**

> session先取款100，session2再存款200，session1再回滚
>
> session1：取款100失败后回滚
>
> ![1551326328945](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551326328945.png)
>
> session2：存款200成功
>
> ![1551326374952](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551326374952.png)



**设置隔离级别为“提交读”（Oracle默认事务级别）**：规定事务只能读其他事务已经提交的数据

![1551331930685](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551331930685.png)

#### 13.1.3 不可重复读——REPEATABLE-READ事务隔离级别以上可避免

> 事务在多次读取中，由于数据被别的数据更改，导致读的数据不一致

![1551332579873](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551332579873.png)

#### 13.1.4 幻读——SERIALIZABLE事务隔离级别可避免

> 事务B修改事务A搜索的结果集，导致事务A像出现了幻觉一样

事务A：更新三条记录，但实际更新了四条

![1551332768931](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551332768931.png)

事务B：增加了一条记录

![1551332790275](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551332790275.png)

解决办法：

![1551332996793](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551332996793.png)

## 14 锁模块之当前读和快照读

### 14.1 InnoDB可重复读隔离级别下是如何避免幻读的

​	· 表象：快照读（非阻塞读）——伪MVCC

​	· 内在：next-key锁（行锁+gap锁）

### 14.2 当前读和快照读

​	· 当前读：select...lock in share mode，select...for update——加共享锁

​			  update，delete，insert——加排它锁

![1551333504053](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551333504053.png)

​	· 快照读：不加锁的非阻塞读，select，是基本多版本的并发控制

> 不加锁的条件是事务隔离级别不为SERIALIZABLE的情况

> RC隔离级别下，当前读和快照读结果是一样的
>
> ![1551401766169](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551401766169.png)
>
> RR隔离级别下，先读再更改数据再读，则当前读数据已更新，而快照读是更新前的结果
>
> ​			，先更改数据再读，则快照读和当前读都是更新后的数据

### 14.3 RC、RR级别下的InnoDB的非阻塞读（快照读）如何实现

#### 14.3.1 数据行里的DB_TRX_ID、DB_ROLL_PTR、DB_ROW_ID字段

​	· DB_TRX_ID：最近一次对本行数据做修改的事务ID

​	· DB_ROLL_PTR：回滚指针，写入回滚段的undo日志记录

​	· DB_ROW_ID：行号，包含一个随着新行插入单调递增的行ID

#### 14.3.2 undo日志：对数据进行变更操作时会产生，存储了老版数据

​	· insert_undoLog：事务对数据进行Insert时产生，只在回滚时需要，事务提交后可丢弃

​	· update_undoLog：不仅在事务回滚时需要，快照读时也需要，不能随便删除

![1551404827144](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551404827144.png)

**例如要修改Field2的值：**

​	· 用排它锁将改行锁定，复制该行修改前的数据到Undo log中

​	· 修改当前行的值

​	· 填写事务ID，将回滚指针指向undo log

![1551404986542](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551404986542.png)

**若又对Field3数据进行了修改：**

​	· 再增加一条Undo log日志，上述操作再来一遍

#### 14.3.3 read view

​	· 当执行select操作时，需要read view决定看的是哪个版本的数据

​	· 遵循可见性算法，将要修改数据的DB_TRX_ID取出来，与系统其它活跃事务ID做对比，若大于等于这些ID，则通过DB_ROLL_PTR取出上一层undo log中的DB_TRX_ID，直到小于等于这些ID

## 15 RR如何避免幻读

### 15.1 next-key锁（行锁+gap锁）

> Gap锁：锁定一个范围，但不包括本身，在RC及更低层次是没有的，RR及更高默认支持

### 15.2 对主键索引或者唯一索引会用Gap锁吗

​	· 如果where条件全部命中，则不会用Gap锁，只会加记录锁

![1551406998903](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551406998903.png)

​	· 如果where条件部分命中或者全不命中，则会加Gap锁

### 15.3 Gap锁会用在非唯一索引或者不走索引的当前读中

> 注：创建表时如何添加索引：
>
> ![1551408559962](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551408559962.png)

![1551407593031](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551407593031.png)

> 插入values(‘test’,大于6小于等于11)都会被block住，插入5,12则都可以
>
> 插入values(‘bb’,6)可以，因为bb<c，插入values(‘dd’,6)会被block，因为dd<f

![1551408463218](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551408463218.png)

> 会对所有地方都上锁，需要避免，会降低数据库的效率

## 14 关键语法讲解

### 14.1 关键语法

​	· GROUP BY

​	· HAVING

​	· 统计相关：COUNT、SUM、MAX、MIN、AVG

### 14.2 GROUP BY

​	· 满足“SELECT子句中的列名必须为分组列或列函数”

​	· 列函数对于GROUPBY子句定义的每个组各返回一个结果

![1551408970305](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551408970305.png)

![1551409648773](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551409648773.png)

![1551409671606](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551409671606.png)

> GROUPBY会把结果先放在临时表再进行统计



![1551409734632](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551409734632.png)

> 这里出错是因为聚合student_id后的course_id会返回多个结果



![1551409895167](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551409895167.png)

### 14.3 *HAVING

​	· 通常与GROUP BY子句一起使用，如果没有GROUPBY则和WHERE一样功能

​	**· *WHERE过滤行，HAVING过滤组**

​	**· 出现在同一SQL的顺序：WHERE>GROUP BY>HAVIING**

![1551410116431](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410116431.png)

![1551410276712](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410276712.png)

![1551410465278](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410465278.png)

## 15 SQL三大范式

### 15.1 第一范式——确保每列保持原子性

![1551410782608](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410782608.png)



### 15.2 第二范式——确保表中的每列都和主键相关

![1551410812915](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410812915.png)

![1551410823569](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410823569.png)



### 15.3 第三范式——确保每列都和主键列直接相关，而不是间接相关

![1551410839113](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410839113.png)

## 16 本章小结

![1551410619134](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551410619134.png)

## 17 彩蛋

### 17.1 面试的三层架构

​	· 首轮：面试技术基本功

​	· 次轮：架构设计，通过技术解决某些场景下的问题

​	· 末轮：稳定性以及未来规划，压工资

### 17.2 如何备战心仪公司

​	· 认真研究职位要求，提炼出特别需要准备的知识点

​	· 通过内部人士打听团队所做的项目的信息，重点备战这些知识点

### 17.3 项目介绍如何表达比较好

​	· 站在码农的角度介绍项目，专注技术指标以及解决思路

​	· 自信，脉路要清晰：项目用途，自己的角色，如何解决难题

​	· 项目若找不到难点，则谈谈改进，前提是熟悉相关涉及的知识点

​	· 事前用图形将你的项目勾画清楚

# 四、Redis

## 1 Redis简介

> java中使用需要导入JedisUtil.java

![1551411572363](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551411572363.png)

### 1.1 缓存中间件——Memcache和Redis的区别

#### 1.1.1 Memcache：代码层次类似Hash

​	· 支持简单数据类型

​	· 不支持数据持久化存储

​	· 不支持主存同步

​	· 不支持分片

#### 1.1.2 Redis

​	· 数据类型丰富

​	· 支持数据持久化存储

​	· 支持主存同步

​	· 支持分片

### 1.2 为什么Redis能这么快

> 10w+QPS（QPS即query per second，每秒内查询次数）

​	· 完全基于内存，绝大部分请求是纯粹的内存操作，执行效率高

​	· 数据结构简单，对数据操作也简单，存储结构就是键值对

​	· 采用单线程，单线程也能处理高并发请求，想多核也可启动多实例，不会有并发的冲突问题，效率更高（并发 != 并行）

​	· 使用多路IO复用模型，非阻塞IO

### 1.3 多路IO复用模型

> FD：File Descriptor，文件描述符
>
> ![1551412210431](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551412210431.png)

#### 1.3.1 传统的阻塞IO模型

![1551412241537](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551412241537.png)

> 若当前FD不可读，不可写，则会导致整个服务不可相应不可用

#### 1.3.2 Select系统调用

![1551412331600](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551412331600.png)

> Select负责监听文件是否可读可写

#### 1.3.3 Redis采用的IO多路复用函数

​	**epoll/kqueue/evport/select**

​	· 因地制宜，根据平台的不同选择不同的函数

​	· 优先选择时间复杂度为O(1)的IO多路复用函数作为底层实现

​	· 以时间复杂度为O(n)的select作为保底

​	· 基于react设计模式监听IO事件

## 2 Redis常用数据类型

### 2.1 String：最基本的数据类型，二进制安全（可包含任何数据），最大存储512MB

![1551419743914](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551419743914.png)

> 动态字符串sds：
>
> ![1551419782907](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551419782907.png)

### 2.2 Hash：String 元素组成的字典，适合用于存储对象

![1551419859434](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551419859434.png)

### 2.3 List：列表，按照String元素插入顺序排序

![1551422695147](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551422695147.png)

> List是后进先出的，类似于栈，能存储约40亿成员
>
> 可用于展示网页中最新的消息

### 2.4 Set：String元素组成的无序集合，通过哈希表实现，不允许重复

![1551422996683](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551422996683.png)

> 可用于存放用户粉丝集合，可以分析共同关注等功能

### 2.5 Sorted Set：通过分数来为集合中的成员进行从小到大的排序

![1551423277001](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551423277001.png)

### 2.6 用于计数的HyperLogLog，用于支持存储地理位置信息的Geo

### 2.7 底层数据类型基础

![1551423368252](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551423368252.png)

## 3 从海量Key里查询出某一固定前缀的key

### 3.1 留意细节

​	· 摸清数据规模，即问清楚边界

### 3.2 使用Keys对线上业务的影响

​	· KEYS pattern：查找所有符合给定模式pattern的key

​	· KEYS指令一次性返回所有匹配的Key

​	· 键的数量过大会使服务卡顿

### 3.3 SCAN cursor [MATCH pattern] [COUNT count]

​	· 基于游标（cursor）的迭代器，需要基于上一次的游标延续之前的迭代过程

​	· 以0作为游标开始依次新的迭代，知道命令返回游标0完成依次遍历

​	· 不保证每次执行都返回某个给定数量的元素，支持模糊查询

​	· 一次返回的数量不可控，只能是大概率符合count参数

![1551425434748](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551425434748.png)

> 每次返回两个值，第一个是游标，下一次查询就需要填上，第二个就是返回的值了
>
> 返回的游标有可能会重复，需要注意
>
> scan整体花费的时间会比Keys要长

## 4 如何实现分布式锁

### 4.1 分布式锁需要解决的问题

​	· 互斥性：任意时刻只能有一个客户端获取到锁

​	· 安全性：锁只能被使用该锁的客户端删除

​	· 死锁：指获取锁的客户端因为宕机等原因不能释放锁的情况

​	· 容错：宕机时仍然可以获取锁

### 4.2 如何通过Redis实现分布式锁

#### 4.2.1 SETNX KEY value：如果key不存在，则创建并赋值（Set if Not Exist）

​	· 时间复杂度：O(1)

​	· 返回值：设置成功，返回1；设置失败，返回0

![1551426848612](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551426848612.png)

> 因为有上述功能，而且操作是原子的，因此初期被用来实现分布式锁

### 4.3 如何解决SETNX长期有效的问题

#### 4.3.1 EXPIRE key seconds

​	· 设置key的生存时间，当key过期时（生存时间为0），会被自动删除

> 使用expire [keyname] [seconds]来设置

![1551427016301](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551427016301.png)

**实现分布式锁伪代码：**

![1551427076260](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551427076260.png)

​	· 缺点：原子性得不到满足（如果执行if语句时宕机，则其他机器永远得不到key）



### 4.4 SET key value [EX seconds] [PX milliseconds] [NX|XX]

​	· EX seconds：设置键的过期时间为 second 秒

​	· PX millisecond：设置键的过期时间为 millisecond 毫秒

​	· NX：只有键不存在时，才对键进行设置操作（效果等同于SETNX）

​	· XX：只在键已经存在时，才对键进行设置操作

​	· SET操作成功完成时，返回OK，否则返回nil

![1551427637648](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551427637648.png)

> 设置十秒后才可返回，十秒内操作返回nil，十秒后操作成功

**通过以下伪代码，实现分布式锁：**

![1551427721323](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551427721323.png)

### 4.5 大量的key同时过期的注意事项

> 集中过期，由于清除大量的key很耗时，会出现短暂的卡顿现象

​	· 解决方案：在设置key的过期时间的时候，给每个key加上随机值



## 5 如何实现异步队列

### 5.1 使用List作为队列，RPUSH生产消息，LPOP消费消息

![1551428065438](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551428065438.png)



​	· 缺点：没有等待队列里有值就直接消费

​	· 弥补：可以通过再应用层引入Sleep机制去调用LPOP重试

### 5.2 BLPOP key [key...] timeout：阻塞直到队列有消息或者超时

![1551430547241](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551430547241.png)

> 先blpop等待数据，存入数据后立刻返回数据

​	· 缺点：只能供一个消费者消费

### 5.3 pub/sub：主题订阅者模式

​	· 发送者（pub）发送消息，订阅者（sub）接收消息

​	· 订阅者可以订阅任意数量的频道

![1551430657872](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551430657872.png)

| ![1551430739974](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551430739974.png) | ![1551430760213](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551430760213.png) | ![1551430777685](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551430777685.png) |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|                      1.订阅频道myTopic                       |                      2.订阅频道myTopic                       |                    3.订阅频道anotherTopic                    |
| ![1551446864112](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551446864112.png) | ![1551446905507](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551446905507.png) | ![1551446942407](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551446942407.png) |
|                      4.myTopic发布Hello                      |                      5.两订阅均受到消息                      |                   6.未监听客户端未收到消息                   |

> pub/sub的缺点：消息的发布是无状态的，无法保证可达，重新上线有可能就无法收到
>
> 这是就需要处理专业消息队列，如kafka等



## 6 Redis持久化方式之RDB

### 6.1 RDB（快照）持久化：保存某个时间点的全量数据快照

![1551447185434](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551447185434.png)

​	· save 900 1：如果900秒内有一次写入指令，就触发一次快照

​	· save 300 10：如果300秒内有十次写入指令，就触发一次快照，如果不满10，则同上

​	· save 60 10000：如果60秒内有10w次写入指令，就触发一次快照

> 以上规则是为了平衡性能和数据安全



![1551447362380](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551447362380.png)

> 当备份出错时，主进程就停止写入操作，为了保护持久化数据一致性问题



![1551447437251](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551447437251.png)

> 当备份时，需要将RDB文件压缩后保存



**SAVE**：阻塞Redis的服务器进程，直到RDB文件被创建完毕

![1551447832988](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551447832988.png)

> 很少被使用，因为SAVE是使用主线程的，而Redis是使用一个主线程来工作的，会阻塞collect请求

**BGSAVE**：Fork出一个子进程来创建RDB文件，不阻塞服务器进程

![1551447853059](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551447853059.png)

![1551448366866](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551448366866.png)

> 定时转移RDB文件，文件名后加时间戳，这样就可以定时保存全量数据备份

### 6.2 自动化触发RDB持久化的方式

​	· 根据redis.conf配置里的SAVE m n定时触发（用的是BGSAVE）

​	· 主从复制时，主节点自动触发

​	· 执行Debug Reload

​	· 执行ShutDown且没有开启AOF持久化

### 6.3 BGSAVE原理

![1551448863774](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551448863774.png)

> 系统调用fork()：创建进程，实现了Copy-on-Write写时复制

![1551449097455](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551449097455.png)

### 6.4 RDB持久化

**缺点**：

​	· 内存数据的全量同步，数据量大会由于IO而严重影响性能

​	· 可能会因为Redis挂掉而丢失从当前至最近一次快照期间的数据



## 7 Redis持久化之AOF以及混合模式

### 7.1 AOF（Append-Only-File）持久化：保存写状态

​	· 记录下除了查询以外的所有变更数据库状态的指令

​	· 以append的形式追加保存到AOF文件中（增量）

![1551449543076](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551449543076.png)

> 设为yes生效

![1551449560499](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551449560499.png)

> 三种模式，always为一旦缓存发生变化就写入AOF中，everysec为每隔一秒将缓存内容写入到AOF中，no为由操作系统当缓存区被填满才会写入AOF中

### 7.2 日志重写解决AOF文件大小不断增大的问题

​	· 调用fork()，创建一个子进程

​	· 子进程把新的AOF写到一个临时文件里，不依赖原来的AOF文件

​	· 主进程持续将新的变动同时写到内存和原来的AOF里

​	· 主进程获取子进程重写AOF的完成新号，往新AOF同步增量变动

​	· 使用新的AOF文件替换掉旧得AOF文件

### 7.3 Redis数据的恢复

#### 7.3.1 RDB和AOF文件共存情况下的恢复流程

![1551449972164](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551449972164.png)

### 7.4 RDB和AOF的优缺点

​	· RDB优点：全量数据快照，文件小，恢复快

​	· RDB缺点：无法保存最近一次快照之后的数据

​	· AOF优点：可读性高，适合保存增量数据，数据不易丢失

​	· AOF缺点：文件体积大，恢复时间长

### 7.5 RDB-AOF混合持久化方式（默认配置）

![1551450141041](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551450141041.png)

> BFSAVE做镜像全量持久化，AOF做增量持久化



## 8 Pipeline及主从同步

### 8.1   使用Pipeline的好处

​	· Pipeline和Linux的管道类似

​	· Redis基于请求/相应模型，单个请求处理需要一一应答

​	· Pipeline批量执行指令，节省多次IO往返的时间

​	· 有顺序依赖的指令建议分批发送

### 8.2 Redis的同步机制（主从、哨兵、集群）

#### 8.2.1 主从同步原理

![1551450943727](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551450943727.png)

> 一个Master用于写操作，多个Salve用于读操作
>
> 最终一致性：不需要保证M和S是同步的，过了一段时间他们会趋于同步的
>
> 第一次同步时，主节点做一次BGSAVE，并同时将后续修改操作，记录到内存的buffer里面去，完成后将RDB文件全量同步到从节点里面，从节点接受完成后，就将RDB加载到内存中，加载完成后，再告诉主节点，将期间修改的操作记录及增量数据同步到从节点进行存档，到某个时间点之前的全量数据同步完成后，将改时间之后的数据进行存档，这就是整个的同步过程

#### 8.2.2 全同步过程

​	· Salve发送sync命令到Master

​	· Master启动一个后台进程，将Redis中的数据快照保存到文件中

​	· Master将保存数据快照期间接收到的写命令缓存起来

​	· Master完成写文件操作后，将该文件发送给Salve

​	· 使用新的AOF文件替换掉旧的AOF文件

​	· Master将这期间收集的增量写命令发送给Salve端

#### 8.2.3 增量同步过程

​	· Master接收到用户的操作指令，判断是否需要传播到Salve（增删改需要）

​	· 将操作记录追加到AOF文件

​	· 将操作传播到其他Salve：1、对齐主从库；2、往响应缓存写入指令

​	· 将缓存中的数据发送给Salve

### 8.3 Redis Sentinel（官方管理工具，可监控集成）

#### 8.3.1 解决主从同步Master宕机后的主从切换问题

​	· 监控：检查主从服务器是否运行正常

​	· 提醒：通过API向管理员或者其他应用程序发送故障通知

​	· 自动故障迁移：主从切换，将一个Salve升级为Master

### 8.4 流言协议Gossip

> 反熵：在杂乱无章中寻求一致

​	· 每个节点都随机地与对方通信，最终所有节点的状态达成一致

​	· 种子节点定期随机向其他节点发送节点列表以及需要传播的消息

​	· 不保证信息一定会传递给所有节点，但是最终会趋于一致



## 9 Redis集群

### 9.1 如何从海量数据里快速找到所需

​	· 分片：按照某种规则去划分数据，分散存储在多个节点上

​	· 常规的按照哈希划分无法实现节点的动态增减

### 9.2 一致性哈希算法

​	· 对2^32取模，将哈希值空间组织成虚拟的圆环

![1551453997291](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551453997291.png)

​	· 将数据key使用相同的函数Hash计算出哈希值

![1551454071672](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551454071672.png)

​	· 假设NodeC宕机

![1551454124181](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551454124181.png)

> 数据会存储到顺时针最近的一个可用服务器上

​	· 新增服务器 NodeX

![1551454182038](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551454182038.png)

### 9.3 Hash环的数据倾斜问题

![1551454285622](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551454285622.png)

​	· 引入虚拟节点解决数据倾斜的问题

![1551454316408](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551454316408.png)



# 五、Linux

## 1 Linux的体系结构

![1551454441989](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551454441989.png)

​	· 体系结构主要分为用户态（用户上层活动）和内核态

​	· 内核：本质是一段管理计算机硬件设备的程序

​	· 系统调用：内核的访问接口，是一种不能再简化的操作

​	· 公用函数库：系统调用的组合拳

​	· Shell：命令解释器，可编程

![1551454621550](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551454621550.png)

> 输入可看所有系统调用
>
> man 2 查看系统调用；man 3查看公共函数

### 1.1 常用Shell指令

​	· ls：查询目录下的文件列表

​	· cat：查询文件具体内容

​	· more：只能从前向后查看文件

​	· less：查看文件内容，且可以向前向后翻页

​	· echo $SHELL：查看当前shell版本

​	· cat /etc/shells：查看所有shell版本

​	· chsh -s [shell路径]：切换到某shell版本

​	· man [指令]：查询指令用法

## 2 查找特定文件

### 2.1 find

![1551497405961](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551497405961.png)

​	· 作用：在指定目录下查找文件

#### 2.1.1 从当前用户的目录递归查找：

![1551497456165](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551497456165.png)

#### 2.1.2 从根目录递归查找

![1551497551471](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551497551471.png)

#### 2.1.3 找以某字符串为开头的文件

![1551497646302](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551497646302.png)

> 其中*为通配符，表示0个或多个字符

#### 2.1.4 以忽略大小写的方式找以某字符串为开头的文件

![1551497698119](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551497698119.png)

### 2.2 面试里常用的方式

​	· find ~ -name “target3.java”：精确查找文件

​	· find ~ -name “target*”：模糊查找文件

​	· find ~ -iname “target*”：不区分文件名大小写去查找文件

​	· man find：更多关于find指令的使用说明



## 3 检索文件内容

### 3.1 grep

![1551498777881](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551498777881.png)

​	· 全称：Global Regular Expression Print

​	· 作用：查找文件里符合条件的字符串

![1551511329501](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551511329501.png)

> “moo”是要找的文件内容，target*是所有以target开头的文件
>
> 展示内容中只会展示目标内容所在的行

![1551511840163](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551511840163.png)

> 如果后面不跟文件名，则会等待输入内容（标准输入流）中出现符合的字符串

### 3.2 管道操作符|

​	· 可将指令连接起来，前一个指令的输出作为后一个指令的输入

![1551511960465](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551511960465.png)

![1551511986755](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551511986755.png)

> 两种写法，达到同样的效果

#### 3.2.1 使用管道注意的要点

​	· 只处理前一个命令正确输入，不处理错误输出

![1551513153939](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551513153939.png)

> 错误的指令无法被当做输入传递给grep

​	

​	· 右边命令必须能够接受标准输入流，否则传递过程中数据会被抛弃

![1551513198146](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551513198146.png)

> echo无法接受标准输入流

​	

​	· sed,awk,grep,cut,head,top,less,more,wc,join,sort,split等

![1551513324099](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551513324099.png)

> 在日志文件中找带有partial[true]的行



![1551513454774](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551513454774.png)

> 再上一个结果中找engine有关的信息



![1551513511155](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551513511155.png)

> 使用gret -v过滤掉这条查询语句本身

### 3.3 面试里常用的方式

​	· grep ‘partial\\[true\\]’ bcs-plat-al-data.info.log

> 查找包含某个内容的文件，并将相关行展示出来

​	· grep -o ‘engine\\[[0-9a-z*\\]’

> 筛选出符合正则表达式的内容

​	· grep -v ‘grap’

> 过滤掉包含相关字符串的内容



## 4 对日志内容做统计

### 4.1 awk

![1551513788083](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551513788083.png)

​	· 一次读取一行文本，按输入分隔符进行切片，切成多个组成部分

​	· 将切片直接保存在内建的变量中，$1,$2...（$0表示行的全部）

​	· 支持对单个切片的判断，支持循环判断，默认分隔符为空格

![1551514390549](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551514390549.png)

![1551514970355](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551514970355.png)

> 满足$1=tcp && $2=1就打印出整行

![1551515317895](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551515317895.png)

> 满足上述条件或满足是第一行，因此结果包含了第一行的表头



![1551515367875](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551515367875.png)

> 以逗号作为分隔符，进行分割
>
> -F：以什么符号作为分隔符进行切片



![1551515501004](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551515501004.png)

> 用enginearr保存engine的名字



## 5 批量替换文本内容

### 5.1 sed

![1551515834665](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551515834665.png)

​	· 全名stream editor，流编辑器

​	· 适合用于对文本的行内容进行处理

![1551515926196](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551515926196.png)

> sed ‘/[要替换的内容]/[替换后的内容]/’，^str表示以str打头的字符串，开头s表示是对字符串进行的操作
>
> 默认是将变更的内容输出到终端，并不改变文件的内容

![1551516027517](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551516027517.png)

> 加入-i，则可以替换文本内容



![1551516064027](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551516064027.png)

> 句号换成分号，$表示以某某结尾



![1551516119580](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551516119580.png)

> 若加入g，则会全部替换，否则只替换一个



![1551516247860](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551516247860.png)

> 要删除空行，因为不是字符串，所以开头不加s，最后的d表示删除



![1551516303052](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551516303052.png)

> 根据Integer删除其所在行



# 六、Java底层知识：JVM

![1551535410128](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551535410128.png)

## 1 谈谈你对Java的理解

​	· 平台无关性：一次编译，到处运行

​	· GC：垃圾回收机制

​	· 语言特性：泛型，表达式等

​	· 面向对象：封装、继承、多态等

​	· 类库：并发库、网络库等

​	· 异常处理



## 2 平台无关性如何实现

![1551537381775](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551537381775.png)

### 2.1 javap：java平台自带的反编译器

![1551537001223](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551537001223.png)

### 2.2 为什么JVM不直接将源码解析成机器码去执行

​	· 准备工作：每次执行都需要各种检查 

​	· 兼容性：也可以讲别的语言解析成字节码



## 3 JVM如何加载.class文件

###  3.1 Java虚拟机

![1551537581477](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551537581477.png)

​	· Class Loader：依据特定格式，加载class文件到内存

​	· Execution Engine：对命令进行解析

​	· Native Interface：融合不同开发语言的原生库为java所用

​	· Runtime Data Area：JVM内存空间结构模型



## 4 谈谈反射

![1551538100168](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551538100168.png)

### 4.1 写一个反射的例子

> 反射对象：Class、Method、Field。。。

写一个Robot.java：

![1551538245214](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551538245214.png)

再写一个ReflectSample.java：

![1551538534032](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551538534032.png)

> **getDeclaredMethod**方法可以获得public、private、protected的方法，但无法获得继承和接口的方法
>
> 还需要SetAccessible为true
>
> 完成了对私有方法的调用
>
> getDeclaredMethod获得该类所有方法，**getMethod**能获得public方法及继承和接口的方法

![1551539056822](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551539056822.png)

> sayHi方法只给了一个welcome，name是空值

![1551539122815](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551539122815.png)

> 使用Field等设置后，就设置好了name



## 5 谈谈ClassLoader

![1551580370797](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551580370797.png)

### 5.1 类从编译到执行的过程

​	· 编译器将Robot.java源文件编译成Robot.class字节码文件

​	· ClassLoader将字节码转换为JVM中的Class<Robot>对象

​	` JVM利用Class<Robot>对象实例化为Robot对象

### 5.2 ClassLoader种类

​	· BootStrapClassLoader：C++编写，加载核心库java.*

​	· ExtClassLoader：java编写，加载拓展库javax.*

​	· APPClassLoader：java编写，加载程序所在目录

​	· 自定义ClassLoader：java编写，定制化加载

### 5.3 自定义ClassLoader的实现

![1551583175228](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551583175228.png)

自定义一个Wali.java：

![1551583322038](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551583322038.png)

自定义一个ClassLoader：

![1551585144054](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551585144054.png)

![1551585219501](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551585219501.png)

主程序：

![1551585334994](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551585334994.png)

运行结果：

![1551585391283](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551585391283.png)



## 6 ClassLoader的双亲委派机制

![1551585462288](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551585462288.png).1 

### 6.1 为什么要使用双亲委派机制去加载类

​	· 避免多份同样字节码的加载 



##  7 LoadClass和ForName的区别

### 7.1 类的加载方式

​	· 隐式加载：new

​	· 显式加载：loadClass，forName

### 7.2 类的装载（Class对象的生成过程）过程

![1551588648480](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551588648480.png)

​	· Class.forName得到的class是已经初始化完成的

​	· ClassLoder.LoadClass得到的class是还没有链接的 

![1551588820314](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551588820314.png)

> Forname会执行初始化代码，ClassLoader则不会



## 8 Java内存模型之线程独占部分

### 8.1 内存简介

![1551589138419](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551589138419.png)

​	· 32位处理器：2^32的可寻址空间

​	· 64位处理器：2^64的可寻址空间

### 8.2 地址空间的划分

​	· 内核空间

​	· 用户空间

![1551589217030](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551589217030.png)

> 32位用户空间为3GB，64位用户空间为512GB

### 8.3 JVM内存模型——JDK8

![1551589329669](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551589329669.png)

#### 8.3.1 程序计数器（Program Counter Register）

​	· 当前线程所执行的字节码行号指示器（逻辑）

​	· 改变计数器的值来选取下一条需要执行的字节码指令

​	· 和线程是一对一的关系即“线程私有”

​	· 对Java方法计数，如果是Native方法则计数器值为Undefined

​	· 不会发生内存泄露（PCR为逻辑计数器）

#### 8.3.2 Java虚拟机栈（Stack）

​	· Java方法执行的内存模型

​	· 包含多个栈帧

![1551589577651](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551589577651.png)

#### 8.3.3 局部变量表和操作数栈

​	· 局部变量表：包含方法执行过程中的所有变量	· 

​	· 操作数栈：入栈、出栈、复制、交换、产生消费变量

![1551589681575](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551589681575.png)

**执行add(1,2)：**

![1551589836014](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551589836014.png)

#### 8.3.4 递归为什么会引发 java.lang.StackOverflowErrow异常

![1551590604518](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551590604518.png)

> 斐波那契100w就会报错

 	· 递归过深，栈帧数超出虚拟栈深度

​	· 解决方法：限制递归层数或者循环替换递归

#### 8.3.5 虚拟机栈过多会引发 java.lang.OutOfMemoryError异常

![1551590730078](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551590730078.png)

> Windows运行有可能会导致死机

### 8.4 本地方法栈

​	· 与虚拟机栈类似，主要作用于标注了native的方法



## 9 Java内存模型之线程共享部分

### 9.1 元空间（MetaSpace）与永久代（PermGen）的区别

> jdk8后使用元空间替代了永久代 

​	· 元空间使用本地内存，而永久代使用的是jvm内存

**MetaSpace相比PermGen的优势：**

​	· 字符串常量池再永久代中，容易出现性能问题和内存溢出

​	· 类和方法的信息大小难以确定，给永久代的大小制定带来困难

​	· 永久代会为GC带来不必要的复杂性 

​	· 方便HotSpot与其他JVM入JrockIT的集成

### 9.2 Java堆（Heap）

​	· 对象实例的分配区域

![1551591228911](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551591228911.png)

> 堆可以实际上不连续，逻辑上连续

​	· GC管理的主要区域

![1551591290836](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551591290836.png)



## 10 Java内存模型常考题

### 10.1 JVM三大性能调优参数 -Xms -Xmx -Xss的含义

![1551591367052](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551591367052.png)

​	· -Xss：规定了每个线程虚拟机栈（堆栈）的大小

​	· -Xms：堆的初始值（达到初始值后会自动扩容，直至最大值）

​	· -Xmx：堆能达到的最大值

### 10.2 Java内存模型中堆和栈的区别——内存分配策略

​	· 静态存储：编译时确定每个数据目标再运行时的存储空间需求（要求程序代码中不允许有可变数据结构，也不允许嵌套和递归的出现）

​	· 栈式存储：数据区需求在编译时未知，运行时模块入口前确定

​	· 堆式存储：编译时或运行时模块入口都无法确定，动态分配

### 10.3  Java内存模型中堆和栈的区别

​	· 联系：引用对象、数组时，栈里定义变量保存堆中目标的首地址

![1551591843079](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551591843079.png)

​	· 管理方式：栈自动释放，堆需要GC

​	· 空间大小：栈比堆小

​	· 碎片相关：栈产生的碎片远小于堆

​	· 分配方式：栈支持静态和动态分配，而堆仅支持动态分配

​	· 效率：栈的效率比堆高

​	· 栈：灵活程度不够

​	· 堆：动态分配，效率不够栈高

### 10.4 元空间、堆、线程独占部分间的联系——内存角度

![1551592030457](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551592030457.png)

![1551592075545](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551592075545.png)

### 10.5 不同JDK版本之间的intern()方法的区别——JDK6 VS JDK6+

![1551592192008](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551592192008.png)

**JDK6：**

![1551592744214](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551592744214.png)

**JDK7：**

![1551592766430](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551592766430.png)



**字符串试验：**

**JDK8：**

![1551592861585](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551592861585.png)

**JDK6：**

![1551592905837](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551592905837.png)

**解释：**

![1551593043332](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551593043332.png)

 ![1551593165772](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551593165772.png)

> JDK6时，只能放副本进入常量池，副本和本体是不一样的；
>
> JDK7+时，可以放引用进常量池，引用和本体是一样的；



# 七、Java底层知识：GC相关

## 1 垃圾回收之标记算法

### 1.1 对象被判定为垃圾的标准

​	· 没有被其他对象引用

### 1.2 判定对象是否为垃圾的算法

#### 1.2.1 引用计数算法

​	· 通过判断对象的引用数量来决定对象是否可以被回收

​	· 每个对象实例都有一个引用计数器，被引用则+1，完成引用则-1

​	· 任何引用计数为0的对象实例可以被当做垃圾收集

​	· **优点**：执行效率高，程序执行收影响较少

​	· **缺点**：无法检测出循环引用的情况，导致内存泄漏

![1551769206059](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551769206059.png)

#### 1.2.2 可达性分析算法

> 通过判断对象的引用链是否可达来决定对象是否可以被回收

![1551770438753](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551770438753.png)

**可以作为GC Root的对象：**

​	· 虚拟机栈中引用的对象（栈帧中的本地变量表）

​	· 方法区中的常量引用的对象

​	· 方法区中的类静态属性引用的对象

​	· 本地方法栈中JNI（Native方法）的引用对象

​	· 活跃线程的引用对象

## 2 垃圾回收算法之回收算法

### 2.1 标记-清除算法（Mark and Sweep）

​	· 标记：对根集合进行扫描，对存活的对象进行标记

​	· 清除：对堆内存从头到尾进行线性遍历，回收不可达对象内存

![1551772989102](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551772989102.png)

​	· 碎片化

### 2.2 复制算法（Copying）

​	· 分为对象面和空闲面

​	· 对象在对象面上创建

​	· 存活的对象被从对象面复制到空闲面

​	· 将对象面所有对象内存清除 

![1551773112357](C:\Users\31711\Documents\剑指java面试_offer直通车.assets\1551773112357.png)

​	· 解决碎片化问题

​	· 顺序分配内存，简单高效