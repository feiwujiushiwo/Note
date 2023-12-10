# Docker入门指南（下）

## 第六部分：持久化数据

​	有没有发现，每次启动容器时，我们的待办事项列表都是空的。为什么是这样？在这一部分中，我们将深入了解容器的工作原理。

### 1、容器的文件系统

​	当容器运行时，它会使用镜像中的各个层作为其文件系统。同时每个容器还拥有自己的“临时空间”来创建/更新/删除文件。即使它们使用相同的镜像，任何更改都不会在另一个容器中看到。

接下来我们做一个小的实验：

实验内容：使用相同的镜像启动两个容器，在其中一个容器中添加一个文件，然后我们在另一个容器中去尝试访问那个文件。

准备：拉取一个centos7镜像

```bash
docker pull centos:7
```

**启动第一个容器：**

```bash
docker run -d centos:7 /bin/bash -c "while true;do sleep 1; done"
```

1. 查看容器运行状态

```bash
docker ps
```

2. 应该可以看到我们的容器已经成功启动，现在进入我们的第一个容器

```bash
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
> Tips：
>
> id可以只输入前三位，如果失败了就复制完整id。

3. 现在你应该已经在我们的容器里面了，让我们创建一个文件吧！

```bash
echo "12345" > test.txt #这个命令会在当前目录创建一个test.txt文件并写入12345
```

4. 查看我们当前目录，你会看到一个test.txt文件

```bash
ls
```

5. 好了，我们可以退出这个容器了，输入exit并回车   or    按ctrl+D

**现在我们启动第二个容器（使用相同的镜像）：**

```bash
docker run -d centos:7 /bin/bash -c "while true;do sleep 1; done"
```

1. 参考刚才的操作进入第二个容器（上面没太懂的接着往下看）

> 第一步，查看我们第二个容器的id
>
> ```bash
> docker ps
> ```
>
> 第二步，进入容器
>
> ```bash
> docker exec -it <id> /bin/bash
> ```
>
> 现在我们看一下我们当前目录，你会发现并没有我们刚才创建的test.txt文件
>
> ```bash
> ls
> ```
>
> 退出容器

​	到这里我们的实验已经完成，实验中我们在第一个容器创建的test.txt文件并不能在第二个容器中看到，这是因为它仅写入第一个容器的“临时空间”。现在我们可以删除刚才的那两个容器了，使用docker stop和rm命令

```bash
docker stop <id1> <id2>
docker rm  -f <id1> <id2>
```

相信通过这个实验，我们应该知道了为什么每次启动容器时，我们的待办事项列表都是空的。那我们应该怎么解决这个问题呢？

### 2、容器的卷（volumes）

​	在前面的实验中，你看到每个容器每次启动时都从映像定义开始。 虽然容器可以创建、更新和删除文件，但在删除容器时，这些更改将丢失 Docker 会隔离对该容器的所有更改。使用卷，我们就可以实现保存这些数据。 

​	卷提供了连接特定文件系统路径的能力 将容器返回到主机。如果在容器中挂载目录，则该目录会发生变化 目录也可以在主机上看到。如果在容器重启时装载同一目录，则会看到 相同的文件。 

### 3、保留数据

​	默认情况下，todo 应用将其数据存储在 SQLite 数据库（一个关系数据库）中，位于 `/etc/todos/todo.db` 在容器的文件系统中。

> 将所有数据存储在一个文件中只适用于小型演示。
>
> 实际应用大部分都不会储存在一个文件里。

​	如果数据库是单个文件，可以将该文件保留在主机上并使其提供给下一个容器，它应该能够从最后一个容器停止的地方继续。通过创建卷并附加 （通常称为“挂载”）将其保存到存储数据的目录中，可以持久保存数据。作为您的容器 写入  `todo.db` 文件，它会将数据保存到卷中的主机。 

如前所述，您将使用卷装载。将卷装载视为不透明的数据桶。 Docker 完全管理卷，包括磁盘上的存储位置。你只需要记住 卷的名称。 

#### 3.1、创建卷并启动容器

1. 使用  `docker volume create` 命令。 

```bash
docker volume create todo-db
```

2. 停止并再次删除待办事项应用容器  `docker rm -f <id>`，因为它仍在运行而不使用持久卷。 

3. 启动 todo 应用容器，但添加  `--mount` 选项来指定卷装载。为卷命名，然后挂载 它到  `/etc/todos` 在容器中，捕获在路径中创建的所有文件。

```bash
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```

#### 3.2、验证数据持久化

1. 启动容器，添加待办事项。 

2. 停止并删除容器。 

3. 使用前面的步骤启动新容器。 

4. 打开Web应用程序。

5. 检查我们的待办事项是否还存在。

6. 继续删除容器。 

### 4、深入了解volumes

如果想知道在使用卷时，Docker会把数据储存到哪里，可以使用  `docker volume inspect` 命令。 

```bash
docker volume inspect todo-db
```

```console
[
    {
        "CreatedAt": "2019-09-26T02:18:36Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/todo-db/_data",
        "Name": "todo-db",
        "Options": {},
        "Scope": "local"
    }
]
```

> `Mountpoint` 是数据在磁盘上的实际位置。

## 第七部分：使用绑定挂载

​	绑定挂载是另一种类型的挂载，它允许您将主机文件系统中的目录共享到容器中。在处理应用程序时，您可以使用绑定挂载将源代码挂载到容器中。

​	一旦您保存文件，容器就会立即看到您对代码所做的更改。这意味着您可以在容器中运行进程来监视文件系统更改并对其做出响应。

​	在本章中，您将了解如何使用绑定挂载和名为 nodemonopen_in_new 的工具来监视文件更改，然后自动重新启动应用程序。大多数其他语言和框架都有等效的工具。

### 1、演示绑定挂载

1. 进入`getting-started-app`目录

   ```bash
   cd getting-started-app
   ```

2. 使用绑定挂载运行一个centos容器

   ```bash
   docker run -it --mount type=bind,src="$(pwd)",target=/src centos:7 bash
   ```

   > `--mount` 选项告诉 Docker 创建一个绑定挂载，其中  `src` 是 主机上的当前工作目录 （ `getting-started-app`）和 `target` 是该目录应出现在容器内的位置 （ `/src`）。

3. 运行命令后，Docker 会启动一个交互式  `bash` 会话中。

4. 执行`cd src`进入容器的src目录，然后列出目录下的内容。

   ```bash
   cd src
   ls
   ```

   > 这个目录就是我们主机的`getting-started-app`目录

5. 通过创建1.txt文件来检测容器和主机是否可以共享（绑定）文件。（这一步自己尝试完成）

6. 测试完成后结束交互式对话，并删除容器。

​	以上就是对绑定挂载的简要介绍。上面的实验演示了如何在主机和容器之间绑定文件，现在你可以使用绑定挂载来持久化我们应用程序的数据 

### 2、使用绑定挂载来持久化数据

以下步骤介绍如何使用绑定运行开发容器 mount 执行以下操作： 

- 将源代码装载到容器中 
- 安装所有依赖项 
-  `nodemon` 监视文件系统更改 （`nodemon` 是一个在开发 Node.js 应用程序时使用的工具，它可以监视文件更改并自动重启 Node.js 应用程序。）

现在开始使用绑定挂载：

1. 确保没有getting-started容器在运行。

2. 把`getting-started-app`目录绑定挂载到node:18-alpine容器里。

   ```bash
   docker run -dp 127.0.0.1:3000:3000 -w /app --mount type=bind,src="$(pwd)",target=/app node:18-alpine sh -c "yarn install && yarn run dev"
   ```

   > Note：
   >
   > - `-dp 127.0.0.1:3000:3000` - 后台运行、绑定端口 
   > - `-w /app` - 设置“工作目录”
   > - `--mount type=bind,src="$(pwd)",target=/app` - 绑定主机的当前目录到容器的`/app`目录
   > - `node:18-alpine` - 要使用的镜像
   > - `sh -c "yarn install && yarn run dev"` - 命令。使用 `sh` （alpine 没有 `bash` ）启动 shell 并运行 `yarn install` 来安装软件包，然后运行  `yarn run dev` 启动开发服务器。如果查看`package.json` ，将会看到 `dev` 脚本启动 `nodemon`。

3. 使用 `docker logs <container-id>` 查看日志。出现下面的内容就是成功了。

   ```bash
   $docker logs -f <container-id>
   
   nodemon -L src/index.js
   [nodemon] 2.0.20
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching path(s): *.*
   [nodemon] watching extensions: js,mjs,json
   [nodemon] starting `node src/index.js`
   Using sqlite database at /etc/todos/todo.db
   Listening on port 3000
   ```

4. ctl+c退出

### 3、更新应用程序

1. 在 `src/static/js/app.js` 文件中的第 109 行，将“添加项目”按钮更改为简单地说“添加”：

   ```javascript
   - {submitting ? 'Adding...' : 'Add Item'}
   + {submitting ? 'Adding...' : 'Add'}
   ```

2. 刷新 Web 浏览器中的页面，我们应该会看到由于绑定安装而几乎立即反映的更改。 Nodemon 检测到更改并重新启动服务器。节点服务器可能需要几秒钟才能重新启动。

   > Tips：
   >
   > 如果出现错误，请尝试在几秒钟后刷新。

3. 现在我们可以随意进行想要的更改。每次进行更改并保存文件时，由于绑定安装，更改都会反映在容器中。当 Nodemon 检测到更改时，它会自动重新启动容器内的应用程序。

4. 完成后，停止容器并使用以下命令构建新映像：

   ```bash
   docker build -t getting-started .
   ```

### 4、总结

现在我们可以保留数据库并在开发时查看应用程序中的更改，而无需重建镜像。

除了卷挂载和绑定挂载之外，Docker 还支持其他挂载类型和存储驱动程序，以处理更复杂和专门的用例。

## 第八部分：多容器应用程序

> 将容器分开运行有几个重要的原因：
>
> 1. **隔离性和安全性：** 每个容器在其自己的运行环境中执行，容器之间相互隔离。这种隔离性可防止容器之间的相互干扰，降低了安全风险，即使其中一个容器受到攻击，其他容器也能保持相对安全。
> 2. **可维护性和可扩展性：** 将不同的服务或应用程序放置在不同的容器中，使得系统更容易维护和扩展。当需要更新或更改某个服务时，只需针对该服务的容器进行修改，而不会影响其他容器和服务。
> 3. **资源隔离和优化：** 容器能够独立管理资源，如内存、CPU 和存储空间。通过分开运行，您可以为每个容器分配特定的资源，防止其中一个容器消耗过多的资源影响其他容器的性能。
> 4. **轻量级和灵活性：** 容器是轻量级的，可以快速启动和停止，提供了更大的灵活性和便利性。这使得开发人员能够快速部署新的应用或服务，更容易地进行测试和开发。
>
> 总的来说，容器的分开运行有助于提高安全性、维护性和灵活性，使得整体系统更稳定、更易于管理和扩展。

这部分我们要给我们的Web应用程序（Todo App）添加一个数据库 

![multi-container](C:\Users\Lenovo\Desktop\网络安全笔记\附录1、Docker\图片\multi-container.webp)

### 1、容器网络

默认情况下，容器是孤立运行的，与其他容器是互不干涉的。那么，如何允许一个容器与另一个容器通信呢？

> 答案：联网。如果将两个容器放在同一个网络上，它们可以相互通信。

将容器放到网络上有两种方法：

- 启动容器时分配网络。
- 将已运行的容器连接到网络

### 2、创建网络并启动MySQL

1. 创建网络。

   ```bash
   docker network create todo-app
   ```

2. 启动 MySQL 容器并将其连接到网络。您还将定义数据库将用于初始化数据库的一些环境变量。要了解有关 MySQL 环境变量的更多信息，请参阅 MySQL Docker Hub 清单 open_in_new 中的“环境变量”部分。

   ```bash
   docker run -d --network todo-app --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:8.0
   ```

   > Tips：
   >
   > 上面命令中名为 `todo-mysql-data` 的卷安装在 `/var/lib/mysql` 处，这是 MySQL 存储数据的位置。不过我们没有运行 `docker volume create` 命令。 Docker 识别出命名卷并自动创建一个。

3. 要确认数据库已启动并正在运行，请连接到数据库并验证其是否已连接。

   ```bash
   docker exec -it <mysql-container-id> mysql -u root -p
   ```

   > 当出现密码提示时，输入 `secret` 。在 MySQL shell 中，列出数据库验证是否看到 `todos` 数据库。

4. 应该可以看到下面的输出

   ```sql
   mysql> SHOW DATABASES;
   ```

   ```sql
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | performance_schema |
   | sys                |
   | todos              |
   +--------------------+
   5 rows in set (0.00 sec)
   ```

5. 退出数据库，现在我们已经有了一个 `todos` 数据库并且可以使用了。

   ```sql
   mysql> exit
   ```

### 3、连接到MySQL

现在我们知道 MySQL 已启动并正在运行，也可以使用它了。但是，如何使用它？如果在同一网络上运行另一个容器，如何找到该容器？请记住，每个容器都有自己的 IP 地址。

为了回答上述问题并更好地理解容器网络，我们要使用 nicolaka/netshootopen_in_new 容器，该容器附带了许多可用于排除或调试网络问题的工具。

1. 使用 nicolaka/netshoot 镜像启动一个新容器。确保将其连接到同一网络。

   ```bash
   docker run -it --network todo-app nicolaka/netshoot
   ```

2. 在容器内，使用 `dig` 命令，查找主机名 `mysql` 的 IP 地址。这是一个有用的 DNS 工具。

   ```bash
   dig mysql
   ```

   ```
   # 应该看到的输出
   
   ; <<>> DiG 9.18.8 <<>> mysql
   ;; global options: +cmd
   ;; Got answer:
   ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32162
   ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
   
   ;; QUESTION SECTION:
   ;mysql.				IN	A
   
   ;; ANSWER SECTION:
   mysql.			600	IN	A	172.23.0.2
   
   ;; Query time: 0 msec
   ;; SERVER: 127.0.0.11#53(127.0.0.11)
   ;; WHEN: Tue Oct 01 23:47:24 UTC 2019
   ;; MSG SIZE  rcvd: 44
   ```

   > 在“ANSWER SECTION:”中，我们可以看到 `mysql` 的 `A` 记录解析为 `172.23.0.2` （这只是个示例）。虽然 `mysql` 通常不是有效的主机名，但 Docker 能够将其解析为具有该网络别名的容器的 IP 地址。请记住，我们之前使用过 `--network-alias` 。
   >
   > 这意味着我们的的应用程序只需要连接到名为 `mysql` 的主机，它就会与数据库通信。

### 4、将应用程序和MySQL连接

todo 应用程序支持设置一些环境变量来指定 MySQL 连接设置。

- `MYSQL_HOST` - the hostname for the running MySQL server
   `MYSQL_HOST` - 正在运行的 MySQL 服务器的主机名
- `MYSQL_USER` - the username to use for the connection
   `MYSQL_USER` - 用于连接的用户名
- `MYSQL_PASSWORD` - the password to use for the connection
   `MYSQL_PASSWORD` - 用于连接的密码
- `MYSQL_DB` - the database to use once connected
   `MYSQL_DB` - 连接后使用的数据库

> 虽然开发中普遍接受使用环境变量来设置连接设置，但在生产环境中运行应用程序时，建议不要这样做。
>
> [Why You Shouldn't Use Environment Variables for Secret Data](https://blog.diogomonica.com//2017/03/27/why-you-shouldnt-use-env-variables-for-secret-data/)

现在我们开始连接

1. 指定前面的每个环境变量，并将容器连接到应用程序网络。运行此命令时，确保位于 `getting-started-app` 目录中。

   ```bash
   docker run -dp 127.0.0.1:3000:3000 -w /app -v "$(pwd):/app" --network todo-app -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=secret -e MYSQL_DB=todos node:18-alpine sh -c "yarn install && yarn run dev"
   ```

2. 如果查看容器 ( `docker logs -f <container-id>` ) 的日志，应该会看到类似于以下内容的消息，这表明它正在使用 mysql 数据库。

   ```
   nodemon src/index.js
   [nodemon] 2.0.20
   [nodemon] to restart at any time, enter `rs`
   [nodemon] watching dir(s): *.*
   [nodemon] starting `node src/index.js`
   Connected to mysql db at host mysql
   Listening on port 3000
   ```

3. 在浏览器中打开应用程序，添加一些代办事项。

4. 连接到 mysql 数据库并证明待办事项正在写入数据库。密码是 `secret` 。

   ```bash
   docker exec -it <mysql-container-id> mysql -p todos
   ```

   ```sql
   mysql> select * from todo_items;
   +--------------------------------------+--------------------+-----------+
   | id                                   | name               | completed |
   +--------------------------------------+--------------------+-----------+
   | c906ff08-60e6-44e6-8f49-ed56a0853e85 | Do amazing things! |         0 |
   | 2912a79e-8486-4bc3-a4c5-460793a575ab | Be awesome!        |         0 |
   +--------------------------------------+--------------------+-----------+
   ```

## 第九部分：使用docker-compose

Docker Compose 是一个帮助定义和共享多容器应用程序的工具。使用 Compose，我们可以创建一个 YAML 文件来定义服务，并且使用单个命令就可以启动或拆除所有内容。

使用 Compose 的一大优点是可以在文件中定义应用程序堆栈，将其保存在项目存储库的根目录中（现在是版本控制的），并轻松地让其他人为我们的项目做出贡献。别人只需要克隆我们的存储库并使用 Compose 就能启动应用程序。

### 1、创建docker-compose.yml文件

1. 进入 `getting-started-app` 目录并新建一个文件

   ```bash
   cd getting-started-app
   
   touch docker-compose.yml
   ```

2. 现在开始编辑我们的`docker-compose.yml`文件（上）

   ```bash
   vim docker-compose.yml
   ```

   - 定义要作为应用程序一部分运行的容器的名称和镜像。

     ```yaml
     services:
       app:
         image: node:18-alpine
     ```

   - 将 `command` 添加到 `compose.yaml` 文件中。

     ```yaml
     services:
       app:
         image: node:18-alpine
         command: sh -c "yarn install && yarn run dev"
     ```

   - 现在，通过为服务定义 `ports` 来迁移命令的 `-p 127.0.0.1:3000:3000` 部分。

     ```yaml
     services:
       app:
         image: node:18-alpine
         command: sh -c "yarn install && yarn run dev"
         ports:
           - 127.0.0.1:3000:3000
     ```

   - 接下来，使用 `working_dir` 和 `volumes` 定义迁移工作目录和卷映射。

     ```yaml
     services:
       app:
         image: node:18-alpine
         command: sh -c "yarn install && yarn run dev"
         ports:
           - 127.0.0.1:3000:3000
         working_dir: /app
         volumes:
           - ./:/app
     ```

   - 最后，使用 `environment` 迁移环境变量定义。到这里应用程序的yml文件就完成了，还要添加mysql。

     ```yaml
     services:
       app:
         image: node:18-alpine
         command: sh -c "yarn install && yarn run dev"
         ports:
           - 127.0.0.1:3000:3000
         working_dir: /app
         volumes:
           - ./:/app
         environment:
           MYSQL_HOST: mysql
           MYSQL_USER: root
           MYSQL_PASSWORD: secret
           MYSQL_DB: todos
     ```

   - 首先定义新服务并将其命名为 `mysql` ，以便它自动获取网络别名。还要指定要使用的镜像。

     ```yaml
     services:
       app:
         # The app service definition
       mysql:
         image: mysql:8.0
     ```

   - 定义卷映射。

     ```yaml
     services:
       app:
         # The app service definition
       mysql:
         image: mysql:8.0
         volumes:
           - todo-mysql-data:/var/lib/mysql
     
     volumes:
       todo-mysql-data:
     ```

   - 最后，指定环境变量。

     ```yaml
     services:
       app:
         # The app service definition
       mysql:
         image: mysql:8.0
         volumes:
           - todo-mysql-data:/var/lib/mysql
         environment:
           MYSQL_ROOT_PASSWORD: secret
           MYSQL_DATABASE: todos
     
     volumes:
       todo-mysql-data:
     ```

3. 完整的`docker-compose.yml`文件

   ```yaml
   services:
     app:
       image: node:18-alpine
       command: sh -c "yarn install && yarn run dev"
       ports:
         - 127.0.0.1:3000:3000
       working_dir: /app
       volumes:
         - ./:/app
       environment:
         MYSQL_HOST: mysql
         MYSQL_USER: root
         MYSQL_PASSWORD: secret
         MYSQL_DB: todos
   
     mysql:
       image: mysql:8.0
       volumes:
         - todo-mysql-data:/var/lib/mysql
       environment:
         MYSQL_ROOT_PASSWORD: secret
         MYSQL_DATABASE: todos
   
   volumes:
     todo-mysql-data:
   ```

### 2、使用docker-compose来运行我们的多容器应用程序（应用程序堆栈）

1. 停止并删除我们之前的所有容器。

2. 使用 `docker compose up` 命令启动应用程序堆栈。添加 `-d` 标志以在后台运行所有内容。

   ```bash
   docker compose up -d
   ```

   应该看到的输出：

   ```bash
   Creating network "app_default" with the default driver
   Creating volume "app_todo-mysql-data" with default driver
   Creating app_app_1   ... done
   Creating app_mysql_1 ... done
   ```

   Docker Compose 创建了卷以及网络。默认情况下，Docker Compose 会自动专门为应用程序堆栈创建一个网络（这就是我们没有在 Compose 文件中定义网络的原因）。

3. 使用 `docker compose logs -f` 命令查看日志。`-f` 标志位于日志后面，因此会在生成时提供实时输出。

   ```bash
   docker compose logs -f
   ```

   ```bash
   mysql_1  | 2019-10-03T03:07:16.083639Z 0 [Note] mysqld: ready for connections.
   mysql_1  | Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
   app_1    | Connected to mysql db at host mysql
   app_1    | Listening on port 3000
   ```

   服务名称显示在行的开头（通常是彩色的，我这里文档显示有问题）以帮助区分消息。如果要查看特定服务的日志，可以用`docker compose logs -f app` （看mysql就把app换成mysql）。

4. 打开我们的浏览器访问应用程序，应该可以看到已经成功运行。

### 3、查看应用程序堆栈

```bash
docker-compose ps
```

### 4、停止这个应用程序堆栈

```bash
docker compose down
```

> 默认情况下，运行 `docker compose down` 时，不会删除 compose 文件中的命名卷。如果要删除卷，则需要添加 `--volumes` 标志。
>
> Tips：
>
> 停止指定项目`docker-compose -p <project_name> down`

### 5、总结

这部分我们了解了 Docker Compose 以及它如何帮助简化定义和共享应用程序堆栈。

## 第十部分：镜像构建实践

### 1、镜像分层

使用  `docker image history` 命令，您可以看到使用的命令 以创建图像中的每个图层。 

1. 使用  `docker image history` 命令查看  `getting-started` 想象你 创建。 

   ```console
   $ docker image history getting-started
   ```

   您应该得到如下所示的输出。 

   ```plaintext
   IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
   a78a40cbf866        18 seconds ago      /bin/sh -c #(nop)  CMD ["node" "src/index.j…    0B                  
   f1d1808565d6        19 seconds ago      /bin/sh -c yarn install --production            85.4MB              
   a2c054d14948        36 seconds ago      /bin/sh -c #(nop) COPY dir:5dc710ad87c789593…   198kB               
   9577ae713121        37 seconds ago      /bin/sh -c #(nop) WORKDIR /app                  0B                  
   b95baba1cfdb        13 days ago         /bin/sh -c #(nop)  CMD ["node"]                 0B                  
   <missing>           13 days ago         /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B                  
   <missing>           13 days ago         /bin/sh -c #(nop) COPY file:238737301d473041…   116B                
   <missing>           13 days ago         /bin/sh -c apk add --no-cache --virtual .bui…   5.35MB              
   <missing>           13 days ago         /bin/sh -c #(nop)  ENV YARN_VERSION=1.21.1      0B                  
   <missing>           13 days ago         /bin/sh -c addgroup -g 1000 node     && addu…   74.3MB              
   <missing>           13 days ago         /bin/sh -c #(nop)  ENV NODE_VERSION=12.14.1     0B                  
   <missing>           13 days ago         /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B                  
   <missing>           13 days ago         /bin/sh -c #(nop) ADD file:e69d441d729412d24…   5.59MB   
   ```

   每条线代表图像中的一个图层。此处的显示屏显示了底部的底座 顶部的最新图层。使用它，您还可以快速查看每层的大小，从而帮助 诊断大图像。 

2. 您会注意到其中几行被截断。如果将  `--no-trunc` 标志，你会得到 全输出。 

   ```console
   $ docker image history --no-trunc getting-started
   ```

### 2、镜像缓存

现在，您已经了解了分层的实际效果，需要学习一个重要的课程来帮助减少构建 容器映像的时间。一旦图层发生更改，还必须重新创建所有下游图层。 

查看为入门应用创建的以下 Dockerfile。 

```dockerfile
# syntax=docker/dockerfile:1
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
```

返回到映像历史记录输出，您会看到 Dockerfile 中的每个命令都成为映像中的新层。 您可能还记得，当您对映像进行更改时，必须重新安装 yarn 依赖项。每次构建时都提供相同的依赖项没有多大意义。 

若要修复此问题，需要重构 Dockerfile 以帮助支持缓存 的依赖项。对于基于节点的应用程序，这些依赖项是定义的 在  `package.json` 文件。您只能复制该文件，首先安装 依赖项，然后复制其他所有内容。然后，您只需重新创建纱线 依赖项（如果对  `package.json`。 

1. 更新 Dockerfile 以复制到  `package.json` 首先，安装依赖项，然后将其他所有内容复制到其中。 

   ```dockerfile
   # syntax=docker/dockerfile:1
   FROM node:18-alpine
   WORKDIR /app
   COPY package.json yarn.lock ./
   RUN yarn install --production
   COPY . .
   CMD ["node", "src/index.js"]
   ```

2. 创建一个名为  `.dockerignore` 与 Dockerfile 位于同一文件夹中，其中包含以下内容。 

   ```ignore
   node_modules
   ```

   `.dockerignore`文件是一种有选择地仅复制图像相关文件的简单方法。 您可以阅读有关此内容的更多信息 [这里 ](https://docs.docker.com/build/building/context/#dockerignore-files)。 在本例中，  `node_modules` 文件夹应该在第二个中省略  `COPY` 步骤，否则， 它可能会覆盖由命令在  `RUN` 步。 

3. 使用  `docker build`。 

   ```console
   $ docker build -t getting-started .
   ```

   您应看到如下所示的输出。 

   ```plaintext
   [+] Building 16.1s (10/10) FINISHED
   => [internal] load build definition from Dockerfile
   => => transferring dockerfile: 175B
   => [internal] load .dockerignore
   => => transferring context: 2B
   => [internal] load metadata for docker.io/library/node:18-alpine
   => [internal] load build context
   => => transferring context: 53.37MB
   => [1/5] FROM docker.io/library/node:18-alpine
   => CACHED [2/5] WORKDIR /app
   => [3/5] COPY package.json yarn.lock ./
   => [4/5] RUN yarn install --production
   => [5/5] COPY . .
   => exporting to image
   => => exporting layers
   => => writing image     sha256:d6f819013566c54c50124ed94d5e66c452325327217f4f04399b45f94e37d25
   => => naming to docker.io/library/getting-started
   ```

4. 现在，对  `src/static/index.html` 文件。例如，将  `<title>` 到“The Awesome Todo App”。 

5. 现在使用 构建 Docker 映像  `docker build -t getting-started .` 再。这一次，您的输出应该看起来有点不同。 

   ```plaintext
   [+] Building 1.2s (10/10) FINISHED
   => [internal] load build definition from Dockerfile
   => => transferring dockerfile: 37B
   => [internal] load .dockerignore
   => => transferring context: 2B
   => [internal] load metadata for docker.io/library/node:18-alpine
   => [internal] load build context
   => => transferring context: 450.43kB
   => [1/5] FROM docker.io/library/node:18-alpine
   => CACHED [2/5] WORKDIR /app
   => CACHED [3/5] COPY package.json yarn.lock ./
   => CACHED [4/5] RUN yarn install --production
   => [5/5] COPY . .
   => exporting to image
   => => exporting layers
   => => writing image     sha256:91790c87bcb096a83c2bd4eb512bc8b134c757cda0bdee4038187f98148e2eda
   => => naming to docker.io/library/getting-started
   ```

   首先，您应该注意到构建速度要快得多。而且，你会看到 几个步骤使用以前缓存的图层。推拉 此映像及其更新也将快得多。 

### 3、多阶段构建

多阶段构建是一个非常强大的 工具，以帮助使用多个阶段来创建映像。它们有几个优点： 

- 将构建时依赖项与运行时依赖项分开 
- 通过仅发布应用运行所需的内容来减小整体图像大小 

### 4、Maven/Tomcat示例

构建基于 Java 的应用程序时，需要 JDK 将源代码编译为 Java 字节码。然而 生产中不需要 JDK。此外，您可能正在使用 Maven 或 Gradle 等工具来帮助构建应用。 最终映像中也不需要这些。多阶段构建有帮助。 

```dockerfile
# syntax=docker/dockerfile:1
FROM maven AS build
WORKDIR /app
COPY . .
RUN mvn package

FROM tomcat
COPY --from=build /app/target/file.war /usr/local/tomcat/webapps 
```

在此示例中，您将使用一个阶段（称为  `build`） 以使用 Maven 执行实际的 Java 构建。在第二 阶段（从  `FROM tomcat`），请从  `build` 阶段。最终图像只是最后阶段 正在创建，可以使用  `--target` 旗。 

### 5、React示例

在构建 React 应用程序时，你需要一个 Node 环境来编译 JS 代码（通常是 JSX）、SASS 样式表、 以及更多静态 HTML、JS 和 CSS。如果你不做服务器端渲染，你甚至不需要 Node 环境 用于您的生产构建。您可以在静态 nginx 容器中传送静态资源。 

```dockerfile
# syntax=docker/dockerfile:1
FROM node:18 AS build
WORKDIR /app
COPY package* yarn.lock ./
RUN yarn install
COPY public ./public
COPY src ./src
RUN yarn run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
```

在前面的 Dockerfile 示例中，它使用  `node:18` image 来执行构建（最大化层缓存），然后复制输出 放入 nginx 容器中。 

### 6、总结

在本节中，您学习了一些映像构建最佳实践，包括层缓存和多阶段构建。 

## 第十一部分：结语

这些指南全部来自于docker的官方文档，我复制下来稍微修改了一些，第十部分没时间搞了，全部复制过来了。想进一步学docker或者看完整的指南的，点这个链接：

[Getting Started with Docker Overview](https://docs.docker.com/get-started/overview/)

最后推荐一本学习docker的书《每天5分钟玩转docker容器技术》