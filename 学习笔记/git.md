### git 基本命令及介绍

- __设置本地 email 和 name__
```bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

- __创建本地仓库__
```bash
$ git init
```

- __查看仓库状态__
```bash
$ git status
```

- __把文件添加到暂存区__
```bash
$ git add readme.txt  # 添加指定文件
$ git add .  # 添加所有文件
```

- __把文件从暂存区提交到当前分支__
```bash
$ git commit -m "wrote a readme file"
```

- __查看文件修改内容__
```bash
$ git diff readme.txt 
```

- __显示从最近到最远的提交日志__
```bash
$ git log
$ git log --pretty=oneline  # 一行显示
```

- __回退版本 - 穿越__
```bash
$ git reset --hard HEAD^  # 回退几个版本就写几个 ^
$ git reset --hard 89a5952f  # 回到指定版本  commit id
```

- __展示每次命令__
```bash
$ git reflog
```

- __丢弃工作区的修改 - 后悔药__
```bash
$ git checkout -- readme.txt  # git add 之前
$ git reset HEAD readme.txt  # git add 之后，但是还没有 commit
```

- __删除文件__
```bash
# git checkout 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”

# 工作区已经删除，并且确定将版本库里的也删除 - 从版本库中删除该文件，并且删除完以后要 commit
$ git rm readme.txt

# 工作区已经删除，但是后悔删除 - 把误删的文件恢复到最新版本
$ git checkout -- readme.txt
```

- __创建远程仓库__
1. 创建SSH Key
2. 配置SSH Key

- __关联远程仓库__
```bash
$ git remote add origin https://github.com/yourgitname/learngit.git
$ git push -u origin master
```

- __克隆远程仓库__
```bash
$ git clone https://github.com/yourgitname/learngit.git
```

- __创建并切换分支__
```bash
$ git branch dev  # 创建 dev 分支
$ git checkout dev  # 切换到 dev 分支
$ git checkout -b dev  # 创建并切换到 dev 分支
```

- __查看所有分支__
```bash
$ git branch
```

- __合并指定分支到当前分支__
```bash
$ git merge dev  # 切换到 master 再 merge
```

- __删除分支__
```bash
$ git branch -d dev
```
