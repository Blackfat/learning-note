 ### git基本命令

#####  git status

查看版本库中是否有需要添加或提交的文件

##### git diff

查看修改的内容

##### git add filename

往版本库中添加文件(包括新增，修改)，一次可以提交多个文件,实际上就是把文件修改添加到暂存区

##### git rm filename

往版本库中删除文件

##### git commit

往版本库中提交文件,实际上就是把暂存区的所有内容提交到当前分支

##### git log  --pretty=oneline --abbrev-commit

查看提交历史，方便确定回退到哪一个版本

##### git reflog

查看命令历史，方便确定回到未来的哪一个版本

##### git reset --hard commit_id

用HEAD表示当前版本，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100

##### git diff HEAD -- filename

查看工作区和版本库里面最新版本的区别

##### git checkout -- filename

将文件的修改撤销

+ 自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态
+ 已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态

##### git reset HEAD filename

把暂存区的修改撤销掉（unstage），重新放回工作区

##### git checkout .

放弃本地修改，没有提交的可以回到未修改前版本

##### git clean -df

从工作目录中移除没有track的文件,-d表示同时移除目录,-f表示force

##### git remote add origin git@server-name:path/repo-name.git

关联一个远程库

##### git push -u origin master

把当前分支`master`推送到远程,第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令

##### git clone git@server-name:path/repo-name.git

从远程仓库克隆

##### git checkout -b branchname

创建分支并切换相当于`git branch branchname`和`git checkout branchname`

##### git branch

查看所含有分支

##### git merge branname

用于合并指定分支到当前分支

##### git merge --no-ff -m "merge with no-ff" branchname

`--no-ff`参数，表示禁用Fast forward

#####  git branch -d branchname

删除分支(已经合并)

##### git branch -D branchname

删除分支(强行删除)

##### git log --graph --pretty=oneline --abbrev-commit

查看分支的合并情况

##### git stash

将工作现场保存起来，等以后恢复现场后继续工作

##### git stash pop

恢复工作现场，同事把stash内容也删了相当于`git stash apply`

和`git stash drop`

##### git tag version commitId

git打标签

 ##### git tag -a version -m desc commitId

创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字

##### git关联远程分支

git  branch	--set-upstream-to origin/远程分支名（相对于当前分支）



