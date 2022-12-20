## 在连接服务器进行开发时，vscode 配合 remote SSH 无疑是最主流的做法，但从一个原生 vscode——姑且认为是浏览器——到一个赏心悦目的称手的编译器，还需要做出一些配置。

### 文章将不断记录 vscode 的配置过程。

### 配置 SSH

连接远程服务器时，如果每次 ssh username@ip，输入密码将会很繁琐，尤其当远程机器不止一台时，很容易忘记密码或者输入错误，因为 Linux 输入密码时不会显示输入状态。这时可以考虑将 ssh 生成的公钥放在目标机器上，这样下次可以免密登录。SSH 生成秘钥的指令如下

```bash
ssh-keygen -t rsa -b 2048 "comment"
```

默认生成`id_rsa`和`id_rsa.pub`，将`id_rsa.pub`拷贝到目标机器的`.ssh/authorithed_keys`中，vscode 支持 SFTP，可以直接拖拽，命令行用 scp 指令发送也可。

需要注意的是`authorithed_keys`的权限为 owner 的 rw，group 和 others 均没有权限，看起来是`rw-------`，由于拖拽可能权限会改变，需要`chmod`一下，这样就可以愉快的免密登录远程服务器了。

### **Vim** YYDS！

### 插件一览

    - git history
    - Markdown All in One、
    - Vim
    - better-comments
    - Bookmarks
    - project-manager

### 关于**SSH**

一直以来使用 dockers 环境都要经过跳转机到一个拉镜像快的机器，直到最近使用了 docker run 中`--net=host`指令，突然想起如果使用 ssh 端口转发，将会是一件很 cool 的事，能够在本地机器运行远程机器中的 Jupiter Notebook

背景：生产机器：windows（**S**ource），跳转机：Linux（**U**），目标机器：拉 docker 镜像快的机器（**T**arget），S 机器的公钥已经拷贝到机器 U 和 T

- 远程传输，之前 T 机器和其他机器之前传输文件，都要经过两次`scp`，先传到跳转机，再进行下一步动作。ssh 可以使用跳转命令，免去登录跳转机的麻烦，`scp`同样适用

```bash

ssh -J U(user@ip) Target(user@ip)

scp -J U(user@ip) file Target(user@ip):/path

```

- 端口转发。**vscode**界面操作，就可以在本地访问跑在端口 8888 的 Jupyter 了；命令行模式下做两次端口映射

```bash
# 第一步，机器U做一次端口映射，将机器T的8888端口映射到机器U的8889端口
ssh -L 8889:localhost:8888 userName@Target.IP

# 第二步，机器S做一次端口映射，将机器U的8889端口映射到机器S的12345端口
ssh -L 12345:localhost:8889 userName@U.IP
```
