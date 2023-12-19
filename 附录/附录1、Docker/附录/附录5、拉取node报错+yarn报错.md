# Yarn命令解决方案（可能没啥用）

https://github.com/docker/getting-started/issues/381

这个一般是网络问题，我也碰到好几次，有时能成功有时就不行

解决方法是下面3个，推荐第一个，虽然麻烦，但是一定能解决

## 1、一定能解决的：

1. 把依赖文件的压缩包` node_modules.zip`复制到`getting-started-app`目录下并解压，最终目录结构如下：

   `getting-started-app`

   |——`node_modules`

   |——`原本的一些文件`

2. 更改dockerfile文件为

   ```dockerfile
   FROM node:18-alpine
   WORKDIR /app
   COPY . .
   COPY ./node_modules /app
   CMD ["node", "src/index.js"]
   EXPOSE 3000
   ```

3. 更改docker-compose.yml文件为

   ```yaml
   services:
     app:
       build: .
       ports:
         - 3000:3000
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

4. 这时候应该就能成功执行文档里的命令了。

## 2、忽略引擎

RUN yarn install --production --ignore-engines

或者RUN yarn install --ignore-engines

## 3、配置dns

添加

```json
"dns": [
    "8.8.8.8"
  ],
```

到 Docker 引擎配置文件（在“设置”->“Docker 引擎”下）。更改后配置文件如下

```json
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "dns": [
    "8.8.8.8"
  ],
  "experimental": false
}
```

在 Docker 守护进程中配置默认的 DNS 服务器，使所有容器都使用相同的 DNS 设置。这可以通过编辑 Docker 配置文件完成。

1. 找到 Docker 守护进程的配置文件，通常在 `/etc/docker/daemon.json`。
2. 如果文件不存在，创建它；如果存在，确保它的格式正确。
3. 在配置文件中添加类似以下的内容：

```json
{
  "dns": ["<DNS服务器IP>", "<备用DNS服务器IP>"]
}
```

改完之后记得重启。

# node报错

把FROM那一行改成下面的

FROM docker.io/library/node:18-alpine