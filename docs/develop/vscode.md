## 在连接服务器进行开发时，vscode配合remote SSH无疑是最主流的做法，但从一个原生vscode——姑且认为是浏览器——到一个赏心悦目的称手的编译器，还需要做出一些配置。

### 文章将不断记录vscode的配置过程。

### 配置SSH
连接远程服务器时，如果每次ssh username@ip，输入密码将会很繁琐，尤其当远程机器不止一台时，很容易忘记密码或者输入错误，因为Linux输入密码时不会显示输入状态。这时可以考虑将ssh生成的公钥放在目标机器上，这样下次可以免密登录。SSH生成秘钥的指令如下

```bash
ssh-keygen -t rsa -b 2048 "comment"
```

默认生成`id_rsa`和`id_rsa.pub`，将`id_rsa.pub`拷贝到目标机器的`.ssh/authorithed_keys`中，vscode支持SFTP，可以直接拖拽，命令行用scp指令发送也可。

需要注意的是`authorithed_keys`的权限为owner的rw，group和others均没有权限，看起来是`rw-------`，由于拖拽可能权限会改变，需要`chmod`一下，这样就可以愉快的免密登录远程服务器了。

### **Vim** YYDS！

### 插件一览
    - git history
    - Markdown All in One、
    - Vim


### 关于**SSH**
一直以来使用dockers环境都要经过跳转机到一个拉镜像快的机器，直到最近使用了docker run中`--net=host`指令，突然想起如果使用ssh端口转发，将会是一件很cool的事，能够在本地机器运行远程机器中的Jupiter Notebook

背景：生产机器：windows（**S**ource），跳转机：Linux（**U**），目标机器：拉docker镜像快的机器（**T**arget），S机器的公钥已经拷贝到机器U和T

1. 远程传输，之前T机器和其他机器之前传输文件，都要经过两次`scp`，先传到跳转机，再进行下一步动作。ssh可以使用跳转命令，免去登录跳转机的麻烦，`scp`同样适用。
```bash
ssh -J U(user@ip) Target(user@ip)
scp -J U(user@ip) file Target(user@ip):/path
```

2. 重头戏，端口转发。**vscode**已经做的很好了，只用动手一点，就可以在本地**localhost:port**来访问跑在容器端口8888的Jupyter了，但这里还要介绍另外两种方法关于中间有中转机的情况。
    - 两条指令实现两次端口转发
    ```bash
    # 第一步，机器U做一次端口映射，将机器T的8888端口映射到机器U的8889端口
    ssh -L 8889:localhost:8888 userName@Target.IP
    # 第二步，机器S做一次端口映射，将机器U的8889端口映射到机器S的12345端口
    ssh -L 12345:localhost:8889 userName@U.IP
    ```
    两步实现了一条链路的连通，所以引出来一个问题，有没有方法可以一步实现？答案是肯定的。
    - 一次指令实现两次端口转发
    启发来源于上面提到的`-J`参数，所以尝试了带上跳转机的端口转发，能够达到目的。
    ```bash
    # 实现了本地 12345端口 -> U.IP:8888 -> I.IP:8888的映射 
    ssh -L 12345:userName@U.IP:8888 userName@T.IP
    ```
 
