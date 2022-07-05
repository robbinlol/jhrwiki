在连接服务器进行开发时，vscode配合remote SSH无疑是最主流的做法，但从一个原生vscode——姑且认为是浏览器——到一个赏心悦目的称手的编译器，还需要做出一些配置。

文章将不断记录vscode的配置过程。

* 连接远程服务器时，如果每次ssh username@ip，输入密码将会很繁琐，尤其当远程机器不止一台时，很容易忘记密码或者输入错误，因为Linux输入密码时不会显示输入状态。这时可以考虑将ssh生成的公钥放在目标机器上，这样下次可以免密登录。SSH生成秘钥的指令如下
  
    ```
    ssh-keygen -t rsa -b 2048 "comment"
    ```
    
    默认生成`id_rsa`和`id_rsa.pub`，将`id_rsa.pub`拷贝到目标机器的`.ssh/authorithed_keys`中，vscode支持SFTP，可以直接拖拽，命令行用scp指令发送也可。

    需要注意的是`authorithed_keys`的权限为owner的rw，group和others均没有权限，由于拖拽可能权限会改变，需要`chmod`一下，这样就可以愉快的免密登录远程服务器了。
