

## 撤销修改
1. **git checkout -- file**

git checkout -- file 可以丢弃工作区的修改

命令git checkout -- file意思就是，把file文件在工作区的修改全部撤销，这里有两种情况：

一种是file自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是file已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。

>git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令。

2. **git reset HEAD file**
命令git reset HEAD file可以把暂存区的修改撤销掉（unstage），重新放回工作区：

`git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。



