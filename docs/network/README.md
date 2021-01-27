# 计算机网络

!> **计算机网络** 面试题以及答案整理

## HTTP状态码
1. [官方网站](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
2. ![jcVmoh](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/jcVmoh.png)

## HTTP中的请求方法
![Q1BBEG](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/Q1BBEG.png)

## 一个网页的请求过程
[参考](https://github.com/Bulandent/blog/issues/14)

## TCP三次握手

## 关于很多TCP面试题的总结
[参考](https://www.nowcoder.com/discuss/530380?source_id=profile_create_nctrack&channel=-1)

## 拥塞控制

## http302，什么时候会用302，问我实际场景什么时候用302


## 问我502 答错了 我答成503了


## 服务器server怎么把cookie设置到浏览器的

## TCP握手 挥手

## 然后问知道哪些http方法


## 了解 HTTP 协议吗？
HTTP 是超文本传输协议，基于 TCP 协议。

## HTTP 协议的连接过程？

请求 TCP 连接
首先，浏览器与 Web 服务器的 HTTP 80端口建立一个 TCP 套接字连接。

发送 HTTP 请求

通过 TCP 连接，浏览器向服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据组成。

接受请求返回响应

Web服务器解析请求，定位请求资源。服务器将资源复本写到 TCP 套接字，由浏览器读取。一个响应由状态行、响应头部、空行和响应数据组成。

释放TCP连接

若 connection 模式为 close，则服务器主动关闭TCP连接，客户端被动关闭连接，释放 TCP 连接

若connection 模式为 keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求。

解析 HTML 内容

浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析每一个响应头，响应头告知以下为若干字节的HTML文档和文档的字符集。客户端浏览器读取响应数据HTML，根据HTML的语法对其进行格式化，并在浏览器窗口中显示。

当时问到这个回答的很简略，一直准备了 HTTPS 的回答，没想到忽略了基础的 HTTP 协议。

## HTTP 协议的头部有哪些属性？
通用信息头
Request URL 请求的地址 域名
Request Method 请求的方法类型 GET / POST
Status Code 响应状态码 200 OK / 404 NOT-FOUND
Remote Address 表示远程服务器地址 IP地址
响应头
Content-Length 响应体的长度
Content-type 返回的响应MIME类型与编码:告诉浏览器它发送的数据属于什么文件类型
Cache-control 指定请求和响应遵循的缓存机制
public 响应可被任何缓存区缓存
private 对于单个用户的整个或部分响应消息，不能被共享缓存处理
no-cache 表示请求或响应消息不能缓存
请求头
Accept 告诉服务器可以接受的文件格式。
Accept-Encoding gzip等指定浏览器可以支持的web服务器返回的内容压缩编码类型。
Accept-Language 浏览器支持的语言。
Cache-Control 指定请求和响应遵循的缓存机制。
Connection keep-alive 表示是否需要持久连接。
Cookie HTTP请求发送时，会把保存在该请求域名下的所有 cookie 值一起发送给web服务器。
Host 指定请求的服务器的域名和端口号。
Referer 告诉服务器是从哪个网站链接过来的。
User-Agent 简称UA。内容包含发出请求的用户信息。
Authorization 当客户端访问受口令保护时，服务器端会发送401状态码和 www-authenticate 响应头，要求客户机使用 Authorization 来应答。
详细属性见文后附表。
一开始答的时候我竟然报了半天状态码还浑然不知，然后看见面试官一脸诧异的看着我才反应过来Orz。重新听了问题后还是没有答出来Orz。



Header	解释	示例
Accept-Ranges	表明服务器是否支持指定范围请求及哪种类型的分段请求	Accept-Ranges: bytes
Age	从原始服务器到代理缓存形成的估算时间（以秒计，非负）	Age: 12
Allow	对某网络资源的有效的请求行为，不允许则返回405	Allow: GET, HEAD
Cache-Control	告诉所有的缓存机制是否可以缓存及哪种类型	Cache-Control: no-cache
Content-Encoding	web服务器支持的返回内容压缩编码类型。	Content-Encoding: gzip
Content-Language	响应体的语言	Content-Language: en,zh
Content-Length	响应体的长度	Content-Length: 348
Content-Location	请求资源可替代的备用的另一地址	Content-Location: /index.htm
Content-MD5	返回资源的MD5校验值	Content-MD5: Q2hlY2sgSW50ZWdyaXR5IQ==
Content-Range	在整个返回体中本部分的字节位置	Content-Range: bytes 21010-47021/47022
Content-Type	返回内容的MIME类型	Content-Type: text/html; charset=utf-8
Date	原始服务器消息发出的时间	Date: Tue, 01 Nov 2020 08:12:31 GMT
ETag	请求变量的实体标签的当前值	ETag: “737060cd8c284d8af7ad3082f209582d”
Expires	响应过期的日期和时间	Expires: Thu, 01 Dec 2010 16:00:00 GMT
Last-Modified	请求资源的最后修改时间	Last-Modified: Tue, 01 Nov 2020 12:00:00 GMT
Location	用来重定向接收方到非请求URL的位置来完成请求或标识新的资源	Location: http://www.xxx.com/xxx.html
Pragma	包括实现特定的指令，它可应用到响应链上的任何接收方	Pragma: no-cache
Proxy-Authenticate	它指出认证方案和可应用到代理的该URL上的参数	Proxy-Authenticate: Basic
refresh	应用于重定向或一个新的资源被创造，在5秒之后重定向（由网景提出，被大部分浏览器支持）	Refresh: 5; url=http://www.xxx.com/xxx.html
Retry-After	如果实体暂时不可取，通知客户端在指定时间之后再次尝试	Retry-After: 120
Server	web服务器软件名称	Server: Apache/1.3.27 (Unix) (Red-Hat/Linux)
Set-Cookie	设置Http Cookie	Set-Cookie: UserID=JohnDoe; Max-Age=3600; Version=1
Trailer	指出头域在分块传输编码的尾部存在	Trailer: Max-Forwards
Transfer-Encoding	文件传输编码	Transfer-Encoding:chunked
Vary	告诉下游代理是使用缓存响应还是从原始服务器请求	Vary: *
Via	告知代理客户端响应是通过哪里发送的	Via: 1.0 fred, 1.1 nowhere.com (Apache/1.1)
Warning	警告实体可能存在的问题	Warning: 199 Miscellaneous warning
WWW-Authenticate	表明客户端请求实体应该使用的授权方案	WWW-Authenticate: Basic

## DNS 是什么？DNS 的查询过程？
DNS 是域名系统，通常用来解析域名为 IP 地址。
本地解析 通过本地缓存进行解析。
直接解析 向客户机所设定的局部 DNS 服务器发一个查询请求。
递归解析 局部 DNS 服务器向该域名的根域服务器查询，再由根域名服务器一级级向下查询。
迭代解析 局部 DNS 服务器把请求转发至上一级 DNS 服务器，再请求上上级直到查询到该域名。
当时只回答了递归解析和迭代解析，其实还有 Host 文件、本地和直接解析