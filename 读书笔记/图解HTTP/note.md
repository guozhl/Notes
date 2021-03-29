<!-- # 安全
## 前端常见安全问题
- 同源策略
- XSS攻击
- CSRF攻击
- Sql注入
- html脚本注入
- 点击劫持
- window.opener 安全问题
- 文件上传漏洞 -->

<!-- ## csrf 的核心原理
[前端安全系列之二：如何防止CSRF攻击？](https://juejin.cn/post/6844903689702866952)

## Web 安全总结
[Web 安全总结](https://mp.weixin.qq.com/s/rU32rVM6Q-ele01ZB3RFzg) -->

## 第1章
HTTP：超文本传输协议。

TCP/IP协议族分层管理：
- 应用层：决定了向用户提供应用服务时通信的活动（FTP，DNS，HTTP）
- 传输层：对上层应用层，提供处于网络连接中的两台计算机之间的数据传输（TCP/UDP）
- 网络层：处理在网络上流动的数据包（IP）
- 链路层：用来处理连接网络的硬件部分（控制操作系统，硬件的设备驱动，网卡，光纤等）

各协议层在使用HTTP协议的通信过程中的作用：
1. DNS 提供域名到 IP 地址之间的解析服务
2. HTTP 协议生成针对目标 Web 服务器的 HTTP 请求报文
3. TCP 位于传输层，会将大块数据分割成以报文段为单位的数据包按序进行管理，通过三次握手提供可靠的字节流传输
4. IP 协议搜索对方的地址，把各种数据包传送给对方，一边中转一边传送
5. 服务端 TCP 协议按序重组收到的报文段
6. 服务端 IP 协议处理请求的内容
7. 请求的处理结果按同样的协议进行回传

URI: 统一资源标识符。


## 第3章
HTTP通信过程包括从客户端发往服务器端的请求及从服务器端返回客户端的响应。
### HTTP报文
- 请求报文
    - 报文首部
        - 请求行：包含请求方法、请求URI、HTTP版本（GET /login HTTP1.1）
        - 首部字段
            - 请求首部字段
            - 通用首部字段
            - 实体首部字段
        - 其他
    - 空行（CR+LF）
    - 报文主体
- 响应报文
    - 报文首部
        - 状态行：包含状态码、原因短句、HTTP版本（HTTP/1.1 200 ok）
        - 首部字段
            - 响应首部字段
            - 通用首部字段
            - 实体首部字段
        - 其他
    - 空行（CR+LF）
    - 报文主体

### 编码提升传输速率
HTTP传输数据进行编码传输
- 优点：提升传输速率
- 缺点：编码的操作需要计算机完成，会消耗更多的CPU资源

编码方式：
- 压缩传输的内容编码：指明应用在实体内容上的编码格式进行压缩传送，客户端接收后解码。
    - gzip（GUN zip）
    - compress（UNIX 系统的标准压缩）
    - deflate（zlib）
    - identity（不进行编码）
- 分割发送的分块传输编码
    将实体主体分成多个部分（块），每一块都会用十六进制来标记块的大小，最后一块使用“0(CR+LF)”来标记

### 发送多种数据的多部分对象集合
多部分对象集合包含的对象如下：
- multipart/form-data
```
Content-Type: multipart/form-data; boundary=AaB03x

--AaB03x
Content-Disposition: form-data; name="field1"

--AaB03x
Content-Disposition: form-data; name="pics"; filename="file1.txt"
Content-Type: text/plain

--AaB03x--
```
- multipart/byteranges
```
Content-Type: multipart/byteranges; boundary=THIS_STRING_SEPARATES

<!-- 实体1 -->
--THIS_STRING_SEPARATES
Content-Type: application/pdf
Content-Range: bytes 500-999/8000

<!-- 实体2 -->
--THIS_STRING_SEPARATES
Content-Type: application/pdf
Content-Range: bytes 7000-7999/8000

--THIS_STRING_SEPARATES--
```
使用 boundary 字段来多部分对象集合指明的各类实体。

### 获取部分内容的范围请求
断点传输。主要通过首部字段添加 Range：
```
GET /tip.jpg HTTP1.1
Range: bytes=5001-10000
```
```
HTTP/1.1 206 Partial Content
Content-Range: bytes 5001-10000/10000
Content-length: 5000
Content-Type: image/jpeg
```
针对范围请求，响应会返回 206 Partial Content 的响应报文。
__如果服务器无法响应范围请求，则会返回状态码200OK和完整的实体内容__

### 内容协商
内容协商技术包括：
- 服务器驱动协商 - 根据请求首部字段分析返回的资源内容，如中英文。
    - Accept
    - Accept-Charset
    - Accept-Encoding
    - Accept-Language
    - Content-Language
- 客户端驱动协商 - 用户从浏览器的可选项进行选择
- 透明协商 - 由服务端和客户端各自进行内容协商

## 第4章 返回结果的HTTP状态码
- 1XX - Informational，接收的请求正在处理
- 2XX - Success，请求正常处理完毕
    - 200 OK，请求处理成功
    - 204 No Content，返回的响应报文中不含实体的主题部分，一般用于客户端网服务端发送信息，而客户端不需要新信息
    - 206 Partial Content，范围请求
- 3XX - Redirection，需要进行附加操作以完成请求
    - 301 Moved Permanently，永久重定向
    - 302 Found，临时重定向
    - 303 See Other，类似于302，由于请求对应的资源存在着另一个URI，应使用GET方法定向获取请求的资源。__可能建议将POST改为GET请求__
    - 304 Not Modified，当请求附带条件时（If-），而服务端判断请求不满足条件
    - 307 Temporary Redirect，类似于302，但比302严格：禁止POST改为GET
- 4XX - Client Error，服务器无法处理请求
    - 400 Bad Request，请求报文中存在语法错误
    - 401 Unauthorized，表示该请求需要有通过HTTP认证的认证信息
    - 403 Forbidden，服务端拒绝请求
    - 404 Not Founnd，找不到请求资源
- 5XX - Server Error，服务器处理请求出错
    - 500 Internal Server Error，表示服务端在执行时发生了错误
    - 503 Service Unavailable，服务器正忙或在维护，无法处理请求

## 第7章 确保Web安全的HTTPS
### 认识HTTPS
HTTP不足之处：
- 通信使用明文（不加密），内容可能会被窃听
- 不验证通信方的身份，因此有可能遭遇伪装
- 无法证明报文的完整性，所以有可能已遭篡改（中间人攻击）

HTTPS如何解决：
- 加密处理防止被窃听
1. 通信的加密
2. 内容的加密
- 查明对手的证书以判断身份
- 提供摘要功能

HTTP+加密+认证+完整性保护=HTTPS

### HTTPS原理
加密方法：
- 共享密钥加密
加密和解密同用一个密钥的方式称为共享密钥加密，也叫对称密钥加密。
- 公开密钥加密
公开密钥加密使用一对非对称的密钥，一把叫私有密钥，一把叫公开密钥。发送密文的一方使用对方的公开密钥进行加密处理，对方收到被加密的信息后，再使用自己的私有密钥进行解密。

HTTPS使用这两种加密方式的混合方式进行加密。在 __交换密钥环节__ 使用公开密钥加密方式，之后的 __建立通信交换报文阶段__ 使用共享密钥加密方式。原因是公开密钥加密方式处理速度很慢。

## 第9章 基于HTTP的功能追加协议
### HTTP
瓶颈：
- 一条连接上只可发送一个请求。
- 请求只能从客户端开始。客户端不可以接收除响应以外的指令。
- 请求/响应首部未经压缩就发送。首部信息越多延迟就越大。
- 发送冗长的首部。每次互相发送相同的首部造成的浪费较多。
- 可任意选择数据压缩格式。非强制压缩发送。

解决：
- Ajax
是一种有效利用 JavaScript 和 DOM 的操作，以达到局部 Web 页面替换加载的异步通信手段。 ): __只优化了页面局部更新的问题__
- Comet
通过延时应答，模拟实现服务器端向客户端推送。 ): __只优化了服务端主动推送__
- SPDY
1. 多路复用流：通过单一的TCP连接，可以无限制处理多个HTTP请求。
2. 赋予请求优先级：给请求逐个分配优先级顺序。
3. 压缩HTTP首部：压缩HTTP请求和响应的首部。
4. 推送功能：支持服务器主动向客户端推送数据的功能。
5. 服务器提示功能：服务器可以主动提示客户端请求所需的资源。
): __a：要使用SPDY，Web浏览器和Web服务器都需要做改动；b：不支持多域名的资源；c：web端本身的问题。__
- WebSocket
Web浏览器与Web服务器之间全双工通信标准。
1. 推送功能：支持服务器向客户端推送数据
2. 减少通信量：只要建立起WebSocket连接，就希望一直保持连接状态。

### HTTP/2.0
- HTTP Speed + Mobility：用于改善并提高移动端通信时的通信速度和性能的标准。
- Network-Friendly HTTP Upgrade：在移动端通信时改善HTTP性能的标准。

HTTP/2.0的7项技术及讨论：
- 压缩
- 多路复用
- TLS义务化
- 协商
- 客户端拉曳/服务器推送
- 流量控制
- WebSocket

### Web服务器管理文件的WebDAV

<!-- ## 第11章 Web的攻击技术
针对Web应用的攻击模式：
- 主动攻击，例如：SQL注入攻击、OS命令注入攻击
- 被动攻击，例如：跨站脚本攻击、跨站点请求伪造 -->
