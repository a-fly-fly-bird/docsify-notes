# 起步

## 关于版本控制

​	版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。

### 分类

- 本地版本控制系统
- 集中化的版本控制系统
- 分布式版本控制系统

### Git历史

​	Git由Linux的创造者Lunus创造。

### Git是什么

1. 直接记录快照，而非差异比较

​	其他很多版本控制系统都是记录两个版本的差异，而在 Git 中，每当你提交更新或保存项目状态时，它基本上就会对当时的全部文件创建一个快照并保存这个快照的索引。为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。

**Git 更像是一个小型的文件系统。**

2. 近乎所有操作都是本地执行

3. Git保证完整性

   Git中的所有数据在存储前都会计算哈希值（校验和），并且以校验和来引用。

4. Git 一般只添加数据

### 三种状态

1. 已修改（modified）
2. 已暂存（staged）
3. 已提交（committed）

## 安装Git

MacOS：`brew install git `.

### 配置

Git有三个等级的配置文件：

1. system
2. user
3. local

优先级从上到下依次提高。

第一次使用Git前需要先配置：

```sh
git config --global user.name "terry"
git config --global user.email "abc@qq.com"
```

# Git基础

## 已追踪和未追踪

工作目录下的文件状态：已跟踪 或 未跟踪。

以跟踪的文件就是已经纳入Git版本控制系统的文件，在上一次的快照中有它们的记录。

除此之外，就是未跟踪的文件。

## 忽略文件

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。

规则如下：

- 支持**标准的glob模式**，递归应用在整个工作区。（根据维基百科的介绍，在计算机编程中 glob 模式表示带有通配符的路径名，是简化了的正则表达式）
- 匹配模式可以以（/）开头防止递归。
- 匹配模式可以以（/）结尾指定目录。

多级目录可以有多个忽略文件，同样是越靠近源文件的优先级越高。

## 删除文件

```sh
git rm xxx.file
```

上述命令会从已跟踪清单中移除，并从工作目录中移除该文件。

### 忘记添加`.gitignore`文件

`git rm --cached xxx.file`

git rm 命令后面可以列出文件或者目录的名字，也可以使用 glob 模式。

## 移动文件

Git能够推断出是改名而不是新文件。

可以通过`git mv file_from file_to**`或者**

```sh
mv file_from file_to
git rm file_from
git add file_to
```

两种方式Git都能意识到这是一次重命名。

## 查看提交历史

命令：`git log`

差异对比还是GUI工具好用，比如VS Code插件Git History。

比较有用的命令：`git log --oneline`

### 查看文件的提交记录

```sh
git log -- filename_or_path
```

## 撤销操作

实践下来，撤销有很多个命令：

- `git commit --amend`
- `git revert`
- `git reset`

`git commit --amend`可以修改提交信息，如果暂存区有新的文件，也会同时提交。

> “当你在修补最后的提交时，与其说是修复旧提交，倒不如说是完全用一个 新的提交 替换旧的提交，
> 理解这一点非常重要。”
>
> “修补提交最明显的价值是可以稍微改进你最后的提交，而不会让“啊，忘了添加一个文件”或者
> “小修补，修正笔误”这种提交信息弄乱你的仓库历史。”

## 取消暂存文件

如何把暂存区的文件重新放回工作区？

`git reset HEAD filename`

## 撤销对文件的修改

`git checkout -- file`

## 远程仓库的使用

如果你使用 clone 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。

查看远程仓库

`git remote -v`

### 添加远程仓库

`git remote add name url`

### 从远程仓库中抓取和拉取

`git fetch `

这个命令会访问远程仓库，从中拉取所有你还没有的数据。

必须注意 git fetch 命令只会将数据下载到你的本地仓库——它并不会自动合并或修改你当前的工作。当准备好时你必须手动将其合并入你的工作。

### 重命名远程分支

`git remote rename from to`

## 打标签

Git可以把历史中的某一提交打上标签，表示很重要。其实也相当于索引。

### 列出标签

`git tag`

Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）。

轻量标签只是特定提交的引用，而附注标签相当于一个新的提交，有自己完整的信息，可以被校验。

### 附注标签

`git tag -a tag_name -m "info"`

`git show`命令可以查看标签和对应的提交信息。

### 轻量标签

`git tag tag_name`

### 后期打标签

`git tag -a tag_name commit_id`

### 共享标签

默认情况下，git push 命令并不会传送标签到远程仓库服务器上。

`git push origin tag_name` or `git push origin --tags`

### 删除标签

`git tag -d tag_name`

要删除远程标签:

`git push origin --delete tag_name`

### 检出到标签

`git checkout tag_name`

检出会导致分离头指针（detached HEAD），不要在检出的分支上新增修改。

# Git分支

Git鼓励在工作流程中频繁地使用分支与合并。

## 分支简介
在提交时，Git会保存一个提交对象。提交对象包含一个指向内容快照的指针，以及作者的姓名、邮箱、输入信息以及父对象指针。

暂存操作会为每一个文件计算校验和，然后把文件快照保存到Git仓库中。

提交时，Git 会先计算每一个子目录（本例中只有项目根目录）的校验和，
然后在 Git 仓库中这些校验和保存为树对象。随后，Git 便会创建一个提交对象，
它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。

现在，Git 仓库中有五个对象：三个 blob 对象（保存着文件快照）、一个 树 对象
（记录着目录结构和 blob 对象索引）以及一个 提交 对象（包含着指向前述树对象的指针和所有提交信息）。

### 首次提交的结构
![alt text](<_media/CleanShot 2024-02-15 at 13.14.54.png>)

### 后续结构
![alt text](<_media/CleanShot 2024-02-15 at 13.22.31.png>)

Snapshot 就是树对象。

## 分支创建
`git branch branch_name`

这会基于HEAD指向的提交对象创建一个新的指针。

## 分支切换
`git switch branch_name`

## 检出分支
`git checkout branch_name/commit_id`

作用是将`HEAD`指向对应的分支或者提交记录。

`git branch`输出的星号（*）就代表目前检出的分支（HEAD指针所指向的分支）。

## 暂存更改
`git stash`

取出更改: `git stash pop`

## 合并分支
`git merge branch_from`

这个命令会把`branch_from`的更改合并到当前分支。

### fast-forward
新的提交如果是在当前的提交记录上的更改(当前HEAD指向的提交对象是新提交的直接祖先)，那么只需要将指针直接移动到新的提交对象上即可，因为只需要移动指针，所以形象地称为“快进”（fast-forward）。

### 新建和删除分支的时机
修bug会新建hotfix分支，如果bug改好了合并之后就可以删除hotfix分支了，保持分支的干净。

### 合并提交
![alt text](<_media/CleanShot 2024-02-15 at 13.57.04.png>)

这种情况，Git会将三方合并的结果做一个新的快照并创建新的提交对象指向它。

![alt text](<_media/CleanShot 2024-02-15 at 13.58.37.png>)

## 冲突解决
如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们。

这个时候需要你先手动解决冲突然后再合并。

`git status`可以查看因冲突而为合并的文件。

解决冲突后需要`git commit`来完成**合并提交**。

## 分支开发工作流
随着你的提交而不断右移的指针。
稳定分支的指针总是在提交历史中落后一大截，而前沿分支的指针往往比较靠前。

### 推送

![alt text](<_media/CleanShot 2024-02-15 at 14.12.08.png>)

因为一个分支很可能有多人在协作开发，所以

这种情况需要`git merge origin master`来将远程分支的更改同步到本地分支上。

