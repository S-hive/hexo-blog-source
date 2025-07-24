---
title: 使用Docker自托管Obsidian并通过网页浏览器访问
date: 2025-07-04
cssclasses:
  - Debug
  - docker
---
很有意思的事，在我烦恼obsidian无法在其他任何公共电脑上随时访问的时候。过了几天，在我电子邮箱里发现了这个推文[《 Self-host Obsidian using Docker and Access it Via Web Browser》](https://linuxhandbook.com/obsidian-docker-self-host/)，简直是瞌睡送枕头。

---

>Obsidian 是一个笔记软件，基于纯文本 Markdown 文件构建，提供本地优先的知识管理，具有强大的图形视图、反向链接和繁荣的插件生态系统。

我们将实现：
一个自托管Obsidian的Docker容器，运行在我的家庭服务器中，可通过浏览器访问，无需桌面客户端，也无需移动应用程序，无论我身在何处，都只需这一个自托管解决方案。

前提条件：
* 一个安装了 Docker 和 Docker Compose 的 Linux 系统。
* 对终端命令有基本的了解
* 熟悉编辑 YAML 文件。

## 设置 Obsidian

### 1. 为 Obsidian 创建数据目录

```bash
mkdir -p ~/docker/obsidian
cd ~/docker/obsidian
```
创建一个独立目录方便管理
![Pasted20image20250724162824](/images/Pastedimage20250724162824.png)

### 2. 创建 `docker-compose.yml` 文件

设置一个 Docker Compose 文件，这个文件会告诉 Docker 如何运行 Obsidian，使用哪个镜像，打开哪些端口以及其他重要信息。

创建一个名为 `docker-compose.yml` 的文件：
```bash
vim docker-compose.yml
```
将以下内容复制到一个新文件中：
```bash
version: "3.8"
services:
  obsidian:
    image: ghcr.io/linuxserver/obsidian:latest
    container_name: obsidian
    security_opt:
      - no-new-privileges:false
      - seccomp:unconfined
    healthcheck:
      test: timeout 10s bash -c ':> /dev/tcp/127.0.0.1/3000' || exit 1
      interval: 10s
      timeout: 5s
      retries: 3
      start_period: 90s
    ports:
      - "3000:3000"
    shm_size: "2gb"
    volumes:
      - ./config:/config:rw
    environment:
      CUSTOM_USER: yourusername
      PASSWORD: yourpassword
      PUID: 1000
      PGID: 1000
      TZ: Asia/Shanghai
    restart: unless-stopped

```

分析一下其中的几个重要部分：
* `image` : 我们正在使用 LinuxServer.io 提供的最新 Obsidian 镜像。
* `volumes` : 将当前目录中的 `config` 文件夹映射到 Obsidian 的内部配置目录，所有你的 Obsidian 数据和设置都将存储在这里。
* `ports` : 该应用将在您机器的 3000 端口上运行。如果您希望使用其他端口，可以更改此设置。
* `shm_size` : 分配共享内存；对于具有 UI 的应用（如 Obsidian）很有用。
* `environment` : 这里用于设置您的用户、密码、时区和文件所有权。

需要注意有几行需要自行修改的内容：
```bash
    environment:
      CUSTOM_USER: yourusername
      PASSWORD: yourpassword
      PUID: 1000
      PGID: 1000
      TZ: Asia/Shanghai
```
* `yourusername` : 你将用来登录 Obsidian 的用户名。
* `yourpassword` : 选择一个强密码。
* `TZ` : 使用你本地的时区。（示例： Asia/Shanghai ）
* `PUID` 和 `PGID` ：这些应该与主机系统上用户的 UID 和 GID 匹配。要查找它们，请运行
```bash
id yourusername
```

### 3. 部署容器

一旦 `docker-compose.yml` 文件准备就绪且值已自定义，即可开始启动容器：
```bash
docker-compose up -d
```
这个命令告诉 Docker：
* 拉取 Obsidian 镜像（如果尚未下载）
* 使用我们定义的设置创建容器
* 以分离模式运行（ `-d` ），以便它在后台继续运行

![Pasted20image20250724161028](/images/Pastedimage20250724161028.png)

### 在浏览器中访问 Obsidian

完成后，你应该能够在浏览器中打开 Obsidian：
```bash
http://localhost:3000
```
如果你不是在本地运行，请将 `localhost` 替换为你的服务器 IP。

![Pasted20image20250724161308](/images/Pastedimage20250724161308.png)

进入后，它看起来是这样的：

![Pastedimage20250724161951](/images/Pastedimage20250724161951.png)