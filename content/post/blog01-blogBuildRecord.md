---
title: "基于hugo-theme-next主题的博客搭建全过程记录"
date: 2026-01-06
categories:
  - "理想"
tags:
  - "Hugo"
  - "Nginx"
  - "Hugo-theme-next"
  - "Github Actions"
  - "SSH Key pair"
  - "博客搭建"
description: "记录 tekapo.wang 博客搭建全过程，包括服务器、域名的购买、Nginx配置、Hugo部署以及Github Actions的配置，旨在帮助小白最快的构建起自己的博客网站。"
weight: 1
---
## 博客搭建初衷

{{< quote >}}某天晚上突然很想在互联网世界拥有一块属于自己的小天地，  
可以随时记录生活、分享知识、发表见解。  
于是决定搭建一个个人博客网站。经过一番调研，选择了 *Hugo* 作为静态网站生成器，*Nginx* 作为 Web 服务器，并购买了云服务器和域名。  
本文将详细记录从零开始搭建博客的全过程，帮助有类似需求的朋友快速上手。{{< /quote >}}
## 基础设施：服务器与域名

### 云服务器选择

一年前搭建博客的时候，没有买服务器，直接用的 *GitHub Pages* 托管，后来发现功能受限，而且访问速度不够理想，所以这次决定购买一台云服务器。经过对比，最终选择了**阿里云轻量应用服务器 (Simple Application Server / SAS)**，主要考虑到**轻量应用服务器**的价格相对较低，适合个人博客的搭建。  
  - **地域**：**中国香港** (香港节点免 ICP 备案)。
  - **镜像**：系统镜像 `Ubuntu 22.04 LTS` (不要选应用镜像，尽量买台裸机)。
  - **规格**：2核 1G内存。  

**Notice**：购买后在控制台重置 `root` 密码，并记录公网 IP。

### 域名购买与解析

在阿里云域名注册平台购买（推荐 `.com`， `.me`， `.tech` 等）。尽量不要选择 `.cn`，`.com.cn`，`.gov`，因为这些特殊域名需要备案之类的，过程繁琐耗时。购买域名之后还需要进行实名认证（如果之前没认证的话，这个很快），然后是域名解析，因为域名购买后只是一个域名，需要将它指向云服务器的公网 IP。

- 进入“云解析 DNS”控制台。
- 添加 **A 记录**：主机记录 `@`，记录值填服务器公网 IP。
- 添加 **A 记录**：主机记录 `www`，记录值填服务器公网 IP。


## 服务器配置：Nginx 与 HTTPS

这一步是搭建 Web 服务，让外界能通过 HTTP/HTTPS 访问你的服务器。

### Nginx 的作用与安装

作为反向代理服务器和静态资源服务器，监听 80/443 端口，将用户的请求指向博客的 HTML 文件目录。所以需要在服务器上安装 Nginx。我一般是用 `XTermial`这个工具连接服务器，安装 Nginx 的命令如下：
```bash
apt update && apt upgrade -y
apt install nginx -y
```
这两行命令的目的是：先更新软件源索引，然后升级系统所有软件到最新版本，最后安装 Nginx，确保系统是最新的、安全的。

log如下：

```bash
root@tekapo-server:~# apt update && apt install nginx -y
Hit:1 http://mirrors.cloud.aliyuncs.com/ubuntu jammy InRelease
Hit:2 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates InRelease
Hit:3 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-backports InRelease
Hit:4 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-security InRelease
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
123 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libnginx-mod-http-geoip2 libnginx-mod-http-image-filter
  libnginx-mod-http-xslt-filter libnginx-mod-mail libnginx-mod-stream
  libnginx-mod-stream-geoip2 nginx-common nginx-core
Suggested packages:
  fcgiwrap nginx-doc ssl-cert
The following NEW packages will be installed:
  libnginx-mod-http-geoip2 libnginx-mod-http-image-filter
  libnginx-mod-http-xslt-filter libnginx-mod-mail libnginx-mod-stream
  libnginx-mod-stream-geoip2 nginx nginx-common nginx-core
0 upgraded, 9 newly installed, 0 to remove and 123 not upgraded.
Need to get 698 kB of archives.
After this operation, 2,391 kB of additional disk space will be used.
Get:1 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 nginx-common all 1.18.0-6ubuntu14.7 [40.1 kB]
Get:2 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 libnginx-mod-http-geoip2 amd64 1.18.0-6ubuntu14.7 [12.0 kB]
Get:3 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 libnginx-mod-http-image-filter amd64 1.18.0-6ubuntu14.7 [15.5 kB]
Get:4 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 libnginx-mod-http-xslt-filter amd64 1.18.0-6ubuntu14.7 [13.8 kB]
Get:5 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 libnginx-mod-mail amd64 1.18.0-6ubuntu14.7 [45.8 kB]
Get:6 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 libnginx-mod-stream amd64 1.18.0-6ubuntu14.7 [73.0 kB]
Get:7 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 libnginx-mod-stream-geoip2 amd64 1.18.0-6ubuntu14.7 [10.1 kB]
Get:8 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 nginx-core amd64 1.18.0-6ubuntu14.7 [483 kB]
Get:9 http://mirrors.cloud.aliyuncs.com/ubuntu jammy-updates/main amd64 nginx amd64 1.18.0-6ubuntu14.7 [3,878 B]
Fetched 698 kB in 0s (20.7 MB/s)  
Preconfiguring packages ...
Selecting previously unselected package nginx-common.
(Reading database ... 81055 files and directories currently installed.)
Preparing to unpack .../0-nginx-common_1.18.0-6ubuntu14.7_all.deb ...
Unpacking nginx-common (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package libnginx-mod-http-geoip2.
Preparing to unpack .../1-libnginx-mod-http-geoip2_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking libnginx-mod-http-geoip2 (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package libnginx-mod-http-image-filter.
Preparing to unpack .../2-libnginx-mod-http-image-filter_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking libnginx-mod-http-image-filter (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package libnginx-mod-http-xslt-filter.
Preparing to unpack .../3-libnginx-mod-http-xslt-filter_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking libnginx-mod-http-xslt-filter (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package libnginx-mod-mail.
Preparing to unpack .../4-libnginx-mod-mail_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking libnginx-mod-mail (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package libnginx-mod-stream.
Preparing to unpack .../5-libnginx-mod-stream_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking libnginx-mod-stream (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package libnginx-mod-stream-geoip2.
Preparing to unpack .../6-libnginx-mod-stream-geoip2_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking libnginx-mod-stream-geoip2 (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package nginx-core.
Preparing to unpack .../7-nginx-core_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking nginx-core (1.18.0-6ubuntu14.7) ...
Selecting previously unselected package nginx.
Preparing to unpack .../8-nginx_1.18.0-6ubuntu14.7_amd64.deb ...
Unpacking nginx (1.18.0-6ubuntu14.7) ...
Setting up nginx-common (1.18.0-6ubuntu14.7) ...
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /lib/systemd/system/nginx.service.
Setting up libnginx-mod-http-xslt-filter (1.18.0-6ubuntu14.7) ...
Setting up libnginx-mod-http-geoip2 (1.18.0-6ubuntu14.7) ...
Setting up libnginx-mod-mail (1.18.0-6ubuntu14.7) ...
Setting up libnginx-mod-http-image-filter (1.18.0-6ubuntu14.7) ...
Setting up libnginx-mod-stream (1.18.0-6ubuntu14.7) ...
Setting up libnginx-mod-stream-geoip2 (1.18.0-6ubuntu14.7) ...
Setting up nginx-core (1.18.0-6ubuntu14.7) ...
 * Upgrading binary nginx                                                      [ OK ] 
Setting up nginx (1.18.0-6ubuntu14.7) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for ufw (0.36.1-4ubuntu0.1) ...
Scanning processes...                                                                 
Scanning linux images...                                                              

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
root@tekapo-server:~#
```
### 防火墙端口放行
因为阿里云默认开启防火墙，只允许 SSH (22) 端口。Nginx 默认监听 80/443 端口，如果不放行，外部请求会被拦截。
>不过现在买轻量级应用服务器都默认开启了 80/443 端口，所以这一步可以省略。

### 配置 HTTPS (SSL 证书)
我们需要在服务器上安装一个 Certbot (Let's Encrypt 免费证书) 客户端，然后用它来申请 SSL 证书，并配置 Nginx 使用该证书。安装命令如下：
```bash
apt install certbot python3-certbot-nginx -y
certbot --nginx
```

## 本地环境与 Hugo 主题搭建
这一步是博客内容生产环境的搭建。在自己的电脑上安装 Hugo，然后创建博客项目，并添加主题。我使用的是 Win11 操作系统，安装 Hugo 的命令如下：
```powershell
winget install Hugo.Hugo.Extended
```
>注意需要安装的是 Extended 版本，因为它支持 SCSS 编译。
```powershell
## 安装Hugo并创建工作目录
加载个人及系统配置文件用了 789 毫秒。
(base) PS C:\WINDOWS\system32> winget install Hugo.Hugo.Extended
“msstore”源要求在使用前查看以下协议。
Terms of Transaction: https://aka.ms/microsoft-store-terms-of-transaction
源要求将当前计算机的 2 个字母的地理区域发送到后端服务才能正常工作，(例如"US")。

是否同意所有源协议条款?
[Y] 是  [N] 否: Y
已找到 Hugo(扩展版) [Hugo.Hugo.Extended] 版本 0.154.2
此应用程序由其所有者授权给你。
Microsoft 对第三方程序包概不负责，也不向第三方程序包授予任何许可证。
正在下载 https://github.com/gohugoio/hugo/releases/download/v0.154.2/hugo_extended_0.154.2_windows-amd64.zip
  ██████████████████████████████  19.7 MB / 19.7 MB
已成功验证安装程序哈希
正在提取存档...
已成功提取存档
正在启动程序包安装...
添加了命令行别名： "hugo"
已修改路径环境变量；重启 shell 以使用新值。
已成功安装
```
这个时候可以关闭终端，重新开一个新的，测试一下 Hugo 是否安装成功：
```powershell
hugo version
```
如果能看到 Hugo 的版本信息，说明安装成功。接下来创建博客项目，首先我们可以在本地找一个风水宝地，放置博客项目文件夹，比如 `E:\MyHugoProject`，然后在该目录下创建 Hugo 项目。打开终端，进入到当前文件夹，执行以下命令：
```powershell
hugo new site tekapo-blog
```
这会创建一个名为 `tekapo-blog` 的 Hugo 项目文件夹。然后初始化 git 仓库：
```powershell
cd tekapo-blog
git init
```
接下来就是添加主题。我选择的是 `hugo-theme-next` 主题，使用 git 子模块的方式添加主题，之所以要用子模块的方式，是为了避免 git 仓库嵌套问题，并且为了后续能够方便的更新主题。
>添加主题前，如果你在国内网络环境下，可能会遇到网络问题，导致克隆失败。尽管你开了 clash 之类的代理工具，在使用终端进行命令行 git操作时，可能无法自动走代理的通道，这时候可以临时配置 git 全局代理，添加完成后再取消代理。具体命令如下：
```powershell
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```
因为我的代理服务器的协议类型是 http，所以我需要把 git 通过 http 协议和 https 协议访问远程仓库时的代理都配置走 http。然后执行添加主题命令：
```powershell
git submodule add https://github.com/hugo-next/hugo-theme-next.git themes/hugo-theme-next
```
```powershell
加载个人及系统配置文件用了 677 毫秒。
(base) PS C:\WINDOWS\system32> cd E:
(base) PS E:\> cd MyHugoProject
(base) PS E:\MyHugoProject> cd tekapo-blog
(base) PS E:\MyHugoProject\tekapo-blog> git config --global http.proxy http://127.0.0.1:7890
(base) PS E:\MyHugoProject\tekapo-blog> git config --global https.proxy http://127.0.0.1:7890
(base) PS E:\MyHugoProject\tekapo-blog> git submodule add https://github.com/hugo-next/hugo-theme-next.git themes/hugo-theme-next
Cloning into 'E:/MyHugoProject/tekapo-blog/themes/hugo-theme-next'...
remote: Enumerating objects: 1754, done.
remote: Counting objects: 100% (903/903), done.
remote: Compressing objects: 100% (264/264), done.
remote: Total 1754 (delta 660), reused 639 (delta 639), pack-reused 851 (from 1)
Receiving objects: 100% (1754/1754), 6.85 MiB | 4.25 MiB/s, done.
Resolving deltas: 100% (987/987), done.
warning: in the working copy of '.gitmodules', LF will be replaced by CRLF the next time Git touches it
(base) PS E:\MyHugoProject\tekapo-blog>

```
记得取消配置 git 全局代理，避免影响以后别的项目的 git 操作，命令如下：
```powershell
# 取消配置git全局代理
(base) PS E:\MyHugoProject\tekapo-blog> git config --global --unset http.proxy
(base) PS E:\MyHugoProject\tekapo-blog> git config --global --unset https.proxy
(base) PS E:\MyHugoProject\tekapo-blog>
```
>接下来要注意，hugo-theme-next 主题需要配置文件占位符，否则编译可能会失败，具体的配置可以参考主题的官方文档，或者问 AI。我这里就不赘述了，配置好之后，可以运行以下命令，在本地预览博客：
```powershell
hugo server -D
## 或者简单一点直接 hugo server
hugo server
```
如果到这一步可以在本地预览博客，说明本地环境搭建成功，接下来就是把博客部署到服务器上，让全世界都能访问到你的博客。

## 自动化部署：GitHub Actions
为了实现博客的自动化部署，我们可以利用 GitHub Actions 来完成。每次我们在本地更新博客内容并推送到 GitHub 仓库时，GitHub Actions 会自动构建博客并将生成的静态文件部署到我们的服务器上。
大致的原理如下：  
*本地 Push 代码 -> GitHub 触发 Actions -> 启动虚拟机 -> 编译 Hugo -> 通过 SSH (SCP) 将 `public` 目录推送到阿里云服务器 Nginx 目录。*
### SSH Key Pair 生成
为了让 GitHub Actions 能够通过 SSH 连接到我们的服务器，我们需要生成一个密钥对。一把给 GitHub（私钥），一把放服务器（公钥），这样 GitHub 才能把文件传给服务器。可以在VsCode的终端中执行以下命令生成 SSH Key Pair：
```powershell
ssh-keygen -t ed25519 -C "blog-deploy" -f blog_key
```
遇到提示直接按回车（不要设密码！）。执行完后，你的项目根目录下会多出两个文件：blog_key (私钥) 和 blog_key.pub (公钥)。记得给 `.gitignore` 加一行 (防止把私钥不小心上传到公网)：
```powershell
Add-Content .gitignore "`nblog_key"
Add-Content .gitignore "`nblog_key.pub"
```
>注意！本着最佳实践的原则，我们不需要把 hugo 编译的产物 `public`目录和 `resources` 目录上传到 GitHub 仓库。所以我们还需要把它们加入到 `.gitignore` 文件中：
```powershell
# 1. 把 public 文件夹添加到 .gitignore 忽略名单
Add-Content .gitignore "`npublic/"
Add-Content .gitignore "`nresources/"

# 2. 从 Git 的暂存区中移除 public 文件夹（如果有的话）
git rm -r --cached public
git rm -r --cached resources
```
在 VS Code 里打开 blog_key.pub 文件，全选，然后复制里面的内容（以 ssh-ed25519 开头的一行串码）。打开 Xterminal 连接到服务器。首先创建目录。
```bash
mkdir -p ~/.ssh
```
写入公钥。
```bash
echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHDUIJE1QObZbwwVJBmUwTWp7ftSqb3ZSioreN4XbPN+ blog-deploy" >> ~/.ssh/authorized_keys
```
设置权限。
```bash
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
```
执行完上面三步后，输入下面这个命令查看一下文件内容，验证一下。
```bash
cat ~/.ssh/authorized_keys
```
log记录可以参考一下。
```bash
root@tekapo-server:~# mkdir -p ~/.ssh
root@tekapo-server:~# echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHDUIJE1QObZbwwVJBmUwTWp7ftSqb3ZSioreN4XbPN+ blog-deploy" >> ~/.ssh/authorized_keys
root@tekapo-server:~# chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
root@tekapo-server:~# cat ~/.ssh/authorized_keys
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHDUIJE1QObZbwwVJBmUwTWp7ftSqb3ZSioreN4XbPN+ blog-deploy
 ```

### SSH 私钥配置到 GitHub
1. 在 GitHub 创建仓库 (存放代码)，登录 GitHub，New repository，Repository name: 填 tekapo-blog，Visibility: 选 Public (公开)，点击 Create repository。
2. 复制私钥内容，在 VS Code 里打开 blog_key，全选复制（包含 -----BEGIN... 和 -----END...）。进入仓库，点击 Settings -> Secrets and variables -> Actions -> New repository secret，Name: `DEPLOY_KEY`，Secret: 粘贴私钥内容，点击 Add secret。再点 New repository secret，Name: `SERVER_IP`，Secret: 填服务器公网 IP，点击 Add secret。
   
### Nginx 配置
我们要告诉 Nginx 去哪里找博客文件。可以使用 nano 编辑器，比 vim 舒服。

```bash
## 创建存放目录
root@tekapo-server:~# mkdir -p /var/www/html/tekapo-blog
## 修改配置
root@tekapo-server:~# nano /etc/nginx/sites-enabled/default
```
这个时候会进入 nano 编辑器，找到 `server { ... }` 这一大块，按键盘方向键往下找，找到 `server { ... listen 443 ssl ... }` 这一大块区域（这是 HTTPS 的配置），在这一块里，找到 `root /var/www/html;`这一行 ，把 html 后面加上 `/tekapo-blog`。表示 Nginx 要去这个目录找网站文件。为了保险，如果你看到多个 root，把它们都改成 `/var/www/html/tekapo-blog;`。  

保存并退出：按 `Ctrl + O` 保存，按 `Enter` 确认文件名。按 `Ctrl + X` 退出编辑器。

重启 Nginx 让配置生效：
```bash
root@tekapo-server:~# nginx -t && systemctl reload nginx
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@tekapo-server:~#
```

### GitHub Actions 配置
1. 在项目根目录新建文件夹 .github (注意前面有点)。
2. 在 .github 里面新建文件夹 workflows。
3. 在 workflows 里面新建文件 deploy.yml。
```yml
name: Deploy Blog

on:
  push:
    branches:
      - main  # 监听 main 分支的变动

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: 1. 拉取代码
        uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0

      - name: 2. 安装 Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true   

      - name: 3. 编译博客
        run: hugo --minify

      - name: 4. 部署到阿里云
        uses: appleboy/scp-action@master
        with:
          host: ${{ secrets.SERVER_IP }}
          username: root
          key: ${{ secrets.DEPLOY_KEY }}
          source: "public/*"
          target: "/var/www/html/tekapo-blog/"
          strip_components: 1 # 移除 public 这一层，直接放内容
          rm: true # 删除目标路径下的现有文件
```

### *First Deploy*
首先关联远程仓库和本地仓库：
```powershell
git remote add origin https://github.com/Kiritoeo/tekapo-blog.git
```
提交代码：
```powershell
git add .
git commit -m "First auto deploy"
## 重命名主分支为 main 确保 push 可以成功
git branch -M main
## 设置上游分支 并推送代码
git push -u origin main
```

## 写在最后

{{< quote >}}
如果你跟着上述的步骤，一步一步走到了这里，  
那么我相信你已经成功在你的域名下看到了博客内容了，恭喜你！  
或许过程会有一点曲折... 因为 *hugo-theme-next* 主题配置起来稍微复杂了一点，  
但是相信你一定可以通过 AI 和 Google 的帮助，解决遇到的问题。
希望这篇文章能帮助到和我一样想要搭建个人博客的朋友们，  
希望你们都能够在互联网的宇宙中，拥有属于自己的小小星球。🌍
{{< /quote >}}






