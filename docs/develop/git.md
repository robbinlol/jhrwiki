### Git操作

#### git的SSH连接
在使用github时，由于一些原因，HTTPS连接经常为TIMEOUT，需要反复尝试clone。github的大部分代码都支持了SSH连接，目前使用来看，都很顺畅，但是相比HTTPS需要简单配置一下公钥。

在github账户的设置`SSH and GPG key`中，新建`SSH keys`，拷贝生成的`id_rsa.pub`，考虑到多台机器，可以设定Title用来区别。公钥是以`ssh-rsa`开头，生成时设定的comment结尾的字符串。
