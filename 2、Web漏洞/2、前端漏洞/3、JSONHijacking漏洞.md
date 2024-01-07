# JSONHijacking漏洞

## 1、概述

JSON Hijacking也被叫JSONP（JSON with Padding）劫持，网站的某些接口为了方便跨域调用时就会采用JSONP传输数据，当该接口未进行CSRF防护时，可被其他域的JS跨域访问并读取数据，就可能会造成用户敏感信息泄露，是一种特殊的CSRF漏洞。

- 分类：Web通用漏洞
- 风险等级：中~低危

## 2、漏洞分析

### 2.1、同源策略

同源策略SOP（Same Origin Policy），该策略是浏览器的一个安全基石，可以保证数据的完整性和机密性。

假设没有同源策略，且你正好打开了两个网站，一个合法网站、一个恶意网站，这时恶意网站的脚本就能随意操作合法网站的任何可操作资源。

同源：

- 域名
- 协议
- TCP端口号

参考下面的例子来理解：

| URL                                         | 同源策略 | 原因                         |
| ------------------------------------------- | -------- | ---------------------------- |
| `http://www.example.com/dir/page.html`      | 自身     | 作为基准                     |
| `http://www.example.com/dir/another.html`   | 同源     | 满足上述同源要求             |
| `http://sub.example.com/dir2/page2.html`    | 不同源   | 域名不同                     |
| `https://www.example.com/dir/page.html`     | 不同源   | 协议不同 (`http` vs `https`) |
| `http://www.example.com:8080/dir/page.html` | 不同源   | 端口号不同 (`80` vs `8080`)  |

### 2.2、JSONP的介绍

**JSONP实现跨域请求的原理：** 动态创建`<script>`标签，然后利用`<script>`的src不受同源策略约束的特性来实现跨域获取数据。

**组成：** 

- 回调函数：响应到来时应该在页面中调用的函数（在src中设置）
- 数据：传入回调函数中的JSON数据

动态创建`script`标签，设置src，回调函数在src中设置：

```javascript
var script = document.createElement("script");
script.src = "https://api.douban.com/v2/book/search?q=javascript&count=1&callback=handleResponse";
document.body.insertBefore(script, document.body.firstChild);
```

通过回调函数对数据进行操作：

```javascript
function handleResponse(response){
    // 对response数据进行操作的代码
}
```

### 2.3、JSONP劫持原理



### 2.4、JSONP劫持利用



## 3、漏洞防御

1. 参考CSRF防御思路
2. 对JSONP接口暴露的信息进行防护