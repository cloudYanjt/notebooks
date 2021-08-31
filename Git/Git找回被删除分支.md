# Git找回被删除分支
删除一个分支
`git branch -d <branch_name>`
强制删除一个分支
`git branch -D <branch_name>`

显示整个本地仓储的commit，包括所有branch的commit，甚至包括已经撤销的commit。
`git reflog`

Git会自行负责分支的管理，所以当我们删除一个分支时，Git只是删除了指向相关提交的指针，但该提交对象依然会留在版本库中；因此，如果我们知道删除分支时的散列值，就可以将某个删除的分支恢复过来。
`git branch <branch_name> <hash_val>`