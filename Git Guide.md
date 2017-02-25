# 笔记来源
[Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

# command

当前目录作为git仓库  
```git init```  

添加文件到暂存区  
```git add file```   
```git add -f file``` // 跳过忽略配置，强制添加
```git check-ignore -v file``` // 查看哪条忽略规则拦截了该文件

从缓存区提交到分支  
```git commit -m "write a message"```  

查看状态  
```git status```  

差异  
```git diff file```  

日志  
```git log```  
```git log --pretty=oneline```  

命令日志  
```git reflog```  

回滚  
```git reset --hard HEAD^```  
```git reset --hard HEAD^^```  
```git reset --hard HEAD~100```  
```git reset --hard ${versionStr.subStr(0)}```  

重置暂存区  
```git reset HEAD file```  

重置工作区  
```git checkout -- file```  

删除文件  
```git rm file```  

# install & config
site: https://git-scm.com/  

```
git config --global user.name "lixz"
git config --global user.email "lixuzhou@foxmail.com"
git config --global color.ui true
git config --global alias.aliasname <command>
    git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

```.gitignore```文件 [GitHub模板](https://github.com/github/gitignore)

# rule

**file format** : to use text file instead of binary file such as .doc  
**encoding** : utf8  
**notepad++** : UTF-8 without BOM

# Working Directory && stage && branch

**Working Directory**: 工作区  
**.git**: 版本库  
*git add: 需要提交的文件修改通通放到暂存区*  
*git commit: 然后，一次性提交暂存区的所有修改。*
![image](http://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)  
**stage**: 暂存区。Git管理的是修改，而不是文件。执行commit时，只有add到暂存区的修改才会被提交。

# Remote Repository
## Github

```
ssh-keygen -t rsa -C "lixuzhou@foxmail.com"

// create repository 'learngit' on Github

// name romote repo with 'origin' or other
git remote add origin git@github.com:lixuzhou/learngit.git
git clone git@github.com:lixuzhou/learngit.git

// 由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地
// 的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的
// master分支关联起来，在以后的推送或者拉取时就可以简化命令
git push -u origin master    // first time 
git push origin master       // next time

```

# Branch

## 创建、合并、删除

HEAD指针指向当前分支的指针（如master），分支指针指向当前分支的提交（如最新提交）。HEAD、master都是指针。  
创建一个新的分支，就创建了以这个分支名命名的指针，切换分支时，HEAD指向切换后的分支。  

```
// create branch dev and switch to dev
git checkout -b dev
={
    git branch dev
    git checkout dev
}

// list all branches
git branch

// merge dev to current branch
git merge dev
git merge --no-ff -m "merge with no-ff, with a new commit" dev

// delete branch dev
git branch -d dev

// Auto-merging failed because of CONFLICT
// conflict content may be like this:
// <<<<<<< HEAD
// Creating a new branch is quick & simple.
// =======
// Creating a new branch is quick AND simple.
// >>>>>>> feature1

// show merge graph
git log -graph --pretty-oneline --abbrev-commit
```

## 分支策略

### 稳定/（团队）开发
master为稳定版本  
dev为开发版本  
如果有多个团队成员共同开发，每人一个分支，时不时往dev分支上合并  

### Bug分支
当有新的bug需要马上处理的时候，可以先缓存当前工作现场 ```git stash``` ，在需要修复的分支上创建bug分支修复bug，然后合并，再恢复工作现场继续之前的开发。  
```
// coding in branch-dev, issue must be fixed first
git stash

// fix bug in branch-master
git checkout master
git checkout -b issue-101
...fix bug and 'git add'
git commit -m "fix bug 101"
git checkout master
git merge --no-ff -m "merged bug fix 101" issue-101
git branch -d issue-101

git checkout dev
git stash list
git stash apply && git stash drop  =  git stash pop
```

### feature分支
开发一个新的feature时，最好新建一个分支，开发完成再合并。  
如果功能取消，要丢弃一个没有被合并过的分支，可以通过 ```git branch -D <name>``` 强行删除。  

### 团队开发

#### 查看远程库信息
```
git remote
git remote -v
```

#### 创建远程库的分支到本地
从远程库clone时，默认情况下，只能看到本地的master分支  
创建远程```origin```的```dev```分支到本地：```git checkout -b dev origin/dev```

#### push策略 ```git push origin dev```
```master``` 分支是主分支，因此要时刻与远程同步；  
```dev``` 分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；  
```bug``` 分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；  
```feature``` 分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。  

#### push冲突解决 ```git pull```  
如果没有本地分支与远程分支的链接：```git branch --set-upstream <branch> <remote>```，如```git branch --set-upstream dev origin/dev```  
拉取远程版本：```git pull``` 解决冲突，```git commit```，再```git push```  

# tag
tag与HEAD一样，都是指向某个commit的指针。不同的是，HEAD可以移动，tag不可以移动。  
tag可以赋予版本号一个有意义的名称，便于回滚。  

查看所有标签：```git tag```  
打标签：```git tag <name> [commit_id]```  或者加说明文字```git tag -a <name> -m "decription" [commit_id]```  
查看标签信息：```git show <tag_name>```  
删除标签：```git tag -d <tag_name>```  
推送某个标签到远程：```git push origin <tag_name>``` ```git push origin --tags```  
在远程库删除标签：```git push origin :refs/tags/<tagname>```  
