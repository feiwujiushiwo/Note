# 浏览器与Web服务器

## 1、HTTP请求消息

#### 1.1、输入url

**URL：** （Uniform Resource Locator）统一资源定位符

**URL的几种格式：** 

```
#用http协议访问Web服务
http://user:password@www.example.com:80/dir/file.html

#用ftp协议下载或上传文件
ftp://user:passwrd@ftp.example.com:21/dir/file.html

#读取本地文件（使用file方法时不需要联网）
file://localhost/C:/path/file.html
```

http、ftp、file等字符表示浏览器使用的**访问方法**。

> **HTTP协议：** Hyper Text Transfer  Protocol，超文本传输协议。是一种应用层协议，用于在浏览器和Web服务器之间传输数据。HTTP提供了一种请求-响应模型，每个请求和响应都被视为独立的消息。浏览器发送请求（Request），Web服务器响应请求（Response）。HTTP协议的主要特点包括传输效率高、传输可靠性高、兼容性好和灵活性高。目前，HTTP协议已经更新到了第三个版本，即HTTP/2。
>
> **FTP协议：** File Transfer  Protocol，文件传输协议。是用于在网络上进行文件传输的一套标准协议，属于应用层协议。FTP协议包括两个组成部分，一是FTP客户端，二是FTP服务器。其中FTP服务器用来存储文件，用户可以使用FTP客户端通过FTP协议访问位于FTP服务器上的资源。

#### 1.2、解析url

`http` 

`//` 

`www.example.com` 

`/`

`dir`

`file.html`

#### 1.3、HTTP协议与HTTPS

HTTP 协议是 Hyper Text Transfer Protocol（超文本传输协议）的缩写，是用于从万维网（ WWW:World Wide Web ）服务器传输超文本到本地浏览器的传送协议。

HTTPS 协议是 HyperText Transfer Protocol Secure（超文本传输安全协议）的缩写，是一种通过计算机网络进行安全通信的传输协议。

HTTP 的 URL 是由 http:// 起始与默认使用端口 **80**，而 HTTPS 的 URL 则是由 https:// 起始与默认使用端口**443**。

**HTTP 三点注意事项：**

- HTTP 是无连接：无连接的含义是限制每次连接只处理一个请求，服务器处理完客户的请求，并收到客户的应答后，即断开连接，采用这种方式可以节省传输时间。
- HTTP 是媒体独立的：这意味着，只要客户端和服务器知道如何处理的数据内容，任何类型的数据都可以通过HTTP发送，客户端以及服务器指定使用适合的 MIME-type 内容类型。
- HTTP 是无状态：HTTP 协议是无状态协议，无状态是指协议对于事务处理没有记忆能力，缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大，另一方面，在服务器不需要先前信息时它的应答就较快。

> MIME-type，全称为Multipurpose Internet Mail  Extensions，译作“通用互联网邮件扩展类型”。它是一组用于描述内容类型的标准，这些内容类型可能包括文本、图像、视频、音频、应用程序等。MIME-type的主要目的是允许电子邮件应用程序知道如何处理邮件中的数据，以便正确地显示和播放邮件中的内容。
>
> MIME-type的命名规则通常为“类型/子类型”，例如“text/plain”、“image/jpeg”、“video/mp4”等。其中，“类型”部分分为三类：文本、图像和声音。而“子类型”则根据具体的内容类型来定义。
>
> 在HTML文件中，可以使用content-type属性来指定MIME-type。例如，如果一个文件是HTML文档，那么它的content-type应该是“text/html”。

#### 1.4、HTTP请求与响应

浏览器请求包：

```http
GET /hello.txt HTTP/1.1
User-Agent: curl/7.16.3 libcurl/7.16.3 OpenSSL/0.9.7l zlib/1.2.3
Host: www.example.com
Accept-Language: en, mi
```

Web服务器响应包：

```http
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
ETag: "34aa387-d-1568eb00"
Accept-Ranges: bytes
Content-Length: 51
Vary: Accept-Encoding
Content-Type: text/plain
```

输出结果：

```
Hello World! My payload includes a trailing CRLF.
```

## 2、DNS服务器

#### 2.1、什么是ip地址

IP地址（Internet Protocol Address，互联网协议地址）是用于标识和定位网络上设备的数字地址。它是计算机在网络上相互通信时使用的标识符。

IP地址用于识别网络中的设备（比如计算机、服务器、路由器等），就像是邮政地址可以用来寻找特定的物理位置一样。它允许设备彼此发送和接收数据包，并确保数据包能够正确地到达目的地。

IP地址通常由两个部分组成：网络地址和主机地址。IPv4（Internet Protocol version 4）是最常用的IP地址版本，由32位二进制数组成，通常以点分十进制的形式表示（如192.168.1.1）。IPv6（Internet Protocol version 6）是另一种IP地址版本，由128位二进制数组成，以更长的格式表示。

IP地址的不同部分可以确定设备所在的网络和设备在该网络中的具体位置。它们允许路由器将数据包转发到正确的目标，确保数据在网络中正确传递。

总的来说，IP地址是互联网上用于唯一标识和定位设备的一种地址系统，使得设备能够相互通信和交换数据。

#### 2.2、域名与ip地址

#### 2.3、Soket库

#### 2.4、解析器

## 3、通过协议栈发送消息

#### 3.1、数据收发概览

#### 3.2、套接字

#### 3.3、连接阶段

#### 3.4、会话阶段

#### 3.5、断开阶段