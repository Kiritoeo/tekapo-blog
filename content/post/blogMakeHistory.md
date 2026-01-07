---
title: "世界，你好"
description: "Hugo建站的基本用法。"
tags:
  - Hugo
  - 建站
---

# 博客搭建

## 一、云服务器、域名购买

### 1. 云服务器信息

### 2.域名信息

`tekapo.wang`

## 二、域名解析

## 三、安装Nginx

```bash
apt update && apt install nginx -y
```

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

## 四、安装Hugo并创建工作目录

```bash
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

```bash
加载个人及系统配置文件用了 677 毫秒。
(base) PS C:\WINDOWS\system32> cd E:
(base) PS E:\> cd MyHugoProject
(base) PS E:\MyHugoProject> cd tekapo-blog
(base) PS E:\MyHugoProject\tekapo-blog> git config --global http.proxy http://127.0.0.1:7890
(base) PS E:\MyHugoProject\tekapo-blog> git config --global https.proxy http://127.0.0.1:7890
(base) PS E:\MyHugoProject\tekapo-blog> git submodule add https://github.com/AmazingRise/hugo-theme-diary.git themes/diary
Cloning into 'E:/MyHugoProject/tekapo-blog/themes/diary'...
remote: Enumerating objects: 1754, done.
remote: Counting objects: 100% (903/903), done.
remote: Compressing objects: 100% (264/264), done.
remote: Total 1754 (delta 660), reused 639 (delta 639), pack-reused 851 (from 1)
Receiving objects: 100% (1754/1754), 6.85 MiB | 4.25 MiB/s, done.
Resolving deltas: 100% (987/987), done.
warning: in the working copy of '.gitmodules', LF will be replaced by CRLF the next time Git touches it
(base) PS E:\MyHugoProject\tekapo-blog>

```

```bash
# 取消配置git全局代理
(base) PS E:\MyHugoProject\tekapo-blog> git config --global --unset http.proxy
(base) PS E:\MyHugoProject\tekapo-blog> git config --global --unset https.proxy
(base) PS E:\MyHugoProject\tekapo-blog>
```

## 五、部署

配置公钥私钥

 ```bash
root@tekapo-server:~# mkdir -p ~/.ssh
root@tekapo-server:~# echo "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHDUIJE1QObZbwwVJBmUwTWp7ftSqb3ZSioreN4XbPN+ blog-deploy" >> ~/.ssh/authorized_keys
root@tekapo-server:~# chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
root@tekapo-server:~# cat ~/.ssh/authorized_keys
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHDUIJE1QObZbwwVJBmUwTWp7ftSqb3ZSioreN4XbPN+ blog-deploy
root@tekapo-server:~# mkdir -p /var/www/html/tekapo-blog
root@tekapo-server:~# nano /etc/nginx/sites-enabled/default
root@tekapo-server:~# nginx -t && systemctl reload nginx
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
root@tekapo-server:~#
 ```







