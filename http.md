优秀博客： https://blog.csdn.net/qq_42651904/article/details/91355804?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161735959616780255230575%2522%252C%2522scm%25



### 一、OSI与TCP/IP各层的结构与功能

![五层协议的体系结构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOC83LzI5LzE2NGU1MzA3NDcxZThlYmE?x-oss-process=image/format,png)

在互联网中应用层协议很多，域名系统DNS，支持万维网的HTTP协议，支持电子邮件的SMTP协议等。

各层之间为什么要分层？

- 各层之间时独立的
- 灵活性好
- 结构上可分割开
- 易于实现和维护
- 能促进标准化工作

OSI七层模型：物、数、网、传、会、表、应

TCP/IP 四层模型：网、网、传、应

- 应用层：http、ftp、nfs、ssh、telnet
- 传输层：TCP、UDP
- 网络层：IP、ICMP
- 网络接口层：以太网帧协议、ARP

以太网帧协议：

- ARP协议：根据IP地址获取mac地址。
- 以太网帧协议：根据mac地址，完整数据包传输。

IP协议：

- 版本：IPV4、IPV6
- TTL：time   to  live  。 设置数据包在路由节点中的跳转上限。每经过一个路由节点，该值-1，减为0的路由，有义务将该数据包丢弃。

IP地址：可以在网络环境中，唯一标识一台主机

端口号：可以在网络的一台主机上，唯一标识一个进程

IP地址+端口号：可以在网络环境中，唯一标识一个进程

C/S模型：client-server

- 优点：缓存大量数据、

B/S模型：browser-server



#### 1、应用层

- 域名系统（DNS）：该系统用于在Internet中将域名及其公共广播的网络节点转换成IP地址。

  缩写DNS，是因特网的一项核心服务，它作为可以将域名和IP地址相互映射的分布式数据库，能够使人更方便的访问互联网，而不用去记住能够被机器直接读取的IP数串。

- HTTP协议（超文本传输协议）

  超文本传输协议是互联网上应用最为广泛的一种网络协议。
  
- 协议有：HTTP、FTP、SMTP、DNS、HTTPS 、POP3 、DHCP

- TFTP（简单文件传输协议）：

- SNMP（简单网络管理协议）：该协议提供了监控网络设备的方法，一级配置管理，统计信息收集，性能管理及安全管理等。

- TELNET（远程登陆）：提供远程访问其它主机功能，它允许用户登录Internet主机，并在这台主机上执行命令

#### 2、表示层

数据的表示、安全、压缩。格式有：JPEG  ASCII  EBCDIC  加密格式等

#### 3、会话层

建立、管理、终止会话。对应主机进程，指本地主机与远程主机正在进行的会话

#### 4、传输层

运输层的主要任务就是负责向两台主机进程之间的通信提供通用的数据传输服务。协议有TCP、UDP，数据包一旦离开网卡即进入网络传输层。

- UDP的主要特点：面向无连接、不可靠的数据报服务
  1. UDP 是无连接的；
  2. UDP 使用尽最大努力交付，即不保证可靠交付，因此主机不需要维持复杂的链接状态（这里面有许多参数）；
  3. UDP 是面向报文的；
  4. UDP 没有拥塞控制，因此网络出现拥塞不会使源主机的发送速率降低（对实时应用很有用，如 直播，实时视频会议等）；
  5. UDP 支持一对一、一对多、多对一和多对多的交互通信；
  6. UDP 的首部开销小，只有8个字节，比TCP的20个字节的首部要短。
- TCP的主要特点：面向连接、可靠的字节流服务
  1. TCP 是面向连接的。（就好像打电话一样，通话前需要先拨号建立连接，通话结束后要挂机释放连接）；
  2. 每一条 TCP 连接只能有两个端点，每一条TCP连接只能是点对点的（一对一）；
  3. TCP 提供可靠交付的服务。通过TCP连接传送的数据，无差错、不丢失、不重复、并且按序到达；
  4. TCP 提供全双工通信。TCP 允许通信双方的应用进程在任何时候都能发送数据。TCP 连接的两端都设有发送缓存和接收缓存，用来临时存放双方通信的数据；
  5. 面向字节流。TCP 中的“流”（Stream）指的是流入进程或从进程流出的字节序列。“面向字节流”的含义是：虽然应用程序和 TCP 的交互是一次一个数据块（大小不等），但 TCP 把应用程序交下来的数据仅仅看成是一连串的无结构的字节流。

#### 5、网络层

进行逻辑地址寻址，实现不同网络之间的路径选择。协议有：ICMP  IGMP  IP(IPV4  IPV6)

#### 6、数据链路层

建立逻辑连接、进行硬件地址寻址、差错校验等功能。

将比特组合成字节进而组合成帧，用MAC地址访问介质，错误发现但不能纠正

#### 7、物理层

建立、维护、断开物理连接。

TCP/IP层级模型结构，应用层之间的协议通过逐级调用传输层、网络层和物理数据链路层而可以实现应用层的应用程序通讯互联。



### 二、HTTP与HTTPS

#### 1、HTTP

被用于Web浏览器和网站服务器之间传递信息，以明文方式发送内容，不提供任何方式的数据加密,  如果攻击者截取了Web浏览器和网站服务器之间的传输报文，就可以直接读懂其中的信息，因此，HTTP协议不适合传输一些敏感信息，比如：信用卡号、密码等支付信息。

- 状态码：

  - 1xx：提示信息，表示目前是协议处理的中间状态，还需要后续的操作
  - 2xx：成功，保温已经收到并被正确处理
    1. 200   //  OK 是最常见的成功状态码，表示一切正常,如果是非 `HEAD` 请求，服务器返回的响应头都会有 body 数据。
    2.  204   //  No Content  也是常见的成功状态码，与 200 OK 基本相同，但响应头没有 body 数据。
    3.  206   //   Partial  Content  是应用于HTTP分块下载或断电续传，表示响应返回的body数据并不是资源的全部，而是其中一部分，也是服务器处理成功的状态

  - 3xx：重定向，资源位置发生变动，需要客户端重新发送请求

    类状态码表示客户端请求的资源发送了变动，需要客户端用新的URL重新发送请求获取资源，也就是重定向

    1. 301  //   表示永久重定向，说明请求的资源已经不存在了，需改用新的URL再次访问
    2. 302  //   表示临时重定向，说明请求的资源还在，但暂时需要用另一个URL来访问
    3. 304   //   不具有跳转的含义，表示资源未修改，重定向已存在的缓冲文件，也称缓冲重定向，用于缓存控制

  - 4xx：客户端错误，请求报文有误，服务器无法处理

    类状态码表示客户端发送的报文有误，服务器无法处理，也就是错误码的含义

    1. 400   //   表示客户端请求的报文有错误，但知识笼统的错误
    2. 403   //  表示服务器禁止访问资源，并不是客户端的请求出错
    3. 404   //   表示请求的资源在服务器上不存在或未找到，所以无法提供给客户端

  - 5xx：服务器错误，服务器在处理请求时内部发生了错误

    类状态码表示客户端请求报文正确，但是服务器处理时内部发生了错误，属于服务器端的错误码

    1. 500  //  是个笼统通用的错误码，服务器发生了什么错误并不知道
    2. 501  //  表示客户端请求的功能还不支持，类似“即将开业，敬请期待” 的意思
    3. 502  //  通常是服务器作为网关或代理时返回的错误码，表示服务器自身工作正常，访问后端服务器发生了错误
    4. 503  //   表示服务器当前很忙，暂时无法响应服务器，类似“网络服务正忙，请稍后重试”的意思。

#### 2、HTTPS

HTTPS协议是由SSL/TLS+HTTP协议构建的可进行加密传输、身份认证的网络协议，比HTTP协议安全。

TLS与SSL在**传输层**对网络连接进行加密。

主要作用分为两种：一种是建立一个信息安全通道，来保证数据传输的安全；另一种是确认网站的真实性。

#### 3、主要区别

- https协议需要到CA申请证书，一般免费证书较少，因而需要一定费用。

- http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl/tls加密传输协议。

- http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

- http的连接很简单，是无状态的；HTTPS协议是由SSL/TLS+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。

#### 4、客户端在使用HTTPS方式与Web服务器通信时的步骤

1. 客户端使用https的URL(统一资源定位器)访问Web服务器，要求与Web服务器建立SSL连接。
2. Web服务器收到客户端请求后，会将网站的证书信息（证书中包含公钥）传送一份给客户端。
3. 客户端的浏览器与Web服务器开始协商SSL/TLS连接的安全等级，也就是信息加密的等级。
4. 客户端的浏览器根据双方同意的安全等级，建立会话密钥，然后利用网站的公钥将会话密钥加密，并传送给网站。
5. Web服务器利用自己的私钥解密出会话密钥。
6. Web服务器利用会话密钥加密与客户端之间的通信。

#### 













