# centos7搭建gitlab

## 1、gitlab介绍

​	GitLab一个开源的git仓库管理平台，方便团队协作开发、管理。在GitLab上可以实现完整的CI（持续集成）、CD（持续发布）流程。而且还提供了免费使用的Plan，以及免费的可以独立部署的社区版本(https://gitlab.com/gitlab-org/gitlab-ce )。

## 2、环境信息

+ #### 服务器：

  > 操作系统：centos7
  >
  > 硬件信息：2核2G
  >
  > IP：47.115.212.50

+ #### 软件

  > gitlab社区版11.1.4

## 3、准备工作

+ #### 安装基础依赖

`sudo yum install -y curl policycoreutils-python openssh-server`

`sudo systemctl enable sshd`

`sudo systemctl start sshd`

+ #### 安装Postfix

`sudo yum install -y postfix`

`sudo systemctl enable postfix`

`sudo systemctl start postfix`

+ #### 开放ssh以及http服务（80端口）

`sudo firewall-cmd --add-service=ssh --permanent`

`sudo firewall-cmd --add-service=http --permanent`

`sudo firewall-cmd --reload`

## 4、部署

+ #### 安装gitlab

`curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash`

`sudo yum install -y gitlab-ce`

`sudo vi /etc/gitlab/gitlab.rb` //修改配置文件（大概在第十几行）

> ```
> external_url 'http://ip'
> ```

+ #### 启动gitlab

`sudo gitlab-ctl reconfigure`

+ #### 访问gitlab

输入ip加端口访问