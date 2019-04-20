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