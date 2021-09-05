## 🐥 Git使用教程

### Git简介

#### 安装

Git安装完成之后，需要设置，在命令行输入：

~~~
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
~~~

因为Git是分布式版本控制系统，所以每个机器都必须自报家门 

使用了`–global`参数，用了这个参数表示这台机器上的所有仓库都会使用这个配置

#### 创建版本库

版本库又叫仓库，英文名repository，可以简答理解为一个目录，这个目录里的所有文件都可以被Git管理起来，每个文件的修改删除git都会跟踪，以便任何时候都可以追踪历史，或者在将来还原

使用步骤：

1. 新建文件夹代表这个仓库 最好是路径中不包含中文
2. 打开文件夹 并执行`git init`把这个目录变成Git可以管理的仓库
3. 把文件添加到版本库
   - 执行`git add file.xxx`告诉Git将文件添加到仓库中去。
   - 执行`git commit -m "本次提交说明"`告诉Git将文件提交到仓库中
   - add可以多次使用，commit一次可以提交多个文件

### 时光穿梭

执行`git  status`查看当前仓库的状态，如果想要查看具体修改了什么，可以使用`git diff 文件名`

#### 版本回退

每一次修改要执行`git add “filename”`和`git commit -m` 

当修改了很多次之后想要查看修改记录可以使用`git log`查看修改日志

如果想要回退到某一个版本可以执行`git reset  --hard HEAD^`

在Git中，用`HEAD`表示当前版本，也就是最新的提交`1094adb...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

#### 工作区和暂存区

**工作区**：在电脑中能够看到的目录就是工作区

**版本库**：

工作区中有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。

Git版本库中存放了很多东西，其中最重要的就是`stage`（或者叫index的暂存区），还有Git为我们自动创建的第一个分支`master`,以及指向`master`的一个指针叫``HEAD`

![image-20210905150233061](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210905150233061.png)

将文件提交到版本库中添加的时候是分两步执行的：

1. `git add`将文件添加到暂存区`stage`
2. `git commit`提交更改，实际上就是将暂存区的文件提交到当前分支

当执行了commit之后，文件就被提交到了分支中，暂存区中就不存在文件了

#### 管理修改

每一次的修改都需要进行add和commit，不然多次修改有可能不会生效，例如第一次修改的时候执行了`git add `，然后这时候继续修改文件，最后执行`commit`这时候文件记录的修改只有第一次而没有第二次，因为commit的是在暂存区中的内容，但是暂存区中的内容是第一次add的，因此第二次的修改不会生效，如果需要第二次修改的生效，那么需要在第二次的时候继续执行add

#### 撤销修改

执行`git checkout --文件名`可以去丢弃`工作区`的修改

如果是修改了工作区的内容而且添加到了暂存区，想丢弃修改，可以先执行`git reset HEAD<file>`就回到上面的场景

如果修改了文件到版本库，想要撤销本次修改，可以进行版本回退，前提是没有推送到远程仓库

### 远程仓库

#### 添加远程库

可以在Github或者Gitee中建立一个仓库然后和本地的仓库关联，之后就可以将本地的提交到远程中去

要关联一个远程库。需要首先在远程中建立一个仓库，然后使用命令`git remote add origin git@server-name:path/repo-name.git`；

关联之后使用命令`git push -u origin master`

#### 从远程仓库克隆

`git  clone`

### 分支管理

> 创建一个属于自己的分支过后，别人看不到，还继续在原来的分支上正常工作，而你自己在自己的分支上干活，想提交就提交，直到开发完成之后。再一次性提交到原来的分支上，这样既安全又不会影响工作

#### 创建合并分支

对于每一次的修改提交，Git都会把他们创建成一条时间线，这条时间线就是一条分支，这个分支就是主分支，即`master`分支，`HEAD`严格来说不是指向提交，而是指向`master`，master才是指向提交的，所以HEAD指向的就是当前分支，每次提交，master都会向前移动，因此，随着不断的提交，master也会不断的变长。

![image-20210905190339399](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210905190339399.png)

当创建新的分支`dev`之后，Git就新建了一个指针`dev`，指向和master相同的提交，再把`HEAD`指向`dev`,就表示当前的分支在`dev`上、

![image-20210905191043743](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210905191043743.png)

从现在开始，对于工作区的修改和提交就是针对`dev`分支的了。

![image-20210905191210967](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210905191210967.png)

如果在`dev`中完成了工作，那么就可以把dev合并到master中，也就是将master指向`dev`的当前提交，就完成了合并

![image-20210905191342023](https://gitee.com/yan256992/cloudimages/raw/master/img/image-20210905191342023.png)

**具体命令**

1. 创建并切换分支`git checkout -b dev` -b表示创建并切换，相当于以下两条命令`git branch dev,git checkout dev`
2. 使用`git branch`查看当前分支 带*的就是当前的分支
3. 合并分支 `git merge dev`
4. 删除分支 `git branch -d dev`

切换分支还可以使用`git switch -c dev`

**小结：**

- 查看分支：`git branch`
- 创建分支：`git branch <name>`
- 切换分支：`git checkout <name>`或者`git switch <name>`
- 创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`
- 合并某分支到当前分支：`git merge <name>`
- 删除分支：`git branch -d <name>`

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

#### Bug修复

如果在dev分支上正在工作，但是接到master上修复Bug的指令。那么可以使用`git stash`指令，保存当前的工作环境状态，然后在master分支上创建分支，创建之后合并分支，然后使用`git stash apply`回复原来的现场

#### 多人协作

**推送分支**

`git push origin master`,如果需要提交其他分支就将master修改为其他的分支就可以了

**抓取分支**

默认状态下，从远程库中`clone`时，在本地只能看到master分支。此时如果想在其他分支上进行开发的话，就需要在本地创建一个分支与远程的一样

可以执行如下命令`git checkout -b dev origin/dev`,如果提交了新的修改到分支，那么别人提交的时候需要执行git pull拉取最新的修改，然后在提交。否则会发生冲突。

因此，多人协作的工作模式通常是这样：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

### 小结

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。



- rebase操作可以把本地未push的分叉提交历史整理成直线；
- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

### 📚 Reference

廖雪峰：https://www.liaoxuefeng.com/

