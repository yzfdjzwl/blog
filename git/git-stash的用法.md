## 使用`git stash`

`git stash`的使用场景是这样的: 当你正在你的分支下进行开发时，这时候你可能需要切换到你的另一个分支去，你可能要`pull`新的代码下来，但是你又不想添加无用的`commit`。这个时候你就要用到了`git stash`, 它的作用是保存当前正在进行的工作，它会将当前工作压入栈中。

### 基本使用

```
git stash
// do other things
git pop

// or you can do this
git stash save "I just want to save this work and fix my other bug"

// list my stash stacks
git stash list

// or maybe you will see this
stash@{0}: On master: I want to save my work and fix my bug
stash@{1}: On master: save 1

// 0 is the top of stack

// and you will do this to get 
git stash apply stash@{1}

// or you will do this to get the top of stack
git pop

// maybe you need this to clear stack
git stash clear 

// and last, you maybe need this
git stash --help
```