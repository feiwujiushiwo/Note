

# Docker入门指南（上）

## 第一部分：概述

### 0、通过这个教程我们能学到什么

+ 构建镜像并运行容器
+ ~~使用Docker Hub共享镜像~~
+ 使用带有数据库的多个容器来部署 Docker 应用程序。
+ 使用Dockers-compose

### 1、什么是镜像？

运行的容器需要使用隔离的文件系统。这个独立的文件系统由镜像提供，并且镜像必须包含运行应用程序所需的所有内容 - 所有依赖项、配置、脚本、二进制文件等。

镜像还包含容器的其他配置，例如环境变量、要运行的默认命令以及其他元数据。

### 2、什么是容器？

容器是在主机上运行的沙盒进程，与该主机上运行的所有其他进程隔离。这种隔离利用了kernel namespaces and cgroups，这些功能在 Linux 中已经存在很长时间了。 Docker 使这些功能变得易于使用。总而言之，容器：

- 是镜像的**可运行实例**。我们可以通过docker创建、启动、停止、移动或删除容器。
- 可以在本地机、虚拟机上运行，也可以部署到云端。
- 是可移植的（并且可以在任何操作系统上运行）。
- 与其他容器隔离并运行自己的软件、二进制文件、配置等。

> Tips：
>
> ​	可以联想一下VMware，镜像就是虚拟机的iso文件，容器就是我们启动的虚拟机。

## 第二部分：常用命令汇总

### 1、对镜像：

```bash
#在docker hub中搜索镜像
docker search <image>
#从docker hub中拉取镜像
docker pull <iamge:tag> 
#这个命令可以列出所有你已经下载的镜像，如果想单独查看你下载的某个镜像，可以在后面加镜像名
docker images  
#查看某个镜像的构建历史
docker history <image:tag>
#给镜像打一个新标签
docker tag <old image:tag> <new image:tag> 
#把镜像推送到仓库
docker push <image:tag>
#删除镜像
docker rmi <iamge:tag> 
```

### 2、对容器：

```bash
#列出容器
docker ps
#创建容器
docker creat 
#根据镜像启动容器
docker run
#暂停容器
docker pause
#取消暂停
docker unpause
#停止容器，再次启动之后进程号（pid）不变
docker stop
#立即停止容器，再次启动之后进程号（pid）会发生改变
docker kill
#启动容器
docker start
#重启容器
docker restart
#连接上正在运行的容器
docker attach
#进入容器，加上“-it”之后可以创造一个伪终端
docker exec -it
#删除容器（提示：只有stoped状态的容器才能被删除）
docker rm
#主机和容器之间复制文件
docker cp
#把修改后的容器导出为镜像
docker commit
```

### 3、其他：

```bash
#查看详细信息
docker inspect
#查看日志
docker logs
#登录镜像仓库，可用-u指定用户 –p指定密码
docker login
#上传镜像到仓库
docker push
```

**注意：** 有时需要在命令后面加些选项，用到的时候可以自己去查，例如：

 `docker ps` 常用选项：

- `-a` 或 `--all`：显示所有容器，包括运行中的和已停止的。
- `-q` 或 `--quiet`：仅显示容器 ID，适合用于其他命令的输入。
- `-s` 或 `--size`：显示每个容器的磁盘使用情况。
- `-f` 或 `--filter`：根据指定的条件过滤显示容器。

`docker run` 常用选项：

- `-d` 或 `--detach`：以后台模式运行容器。
- `-p` 或 `--publish`：指定端口映射，将主机端口映射到容器内部端口。
- `-v` 或 `--volume`：挂载主机文件系统上的目录或文件到容器内部。
- `--name`：为容器指定名称。
- `-e` 或 `--env`：设置环境变量。
- `-it`：以交互模式运行容器，并分配一个伪终端。

## 第三部分：容器化应用程序

接下来我们将使用在 Node.js 上运行的简单待办事项列表管理器。如果不熟悉 Node.js，不用担心。下面的操作不需要任何 JavaScript 经验。

### 0、准备条件

- 安装了Docker
- 安装了Git
- ~~安装了一个文本编辑器，推荐VScode~~，在linux里用vim就行

### 1、下载这个app

```bash
git clone https://github.com/docker/getting-started-app.git
```

查看我们下载的这个文件的子目录

```bash
#进入文件目录
cd getting-started-app
#查看目录
ls
```

应该可以看到下面的内容：

![image-20231108144434922](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108144434922.png)

### 2、构建应用程序的镜像

这里我们用Dockerfile来构建镜像，不会也没关系，跟着敲一下就可以。

> Note：
>
> Dockerfile 只是一个基于文本的文件，没有文件扩展名，但包含指令脚本。 Docker 可以使用此脚本构建容器镜像。

在getting-started-app目录下创建一个Dockerfile文件

```bash
touch Dockerfile
```

![image-20231108171929375](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108171929375.png)

编写Dockerfile文件

```bash
vim Dockerfile
```

+ 输入下面的内容

+ ```dockerfile
  FROM node:18-alpine
  WORKDIR /app
  COPY . .
  RUN yarn install --production
  CMD ["node", "src/index.js"]
  EXPOSE 3000
  ```

> ```dockerfile
> FROM node:18-alpine
> 
> # 设置工作目录
> WORKDIR /app
> 
> # 复制当前目录下的所有文件到工作目录
> COPY . .
> 
> # 安装 Node.js 依赖项
> RUN yarn install --frozen-lockfile
> # 或者，如果您使用 npm：
> # RUN npm install --production
> 
> # 设置启动命令
> CMD ["node", "src/index.js"]
> 
> # 暴露端口
> EXPOSE 3000
> ```

> Note：
>
> FROM    ----------待补充

构建镜像

```bash
docker build -t getting-started .
```

> Note：
>
> docker build 是根据Dockerfile构建镜像的命令
>
> -t 参数用来给镜像命名
>
>  .  用来告诉Docker在当前目录下寻找Dockerfile构建镜像//当然，你也可以把他换成你的Dockerfile所在 的路径。

查看镜像

```bash
docker images
```

### 3、创建并运行容器

> tips：下面我们直接用来docker run命令来完成容器的创建和启动，而不是先用docker create创建容器，然后再用docker start启动容器。原因是与docker run命令相比，docker create命令更常用于在启动容器之前进行必要的设置。这里我们用docker run更快一点。

使用 `docker run` 命令运行容器并指定刚刚创建的镜像的名称：

```bash
docker run -d -p 3000:3000 getting-started
```

> Note：
>
> -d （--detach的缩写）告诉docker daemon要在后台运行容器，如果不知道为什么要加它的话，你们可以尝试一下不加-d参数运行一下docker run命令。
>
> -p （--publish的缩写）告诉docker daemon要在主机和容器之间创建一个端口映射。格式为`主机端口:容器端口`，如果没有这一步，你没办法从外部访问容器。当然，前面的主机端口可以随便选一个（目前没有被占用的端口），例如我可以改成3001：3000 or 3002：3000 ........
>
> tips: 1、其实-d -p 可以写成 -dp。
>
> ​		2、使用docker run命令时，如果我们本地没有命令里提到的镜像，dacker会自动从docker hub中拉去一个镜像并启动容器。

尝试从我们的浏览器上访问我们的应用程序

- 输入ip+端口号

  ```
  192.168.232.131:3000
  ```

  > tips：这里的冒号要用英文标点，ip是你的虚拟机ip。如果访问不到，关闭防火墙再试一下。
  >
  > ```bash
  > systemctl stop firewalld.service# 关闭防火墙
  > systemctl status firewalld.service# 检查防火墙状态
  > systemctl disable firewalld.service# 永久关闭防火墙（可选可不选）
  > ```
  
- 访问成功会看到下图界面

- ![image-20231108184651036](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108184651036.png)

添加一两个item，看看它是否按我们的预期工作。我们可以将项目标记为完成并将其删除。同时我们的前端会将项目存储在后端。

### 4、查看容器

输入下面的命令查看正在运行的容器

```bash
docker ps
```

> tips：他还有一个进阶版命令，`docker ps -a`，这个命令可以查看所有容器（包括没有运行的）

![image-20231108185838483](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108185838483.png)

### 5、总结

简单总结一下，到现在我们了解了怎么通过Dockerfile来创建镜像，并学习了如何根据我们的镜像启动一个容器。

## 第四部分：更新应用程序

在这个部分中，我们要更新应用程序和镜像。还要学习如何停止和删除容器。

### 1、更新源代码

进入app.js这个文件所在的目录，并更改app.js文件的第56行。

```bash
cd src/static/js
```

```bash
vim app.js
```

![image-20231108195708209](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108195708209.png)

- 把这里改成“你还没有待办事项”

- ```js
  - <p className="text-center">No items yet! Add one above!</p>
  + <p className="text-center">你还没有待办事项！添加一个吧！!</p>
  ```

- 改完之后按esc，然后在**英文模式**下输入 按 shift＋：，输入wq。#保存并退出

返回最getting-started-app目录构建镜像的新版本

```bash
cd ../../../
```

```bash
docker build -t getting-started .
```

启动新的容器

```bash
docker run -dp 3000:3000 getting-started
```

> 这时你会看到一个报错：
>
> docker: Error response from daemon: driver failed programming external connectivity on endpoint laughing_burnell 
> (bb242b2ca4d67eba76e79474fb36bb5125708ebdabd7f45c8eaf16caaabde9dd): Bind for 127.0.0.1:3000 failed: port is already allocated.
>
> 这是因为我们主机的3000端口已经被占用。解决办法有两个
>
> 1、把-dp 3000:3000换成 -dp 3001:3000
>
> 2、删除旧的容器（占用了3000端口的那个）
>
> 我们在这里采用第二种解决方法（旧的容器留着也没用）

### 2、删除旧容器

首先我们要得到我们要删除容器的id，可以使用docker ps命令

```bash
docker ps
```

然后我们需要停止这个正在运行的容器

```bash
docker stop <id>
```

最后我们用docker rm命令删除这个容器

```bash
docker rm <id>
```

### 3、启动更新后的容器

这时候我们启动新的容器就没有问题了，再次输入启动命令

```bash
docker run -dp 3000:3000 getting-started
```

不出意外可以启动成功，这时我们再去浏览器上访问我们的应用程序

```
<ip>:3000 #例如 192.168.232.131:3000
```

![image-20231108232845617](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108232845617.png)

可以看到，我们已经成功更新了我们的应用程序！

### 4、总结

到这里，我们已经学习了如何更新和重建容器，以及如何停止和删除容器。

## 第五部分：分享应用程序

现在我们已经构建了图像，可以选择去分享它。要分享 Docker 镜像，必须使用 Docker registry。默认registry是 Docker Hub，~~我们使用的所有镜像都来自于此~~。

> Docker ID 可以让我们访问 Docker Hub，这是世界上最大的容器镜像库和社区。（好像需要翻墙）

### 1、创建储存库

要推送镜像，首先需要在 镜像库上创建一个存储库。

PASS
