## http和https区别?（http1.0、http1.1、http2、https）http2特性、头部压缩、多路复用，画出http和https的整个过程
- http：Hyper Text Transfer Protocol，超文本传输协议，是一种建立在TCP上的无状态链接，整个基本的工作流程是客户端发送一个HTTP请求，说明想要访问的资源和请求的动作，服务端收到请求之后，开始处理请求，最后通过HTTP响应把结果返回给客户端。HTTP请求：状态行（URL、RequestMethod）、请求头（RequestHeaders）、请求正文（RequestBody:QueryString、FormData）。HTTP响应一样包括这3个。

- http0.9：HTTP协议的最初版本，只支持GET，并且只能请求访问HTML格式的资源

- http1.0：新增了POST、Head（服务器只会返回头部信息，可以用来检查超链接的有效性，省带宽并且速度快）请求，添加Content-Type可以支持多种数据格式请求，添加了cache，但是每次TCP只能发送一个请求，当服务器响应只会就会关闭这次连接，下一个请求需要再次建立TCP连接，就是不支持keepalive

- http1.1：解决了1.0的keepalive问题，加入了持久连接，一个TCP连接可以允许多个http请求，添加了管道机制，一个TCP连接允许同时多个请求的发送，增加了并发性；新增了PUT（幂等）、DELETE、PATCH（非幂等）等请求方式；

- http2.0：新增了多路复用、头部压缩、服务器推送；

- https：HTTP协议传输的数据都是未加密的，也就是明文的，因此使用HTTP协议传输隐私信息非常不安全。为了保证这些隐私数据能加密传输，于是网景公司设计了SSL（Secure Sockets Layer）协议用于对HTTP协议传输的数据进行加密，从而就诞生了HTTPS。SSL目前的版本是3.0，被IETF（Internet Engineering Task Force）定义在RFC 6101中，之后IETF对SSL 3.0进行了升级，于是出现了TLS（Transport Layer Security） 1.0，定义在RFC 2246。实际上我们现在的HTTPS都是用的TLS协议，但是由于SSL出现的时间比较早，并且依旧被现在浏览器所支持，因此SSL依然是HTTPS的代名词，但无论是TLS还是SSL都是上个世纪的事情，SSL最后一个版本是3.0，今后TLS将会继承SSL优良血统继续为我们进行加密服务。    
参考连接：[https://www.cnblogs.com/zery/p/5164795.html](https://www.cnblogs.com/zery/p/5164795.html)

- keep-alive timeout：一个http请求产生的tcp连接在传送完最后一个响应后，还需要hold住keepalive timeout秒后，才会关闭这个连接（比如nginx的keepalive_timeout、Apache的KeepAliveTimeout）；

- http请求超时：分为请求超时、响应超时；
    - 请求超时（setConnectionTimeout）：客户端发起请求，通信层开始请求与服务器建立连接（包括再重试），如果在规定时间内（一般都是系统winHttp或浏览器默认规定的、或者自己用HttpClient设置的connectionTimeout）还没有连接到服务器，那么认为超时。
    - 响应超时（setReadTimeout）：请求已经连接到服务器，当服务器开始处理时（比如在数据库执行查询时），并且在规定的时间内还没有对stream做出反馈，那么客户端就认为超时，主动断开与服务器的连接。
    - 注意点1：有时设置了connection为10s，read为10s，但是一两秒就失败的情况，它是属于NotFoundHostException不属于超时，因为还没到连接的时候。
    - 注意点2：下载文件，服务器一直发送了几个小时都没结束，因为这个过程中服务器和客户端是有交互的，一直在发送流到客户端，所以也不会超时，但是如果中间有10s没有发送流，还是会发生超时的。

- 幂等性：幂等通俗来说是指不管进行多少次重复操作，都是实现相同的结果。

- 头部压缩：HTTP/1.1并不支持 HTTP 首部压缩，为此 SPDY 和 HTTP/2 应运而生， SPDY 使用的是通用的 DEFLATE 算法，而 HTTP/2 则使用了专门为首部压缩而设计的 HPACK 算法。

- 多路复用：允许同时通过单一的HTTP/2连接发起多重的请求-响应消息。关键之一就是在 应用层(HTTP/2)和传输层(TCP or UDP)之间增加一个二进制分帧层。在 HTTP/1.1 协议中 「浏览器客户端在同一时间，针对同一域名下的请求有一定数量限制。超过限制数目的请求会被阻塞」。这也是为什么需要多个静态资源cdn域名的原因之一。HTTP/2通信都在一个连接上完成，这个连接可以承载任意数量的双向数据流。

- 为什么连接的时候是三次握手，关闭的时候却是四次握手？     
因为当Server端收到Client端的SYN连接请求报文后，可以直接发送SYN+ACK报文。其中ACK报文是用来应答的，SYN报文是用来同步的。但是关闭连接时，当Server端收到FIN报文时，很可能并不会立即关闭SOCKET，所以只能先回复一个ACK报文，告诉Client端，"你发的FIN报文我收到了"。只有等到我Server端所有的报文都发送完了，我才能发送FIN报文，因此不能一起发送。故需要四步握手。

- 为什么TIME_WAIT状态需要经过2MSL(最大报文段生存时间)才能返回到CLOSE状态？    
虽然按道理，四个报文都发送完毕，我们可以直接进入CLOSE状态了，但是我们必须假象网络是不可靠的，有可以最后一个ACK丢失。所以TIME_WAIT状态就是用来重发可能丢失的ACK报文。

- http请求过程：
![http请求过程](http://www.chenhongjun.cn/images/http/http.png)

- http请求报文：
![http请求报文](http://www.chenhongjun.cn/images/http/http-request-message.png)

- http响应报文：
![http响应报文](http://www.chenhongjun.cn/images/http/http-response-message.png)

- https请求过程：   
大概流程（在通信过程中，对称加密和非对称加密起了重大作用）：   
1.浏览器检查证书是不是由可以信赖的机构颁发的，确认证书有效和此证书是此网站的    
2.使用公钥加密了一个随机对称密钥，包括加密的URL一起发送到服务器    
3.服务器用自己的私匙解密了你发送的钥匙。然后用这把对称加密的钥匙给你请求的URL链接解密   
4.服务器用你发的对称钥匙给你请求的网页加密。你也有相同的钥匙就可以解密发回来的网页了   
![https请求过程](http://www.chenhongjun.cn/images/http/https.png)

- TCP/IP协议模型：
![TCP/IP协议模型1](http://www.chenhongjun.cn/images/http/tcp-ip.png)    

- TCP/IP数据传送过程中的包装：
![TCP/IP协议模型2](http://www.chenhongjun.cn/images/http/tcp-ip2.png)

- TCP整个流程：    
**注意点：在TIME_WAIT状态中，如果TCP client端最后一次发送的ACK丢失了，它将重新发送。TIME_WAIT状态中所需要的时间是依赖于实现方法的。典型的值为30秒、1分钟和2分钟。等待之后连接正式关闭，并且所有的资源(包括端口号)都被释放。**
![TCP整个流程](http://www.chenhongjun.cn/images/http/tcp-whole-process.png)

- TCP客户端流程：    
![TCP客户端流程](http://www.chenhongjun.cn/images/http/tcp-client.png)

- TCP服务端流程：
![TCP服务端流程](http://www.chenhongjun.cn/images/http/tcp-server.png)

参考：[https://www.jianshu.com/p/c1d6a294d3c0](https://www.jianshu.com/p/c1d6a294d3c0)     
参考：[https://blog.csdn.net/whuslei/article/details/6667471/](https://blog.csdn.net/whuslei/article/details/6667471/)


## https比http慢多少，如何进行优化
开启 SSL 会增加内存、CPU、网络带宽的开销，慢的原因主要是加解密的时间。   
https其实就是建构在SSL/TLS之上的 http协议，所以要比较https比http多用多少服务器资源，主要看SSL/TLS本身消耗多少服务器资源。    
http使用TCP 三次握手建立连接，客户端和服务器需要交换3个包，https除了 TCP 的三个包，还要加上 ssl握手需要的9个包，所以一共是12个包。http 建立连接，按照下面链接中针对Computer Science House的测试，是114毫秒；https建立连接，耗费436毫秒。ssl 部分花费322毫秒，包括网络延时和ssl 本身加解密的开销（服务器根据客户端的信息确定是否需要生成新的主密钥；服务器回复该主密钥，并返回给客户端一个用主密钥认证的信息；服务器向客户端请求数字签名和公开密钥）。    

优化：对称加密/解密要消耗过多的CPU,为此有专门的SSL芯片。从内存、CPU、网络针性去优化吧。    


## https有什么缺点
- https需要多次握手，会一定程度上降低用户的访问速度
- 网站改用https以后，由http跳转到https的方式增加了用户访问耗时（多数网站采用301、302跳转）
- https涉及到的安全算法会消耗CPU资源，需要增加大量机器（https访问过程需要加解密）
- ssl证书费用较高，并且需要在服务器上进行部署、更新等
