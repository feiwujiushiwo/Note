# Docke安装

## 1、Centos7

```bash
yum -y update

yum remove docker  docker-common docker-selinux docker-engine#之前安装过就敲一下这个命令

yum install -y yum-utils device-mapper-persistent-data lvm2

yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

yum list docker-ce --showduplicates | sort -r

yum -y install docker-ce-24.0.7-1.el7

systemctl start docker

systemctl enable docker
```

## 2、基础工具准备

```bash
yum -y install unzip git wget vim#暂时就这些
```

## 3、关于镜像源

​	默认的Docker Hub拉取镜像可能会很慢，可以考虑换下源或者用阿里的镜像加速服务（翻墙也行），下面我放了镜像加速的链接

[容器镜像服务](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

- 下面是参考例子，直接复制粘贴行不通的。具体的命令在上面的链接里面找。（搞不起的可以去网上找个教程）

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://co74gfqm.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 4、docker-compose

[Docker Compose教程](https://www.runoob.com/docker/docker-compose.html)

下载慢的话我这里可以选择国内的一些链接，或者我这里有可执行文件，找我要然后拖到对应的目录下。
