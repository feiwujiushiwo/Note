# Yarn命令解决方案（可能没啥用）

https://github.com/docker/getting-started/issues/381

这个一般是网络问题，我也碰到好几次，有时能成功有时就不行

解决方法是下面两个

1、忽略引擎

RUN yarn install --production --ignore-engines



或者RUN yarn install --ignore-engines

2、配置dns

添加

```
"dns": [
    "8.8.8.8"
  ],
```

到 Docker 引擎配置文件（在“设置”->“Docker 引擎”下）。更改后配置文件如下

```
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

```
{
  "dns": ["<DNS服务器IP>", "<备用DNS服务器IP>"]
}
```

改完之后记得重启。