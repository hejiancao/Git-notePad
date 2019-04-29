# Git-notePad
介绍如何使用版本控制工具git以及遇到一些常见问题如何解决


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





