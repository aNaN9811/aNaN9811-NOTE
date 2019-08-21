# Git 命令行

```java
//作用是checkout远程的dev分支，在本地起名为dev分支，并切换到本地的dev分支
$ git checkout -b dev origin/dev

//删除分支
$ git branch -d 分支名称
```

```java
//将工作区保存在暂存区
$ git stash

//查询暂存区
$ git stash list

//将暂存区的恢复到工作区
$ git stash pop

or

$ git stash apply stash@{0}

//删除暂存区
$ git stash drop
```

```java
//查询 Git 历史记录
$ git log

//减少输出的信息
$ git log --pretty=oneline
```

```java
//删除文件
$ rm filename
```

```java
//丢弃工作区的修改
$ git checkout -- filename

//把暂存区的修改撤销掉（unstage），重新放回工作区（已经 add，但未 commit）
$ git reset HEAD filename

//回退上一个版本
$ git reset --hard HEAD^

//上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`

//commit id`是`1094adb...`，指定回到某个版本
$ git reset --hard 1094a

//记录你的每一次命令
$ git reflog
```

```java
//查看状态
$ git status
```

```java
//远程分支和本地分支合并

//取回远程主机（分支）的更新
//所有分支
$ git fetch <远程主机名>//$ git fetch origin
$ git fetch 
$ git fetch <远程主机名> <分支名>

//查看远程版本库上修改了什么东西
$ git diff origin/分支名

//查看远程分支
$ git branch -r
//查看所有分支
$ git branch -a

//创建新分支便于合并远程分支
//同上面的方法
$ git checkout -b dev origin/dev
//此外也可以使用 git merge 或者 git rebase 命令，在本地分支上合并远程分支
$ git merge origin/master
# 或者
$ git rebase origin/master
```

```java
//git强制覆盖本地代码：
$ git fetch --all
$ git reset --hard origin/master
$ git pull
```

Git 显示的内容太长按 q 键结束