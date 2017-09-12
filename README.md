# Git
加入git有两年多的时间。最近领导要求开始团队内分享。基于团队目前还没用过git的情况下。我把之前学习git时做的笔记又重新翻出来。算是温故知新。同时也打算把笔记上传到gitHub上。毕竟能节约硬盘空间的事还是可以想一想。
![img1](img/img1.png)


# Windows上安装Git
[github下载](https://git-for-windows.github.io/) 
[国内镜像下载](https://pan.baidu.com/s/1kU5OCOB#list/path=%2Fpub%2Fgit)

全部按默认选项安装即可，安装完成后，在开始菜单里找到"Git"->"Git Bash"，蹦出一个类似命令行窗口的东西，就说明Git安装成功！

![Git Bash](img/GitBash.png)

安装完成后，还需要最后一步设置，设置名字和Email地址：
````javascript
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
````

备注：`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。


# 工作区和暂存区图解
![git-img](img/git-img.jpg)


# 创建版本库
#### Git初始化
- 新建一个空文件夹或者已有东西的目录也是可以的，目录名（包括父目录）不包含中文。
- 使用命令`git init`初始化一个Git仓库。
````javascript
$ git init
````

#### 添加文件到Git仓库
- 添加文件到Git仓库，分两步：
    * 使用命令`git add <file>`。告诉Git把文件添加到暂存区(Staged)。注意，此命令可反复多次使用，添加多个文件；`<file>` → 指定的某个文件。
    * 使用命令`git commit`，告诉Git把文件提交到仓库区(版本库分支master)。`-m "changes log"` → 本次提交的说明  使用完整命令`git commit -m "changes log"`
    
````javascript
$ git add [file1] [file2]        //添加指定文件到暂存区(Staged)
$ git add .                      //添加当前目录的所有文件到暂存区(Staged)
$ git add -u                     //添加修改和删除，但是不包括新建文件
$ git commit -m "add 3 files."   //提交暂存区(Staged)到仓库区(版本库分支master)
````
    
备注：每次修改，如果不add到暂存区，那就不会加入到commit中。


# 管理修改 
- 要随时掌握工作区的状态，使用`git status`命令。
- 如果`git status`告诉你有文件被修改过，用`git diff <file>`可以查看修改内容。`<file>` → 指定的某个文件。
    * `git diff`,   查看暂存区(Staged)和工作区(本地文件)的差别。  
    * `git diff --cached` 或者 `git diff --staged` ,   查看暂存区(Staged)和当前分支最新commit之间的差别
    * `git diff HEAD`  查看工作区(本地文件)与当前分支最新commit之间的差别    
    
````javascript
$ git diff [file]           //显示暂存区和工作区的差别
$ git diff --cached [file]  //显示暂存区和当前分支最新commit的差别 → (commit 之后暂存区是空的)
$ git diff HEAD             //显示工作区与当前分支最新commit的差别
````


# 查看日志
- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
    * 如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数 `git log --pretty=oneline`
   
````javascript
$ git log                   //查看提交历史
$ git log --pretty=oneline  //简化版历史信息
````
 

<a name="版本回退"></a>
# 版本回退
- HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令 `git reset --hard commit_id`
    * `HEAD^`   上一个版本
    * `HEAD^^ `  上上一个版本 （可一直往上叠加）
    * `HEAD~100`   往上100个版本写100个^比较容易数不过来，所以写成HEAD~100
    * `commit_id`   commit_id （版本号没必要写全，前几位就可以了）
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。 
````javascript
$ git reset --hard HEAD           //放弃工作目录下的所有修改
$ git reset --hard [commit_id]    //将HEAD重置到指定的版本，并抛弃该版本之后的所有修改
$ git reflog                      //显示当前分支的最近几次提交
````


<a name="撤销修改"></a>
# 撤销修改
##### 如果你编写错误，可以使用命令`git checkout -- <file>` 放弃某个文件的所有本地修改，这里有两种情况：
- 一种是当前文件自修改后还没有被放到暂存区，现在撤销修改，就回到和版本库一模一样的状态。
- 一种是当前文件已经添加到暂存区后，又作了修改，现在撤销修改，就回到添加到暂存区后的状态。

总结：总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。
备注：`git checkout -- <file>`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令。

##### 如果你编写错误，还`git add`到暂存区了，可以使用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区
- `git reset HEAD <file>`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

##### 小结时间：
- 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- <file>`
- 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
- 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考 [版本回退](#版本回退)一节，不过前提还是没有推送到远程库。    


# 删除文件
##### 假如你在文件管理器中把没用的文件删了，现在你有两个选择：
- 一是确实要从版本库中删除该文件，那就用命令`git rm <file>`删掉，并且提交到版本库，那么现在，文件就从版本库中被删除了。
- 另一种情况是误删了，但还没来得及执行`git rm <file>`命令，那就执行`git checkout -- <file>`可以把版本库的东西重新写回工作区。
- 但是如果你已经执行`git rm <file>`命令了，这时候执行`git status`会看到"Changes to be committed"，代表就连工作区的该文件也删除了。这个命令相当于同时执行了`删除文件`，`git add <file>`。如果想恢复该文件，可以参考[撤销修改](#撤销修改)一节。

# 添加远程仓库
<a name="新增仓库"></a>
##### 现在的情景是，你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得。
- 登录进你的GitHub，然后找到"New"按钮，创建一个新的仓库。
![one](img/img-2.png)
- 根据你的项目进行相对应的填写。勾选是否要初始化README文件。最后点击"Create repository"按钮确定，就成功地创建了一个新的Git远程仓库。
![two](img/img-3.png)

##### 目前这个远程仓库还是空的。GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。根据提示使用命令`git remote add [shortname] [url]`关联远程仓库。添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。
````javascript
$ git remote add origin https://github.com/xxx/xxx                // HTTPS
$ git remote add origin git@server-name:<path/repo-name.git>      // SSH 
````
备注：有关https跟ssh提交的区别可[查看链接](https://help.github.com/articles/which-remote-url-should-i-use/);

##### 关联后，使用命令`git push`把本地库的内容推送到远程，实际上是把当前分支master推送到远程。
````javascript
$ git push -u origin master 
````
备注：由于远程库是空的，我们第一次推送master分支时，加上了`-u`参数，Git不但会把本地master分支内容推送到远程新master分支，还会把本地master分支和远程master分支关联起来，在以后的推送或者拉取时就可以直接简化命令。


# 克隆远程仓库
##### 如果我们没有本地Git库，一切从零开始，那么最好的方式是先创建远程库，然后，从远程库克隆。新增仓库步骤跟添加远程仓库[新增仓库](#新增仓库)一样。
##### 根据提示使用命令`git clone [url]`从远程库下载一个项目和它的整个历史代码到本地仓库。
````javascript
$ git clone git@server-name:<path/repo-name.git>    //SSH
$ git clone https://github.com/xxx/xxx              // HTTPS
````
备注：该命令会在本地主机生成一个目录，与远程主机的版本库同名。如果要指定不同的目录名，可以将目录名作为`git clone [url] [fileName]`命令的第二个参数。


# 分支管理
#### git 的分支整体预览图如下:
![git-model](img/git-model.png)
##### 从上图可以看到主要包含下面几个分支：
- master： 主分支，提供给用户使用的正式版本。
- develop： 日常开发分支，该分支正常保存了开发的最新代码。
- feature： 具体的功能开发分支，从Develop分支上面分出来的。开发完成后，要再并入Develop。
- release： 预发布分支，它是指发布正式版本之前（即合并到Master分支之前），我们可能需要有一个预发布的版本进行测试。比如说某一期的功能全部开发完成，那么就将 develop 分支合并到 release 分支，测试没有问题并且到了发布日期就合并到 master 分支，进行发布。记得预发布结束以后，还需要合并进Develop分支。它的命名，可以采用release-*的形式。
- hotfix： 修补bug分支。软件正式发布以后，难免会出现bug。这时就需要创建一个分支，进行bug修补。

##### 这里顺便推荐两个关于分支解说的文章:
###### [Git 最佳实践：分支管理](http://blog.jobbole.com/109466/) 
###### [Git分支管理策略](http://www.ruanyifeng.com/blog/2012/07/git.html)
