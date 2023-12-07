# URL任意跳转漏洞

### 1.1、定义

服务端未对传入的跳转url  变量进行检查和控制，可能导致构造任意一个恶意地址，诱导用户跳转到恶意网站。由于是从可信的站点跳转出去的，用户会比较信任，所以跳转漏洞一般用于钓鱼攻击，通过转到恶意网站欺骗用户输入用户名和密码盗取用户信息，或欺骗用户进行金钱交易；还可以造成xss 漏洞。

## 攻击方式及危害

url跳转中最常见的跳转在登陆口，支付口，也有跳转目录的同样可以跳转URL。借助URL  跳转，也可以突破常见的基于“白名单方式”的一些安全限制，如传统IM 里对于URL 的传播会进行安全校验，但是对于大公司的域名或URL  将直接允许通过并且显示会可信的URL，而一旦该URL  里包含一些跳转漏洞将可能导致安全限制被绕过。如果引用一些资源的限制是依赖于“白名单方式”，同样可能被绕过导致安全风险，如，常见的一些应用允许引入可信站点，如baidu.com 的视频，限制方式往往是检查URL 是否是baidu.com 来实现，如果baidu.com 内含一个url  跳转漏洞，将导致最终引入的资源属于不可信的第三方资源或者恶意站点，最终导致安全问题。

## 漏洞检测

修改参数中的合法URL 为非法URL，然后查看是否能正常跳转或者响应包是否包含了任意的构造URL

| 参数           | 描述            |
| -------------- | --------------- |
| redirect       | 重定向链接      |
| url            | URL 地址        |
| redirectUrl    | 重定向 URL 地址 |
| callback       | 回调链接        |
| return_url     | 返回 URL 地址   |
| toUrl          | 到达 URL 地址   |
| ReturnUrl      | 返回 URL 地址   |
| fromUrl        | 来源 URL 地址   |
| redUrl         | 红色 URL 地址   |
| request        | 请求链接        |
| redirect_to    | 重定向到        |
| redirect_url   | 重定向 URL 地址 |
| jump           | 跳转链接        |
| jump_to        | 跳转到链接      |
| target         | 目标链接        |
| to             | 到链接          |
| goto           | 转至链接        |
| link           | 链接            |
| linkto         | 链接到链接      |
| domain         | 域名            |
| oauth_callback | OAuth 回调链接  |

## 绕过

### 例子：存在URL重定向漏洞的网站

- 绕过方式
  - `@`绕过：`http://www.aaa.com/acbUrl=http://login.aaa.com@test.com`，在火狐浏览器中会有弹窗提示，其他浏览器没有。
  - `?`绕过：`http://www.aaa.com/acb?Url=http://test.com?login.aaa.com`
  - `#`绕过：`http://www.aaa.com/acb?Url=http://test.com#login.aaa.com`
  - `/`绕过：`http://www.aaa.com/acb?Url=http://test.com/login.aaa.com`
  - `\`绕过（两个反斜杠）：`http://www.aaa.com/acb?Url=http://test.com\\login.aaa.com`
  - `\`绕过（一个反斜杠）：`http://www.aaa.com/acb?Url=http://test.com\login.aaa.com`
  - `\`绕过（一个反斜杠 + 一个点）：`http://www.aaa.com/acb?Url=http://test.com\.login.aaa.com`
- 利用白名单缺陷绕过限制
  - `http://www.aaa.com/acb?Url=http://login.aaa.com`：利用白名单限制不全的漏洞。
- 多重验证&跳转绕过限制
  - `http://www.aaa.com/acb?Url=http://login.aaa.com/acb?url=http://login.aaa.com`：利用多重跳转绕过限制。
- 点击触发达到绕过URL 跳转限制
  - `http://www.aaa.com/acb?Url=http://test.com`：通过点击触发跳转绕过限制。
- xip.io绕过
  - `http://127.0.0.1/url.php?username=1&password=1&redict=http://www.xiaozhupeiqi.com.220.181.57.217.xip.io`：会跳转到百度。
- 白名单网站可信
  - 如果URL跳转点信任百度URL、Google URL或其他，则可以多次跳转达到自己的恶意界面。
- 协议绕过
  - `http://127.0.0.1/url.php?username=1&password=1&redict=//www.xiaozhupeiqi.com@www.baidu.com`：尝试协议转换绕过。