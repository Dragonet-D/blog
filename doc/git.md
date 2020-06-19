# git 使用心得

## git 流程图

![](https://user-gold-cdn.xitu.io/2020/6/15/172b390eab77fcbd?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

- `Workspace` 工作区
- `Index / Stage` 暂存区
- `Repository` 仓库区(或本地仓库)
- `Remote` 远程仓库

## 配置 git

1. 配置全局用户

```shell script
git config --global user.name "用户名"
```

2. 配置别名

```shell script
git config --global alias alias.co checkout
```

3. 删除全局配置

```shell script
git config --global --unset alias.xxx
```

## 查看 git 信息

1. 查看系统配置

```shell script
git config --list
```

2. 查看用户配置

```shell script
cat ~/.gitconfig
```

3. 查看当前项目的 git 配置

```shell script
cat .git/config
```

4. 暂存区中的文件

```shell script
git ls-files
```

5. 查看本地 git 命令历史

```shell script
git reflog
```

6. 查看所有 git 命令

```shell script
git --help -a
```

7. 查看当前 HEAD 指向

```shell script
cat .git/HEAD
```

8. git 中 D 向下翻一行, F 向下翻页, B 向上翻页, Q 退出
9. 查看提交历史

```shell script
git log --oneline # 一行一行的显示
        --grep="关键字" # 查找关键字(提交的注释)与关键字有关的记录
        --graph # 图形化显示
        --all # 所有记录显示出来
        --author "username" # 查找这个作者的记录
        --reverse # 提交顺序翻转
        --num # 显示最近的多少条提交记录
        --p # 每次提交所引入的差异(按补丁的格式输出)
        --before= 1 day/a week/1 "2020-06-19" # 查找规定的时间(如: 1天/1周)之前的记录
        --stat # 显示每次更新文件修改统计信息,会列出具体文件列表
        --pretty=format:"xx" # 可以定制要显示的记录格式
```

## Git 常用命令

1. 将本地分支和远程分支进行关联

```shell script
git push -u origin branchName
```

2. 临时将工作区文件的修改保存至堆栈中

```shell script
git stash
```

3. 将之前保存在堆栈中的文件取出来

```shell script
git stash pop
```

#### commit

1. 跳过验证继续提交

```shell script
git commit --no-verify
git commit -n
```

2. 编辑器会弹出上一次提交的信息,可以在这里修改提交信息

```shell script
git commit --amend
```

3. 修复提交, 同时修改提交信息

```shell script
git commit --amend -m "本次提交的说明"
```

4. 加入--no-edit 标记会修复提交但不修改提交信息,编辑器不会弹出上一次的信息

```shell script
git commit --amend --no-edit
```

git commit --amend 既可以修改上次提交的文件内容，也可以修改上次提交的说明。会用一个新的 commit 更新并替换最近一次提交的 commit 。如果暂存区有内容，这个新的 commit 会把任何修改内容和上一个 commit 的内容结合起来。如果暂存区没有内容，那么这个操作就只会把上次的 commit 消息重写一遍。`永远不要修复一个已经推送到公共仓库中的提交，会拒绝推送到仓库`

#### push && pull

1. 不管是否存在对应的远程分支,将本地的所有分支都推送到远程主机

```shell script
git push --all origin
```

2. 等同于 fetch + merge

```shell script
git fetch origin branchName
git merge origin/branchName
```

3. 如果远程主机的版本比本地版本新, 推送时 Git 会报错,要求先在本地做 git pull 合并差异,然后再推送到远程主机.这时,如果你一定要推送,可以使用--force 选项

```shell script
git push --force origin | git push -f origin
```

#### branch

1. 查看远程分支

```shell script
git branch -r
```

2. 查看所有分支

```shell script
git branch -a
```

3. 查看所有分支并带上最新的提交信息

```shell script
git branch -av
```

4. 查看本地分支对应的远程分支

```shell script
git branch -vv
```

5. 新建分支

```shell script
git branch branchName
git checkout branchName
git checkout -b branchName # 新建一个分支并切换过去
git checkout -b BranchName master # 基于master分支建一个分支,并且切换到该分支

# 新建一条空分支
git checkout --orphan emptyBeanchName
git rm -rf .
```

6. 删除分支

```shell script
# 删除本地分支,会阻止删除包含未合并更改的分支
git branch -d branchName
# 强制删除分支,即使包含未合并的更改
git branch -D branchName
# 推送一个空分支到远程分支,其实就相当于删除远程分支
git push origin :远程分支名
git push origin --delete 远程分支名
```

7. 修改分支名

```shell script
git branch -m branchName
```

## merge 三种合并方法

![](https://user-gold-cdn.xitu.io/2020/6/15/172b390eac6f9586?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

```shell script
# 默认fast-forward, HEAD指针直接指向被合并的分支
git merge

# 禁止快进式合并
git merge --no-ff
git merge --squash
```

- `fast-forward`: 会在当前分支的提交历史中添加进被合并分支的提交历史(并不是每次 merge 都会发生快速合并)
- `--no-ff`: 会生成一个新的提交, 让当前分支的提交历史不会那么乱;
- `squash`: 不会生成新的提交, 会将合并分支多次提交的内容直接存到工作区和暂存区,由开发者手动去提交,这样当前分支最终只会多出一条提交记录,不会掺杂被合并分支的提交历史;

## stash

```shell script
# 给本次存储加个备注, 以防时间久了忘记
git stash save "存储"
# 存储未追踪到的文件
git stash -u
# 查看存储记录
git stash list

# 在 Windows 上和 PowerShell 中, 需要加上双引号

# 恢复后, stash 记录并不删除
git stash apply "stash@{index}"

# 恢复的同时把stash 记录也删了
git stash pop "stash@{index}"

# 删除 stash 记录
git stash drop "stash@{index}"

# 删除所有存储的进度
git stash clear

# 查看当前记录中修改了那些文件
git stash show "stash@{index}"

# 查看当前记录中修改了哪些文件的内容
git stash show -p "stash@{index}"
```

## diff

```shell script
# 查看工作区和暂存区单个文件的对比
git diff fileName
# 查看工作区和暂存区所有文件的对比
git diff
# 查看工作区和暂存区所有文件的对比,并显示出所有有差异的文件列表
git diff --stat
```
1, 你修改了某个文件, 但是没有提交到暂存区,这时候会有对比的内容,一旦提交到暂存区,就不会有对比的内容(因为暂存区已经更新)
2, 如果你新建了一个文件,但是没有提交到暂存区,这时候diff是没有结果的

```shell script
# 查看暂存区与上次提交到本地仓库的快照(即最新提交到本地仓库的快照)的对比
git diff --cached/--staged
# 查看工作区与上次提交到本地仓库的快照(即最新提交到本地仓库的快照)的对比
git diff branchName
# 查看工作区与HEAD指向(默认当前分支最新的提交)的对比
git diff HEAD
```

```shell script
# 查看两个本地分支中某一个文件的对比
git diff branchName .. branchName fileName
# 查看两个本地分支所有的对比
git diff branchName .. branchName
# 查看远程分支和本地分支的对比
git diff origin/branchName .. branchName
# 查看远程分支和远程分支的对比
git diff origin/branchName..origin/branchName
```

```shell script
# 查看两个commit的对比
git diff commit1..commit2
```

## remote
```shell script
# 查看所有远程主机
git remote
# 查看关联的远程仓库的详细信息
git remote -v
# 删除远程仓库的"关联"
git remote rm projectName
# 设置远程仓库的"关联"
git remote set-url origin <newUrl>
```
## tag
```shell script
# 默认在 HEAD 上创建一个标签
git tag v1.0
# 指定一个 commit id 创建一个标签
git tag v0.9 f52c633
# 穿件带有说明的标签, 用-a 指定标签名, -m 指定说明文字
git tag -a v0.1 -m "version 0.1 released"
# 查看所有标签
# 注意: 标签不是按时间顺序列出, 而是按字母顺序的
git tag
# 查看单个标签的具体信息
git show <tagname>
# 推送一个本地标签
git push origin <tagname>
# 推送全部未推送过的本地标签
git push origin --tags

# 删除本地标签
# 因为创建的标签都只储存在本地, 不会自动推送到远程
# 所以,打错的标签可以在本地安全删除
git tag -d v0.1
# 删除一个远程标签(先删除本地tag, 然后再删除远程tag)
git push origin :refs/tags/tagname
```
## 删除文件
```shell script
# 删除暂存区和工作区的文件
git rm filename
# 只删除暂存区的文件,不会删除工作区的文件
git rm --cached filename
```
如果在配置.gitignore文件之前就把某个文件上传到远程仓库了,这时候想把远程仓库中的该文件删除,此时你配置.gitignore文件也没有用,因为改文件已经被追踪了,但又不想在本地删除该文件后重新提交到远程仓库,这时候可以使用 git rm --cached filename命令取消该文件的追踪,这样下次提交的时候,git就不会再提交这个文件,从而远程仓库的该文件也会被删除
