# Git-notePad
介绍如何使用版本控制工具git以及遇到一些常见问题如何解决
[readme.md写法](https://blog.csdn.net/htwhtw123/article/details/77069581)

[参考链接](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)<br>
首先，选择一个合适的地方，创建一个空目录：<br/>
```
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit
```
通过git init命令把这个目录变成Git可以管理的仓库：<br>
```
$ git init
```
把文件添加到暂存区<br>
```
$ git add readme.txt 
```
提交文件并注释
```
$ git commit -m "wrote a readme file" 
```
查看有哪些文件发生改动
```
$ git status 
```
查询文件差异
```
$ git diff readme.txt 
```
### 版本回退：
```
查看提交日志：
$ git log 查看提交日志
$ git log --pretty=oneline 只显示commit_id的日志
$ git reset --hard HEAD^
说明：首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交1094adb...（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

$ git reset --hard 1094a
说明：回退到某个版本之后，又后悔了，想回到最新版本，但是git log 时发现节点已经不见了，此时可以通过commitid来进行跳转版本,如果窗口关闭了找不到commitid，可以通过git reflog查找
```

查看工作区和版本库里readme.txt文件的区别
```
git diff HEAD -- readme.txt
```

### 撤销修改：
```
git checkout -- file 丢弃工作区的修改
说明：git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令

git reset HEAD <file>  把暂存区的修改撤销掉（unstage），重新放回工作区
场景1：当你乱改了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令 git checkout -- file。
场景2：当你不但乱改了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两份，第一步用命令git reset HEAD <file> 回到场景1，第二步按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，使用版本回退，git reset --hard HEAD^,不过前提是没有推送到远程库。
```

### 分支：
分支在实际中有什么用呢？假设你准备开发一个新功能，但是需要两周才能完成，第一周你写了50%的代码，如果立刻提交，由于代码还没写完，不完整的代码库会导致别人不能干活了。如果等代码全部写完再一次提交，又存在丢失每天进度的巨大风险。<br>
现在有了分支，就不用怕了。你创建了一个属于你自己的分支，别人看不到，还继续在原来的分支上正常工作，而你在自己的分支上干活，想提交就提交，直到开发完毕后，再一次性合并到原来的分支上，这样，既安全，又不影响别人工作。<br>
```
Git鼓励大量使用分支
git branch <name> 创建分支
git checkout <name> 切换分支
git checkout -b <name> 创建+切换分支
git branch 查看分支
git merge <name> 合并某分支到当前分支
git branch -D <name> 删除分支
```

### BUG分支：
Git还提供了一个stash功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：<br>
```
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```
首先确定要在哪个分支上修复bug，假定需要在master分支上修复，就从master创建临时分支
修复完成后，切换到master分支，并完成合并，最后删除issue-101分支
现在，是时候接着回到dev分支干活了！<br>
工作区是干净的，刚才的工作现场存到哪去了？用git stash list命令看看
```
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```
一是用git stash apply恢复，但是恢复后，stash内容并不删除，你需要用git stash drop来删除<br>
另一种方式是用git stash pop，恢复的同时把stash内容也删了<br>
```
小结：
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。
如果有多个stash，git stash pop stash@{id} 或者 git stash apply stash@{id}
```

```
git remote 查看远程库的信息
git remote -v  显示更详细的信息
```

### 多人协作
多人协作的工作模式通常是这样：<br>
1.首先，可以试图用git push origin <branch-name>推送自己的修改；<br>
2.如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；<br>
3.如果合并有冲突，则解决冲突，并在本地提交；<br>
4.没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！<br>
5.如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。
  
### 忽略提交
在项目中有些配置文件不需要提交，但是有同学在后面开发中发现在.igonore文件中无论如何都无法忽略某些文件的提交。<br>
原因在这里：<br>
已经维护起来的文件，即使加上了gitignore，也无济于事。----<br>
那么如何解决呢？方式如下<br>
git update-index --assume-unchanged   要忽略的文件夹/文件夹下文件名<br>
比如我要忽略项目下.idea文件夹下所有xml文件,idea下都是xml文件（我用的webstorm）：<br>
git update-index --assume-unchanged   .idea/*.xml<br>
如果要重新恢复提交，使用如下命令：<br>
git update-index --no-assume-unchanged   .idea/*.xml<br>
这样每次提交就不会提交idea下的文件了。<br>



### 从远程仓库克隆
```
$ git clone git@github.com:hejiancao/gitskills.git
```

### 添加远程库
假如在本地创建了一个git仓库，又想在github创建一个git仓库，并且让这两个仓库进行远程同步。
```

登陆github，“Create a new repo”,创建一个新的仓库
$ git remote add origin git@github.com:hejiancao/test.git 这步是关联两个仓库
$ git push -u origin master
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
```


# 常见问题
### 如果发生以下错误，是因为ssh秘钥的问题，需要按照下面步骤重新设置秘钥
```
$ git push -u origin master
ERROR: Permission to michaelliao/learngit.git denied to anna.
```
解决方案：<br>
	1. git bash之后，重新设置名字和邮箱, git config --global user.name "yourname", git config --global user.email“your@email.com"<br>
	2. 切换到C盘，搜索.ssh文件夹，删除known_hosts文件<br>
	3. $ ssh-keygen -t rsa -C "your@email.com" , 直接点回车，说明会在默认文件id_rsa上生成ssh key , 然后系统要求输入密码，直接按回车表示不设密码， 重复密码时也是直接回车，之后提示你shh key已经生成成功。<br>
	4. 然后系统会自动在.ssh文件夹下生成两个文件，id_rsa和id_rsa.pub，用记事本打开id_rsa.pub，全部复制<br>
	5. 打开https://github.com/，登陆你的账户，进入设置,找到"SSH and GPG keys" ,  "New SSH key" ,  把刚刚复制的key粘贴进去，点击"Add SSH key"<br>
	6. 输入命令：ssh -T git@github.com , 然后跳出一推话，输入yes，然后提示你成功了！<br>
	7. 退出git重新进入路径即可<br>
如果需要同步协作，只需要把本地的sshkey告诉管理者，管理者在gibhub上添加你的sshkey即可。<br>

### 在把本地库第一次推送到远程库的时候，如果出现提示
```
Updates were rejected because the tip of your current branch is behind
```
解决方案如下：<br>
	1. git pull origin master --allow-unrelated-histories<br>
	2. git push -u origin master<br>




















