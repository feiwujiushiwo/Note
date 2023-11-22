# 一、什么是Docker？

## Docker概述

Docker 是一个用于开发、发布和运行应用程序的开放平台。 Docker 使您能够将应用程序与基础设施分离，以便您可以快速交付软件。

借助 Docker，您可以像管理应用程序一样管理基础设施。通过利用 Docker 的方法来传送、测试和部署代码，您可以显着减少编写代码和在生产中运行代码之间的延迟。

## Docker平台

Docker 提供了在称为容器的隔离环境中打包和运行应用程序的能力。隔离和安全性使您可以在给定主机上同时运行多个容器。

容器是轻量级的，包含运行应用程序所需的一切，因此您不需要依赖主机上安装的内容。您可以在工作时共享容器，并确保与您共享的每个人都获得以相同方式工作的相同容器。

Docker 提供了工具和平台来管理容器的生命周期：

+ 使用容器开发您的应用程序及其支持组件。
+ 容器成为分发和测试应用程序的单元。
+ 准备就绪后，将应用程序作为容器或编排服务部署到生产环境中。无论您的生产环境是本地数据中心、云提供商还是两者的混合，这都是一样的。

## 我们可以用Docker做什么？

### 快速、一致地交付应用程序

Docker 允许开发人员使用提供应用程序和服务的本地容器在标准化环境中工作，从而简化了开发生命周期。容器非常适合持续集成和持续交付 (CI/CD) 工作流程。

考虑以下示例场景：

+ 您的开发人员在本地编写代码并使用 Docker 容器与同事共享他们的工作。
+ 他们使用 Docker 将应用程序推送到测试环境中并运行自动和手动测试。
+ 当开发人员发现错误时，可以在开发环境中修复它们，并将其重新部署到测试环境中进行测试和验证。
+ 测试完成后，向客户提供修复就像将更新的映像推送到生产环境一样简单。

### 响应式部署和扩展

Docker 基于容器的平台支持高度可移植的工作负载。 Docker 容器可以在开发人员的本地笔记本电脑、数据中心的物理机或虚拟机、云提供商或混合环境中运行。

Docker 的可移植性和轻量级特性还使得动态管理工作负载、根据业务需求几乎实时地扩展或拆除应用程序和服务变得很容易。

### 在相同硬件上运行更多工作负载

Docker 轻量且快速。它为基于虚拟机管理程序的虚拟机提供了可行且经济高效的替代方案，因此您可以利用更多服务器容量来实现业务目标。

Docker 非常适合高密度环境以及需要用更少的资源做更多事情的中小型部署。

## Docker架构

Docker 使用客户端-服务器架构。 Docker 客户端与 Docker 守护进程通信，后者负责构建、运行和分发 Docker 容器的繁重工作。

Docker 客户端和守护进程可以在同一系统上运行，也可以将 Docker 客户端连接到远程 Docker 守护进程。 Docker 客户端和守护进程使用 REST API 通过 UNIX 套接字或网络接口进行通信。

另一个 Docker 客户端是 Docker Compose，它允许您使用由一组容器组成的应用程序。

<img src="C:\Users\Lenovo\Desktop\Docker\图片\docker-architecture.png" alt="docker-architecture" style="zoom:67%;" />

### Docker daemon

Docker 守护进程 ( `dockerd` ) 侦听 Docker API 请求并管理 Docker 对象，例如映像、容器、网络和卷。守护进程还可以与其他守护进程通信来管理 Docker 服务。

### Docker client

Docker 客户端 ( `docker` ) 是许多 Docker 用户与 Docker 交互的主要方式。当您使用 `docker run` 等命令时，客户端会将这些命令发送到 `dockerd` ，由后者执行这些命令。 `docker` 命令使用 Docker API。 Docker 客户端可以与多个守护进程通信。

### Docker desktop

Docker Desktop 是一款适用于 Mac、Windows 或 Linux 环境的易于安装的应用程序，使您能够构建和共享容器化应用程序和微服务。 Docker Desktop 包括 Docker 守护进程 ( `dockerd` )、Docker 客户端 ( `docker` )、Docker Compose、Docker Content Trust、Kubernetes 和 Credential Helper。有关更多信息，请参阅 Docker 桌面。

### Docker registries

Docker registries存储 Docker 镜像。 Docker Hub 是任何人都可以使用的公共registries，Docker 默认在 Docker Hub 上查找镜像。您甚至可以运行自己的私人registries。

当您使用 `docker pull` 或 `docker run` 命令时，Docker 会从您配置的registries中提取所需的映像。当您使用 `docker push` 命令时，Docker 会将您的映像推送到您配置的registries。

### Docker objects

当您使用 Docker 时，您正在创建和使用映像、容器、网络、卷、插件和其他对象。本节简要概述其中一些对象。

- 镜像
- 容器

## 底层技术

Docker 采用 Go 语言编写，并利用 Linux 内核的多个特性来提供其功能。 Docker 使用一种名为 `namespaces` 的技术来提供称为容器的隔离工作空间。当您运行容器时，Docker 会为该容器创建一组命名空间。

这些命名空间提供了一层隔离。容器的每个方面都在单独的命名空间中运行，并且其访问仅限于该命名空间。

# 二、安装Docker

PASS

# 三、让我们开始学习Docker吧

## 第一部分：概述

### 0、通过这个教程我们能学到什么

+ 构建镜像并运行容器
+ 使用Docker Hub共享镜像
+ 使用带有数据库的多个容器来部署 Docker 应用程序。
+ 使用Dockers-compose

### 1、什么是镜像？

运行的容器需要使用隔离的文件系统。这个独立的文件系统由镜像提供，并且镜像必须包含运行应用程序所需的所有内容 - 所有依赖项、配置、脚本、二进制文件等。

镜像还包含容器的其他配置，例如环境变量、要运行的默认命令以及其他元数据。

### 2、什么是容器？

容器是在主机上运行的沙盒进程，与该主机上运行的所有其他进程隔离。这种隔离利用了kernel namespaces and cgroups，这些功能在 Linux 中已经存在很长时间了。 Docker 使这些功能变得易于使用。总而言之，容器：

- 是镜像的可运行实例。您可以使用 Docker API 或 CLI 创建、启动、停止、移动或删除容器。
- 可以在本地机、虚拟机上运行，也可以部署到云端。
- 是可移植的（并且可以在任何操作系统上运行）。
- 与其他容器隔离并运行自己的软件、二进制文件、配置等。

> tips：
>
> ​	可以联想一下VMware，镜像就是虚拟机的iso文件，容器就是我们启动的虚拟机。

## 第二部分：常用命令汇总

### 1、对镜像：

```
docker search <image> #在docker hub中搜索镜像

docker pull <iamge:tag> #从docker hub中拉取镜像

docker images #这个命令可以列出所有你已经下载的镜像，如果想单独查看你下载的某个镜像，可以在后面加镜像名 

docker history <image:tag> #查看某个镜像的构建历史

docker commit #把容器打包成新的镜像

docker tag <old image:tag> <new image:tag> #给镜像打一个新标签

docker push <image:tag> #把镜像推送到仓库

docker rmi <iamge:tag> #删除镜像

```

### 2、对容器：

```
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

docker logs
#删除容器（提示：只有stoped状态的容器才能被删除）
docker rm
```

### 3、共用：

```
#查看详细信息
docker inspect
```

### 4、Dockerfile：

```

```

## 第三部分：容器化应用程序

接下来我们将使用在 Node.js 上运行的简单待办事项列表管理器。如果不熟悉 Node.js，不用担心。下面的操作不需要任何 JavaScript 经验。

### 0、准备条件

- 安装了Docker
- 安装了Git
- ~~安装了一个文本编辑器，推荐VScode~~，在linux里用vim就行

### 1、下载这个app

```
git clone https://github.com/docker/getting-started-app.git
```

查看我们下载的这个文件的子目录

```
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

```
touch Dockerfile
```

![image-20231108171929375](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108171929375.png)

编写Dockerfile文件

```
vim Dockerfile
```

+ 输入下面的内容

+ ```
  FROM node:18-alpine
  WORKDIR /app
  COPY . .
  RUN yarn install --production
  CMD ["node", "src/index.js"]
  EXPOSE 3000
  ```

> Note：
>
> FROM    ----------待补充

构建镜像

```
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

```
docker ps
```

### 3、创建并运行容器

> tips：下面我们直接用来docker run命令来完成容器的创建和启动，而不是先用docker create创建容器，然后再用docker start启动容器。原因是与docker run命令相比，docker create命令更常用于在启动容器之前进行必要的设置。这里我们用docker run更快一点。

使用 `docker run` 命令运行容器并指定刚刚创建的镜像的名称：

```
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
  > ```
  > # 关闭防火墙
  > systemctl stop firewalld.service
  > # 检查防火墙状态 在下方出现disavtive（dead），这样就说明防火墙已经关闭。
  > systemctl status firewalld.service
  > # 永久关闭防火墙（可选可不选）
  > systemctl disable firewalld.service
  > ```

- 访问成功会看到下图界面

- ![image-20231108184651036](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108184651036.png)

添加一两个item，看看它是否按我们的预期工作。我们可以将项目标记为完成并将其删除。同时我们的前端会将项目存储在后端。

### 4、查看容器

输入下面的命令查看正在运行的容器

```
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

```
cd src/static/js
```

```
vim app.js
```

![image-20231108195708209](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20231108195708209.png)

- 把这里改成“你还没有待办事项”

- ```
  - <p className="text-center">No items yet! Add one above!</p>
  + <p className="text-center">你还没有待办事项！添加一个吧！!</p>
  ```

- 改完之后在英文模式下输入 按 shift＋：，输入wq。#保存并退出

返回最getting-started-app目录构建镜像的新版本

```
cd ../../../
```

```
docker build -t getting-started .
```

启动新的容器

```
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

```
docker ps
```

然后我们需要停止这个正在运行的容器

```
docker stop <id>
```

最后我们用docker rm命令删除这个容器

```
docker rm <id>
```

### 3、启动更新后的容器

这时候我们启动新的容器就没有问题了，再次输入启动命令

```
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

## 第五部分：分享应用程序（这一部分先不讲）

现在我们已经构建了图像，可以选择去分享它。要分享 Docker 镜像，必须使用 Docker registry。默认registry是 Docker Hub，~~我们使用的所有镜像都来自于此。~~，我们应该都是换过镜像库的了，前面那个安装docker的文档给出了换镜像库的方法。

> Docker ID 可以让我们访问 Docker Hub，这是世界上最大的容器镜像库和社区。（好像需要科学上网）

### 1、创建储存库

要推送镜像，首先需要在 镜像库上创建一个存储库。

PASS

## 第六部分：持久化数据库

有没有发现，每次启动容器时，我们的待办事项列表都是空的。为什么是这样？在这一部分中，我们将深入了解容器的工作原理。

### 1、容器的文件系统

当容器运行时，它会使用镜像中的各个层作为其文件系统。同时每个容器还拥有自己的“临时空间”来创建/更新/删除文件。即使它们使用相同的镜像，任何更改都不会在另一个容器中看到。

接下来我们做一个小的实验：

实验内容：使用相同的镜像启动两个容器，在其中一个容器中添加一个文件，然后我们在另一个容器中去尝试访问那个文件。

准备：拉取一个centos7镜像

```
docker pull centos:7
```

启动第一个容器：

```
docker run -d centos:7 /bin/bash -c "while true;do sleep 1; done"
```

- 查看容器运行状态

- ```
  docker ps
  ```

- 应该可以看到我们的容器已经成功启动，现在进入我们的第一个容器

- ```
  docker exec -it <id> /bin/bash
  ```

  > Note：
  >
  > exec是进入容器的命令，-it（-i -t）告诉docker我们要以交互模式进入容器，也就是创造一个伪终端让我们能与容器进行交互。
  >
  > 进入容器时要加/bin/bash，是因为在Docker中，必须要保持一个进程的运行，否则容器启动后会马上被kill掉。/bin/bash表示启动容器后启动bash，对指定的容器执行bash。
  >
  > Bash是一个命令处理器，通常运行于文本窗口中，并能执行用户直接输入的命令。
  >
  > 
  >
  > tips：id可以只输入前四位，如果失败了就复制完整id。

- 现在你应该已经在我们的容器里面了，让我们创建一个文件吧！

- ```
  echo "12345" > test.txt #这个命令会在当前目录创建一个test.txt文件并写入12345
  ```

- 查看我们当前目录，你会看到一个test.txt文件

- ```
  ls
  ```

- 好了，我们可以退出这个容器了，输入exit并回车   or    按ctrl+D

现在我们启动第二个容器（使用相同的镜像）：

```
docker run -d centos:7 /bin/bash -c "while true;do sleep 1; done"
```

- 一个小小的考验，请参考我们刚才的操作进入第二个容器。相信你们可以完成。



- 没有完成也没关系，跟着下面的步骤走

- 第一步，查看我们第二个容器的id

- ```
  docker ps
  ```

- 第二步，进入容器

- ```
  docker exec -it <id> /bin/bash
  ```

- 现在我们看一下我们当前目录，你会发现并没有我们刚才创建的test.txt文件

- ```
  ls
  ```

- 退出容器

到这里我们的实验已经完成，实验中我们在第一个容器创建的test.txt文件并不能在第二个容器中看到，这是因为它仅写入第一个容器的“临时空间”。现在我们可以删除刚才的那两个容器了，使用docker stop和rm命令

> tips：可以一次停止或删除多个容器

```
docker stop <id1> <id2>
docker rm <id1> <id2>
```

相信通过这个实验，我们应该知道了为什么每次启动容器时，我们的待办事项列表都是空的。那我们应该怎么解决这个问题呢？

### 2、容器的卷（volumes）





### 3、总结

## 第七部分：使用绑定挂载



## 第八部分：多容器应用程序



## 第九部分：使用docker-compose



## 第十部分：镜像构建最佳实践



## 第十一部分：结语

# 四、小练习

## 1、搭建一个LAMP环境

## 2、用docker搭建LAMP环境

## 3、创建一个自己的镜像库-Harbor