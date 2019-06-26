# 计算机网络
## 网络概述
#### 1.分层
	OSI七层协议：应用层、表示层、会话层、运输层、网络层、数据链路层、物理层
	TCP/IP四层协议：应用层、运输层、网际层、网络接口层
	五层协议：应用层、运输层、网络层、数据链路层、物理层
#### 2.各层协议
	应用层：HTTP,FTP,TELENT,SMTP,DNS解析,TFTP,SNMP,DHCP
	运输层：TCP,UDP
	网络层：IP,ARP(TCP/IP模型),RIP,OSPF,BGP,RARP,ICMP,NAT
	链路层：PPP,ARP(OSI模型),HDLC,FR

## 运输层
#### 1.TCP连接三次握手
	第一步：客户端的TCP首先向服务端的TCP发出一个特殊的TCP报文段，该报文段不包含任何数据。但在报文段首部
	中的SYN标志位被置为1。另外，客户会随机地选择一个初始序号client_isn，并将此编号放置于该起始的TCP SYN
	报文段的序号字段中。该报文段会被封装在一个IP数据报中，并发送给服务器。
	第二步：当包含TCP SYN报文段的IP数据报到达服务器主机，服务器会从该数据报中提取SYN报文段，为该TCP连接
	分配TCP缓存和变量，并向该客户TCP发送允许连接的报文段。首先，SYN被置为1，该TCP报文段首部的确认号字段
	ACK为client_isn+1。最后，服务器选择自己的初始序号server_isn，并将其放到TCP报文段的首部字段中。
	第三步：在收到SYN ACK报文段后，客户也要给该连接分配缓存和变量。客户主机则向服务器发送另一个报文段，
	这最后一个报文段对服务器的允许连接的报文段进行了确认，将值server_isn+1放置到TCP报文段首部的确认字段
	ACK来完成工作。因为连接建立了，所以SYN置为0。
	总结：
	①SYN=1，seq=client_isn   客=>服
	②SYN=1, ACK=client_isn+1, seq=server_isn  服=>客
	③SYN=0，ACK=server_isn+1, seq=client_isn+1  客=>服
#### 2.TCP释放连接四次挥手
	第一步：当客户端打算关闭连接，客户TCP向服务器进程发送一个特殊的TCP报文段。这个特殊的报文段让其首部的FIN标志位
	置为1。
	第二步：当服务器接收到该报文段后，就向发送方回送一个确认报文段
	第三步：服务器发送它自己的终止报文段，其FIN被置为1
	第四步：最后，该客户对这个服务器的终止报文段进行确认。
	总结：
	①FIN=1, seq=client_isn   客=>服
	②SYN=1, ACK=client_isn+1,seq=server_isn  服=>客
	③FIN=1, ACK=client_isn+1,seq=server_isn2 服=>客
	④ACK=server_isn2+1, seq=client_isn+1 客=>服
#### 3.TCP为什么可靠
	三次握手，超时重传，滑动窗口，拥塞控制
#### 4.TCP流量控制（滑动窗口）
	所谓流量控制就是让发送方的发送速率不要太快，要让接收方来得及接收
	方法：利用滑动窗口实现流量控制
	设A向B发送数据，在连接建立时，B告诉A:"我的接收窗口rwnd=400"。因此，发送窗口不能超过接收方给出的接收窗口的值
	注：TCP的窗口单位是字节，不是报文段。
#### 5.TCP拥塞控制
	所谓拥塞控制就是防止过多的数据注入到网络中，这样可以使网络中的路由器或链路不至于过载
	拥塞控制的四种方法：
	①慢开始：由小到大逐渐增大发送窗口，即由小到大逐渐增大拥塞窗口的数值。
	使用慢开始算法后，每经历一个传输轮次（RTT），拥塞窗口cwnd就加倍
	②拥塞避免：思路是让拥塞窗口cwnd缓慢地增大，即经过一个RTT后就把发送方的拥塞窗口cwnd加1，而不是加倍
	③快重传：快重传算法首先要求接收方每收到一个失序的报文段后就立即发出确认，而不要等待自己发送数据时才进行捎带
	确认。
	eg:发送M1,确认M1。发送M2,确认M2。发送M3,丢失。发送M4,重复确认M2。发送M5，重复确认M2......
	快重传算法规定，发送方只要一连收到三个重复确认就应当立即重传对方尚未收到的报文段M3,而不必等待为M3设置的重传计时
	器到期。
	④快恢复：与快重传配合使用。

## 协议
#### 1.页面请求的工作流程
	①用户通过浏览器输入链接地址请求所需资源
	②浏览器接受用户的请求，并把请求组装成指定的格式发送给服务器端，客户端和服务器端之间通过HTTP来完成具体的交互
	③服务器接收到客户端发来的请求，并查找用户所需要的资源
	④服务器查找到资源后，把资源返回给客户端
	⑤服务器通过把响应消息组装成特定的消息格式后返回给客户端，这个过程通过HTTP来完成
	⑥浏览器对HTML进行解析，并把响应结果返回给客户。
#### 2.HTTP和HTTPS的区别
	①https协议要申请证书到ca，需要一定经济成本
	②http是明文传输，https是加密的安全传输
	③http端口是80，https是443
	④http连接简单，没有状态。https是ssl加密的传输，身份认证的网络协议，较为安全。
#### 3.http的请求有哪些，502和504的区别
	OPTIONS: 返回服务器针对特定资源所支持的HTTP请求方法
	HEAD：向服务器索要与GET请求相一致的响应。
	GET：向特定的资源发出请求
	POST:向指定资源提交数据进行处理请求。
	PUT：向指定资源位置上传最新内容
	DELETE：请求服务器删除Request-URI所标识的资源
	TRACE：回显服务器收到的请求，主要用于测试和诊断。

	502：作为网关或者代理工作的服务器尝试执行请求时，从上游服务器接收到无效的响应。
	504：作为网关或者代理工作的服务器尝试执行请求时，未能及时从上游服务器（URL标识出的服务器）或者辅助服务器（DNS）收到响应
#### 4.http1.1和http1.0的区别
	①缓存处理，http1.1引入了更多的缓存控制策略
	②带宽优化及网络连接的使用。HTTP1.0存在浪费带宽现象，例如客户端只需要某个对象的一部分，服务器却将整个对象送过来了。
	HTTP1.1在请求头引入了range头域，允许只请求资源的某个部分
	③错误通知的管理，HTTP1.1新增了24个错误状态响应码
	④长连接,HTTP1.1支持长连接和请求的流水线。在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗跟延迟。

		
