---
title: HTTP协议
date: 2020-01-29 16:09:07
tags:
- 网络
categories:
- 学习
---

# HTTP协议简介

> **HTTP**(HyperText Transfer Protocol，超文本传输协议)，是一种用于分布式、协作式和超媒体信息系统的应用层协议。HTTP是万维网数据通信的基础。



# HTTP协议版本

## HTTP/1.1

1999年6月公布的`RFC 2616`，定义了HTTP协议中现今广泛使用的一个版本——HTTP/1.1

### 持久连接

1.1 版的最大变化，就是引入了持久连接（persistent connection），即TCP连接默认不关闭，可以被多个请求复用，不用声明`Connection: keep-alive`

客户端和服务器发现对方一段时间没有活动，就可以主动关闭连接。不过，规范的做法是，客户端在最后一个请求时，发送`Connection: close`，明确要求服务器关闭TCP连接

### 管道机制

1.1 版还引入了管道机制（pipelining），即在同一个TCP连接里面，客户端可以同时发送多个请求。这样就进一步改进了HTTP协议的效率。

举例来说，客户端需要请求两个资源。以前的做法是，在同一个TCP连接里面，先发送A请求，然后等待服务器做出回应，收到后再发出B请求。管道机制则是允许浏览器同时发出A请求和B请求，但是服务器还是按照顺序，先回应A请求，完成后再回应B请求。

### Content-Length字段

一个TCP连接现在可以传送多个回应，势必就要有一种机制，区分数据包是属于哪一个回应的。这就是`Content-length`字段的作用，声明本次回应的数据长度。

`Content-Length: 3495`

上面代码告诉浏览器，本次回应的长度是3495个字节，后面的字节就属于下一个回应了。

在1.0版中，`Content-Length`字段不是必需的，因为浏览器发现服务器关闭了TCP连接，就表明收到的数据包已经全了。



## HTTP/2

 HTTP/2标准于2015年5月以`RFC 7540`正式发表，取代HTTP 1.1成为HTTP的实现标准

### 二进制协议

HTTP/1.1 版的头信息肯定是文本（ASCII编码），数据体可以是文本，也可以是二进制。HTTP/2 则是一个彻底的二进制协议，头信息和数据体都是二进制，并且统称为"帧"（frame）：头信息帧和数据帧。

二进制协议的一个好处是，可以定义额外的帧。HTTP/2 定义了近十种帧，为将来的高级应用打好了基础。如果使用文本实现这种功能，解析数据将会变得非常麻烦，二进制解析则方便得多。





# 与HTTP关系密切的协议、服务和方案

## IP协议

IP(Internet Protocol)协议位于网络层，作用是把各种数据包发送给目标

- IP地址：指明了节点被分配到的地址，可变换
- MAC地址：网卡所属的固定地址，基本不会更改
- IP地址可以和MAC地址进行配对

## TCP协议

TCP 位于传输层，提供可靠的字节流服务

### 字节流服务

字节流服务(Byte Stream Service)是指，为了方便传输，将大块数据分割成以报文段（ segment ）为单位的数据包进行管理

### 可靠的传输服务

可以把数据准确可靠地传给对方

### 三次握手

TCP 协议采用了**三次握手(hree-way handshaking)策略**确保数据能准确无误地送达目标处

>标志位(flag)：
>- SYN(synchronize)：发送端发送给接收端
>- SYN/ACK：接收端回传确认信息
>- ACK(acknowledgement)：发送端再回传数据包，代表“握手”结束



## DNS服务

**DNS(Domain Name System)服务**和 HTTP 协议一样位于应用层的协议。它提供域名到 IP 地址之间的解析服务。

> 计算机既可以被赋予 IP 地址，也可以被赋予主机名和域名。
>
> 用户通常使用主机名或域名来访问对方的计算机，而不是直接通过 IP 地址访问。

DNS 协议提供通过域名查找 IP 地址，或逆向从 IP 地址反查域名的服务。



## 统一资源标识符方案

> **统一资源标识符**(Uniform Resource Identifier，URI)用于在世界范围内唯一标识并定位信息资源
>
> 两种形式：
>
> - 统一资源定位符(Uniform Resource Locator，URL)
> - 统一资源名(Uniform Resource Name，URN)

### URL

统一资源定位符(URL)是URI最常见的形式，可以明确的说明如何从一个精确、固定的位置获取资源

格式及示例：

> ​      ①                           ②                                      ③            ④             ⑤                   ⑥                          ⑦                     ⑧
>
> `http:`//`username:password`@`example.com`:`8042`**/over/there/**`index.html`**?id=456123&page=3**`#name`

- ①协议部分：指定使用协议，`//`为分隔符

- ②登录信息(认证)部分：指定用户名和密码作为获取资源的身份认证，此项是可选项

- ③服务器地址部分：域名或IP地址均可。

- ④服务器端口号部分：指定服务器连接的网络端口号，用户省略则自动使用默认端口号

- ⑤虚拟目录部分：从服务器地址后的第一个`/`到最后一个 `/`为止，虚拟目录不是URL必须的部分。

- ⑥文件名部分：从服务器地址后的最后一个`/`到`?`为止是文件名部分；如果没有`?`则是从服务器地址后的最后一个`/`开始到`#`为止是文件名部分；如果`?`和`#`均没有，则从服务器地址后的最后一个`/`到URL结束都是文件名部分。文件名部分也不是一个URL必须的部分，如果省略该部分，则使用默认的文件名

- ⑦查询参数部分：从`?`开始到`#`为止是查询参数部分，允许有多个参数，参数与参数之间用`&`作为分隔符。

- ⑧锚部分：从`#`开始到最后，锚部分也不是一个URL必须的部分。

  

# HTTP报文

## 请求报文

### 结构

- 请求行
  - 请求方法
  - 请求URI
  - HTTP版本

- 请求头
  - 请求首部字段
  - 通用首部字段
  - 实体首部字段
  - 其他
- 空白行

- 请求正文

```cmd
# 请求行
POST /login.php HTTP/1.1     
# 请求头(一个“:”是一行)
HOST: www.xxx.com                 
User-Agent: Mozilla/5.0(Windows NT 6.1;rv15.0)
Gecko/20100101 Firefox/1.5
# 空白行代表请求头结束

# 请求正文(非必须)
Username=admin&password=admin
```

### 请求方法(大小写敏感)

| **GET**  | 请求获取由Request-URI所标识的资源。                       |
| :------- | --------------------------------------------------------- |
| **POST** | 在Request-URI所标识的资源后附加新的数据(请求正文中)       |
| **HEAD** | 请求获取Request-URI所标识的资源的响应消息报头             |
| OPTIONS  | 请求查询服务器的性能，或查询与资源相关的选项和需求        |
| PUT      | 请求服务器存储一个资源，并用Request-URI所标识的资源       |
| DELETE   | 请求服务器删除由Request-URI所标识的资源                   |
| TRACE    | 请求服务器回送收到的请求信息，主要用于测试或诊断          |
| CONNECT  | 通常用于SSL加密服务器的链接（经由非加密的HTTP代理服务器） |
| PATCH    | 用于将局部修改应用到资源                                  |

### 常见请求头

| User-Agent       | 客户端将他的操作系统、浏览器和其他属性告诉服务器         |
| :--------------- | -------------------------------------------------------- |
| Host             | 指定请求资源的主机地址和端口号，如Host:www.xxx.com       |
| Connection       | 当前连接是否保持，如Connection：Keep-Alive               |
| Accept-Language  | 指定一种自然语言，如Accept-Language:zh-cn                |
| Accept           | 浏览器可以接受的MIME类型                                 |
| Referer          | 包含一个URL，用户从该URL代表的页面发出访问当前请求的页面 |
| Cooike(身份令牌) | Cookie是一段文本，它常用来表示请求者身份                 |
| Range            | Range可以请求实体的部分内容，多线程下载器常用此请求      |
| x-forward-for    | XXF头，它代表请求端的IP，可以有多个，中间逗号隔开        |

## 响应报文

### 结构

- 响应行(状态行)
  - 状态码
  - 原因短语
  - HTTP版本
- 响应头
  - 通用首部字段
  - 响应首部字段
  - 实体首部字段
  - 其他
- 空白行
- 响应正文

```cmd
# 响应行
HTTP/1.1 200 OK
# 响应头(消息报头)
Date:Thu,28 Feb 2013 07:36:47 GMT     
Server:BWS/1.0
Content-Length:4199
Content-Type:text/html;charset=utf8
Cache-Control:private
Expries: Date:Thu,28 Feb 2013 07:36:47 GMT
Content-Encoding:gzip
set-Cookie:HPS_PSSID=2022_1438_1944_1788;path=/;
domain=.xxx.com
Connection:Keep-Alive
# 空白行代表响应头结束

# 响应正文(消息主体)
<html>                 
    <head><title>index.html</title></head>
</html>
```

### 常见状态码

| 200  | 客户端请求成功                       |
| :--: | ------------------------------------ |
| 302  | 临时跳转，跳转的地址通过Location指定 |
| 400  | 客户端请求语法错误，服务器无法识别   |
| 401  | 请求未经授权                         |
| 403  | 服务器收到请求，但拒绝提供服务       |
| 404  | 请求资源不存在                       |
| 500  | 服务器内部错误                       |

### 常见响应头

| Server         | WEB服务器名称，如Server：Apache/1.3.6(Unix)                  |
| -------------- | ------------------------------------------------------------ |
| Content-Type   | 用来指明发送给接收者的实体正文的媒体类型（服务器可接受的文本类型） |
| Content-Length | 指明实体正文长度                                             |
| Keep-Alive     | 保持连接时间                                                 |
| Date           | 格林尼治时间                                                 |
| Location       | 表示客户端应当到哪里去提取文档，当客户端接收到请求后会再次发送请求 |
| Set-Cookie     | 向客户端设置Cookie                                           |