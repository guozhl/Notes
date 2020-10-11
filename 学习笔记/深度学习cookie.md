### 1. 什么是cookie
众所周知，HTTP协议是无状态的协议。什么叫无状态呢？

__无状态是指Web浏览器与Web服务器之间不需要建立持久的连接，这意味着当一个客户端向服务器端发出请求，然后Web服务器返回响应（Response），连接就被关闭了，在服务器端不保留连接的有关信息。也就是说，HTTP请求只能由客户端发起，而服务器不能主动向客户端发送数据。__


这样的话，客户端通过HTTP协议访问服务端的时候，并没有一个标识证明自己是谁。对于无状态协议，如果我们只是普通的访问一个静态资源，并无大碍。而如果我们要访问的是需要登陆的网站，例如淘宝，无状态协议会无法判断每次进行访问的是谁。

为了解决这个问题，我们需要在每次请求服务的时候，给服务器一个标识，告诉服务器自己是谁，而这个标识就是cookie里携带的信息。

### 2. cookie机制
当用户第一次向一个网站发送登陆请求的时候，服务器会发送一个HttpResponse响应到客户端，其中包含Set-Cookie的头部。客户端保存cookie，之后的每次请求都会包含一个Cookie的头部，作为身份标识。

表1：为Cookie服务的首部字段
首部字段名 | 说明 | 首部类型
:-: | :-: | :-:
Set-Cookie | 开始状态管理所使用的Cookie信息 | 响应首部字段 
Cookie | 服务器接收到的Cookie信息| 请求首部字段 

![cookie机制](/cookie.png)


### 3. cookie属性
#### Set-Cookie
```
Set-Cookie: status=enable; expires=Tue, 05 Jul 2011 07:26:31 GMT; => path=/; domain=.hackr.jp;
```
当服务器准备开始管理客户端当状态时，会事先告知各种信息。下面的表格列举了Set-cookie的字段值。

表2：Set-Cookie字段的属性
属性 | 说明
:-: | :-:
NAME=VALUE | 赋予Cookie的名称和其值（必需项） 
expires=DATE | Cookie的有效期（若不明确指定则默认为浏览器关闭前为止）
path=PATH | 将服务器上的文件目录作为Cookie的适用对象（若不指定则默认为文档所在的文件目录）
domain=域名 | 作为Cookie适用对象的域名（若不指定则默认为创建Cookie的服务器的域名）
Secure | 仅在HTTPS安全通信时才会发送Cookie
HttpOnly | 加以限制，使Cookie不能被JavaScript脚本访问

##### expires属性
Cookie的expires属性指定浏览器可发送Cookie的有效期。

当省略expires属性时，其有效期仅限于维持浏览器会话（Session）时间段内。这通常限于浏览器应用程序被关闭之前。

另外，一旦Cookie从服务器端发送至客户端，服务器端不存在可以显示删除Cookie的方法。但可通过覆盖已过期但Cookid，实现对客户端Cookie对实质性删除操作。
##### HttpOnly属性
Cookie的HttpOnly属性是Cookie的扩展功能，它使JavaScript脚本无法获得Cookie。其主要目的是为防止跨站脚本攻击对Cookie的信息窃取。

#### Cookie
```
Cookie: status=enable
```
首部字段Cookie会告知服务器，当客户端想获得HTTP状态管理支持时，就会在请求中包含从服务器接收到的Cookie。接收到多个Cookie时，同样可以以多个Cookie形式发送。


### 参考资料
1. 《图解HTTP》
2. [深入理解Cookie](https://www.jianshu.com/p/6fc9cea6daa2)
3. [当浏览器全面禁用三方 Cookie](https://mp.weixin.qq.com/s/vHeRStcCUarwqsY7Y1rpGg)
