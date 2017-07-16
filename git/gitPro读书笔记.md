> 本文为Git Pro读书笔记，所有内容均来自Git Pro

## 1 Git基础

### 1.1 记录每次更新到仓库

在Git里，文件有4种状态，modified, staged, commited, 还有一种状态是untracked。

Git只会暂存运行git add 时的版本，如果文件作了修改，需要运行git add 将最新的版本暂存起来, git add是一个多功能的命令。

1. 用来跟踪。
2. 将新的修改放入暂存区。
3. 还可以用于合并时把有冲突的文件标记为已解决的文件。

#### 1.1.1 查看已暂存和未暂存的更新

`git diff` 比较的是当前文件和暂存区快照的差异。
`git diff --cached(--staged)`比较的是暂存区和之前提交的版本(committed)的差异。

如果改动后，没有暂存，使用git status会看到暂存前后的两个版本。(哪些文件改变了), git diff也是看到暂存前后的两个版本(哪些文件具体改变了什么，比如增加什么代码，删除什么代码)。

#### 1.1.2 提交

git commit提交，每次提交记录的是放在暂存区里的快照，任何还未暂存的仍然保持已经修改状态。
每一次提交操作，都是对项目做一个快照，以后可以回到这个状态, 或者进行比较。

我们可以跳过add步骤，使用git commit -a -m "test", Git会自动自动把已经跟踪过的文件暂存起来一并提交，从而跳过git add步骤。

#### 1.1.3 移除

`git rm README.md` , 会从暂存区里删除，并且工作目录也没有了。

如果删除之前已经修改过并且已经放到暂存区的话，则必须使用`git rm -f README.md`强制删除, 这个删除命令是从git管理仓库里将README删除，并且工作目录里的文件也会被删除掉。

但是如果我们想要从暂存区里删除，但是想要保存工作区里的东西，该怎么办呢？使用`git rm --cached README`。

如果只是使用`rm README.md`， 那么`git status`会提示`Changes not staged for commit`。然后再运行`git rm README.md`会提示此次的操作记录`Changes to be committed... deleted: README.md`。

####  1.1.4 重命名

`git mv README README1`它相当于下面这三条命令。

```
$ mv README README1
$ git rm README
$ git add README1
```

### 1.2 查看提交历史

```
git log

git log -p， 查看提交的内容变化

git log -2, 查看最近2次的提交

git log --stat, 仅显示简要的增加行数修改

git log --pretty=oneline, 改变显示的风格

git log --author=yuzf, 只显示作者为yuzf的人的提交历史
```

还有一点儿其他的内容，但是不重要。

### 1.3 撤销操作

#### 1.3.1 撤销最后一次提交

`git commit --amend`, 此命令将使用当前暂存区提交。如果刚才提交后没有任何改动，直接运行命名，相当于有机会重新编辑提交说明, 一般使用这个命令后再push到远程仓库会这样使用: `git push origin localBranch:remoteBranch --force`。

#### 1.3.2 取消已暂存的文件

不需要硬记, 只需要git status, 会有提示的。`git reset HAED file`会从暂存区里将file取出来。

#### 1.3.3 取消对文件的修改

`git checkout -- someFile`, 这条命令的意思是: 我们将之前的版本(暂存区里的版本)复制出来重写了此文件。这是一条非常危险的命令, 因为一旦用了, 我们之前所做的修改就都没有了。

记住，任何提交到git里的都可以恢复，失去的数据是没有提交的，因为对git来说，它似乎从来没有失去过。

### 1.4 远程仓库的使用

#### 1.4.1 查看远程仓库

`git remote`, 列出远程仓库名。
`git remote -v`, 列出远程仓库名和地址。

#### 1.4.2 添加远程仓库

`git remote add upstream git@gitlab.xxxxxx.com:xx/xx-xx-com.git`, upstream是为远程仓库制定一个名字。


#### 1.4.3 从远程仓库抓取数据

`git fetch [remote-name]`, 此命令会到远程仓库中拉取所有你本地仓库中还没有的数据。

我用下面几个命令来告诉自己:

```
git clone remoteOrigin

git branch 
// dev

git branch -a
/*
    dev
    remotes/origin/dev
    remotes/origin/master
*/

git remote add upstream xxx

git fetch upstream

git branch 
// dev

git branch -a
/*
    dev
    remotes/origin/dev
    remotes/origin/master
    remotes/upstream/dev
    remotes/upstream/master
*/
```

有一点很重要，fetch只是将远段的数据拉取到本地仓库，并不会自动合并到当前工作分支。
而git pull，自动抓取数据下来，然后将远端分支自动合并到本地仓库的当前分支。

#### 1.4.4 推送数据到远程仓库

使用命令`git push remote-name localBranch:remoteBranch` 

#### 1.4.5 查看远程仓库信息

`git remote show [remote-name]`用于查看远程仓库详细信息。

#### 1.4.6 远程仓库的删除和重命名

```
git remote rename origin test // 重命名远程仓库
git remote rm origin // 删除远程仓库
```

### 1.5 打标签

1. 列出现有的标签

```
git tag
```

2. 新建标签

```
含附注的标签
git tag -a v1.0 -m "my 1.0 version"
git show v1.0

轻量级标签
git tag v1.1
```

等等，还有其他的关于标签的。

## 2 分支

在Git中提交时，会保存一个提交对象(commit), 它会包含一个指向暂存内容快照的指针。实际上，master是一个指针，指向最后一个commit对象。而新建分支，就是新创建一个指针，指向commit对象。

### 2.1 何谓分支

[何谓分支](https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E4%BD%95%E8%B0%93%E5%88%86%E6%94%AF), 我认为这一小节尤其的重要。

#### 2.1.1 新建分支

`git branch newBranchName`会创建一个新的分支指针，它将指向当前的commit对象。

那么，Git如何知道在哪一个分支上呢？很简单，Git保存了一个名为HAED的指针(HAED即当前分支的别名)，HEAD指向指向哪一个分支，就在哪一个分支。

#### 2.1.2 切换分支

`git checkout branchName`命令会让我们切换分支(即将HEAD指针和branchName分支指针指向的commit一致), 假如我们切换到一个新的分支, 然后commit后，新的分支就会向前移动一格，同样的，HAED指针仍然指向新的分支。而master分支是位于第二格的。

当切换分支后，工作区也会发生相应的改变, `git checkout -b newBranch`这个命令可以新建分支并切换分支。


#### 2.1.3 基本的分支与合并

如果你正在开发一个新功能，突然接到通知之前的版本有一个bug，现在需要你紧急修复，因此你所需要做的事情如下:

1. git status // on branch newFunction(#53)
2. git checkout master // 切回master, 注意这里，留意暂存区里或者工作目录里，那些还没有提交的修改，它会和你即将检出的分支产生冲突从而阻止Git为你切换分支。因此，切换分支的时候一定要保持一个干净的工作区域, 后面会介绍几种绕过这种问题的方法(分别叫做stashing 和 commit amend)
3. git checkout -b hotfix
4. do something
5. git commit -a -m "fix a bug"
6. git checkout master
7. git merge hotfix // git会提示Fast forward(快进), 由于master分支所在的commit对象是要并入的hotfix分支的直接上游，Git只需要将master分支直接右移，这种合并称为快进。
8. git branch -d hotfix // 删除hotfix分支
9. git checkout #53 // 之前的那个新功能的分支

值得注意的是之前`hotfix`分支的修改内容尚未包含到iss53中来。如果要纳入此次修改，有两种方法:

1. git merge master // 将master合并到#53来
2. 等#53开发完结束后，再将#53分支的更新并入到master里。

#### 2.1.4 分支的合并

当你在#53提交了多个commit后，你的功能已经完成了，此时你想要将当前的分支合并回master分支下，此时的合并就并不能像之前的`Fast forword`, 因为已经产生分叉了。因此，当你执行下面的命令时：

1. git checkout master
2. git merge issue53

会提示`Auto-merging, Merge made by the 'recursive' strategy`, Git的底层操作是这样的: Git会将master和issue53的之前的共同祖先, master的末端，issue53的末端进行一次简单的三方合并，并且对三方合并后的结果重新做了一个新的快照, master会自动指向它的提交对象。这个提交对象比较特殊，它有两个祖先，即master和issue53之前对应的commit。

#### 2.1.5 遇到冲突时的合并

有时候合并并不会十分的顺利，当不同的分支修改了同一文件的同一部分，就会产生冲突。Git就无法将干净的将两者合并到一起。

因此，当发生冲突时，Git作了合并，但是并没有提交，它会停下来等你解决冲突。要看合并的时候哪些文件差生了冲突，可以使用`git status`进行查看。任何包含未解决冲突的文件都会以未合并的状态(unmerged)列出来。

当发生冲突时，一旦你解决了后，并且git add后，就表示冲突已经解决。但是仍然处于合并中，然后commit后，此次合并就结束了。当然，还可以使用`git mergetool`，Git会提供一个可视化的工具来帮助解决冲突。

### 2.2 分支管理

1. `git branch -v`, 如果要查看每个分支最后一次commit信息。
2. `git branch --merged`, 列出已经和当前分支合并过了的分支。
3. `git branch --no-merged`, 列出还没有和当前分支合并过的分支。
4. `git branch -d branchName`, 删除分支。
5. `git branch -D branchName`, 一个还未被合并的分支是不可以删除的，因此如果要删除的话要使用这个命令。

### 2.3 远程分支

使用`git fetch origin`, 来同步远程服务器上的数据到本地, 它会从远程服务器上找到你没有拥有的数据，然后更新你的本地数据库, 然后把origin/master指针移动到最新的位置上。但是它不会将fetch下来的分支合并到当前的分支。

简单的说`git fetch origin`会更新remote索引。可以通过`git branch -a`查看你的仓库的所有分支, 包括fetch下来的分支。

值得注意的是，fetch操作下好新的远程分支之后，你依然无法在本地编辑该远程仓库的分支。换句话说，你不会有一个新的分支(dev)，有的只是一个你无法移动的upstream/dev指针。如果要把该分支的远程内容合并到当前分支，可以运行`git merge upstream/dev`。如果想要一份自己的`dev`分支来进行开发，可以在远程分支的基础上分化出一个新的分支来。

```
git checkout -b localDev upstream/dev
```

这会切换到新建的`dev`本地分支，其内容同远程分支`upstream/dev`一致，这样你就可以继续开发了。

#### 2.3.1 推送本地分支

`git push origin localBranch:remoteBranch`, 将localBranch推送到origin的remoteBranch里。

#### 2.3.2 跟踪远程分支

从远程仓库`checkout`出来的本地分支，称为跟踪分支。跟踪分支是一种和某个远程分支有直接联系的本地分支。在跟踪分支里输入`git push`，Git会自行判断应该向哪个服务器的哪个分支推送数据。同样，在这些分支里运行`git pull`会获取所有远程索引，并把它们的数据都合并到本地分支来。

在克隆仓库时，git通常会自动创建一个master分支来跟踪origin/master.这就是为什么最开始可以使用pull和push 的原因。

当然，也可以随心所欲的跟踪其他分支，可以这样`git checkout -b [分支名] [远程名]/[分支名]`, 如果使用的Git版本是高于1.6.2的，还可以使用`git checkout --track origin/serverfix`这个命令。

#### 2.3.3 删除远程分支

如果你的远程分支已经合并到远程master分支里了，所以远程分支没有用了，那么该如何删除呢？`git push origin :serverfix`, 这样就删除了origin的serverfix分支。

### 2.4 分支的衍合

[分支的衍合(v1)](https://git-scm.com/book/zh/v1/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88)

[分支的变基](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)

后来，我才发现分支的衍合v1里，讲的有错误。

#### 2.4.1 基本的衍合操作

之前，我们用到了整合分支的命令`merge`, 它会进行三方合并。其实，还有另一个选择: 可以在一个分支里产生的变化补丁(A)在另一个分支(B)的基础上重新打一遍，这样，就将一个分支(A)合并到了另一个分支(B)上。

在Git里，这种操作叫做衍合`rebase`, 有了rebase命令，就可以把在一个分支里提交的改变移到另一个分支里重放一遍。 

下面我们看一个例子:

```
git checkout master 
git rebase dev 
```

Git的底层实现实现如下(个人的理解)：

1. 找到master分支与dev分支的共同祖先， 假如为M。
2. 在master分支下，跳到M那个commit对象的位置。
3. 生成dev分支下M对象后面的commit对象的一系列文件补丁(A)。
4. 以master下M位置处为出发点，将文件补丁(A)打在M后面。
5. 然后将master自身的commit对象接在已经打好补丁的commit对象后面。

根据[分支的变基](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%8F%98%E5%9F%BA)，我认为也可以这么去理解。

1. 找到master分支与dev分支的共同祖先，假如为M。
2. 所谓变基，即是改变基底，git rebase dev的意思是将当前master分支的基底改为dev分支的最后一次提交。
3. 然后对比当前分支(master)相对于该祖先的历次提交，提取相应的修改并存为临时文件(L)。
4. 然后以dev最后的一次commit为基底，然后将之前存的临时文件(L)的修改按序应用。 

一般我们使用rebase的目的，是想要得到一个能在远程分支上干净应用的补丁。它和merge的最终结果是一样的，只是得到的commit时间线不一样。

## others

tracking, 如果`git push -u origin master`, 就会将本地的当前分支和origin的master分支绑定到一起，下次作操作的时候，就可以少写参数。这个就叫已经`tracking`了， 如果你不知道你的分支是否已经`tracking`了， 那么`cat .git/config`