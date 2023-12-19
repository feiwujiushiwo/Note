# DNSlog外带

> 在进行盲注的时候，因为需要频繁发送访问请求，有的网站配置了waf就有可能让我们的注入受阻。所以用DNSlog外带可以减少请求并且直接回显数据。（非常好用）

## 0、一些必备知识

**DNS：**

DNS(Domain Name System)就是域名系统，负责把域名转换成IP地址；例如向浏览器访问a.com,浏览器就会将其解析成真实的IP访问对应服务器上的服务。

**DNSlog：**

就是DNS的日志，DNS在域名解析时会留下域名和解析ip的记录。

**DNSlog外带原理：**

DNS在解析的时候会留下日志，我们将信息放在高级域名中，传递到自己这里，然后通过读日志获取信息。

## 1、常用场景

sql盲注

无回显的命令执行

无回显的XSS

无回显的SSRF

Blind XXE

## 2、推荐的DNSlog平台

`http://ceye.io/` 

`http://www.dnslog.cn/` 

`http://eyes.sh/dns/`

