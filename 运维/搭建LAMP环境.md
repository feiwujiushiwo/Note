# 搭建LAMP环境

+++

## 1,准备

+++

+ 操作系统 : centos7

+++

## 2,过程

+++

+ 安装apache及其扩展包

```
yum -y install httpd httpd-manual mod_ssl mod_perl
```

+ 启动apache

```
systemctl start httpd.service
```

+ 改一下默认端口(记得开放防火墙和那个端口的http服务)

+ 访问虚拟机IP测试结果 192.168.232.131:82

![image-20231104160623698](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104160623698.png)

+++

+ 安装mysql数据库

```
wget http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql57-community-release-el7-10.noarch.rpm
yum -y install mysql-community-server --nogpgcheck
```

+ 启动mysql

```
systemctl start mysqld.service
```

+ 登录mysql

```
systemctl status mysqld.service
grep "password" /var/log/mysqld.log
mysql -uroot -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'NewPassWord1.';
```

+ 添加数据库

```
create database <数据库>; 
show databases;
```

+ ctl+d退出

+++

+ 安装php

```
yum -y install php php-mysql gd php-gd gd-devel php-xml php-common php-mbstring php-ldap php-pear php-xmlrpc php-imap
```

+ 创建php页面

```
echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
```

+++

## 3,测试结果

+++

输入192.168.232.131:82/phpinfo.php

![image-20231104160836907](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104160836907.png)

成功访问

+++

+++

# docker搭建

+++

## 1,准备

+++

+ 我的docker是之前装好的

+++

## 2,过程

+ 拉取lamp镜像

```
docker pull linode/lamp
```

+ 运行容器并映射端口和磁盘

```
docker run -d -v/var/www/html:/var/www/example.com/public_html -p89:80 -p3307:3306 linode/lamp /bin/bash -c "while true ; do sleep 1;done"
```

![image-20231105164634697](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231105164634697.png)

可以看到已完成挂载

+ 把主机的89端口添加到http服务上(不确定,但是我做了)

```
semanage port -a -t http_port_t -p tcp 89
```

+ 进入容器启动apache2

```
# 进入容器
docker exec -it 255158decb6b /bin/bash
# 启动apache
service apache2 start 
```

+ 退出容器

+++

## 3,测试结果

+++

访问192.168.232.131:89

![屏幕截图 2023-11-04 155814](C:\Users\Lenovo\Pictures\Screenshots\屏幕截图 2023-11-04 155814.png)

+ 访问192.168.232.131:89/phpinfo.php

![image-20231105173230072](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231105173230072.png)

成功访问

+++

+++

# Burpsuite

+++

## 1,proxy

+ 配置https，之前完成的。
+ 拦截返回包

![屏幕截图 2023-11-04 154910](C:\Users\Lenovo\Pictures\Screenshots\屏幕截图 2023-11-04 154910.png)

+ 查看拦截记录

![image-20231104162856817](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104162856817.png)

+ 设置编码字符集

![image-20231104163136226](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104163136226.png)

+++

## 2，repeater

+ 切换http1和http2

![屏幕截图 2023-11-04 163813](C:\Users\Lenovo\Pictures\Screenshots\屏幕截图 2023-11-04 163813.png)

![屏幕截图 2023-11-04 163817](C:\Users\Lenovo\Pictures\Screenshots\屏幕截图 2023-11-04 163817.png)

+ update content-length

![image-20231104165215465](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104165215465.png)

![image-20231104165522580](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104165522580.png)

+ 设置不能自动替换行

![image-20231104165910729](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104165910729.png)

![屏幕截图 2023-11-04 165853](C:\Users\Lenovo\Pictures\Screenshots\屏幕截图 2023-11-04 165853.png)

+++

## 3，intruder

+ 字典/数字遍历

![image-20231104171609792](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104171609792.png)

![image-20231104175020000](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231104175020000.png)

+++

# SQL注入

+++

## 1，环境配置

+++

+++

## 2，注入

+++

+ 参考文档
+ 复制他的脚本到记事本，保存命名为1.php并放到phpstudy的www目录下，打开phpstudy，启动wamp，然后访问127.0.0.1/1.php。访问结果如下：

![image-20231105170936375](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231105170936375.png)

+ 复制2.x的POC。

+ 打开测试界面192.168.232.131:5005/user.php：

![image-20231105171318717](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231105171318717.png)

+ 用burp抓取当前页面的请求包

![image-20231105171614102](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231105171614102.png)

+ 发送到repeater模块并添加POC到Referer，然后点击send

![image-20231105171853962](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231105171853962.png)

+ 获得phpinfo页面