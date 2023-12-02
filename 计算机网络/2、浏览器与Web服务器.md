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

> 下面是以用http开头的url为例。

`http://` 这里是浏览器使用的访问方法

`www.example.com` 域名 

`/dir/file.html` 表示请求文件的路径

**目录结构示意图：** 

> `/`
>
> |——`index.html`
>
> |——`dir1`
>
> |		|——`file1.html`
>
> |		|——`file2.html`
>
> |——`dir2`
>
> |——`dir3`

**几种常见的url形式：** 

```
http://www.example.com
http://www.example.com/

http://www.example.com/dir1
http://www.example.com/dir1/

http://www.example.com/dir1/file1.html
```

> 拓展：http的大小写没有限制。

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

IP地址通常由两个部分组成：

- 网络号
- 主机号

IP地址的版本：

- IPv4（Internet Protocol version 4）是最常用的IP地址版本，长度32比特，地址总数为2^32个。通常以点分十进制的形式表示（如192.168.1.1）。
- IPv6（Internet Protocol version 6）是另一种IP地址版本，长度128比特，地址总数为2^128个。

IPv4地址的形式：

| 十进制形式  | 二进制形式                          |
| ----------- | ----------------------------------- |
| 192.168.1.1 | 11000000.10101000.00000001.00000001 |

| 十进制形式 | 二进制形式 |
| ---------- | ---------- |
| 192        | 11000000   |
| 168        | 10101000   |
| 1          | 00000001   |
| 1          | 00000001   |

IPv4地址的分类：

| 类别       | 开头限制 | 范围                        | 网络号 | 主机号 | 可分配地址数量 |
| ---------- | -------- | --------------------------- | ------ | ------ | -------------- |
| A类        | 0        | 1.0.0.0 - 126.0.0.0         | 8位    | 24位   | 126×16777214   |
| B类        | 10       | 128.0.0.0 - 191.255.0.0     | 16位   | 16位   | 16384×65534    |
| C类        | 110      | 192.0.0.0 - 223.255.255.0   | 24位   | 8位    | 2097152×254    |
| D类 (多播) | 1110     | 224.0.0.0 - 239.255.255.255 | -      | -      |                |
| E类        | 1111     | 240.0.0.0 - 255.255.255.255 | -      | -      |                |
| 回环地址   | -        | 127.0.0.0 - 127.255.255.255 | -      | -      |                |

> 注意：
>
> 上方的范围说的是网络地址的范围
>
> 计算数量时要注意开头限制并且去掉网络地址和广播地址

> Tips：
>
> 1、只有A、B、C类地址可以分配给网络中的主机或路由器的各个接口
>
> 2、主机号”全0“，是网络地址，不能分配
>
> 3、主机号”全1“，是广播地址，不能分配

#### 2.2、域名与ip地址

域名和IP地址之间存在映射关系。

- 域名是人类可读的网址
- IP地址是网络设备在互联网上的唯一标识。

> 每个域名对应一个或多个IP地址。这是因为一个域名可以指向多个服务器或服务器组成的集群，以提高性能、负载均衡或容错能力。而一个IP地址也可能对应多个域名，这通常是因为共享托管或虚拟主机服务。因此，域名和IP地址之间是一对多的映射关系。

当你在浏览器中键入一个域名（比如www.example.com）时

- 浏览器调用操作系统中的Socket库中的解析器
- 解析器向DNS服务器发送查询消息
- DNS服务器返回响应消息
- 解析器从响应消息中提取出IP地址并告诉浏览器
- 浏览器将IP地址和请求包一起交给操作系统
- 操作系统内部的协议栈发送消息给Web服务器
- ......
- 浏览器收到Web服务器的响应包并渲染