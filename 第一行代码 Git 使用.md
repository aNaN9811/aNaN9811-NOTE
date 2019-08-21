### Git 时间 ——初始版本控制工具

Git 是一个开源的分布式版本控制工具，初衷是为了更好地管理 Linux 内核

配置身份

```
git config --global user.name "Tony"
git config --global user.email "tony@gmail.com"
```

配置完成后可以使用同样的命令来查看是否配置成功，只需要将最后的名字和邮箱地址去掉即可

然后创建代码仓库（Respository）用于保存版本管理所需信息的地方，进入 BroadcastBest 目录下面，输入

```
git init
```

就可以完成代码仓库创建，仓库创建完成后会在 BroadcastBest 项目的根目录下生成一个隐藏的 .git 文件夹，这个文件夹就是用来记录本地所有的 Git 操作，通过 ls -al 命令来查看，如果你想要删除本地仓库，只需要删除这个文件夹就行了



### 提交本地代码

仓库建立完成后就可以提交代码了，只需要使用 add 和 commit 命令就可以

add 用于把想要提交的代码先添加进来，而 commit 则是真正地去执行提交操作

```java
//添加 build.gradle 文件就可以输入
git add build.gradle

//上面是添加单个文件的方法，添加某个目录只需要在 add 后面加上目录的名字就可以，比如将这个 app 目录下的所有文件都进行添加
git add app

//一次性把所有文件都添加好，只需要在 add 后面加一个点，表示添加所有文件
git add .
```

 BroadcastBestPractice 项目下的所有文件都添加好了，现在提交一下，输入

```java
//commit 命令的后面我们一定要通过 -m 参数来加上提交的描述信息，没有描述信息的提交被认为是不合法的
git commit -m "First commit"
```



### 查看修改内容

查看文件修改情况的方法非常简单，只需要使用 status 命令就可以，在项目的根目录下输入

```java
git status
```

查看更改的内容

```java
git diff
```

如果只想查看 MainActivity.java 这个文件的更改内容，可以使用

```java
git diff app/src/main/java/com/example/providertest/MainActivity.java
```

减号代表删除的部分，加号代表添加的部分



### 撤销未提交的修改

只要代码还未提交，所有修改的内容都是可以撤销的，撤销修改可以使用 checkout 命令

```java
git checkout app/src/main/java/com/example/providertest/MainActivity.java
```

重新运行 git status 检查发现一切的修改都被撤销了

不过这种方法只适用于那些还没有执行过 add 命令的文件，如果某个文件已经被添加过了，这种方式就无法撤销其更改的内容

验证，将那本数的价格改回 55.55，输入 git add，输入 git status 检查，再执行 checkout ，MainActivity 仍然是处于已添加的状态，所修改的内容无法撤销掉

对于已添加的文件，我们应该先对其取消添加，然后才可以使用 checkout 撤回提交，取消添加使用的是 reset 命令

```java
git reset HEAD app/src/main/java/com/example/providertest/MainActivity.java
```



### 查看提交记录

使用 log 命令查看历史提交信息

```java
git log
```

每次提交记录都会包含提交 id 提交人、提交日期以及提交描述这 4  个信息

当提交记录非常多的时候，如果我们只想查看其中一条记录，可以在命令中指定该记录的 id，并加上 -1 参数表示我们只想看到一行记录

```java
git log <id> -1
```

如果想要查看这条提交记录具体修改了什么内容，可以在命令中加入 -p 参数

```java
git log <id> -1 -p
```

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数

```java
git log --pretty=oneline
```



现在，我们回顾一下`readme.txt`文件一共有几个版本被提交到Git仓库里了：

版本1：wrote a readme file

```
Git is a version control system.
Git is free software.
```

版本2：add distributed

```
Git is a distributed version control system.
Git is free software.
```

版本3：append GPL

```
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

当然了，在实际工作中，我们脑子里怎么可能记得一个几千行的文件每次都改了什么内容，不然要版本控制系统干什么。版本控制系统肯定有某个命令可以告诉我们历史记录，在Git中，我们用`git log`命令查看：

```
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

`git log`命令显示从最近到最远的提交日志，我们可以看到3次提交，最近的一次是`append GPL`，上一次是`add distributed`，最早的一次是`wrote a readme file`。

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上`--pretty=oneline`参数：

```
$ git log --pretty=oneline
1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master) append GPL
e475afc93c209a690c39c13a46716e8fa000c366 add distributed
eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0 wrote a readme file
```

需要友情提示的是，你看到的一大串类似`1094adb...`的是`commit id`（版本号），和SVN不一样，Git的`commit id`不是1，2，3……递增的数字，而是一个SHA1计算出来的一个非常大的数字，用十六进制表示，而且你看到的`commit id`和我的肯定不一样，以你自己的为准。为什么`commit id`需要用这么一大串数字表示呢？因为Git是分布式的版本控制系统，后面我们还要研究多人在同一个版本库里工作，如果大家都用1，2，3……作为版本号，那肯定就冲突了。



### 版本回退

首先，Git必须知道当前版本是哪个版本，在Git中，用`HEAD`表示当前版本，也就是最新的提交`1094adb...`（注意我的提交ID和你的肯定不一样），上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在，我们要把当前版本`append GPL`回退到上一个版本`add distributed`，就可以使用`git reset`命令：

```
$ git reset --hard HEAD^
HEAD is now at e475afc add distributed
```

`--hard`参数有啥意义？这个后面再讲，现在你先放心使用。

看看`readme.txt`的内容是不是版本`add distributed`：

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software.
```

果然被还原了。

还可以继续回退到上一个版本`wrote a readme file`，不过且慢，然我们用`git log`再看看现在版本库的状态：

```
$ git log
commit e475afc93c209a690c39c13a46716e8fa000c366 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

最新的那个版本`append GPL`已经看不到了！好比你从21世纪坐时光穿梭机来到了19世纪，想再回去已经回不去了，肿么办？

办法其实还是有的，只要上面的命令行窗口还没有被关掉，你就可以顺着往上找啊找啊，找到那个`append GPL`的`commit id`是`1094adb...`，于是就可以指定回到未来的某个版本：

```
$ git reset --hard 1094a
HEAD is now at 83b0afe append GPL
```

版本号没必要写全，前几位就可以了，Git会自动去找。当然也不能只写前一两位，因为Git可能会找到多个版本号，就无法确定是哪一个了。

再小心翼翼地看看`readme.txt`的内容：

```
$ cat readme.txt
Git is a distributed version control system.
Git is free software distributed under the GPL.
```

果然，我胡汉三又回来了。

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`append GPL`：

```ascii
┌────┐
│HEAD│
└────┘
   │
   └──> ○ append GPL
        │
        ○ add distributed
        │
        ○ wrote a readme file
```

改为指向`add distributed`：

```ascii
┌────┐
│HEAD│
└────┘
   │
   │    ○ append GPL
   │    │
   └──> ○ add distributed
        │
        ○ wrote a readme file
```

然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。

现在，你回退到了某个版本，关掉了电脑，第二天早上就后悔了，想恢复到新版本怎么办？找不到新版本的`commit id`怎么办？

在Git中，总是有后悔药可以吃的。当你用`$ git reset --hard HEAD^`回退到`add distributed`版本时，再想恢复到`append GPL`，就必须找到`append GPL`的commit id。Git提供了一个命令`git reflog`用来记录你的每一次命令：

```
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
e475afc HEAD@{3}: commit: add distributed
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

终于舒了口气，从输出可知，`append GPL`的commit id是`1094adb`，现在，你又可以乘坐时光机回到未来了。



### 小结

现在总结一下：

- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。



### 一个Windows下的批处理脚本：快速提交本地代码到远程git仓库

平时自己会在多个本地git仓库中写代码，然后push到远程（比如GitHub），每次提交代码都要输入`git add -A`、`git commit -m "xxx"`、`git push origin master`，比较麻烦，所以写了一个适用于Windows下的bat批处理脚本来做这件事情，把下面的代码保存为`auto-commit.bat`文件并放到git仓库根目录下，这样每次提交本仓库的代码时只需双击`auto-commit.bat`文件，然后输入commit信息，就可以快速提交代码了。

```bat
:: input commit message
set /p commit_msg=Please input commit message:
:: show git status
git status
:: add all changing
git add -A
:: local commit
git commit -m "%commit_msg%"
:: push to remote repository
git push origin master
:: make a pause 
pause
```



### Git 时间——版本控制工具的高级用法

打开 Git Bash 并进入到 LBSTest 项目的根目录，然后执行提交操作：

```java
git init
git add .
git commit -m "First Commit"
```



### 分支用法

分支是版本控制工具中比较高级且比较重要的一个概念，它主要的作用就是在现有代码的基础上开辟一个分叉口，使得代码可以在主干线和分支线上同时进行开发，且相互之间不会影响

当版本出现问题的时候如果不建立分支的话，你会非常头疼。举个例子，比如你的公司开发了一个软件，推出了 1.0 版本，但是领导马上给你提出新的需求让你投入到 1.1 版本的开发工作当中，过不久发现 1.0 版本有几个重大 bug ，你要立刻修复这些 bug，并重新发布 1.0 版本，但这个时候你会发现你已经根本无法修改这些 bug 了，因为现在 1.1 版本已开发到一半了，如果在现有的基础上修复这些 bug，那么更新的 1.0 版本将会带有一半 1.1 版本的功能。现在你进退两难，但是如果你使用了分支的话，就完全不会存在这个让人头疼的问题了，你只需要在发布 1.0 版本的时候建立一个分支，然后在主干线上继续开发 1.1 版本的功能，当 1.0 版本上发现任何 bug 的时候，就在分支线上进行修改，然后发布新的 1.0 版本，并记得将修改后的代码合并到主干线上。这样的话，不仅可以轻松解决 1.0 版本存在的 bug，而且保证了主干线上的代码也已经修复了这些 bug，当 1.1 版本发布的时候就不会有同样的 bug 存在了

分支的英文名是 branch ，如果想要查看当前的版本库中有哪些分支，可以使用 git branch 这个命令，由于 LBSTest 项目中还没有创建过任何分支，因此只有一个 master 分支存在，这也就是前面所说的主干线

创建一个分支

```java
git branch version1.0
```

再次输入 git branch 检查，发现有一个叫做 version1.0 的分支出现，master 分支前面有一个 “ * ”，说明目前我们的代码还是在 master 分支上的，切换到 version1.0 上只需要使用 checkout 命令即可

```java
git checkout version1.0
```

再次输入 git branch 来进行检查，可以看到我们已经成功把代码切换到 version1.0 这个分支上了

需要注意的是，在 version1.0 分支上修改并提交的代码将不会影响到 master 分支，同样的，在 master 分支上修改并提交的代码也不会影响到 version1.0 分支，因此如果我们在 version1.0 分支上修复了一个 bug，在 master 分支上这个 bug 仍然存在，这是将修改的代码一行行复制到 master 分支上显然不是一种聪明的做法，最好的办法是使用 merge 命令来完成合并操作，如下所示：

```java
git checkout master
git merge version1.0
```

当不需要分支的时候，可以使用如下命令将这个分支删掉：

```java
git branch -D version1.0
```



### 与远程版本库协作

比如现在有一个远程版本库的 Git 地址是 https://github.com/example/test.git ，可以使用以下命令将代码下载到本地

```java
git clone https://github.com/example/test.git 
```

之后你在这份代码的基础上进行了一些修改和提交，接下来借助 push 命令来把本地修改的内容同步到远程版本库上

```java
git push origin master
```

其中 origin 部分指定的是远程版本库的 Git 地址，master 部分指定的是同步到哪一个分支上，上述命令就完成了将本地代码同步到 http://github.com/example/test.git 这个版本库的 master 分支上的功能

Git 提供了两种命令来完成将远程版本库上的修改同步到本地的方法，分别是 fetch 和 pull，fetch 的语法规则和 push 是差不多的

```java
git fetch origin master
```

执行这个命令后，就会将远程版本库上的代码同步到本地，不过同步下来的代码并不会合并到任何分支上去，而是会存放到一个 origin/master 分支上，这时我们可以通过 diff 命令来查看远程版本库上到底修改了哪些东西

```java
git diff origin/master
```

之后再调用 merge 命令将 origin/master 分支上的修改合并到主分支上即可

```java
git merge origin/master
```

而 pull 命令则是相当于将 fetch 和 merge 这两个命令放在一起执行了，它可以从远程版本库上获取最新的代码并且合并到本地

```java
git pull origin master
```


