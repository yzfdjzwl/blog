`git pull`: 取回远程主机某个分支的更新，再与本地的指定分支合并。

用法:
```
git pull <远程仓库> <远程分支名>:<本地分支名>

// 如
git pull upstream master:dev 
```

`git fetch`: 相当于是从远程获取最新版本到本地，不会自动merge。

用法:
```
git fetch <远程仓库> <远程分支名>:<本地分支名>

// 如
git fetch upstream master:dev
git diff dev
git merge dev
```