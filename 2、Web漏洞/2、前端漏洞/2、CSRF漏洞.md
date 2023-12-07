# CSRF漏洞

## 1、概述

### 1.1、定义

**跨站请求伪造**（Cross-Site Request Forgery）是一种网络安全漏洞，攻击者利用用户当前已经认证的会话来执行未经用户授权的操作。攻击者通过诱使受害者在其身份验证的网站上执行恶意操作来实施此攻击，从而绕过了目标网站对请求来源的验证机制。

> 网站的敏感操作缺乏二次校验或所依赖的参数可被预测。简单来说就是可以“一次性”完成。所谓“一次性”，是指该请求提交时的表单可被事先构造，经攻击者发送给受害用户，当受攻击的用户点击链接时，在不知情的情况下自动完成了对表单的提交。

**流程：** 

​	构造表单————>攻击者发给受害者————>受害者点击链接————>表单成功提交————>受害者信息泄露

> 对于GET类型的请求，可直接构造恶意链接。若请求是POST类型，则可以采用<font color=red>JS自动提交</font>，受害者仍然是打开一个链接就会执行。

```html
<!DOCTYPE html>
<html>
<head>
  <title>CSRF Attack Example</title>
</head>
<body>

  <!-- 正常站点上的表单 -->
  <form id="myForm" action="https://example.com/transfer" method="POST">
    <input type="hidden" name="amount" value="1000000">
    <input type="submit" value="Transfer">
  </form>

  <script>
    // JavaScript 代码，当页面加载后自动提交表单
    document.addEventListener('DOMContentLoaded', function() {
      document.getElementById('myForm').submit(); // 提交表单
    });
  </script>

</body>
</html>
```

### 1.2、风险等级及危害

​	中~低危

## 2、漏洞原理

- **已认证会话利用**：攻击者利用用户当前已在浏览器中认证过的会话信息。
- **潜在攻击路径**：受害者已经登录到目标网站，并在另一个标签页中访问了恶意网站，导致恶意网站的请求发送到目标网站。
- **缺乏防护措施**：目标网站未对请求来源进行验证，无法确认请求是否来自合法来源。

## 3、漏洞防御

1. **CSRF Token**：引入一次性令牌（CSRF Token）用于验证请求来源的合法性。
2. **验证码：** 类似Token
3. **同源策略**：浏览器实施同源策略以防止跨域请求。
4. **Referer 检查**：检查Refer是否来自本站。
5. **双重提交 cookie**：设置一个随机值的 cookie，并将其作为表单字段发送，服务器确认收到的两个值是否匹配。

## 4、例子

假设用户在网银上登录并且已经认证。攻击者知道网银系统中转账的 URL 是 `/transfer`，并且该网站没有 CSRF 防护措施。攻击者在恶意站点上放置以下代码：

```html
html
<img src="http://bank.com/transfer?to=attacker&amount=1000&submit=Transfer" style="display:none;" />
```

攻击者通过诱导受害者访问恶意站点，在后台发送一笔 1000 元的转账请求到攻击者账户，因为用户已经在银行系统登录，因此请求会带着用户的认证信息执行。

## 5、参考资源

- [OWASP CSRF](https://owasp.org/www-community/attacks/csrf)
- [CSRF Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
