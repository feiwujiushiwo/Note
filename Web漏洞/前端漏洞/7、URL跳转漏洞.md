# url任意跳转漏洞

服务端未对传入的跳转url  变量进行检查和控制，可能导致构造任意一个恶意地址，诱导用户跳转到恶意网站。由于是从可信的站点跳转出去的，用户会比较信任，所以跳转漏洞一般用于钓鱼攻击，通过转到恶意网站欺骗用户输入用户名和密码盗取用户信息，或欺骗用户进行金钱交易；还可以造成xss 漏洞。

## 攻击方式及危害

url跳转中最常见的跳转在登陆口，支付口，也有跳转目录的同样可以跳转URL。借助URL  跳转，也可以突破常见的基于“白名单方式”的一些安全限制，如传统IM 里对于URL 的传播会进行安全校验，但是对于大公司的域名或URL  将直接允许通过并且显示会可信的URL，而一旦该URL  里包含一些跳转漏洞将可能导致安全限制被绕过。如果引用一些资源的限制是依赖于“白名单方式”，同样可能被绕过导致安全风险，如，常见的一些应用允许引入可信站点，如baidu.com 的视频，限制方式往往是检查URL 是否是baidu.com 来实现，如果baidu.com 内含一个url  跳转漏洞，将导致最终引入的资源属于不可信的第三方资源或者恶意站点，最终导致安全问题。

## 漏洞检测

修改参数中的合法URL 为非法URL，然后查看是否能正常跳转或者响应包是否包含了任意的构造URL

- 常见参数：
   redirect
   url
   redirectUrl
   callback
   return_url
   toUrl
   ReturnUrl
   fromUrl
   redUrl
   request
   redirect_to
   redirect_url
   jump
   jump_to
   target
   to
   goto
   link
   linkto
   domain
   oauth_callback

## 绕过

例子：
 存在URL重定向漏洞的网站`http://www.aaa.com/acb`
 `login.aaa.com`是一个子域名
 `http://test.com`是重定向的目标网站

- `@`绕过
   用这方法在火狐里进行跳转，会有弹窗提示，在其它游览器则没有
   `http://www.aaa.com/acb?Url=http://login.aaa.com@test.com`//ssrf也可用
   后面的test.com 就是要跳转到的域名，前面的域名都是用来辅助以绕过限制的
- `?`绕过
   `http://www.aaa.com/acb?Url=http://test.com?login.aaa.com`
   `?`放到你添加的想要跳转的域名的后面，
- `#`绕过
   `http://www.aaa.com/acb?Url=http://test.com#login.aaa.com`
- `/`绕过
   `http://www.aaa.com/acb?Url=http://test.com/login.aaa.com`
   正斜杠前面跟上你想跳转的域名地址
- `\`绕过
  - 两个反斜杠绕过方法
     `http://www.aaa.com/acb?Url=http://test.com\\login.aaa.com`
     两个反斜杠前面跟上你想跳转的域名地址
  - 一个反斜杠绕过方法
     `http://www.aaa.com/acb?Url=http://test.com\login.aaa.com`
  - 一个反斜杠一个点
     `http://www.aaa.com/acb?Url=http://test.com\.login.aaa.com`
- 利用白名单缺陷绕过限制
   有的域名白名单限制是不全的，比如如果想利用一个跳转，而这个跳转是通用，在这个公司网站很多子域名等都可以跳转，那么你买个域名也不算贵对吧，为什么这么说呢，这个问题就是白名单限制不当，比如，当跳转的域名包含这个网站下的所有域名，
   比如：`http://www.aaa.com/acb?Url=http://login.aaa.com`
   这个login.aaa.com 也可以改成aaa.com 同样可以跳转对吧，因为白名单里只要有包含这个域名就直接成功跳转。那么当我在这个域名前面加上如`testaaa.com`，白名单里会检查是否包含aaa.com 这个域名，包含，然后直接跳转，而并没有检查这个域名的整个信息，然后可以利用这个问题，直接注册一个`testaaa.com` 这个域名就可以利用这个跳转。
- 多重验证&跳转绕过限制
   比如你登陆账户后会出现另一个验证页面，输入手机验证码进行验证，此时这上面的URL 很可能存在任意跳转的问题。多重跳转的问题导致可绕过URL 限制
   如`http://www.aaa.com/acb?Url=http://login.aaa.com/acb?url=http://login.aaa.com`
   这个结构的多重跳转你修改最后面的URL 就可以达到任意URL 跳转，中间的URL 就没必要动了。
- 点击触发达到绕过URL 跳转限制
   比如很多登陆页面的地方，其URL 是一个跳转的URL，
   如：`http://www.aaa.com/acb?Url=http://test.com`
   你直接修改了后面为任意URL，但是还是停留在原地，似乎没什么问题，但是，当你输入账号和密码后点击登陆按钮后，就会触发跳转，当然，这个账户和密码不一定要对的，随便都可以，但得视系统而定吧。
- xip.io绕过
   `http://127.0.0.1/url.php?username=1&password=1&redict=http://www.xiaozhupeiqi.com.220.181.57.217.xip.io`会跳转到百度
- 白名单网站可信
   如果url跳转点信任百度url，google url或者其他，则可以多次跳转达到自己的恶意界面。
- 协议绕过
   http与https协议转换尝试，或者省略
   `http://127.0.0.1/url.php?username=1&password=1&redict=//www.xiaozhupeiqi.com@www.baidu.com`
   `http://127.0.0.1/url.php?username=1&password=1&redict=////www.xiaozhupeiqi.com@www.baidu.com`//多斜线
- xss跳转

```
<meta  content="1;url=http://www.baidu.com" http-equiv="refresh">
```