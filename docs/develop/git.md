# Git

## 配置Github SSH Keys
在使用github时，由于一些原因，HTTPS连接经常为TIMEOUT，需要反复尝试clone。github的大部分代码都支持了SSH连接，目前使用来看，都很顺畅，但是相比HTTPS需要简单配置一下`SSH`公钥。

在github账户设置的`SSH and GPG key`中，新建`SSH keys`，拷贝生成的`id_rsa.pub`，考虑到多台机器，可以设定Title用来区别。公钥是以`ssh-rsa`开头，生成时设定的comment结尾的字符串。

```bash
ssh-keygen -t rsa -b 2048 "comment"
```

## 提交PR

   **提交PR前的第一步，配置用户的姓名的邮箱，也就是签名，否则PR提交之后的作者是Unknown**

1. 首先在 Github 上fork项目至自己的仓库，这样将会有完全修改的权限

2. `clone`至本地后， 此时`git remote -v`将会看到自己的仓库地址
    
```bash
origin	git@github.com:robbinlol/jhrwiki.git (fetch)

origin	git@github.com:robbinlol/jhrwiki.git (push)
```
3. 更新代码前一定要和远程保持更新，如果是一个更新频繁的项目的话，此时可能会提示无法合并，因为本地产生冲突，使用`git stash`暂存此次更改

4. 正常更新代码的三个操作，`git add`, `git commit --signoff -m "comment"`, `git push origin master`，之后可以使用`git tag`指令，方便记录提交，而不是通过`git log`或者`git reflog`查找不那么移动的哈希id1

5. 如果两次提交之间只有小的变动，如果作为两次提交，操作历史的图将会不那么好看，可以考虑`git rebase`指令，相当于重新定位`git HEAD`的位置，并把两次的提交合并

6. 最后，如果真的因为弄乱HEAD造成分离，可以考虑`git reset`，`reset`默认的程度是`--mixed`,保留源码，回退了Index（也就是add 操作的暂存区）和commit，此外还有`--soft`和`--hard`两种方式，分别为保留了源码和Index以及完全回退到指定版本
```bash
git reset HEAD^2 # 回退两个版本

git reset commitID # 回退到指定版本
```

## **git**操作技巧

- 取消`staging`区域的文件
```bash
git restore --staged files
```

- 创建`.gitignore`文件
   直接说明需要忽略的规则
```bash
*.cc
*.py
src/
include/
lib/
```

- `git merge`合并分支以及手工解决冲突
```bash
git checkout A
git merge B
# 出现冲突的话，手动删除不一致的地方，再进行后续操作
```
