## Git 常用命令

### Git 配置

```shell
#安装完成后初始化：
$git config --global user.name 'Git的用户名'
$git config --global user.email '注册GitHub的邮箱'

#查看配置信息，确认配置
$ git config --list

#ssh连接GitHub：
#打开git bash
$ ssh-keygen.exe   用以生成公钥和私钥。
#进入公钥所在目录（windows下一般在用户/.ssh目录下）
$ cat id_rsa.pub  #显示公钥。
#复制公钥添加到github-setting-SSH and GPG keys中。
$ ssh git@github.com  #会显示连接成功。
```
### Git 项目操作

```shell
#新拉项目
git clone xxxxxx
#添加fileName文件到暂存区
git add fileName
#添加所有文件到暂存区 
git add --all  或 git add .
#提交到版本库
git commit -m '版本标注'
#推送到远程仓库
git push
#切换到develop分支
git checkout -b develop origin/develop
# 进入你需要修改的commit编辑界面，编辑后保存退出
git commit --amend 

# 1.查看所有分支
git branch -a
# 2.查看当前使用分支(结果列表中前面标*号的表示当前使用分支)
git branch
# 3.切换分支
git checkout 分支名

#回到历史某个版本
git log
#回到某个历史版本
git reset --hard 139dcfaa558e3276b30b6b2e5cbbb9c00bbdca96 

#新建tag
$ git tag v1.6
#列出已有的tag
$ git tag
#同步tag到远程服务器
$ git push origin v1.6
```


### Git stash命令
```shell
git stash save "save message"
#执行存储时，添加备注，方便查找，只有git stash 也要可以的，但查找时不方便识别。

git stash list
#查看stash了哪些存储

git stash show
#显示做了哪些改动，默认show第一个存储,如果要显示其他存贮，后面加stash@{$num}，比如第二个 git stash show stash@{1}

git stash show -p
#显示第一个存储的改动，如果想显示其他存存储，命令：git stash show  stash@{$num}  -p ，比如第二个：git stash show  stash@{1}  -p

git stash apply
#应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即stash@{0}，如果要使用其他个，git stash apply stash@{$num} ， 比如第二个：git stash apply stash@{1} 

git stash pop
#命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1}
git stash pop stash@{0}

git stash drop stash@{$num}
#丢弃stash@{$num}存储，从列表中删除这个存储

git stash clear
#删除所有缓存的stash
```
### Git 分支合并
```shell
#git合并dev分支代码至master分支
#操作步骤
#1.先将dev分支上所有有代码提交至git上，提交的命令一般就是这几个，先复习下：
#2.切换当前分支至主干（master）
git checkout master 
#3.如果多人开发建议执行如下命令,拉取最新的代码
git pull origin master
#4.合并（merge)分支代码
git merge dev
#5.merge完成后可执行如下命令，查看是否有冲突
git status
#6.提交代码至主干（master）
git push
#7.最后切换回原dev开发分支
git checkout dev
```

