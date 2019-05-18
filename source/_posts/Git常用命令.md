---
title: Git常用命令
date: 2019-05-18 08:28:53
category: 工具
tag: Git
---

``` bash
# 在hexo目录下新建本地仓库(当前处于master分支)
$ git init
Initialized empty Git repository in D:/Project/java/hexo/.git/

# 为了便于管理，新建分支source,并从master切换至source分支
## 此步骤为 git branch source(新建分支) git checkout source(切换分支) 的合并
$ git checkout -b source
Switched to a new branch 'source'

# 将当前目录下所有文件加入,以便commit
$ git add .
warning: LF will be replaced by CRLF in package.json.
The file will have its original line endings in your working directory.
...

# 提交所有的改变至本地仓库
$ git commit -m '保存博客原文及配置至source分支'
[source (root-commit) 007ae29] 保存博客原文及配置至source分支
 71 files changed, 7654 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 1.txt

# 查看本地仓库相关状态
$ git status
On branch source
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
  (commit or discard the untracked or modified content in submodules)

        modified:   "source/_posts/Git\345\270\270\347\224\250\345\221\275\344\273\244.md"
        modified:   themes/fexo (modified content, untracked content)

no changes added to commit (use "git add" and/or "git commit -a")

# 查看所有分支(包括本地分支与远程相关联分支) * 号代表当前所有分支
$ git branch -a
* source

# 将本地仓库与远程仓库相关联，以便push(origin为关联时设置的名称,可任意取值)
$ git remote add origin git@github.com:SJshenjian/SJshenjian.github.io.git

# 查看关联好的远程分支状态
$ git remote
origin

# 与远程关联好后，没有执行 git pull，不会看到远程具体分支，如下结果所示(有时不需要pull,仅仅需要push)
$ git branch -a
* source

# 将本地分支source提交至远程仓库source分支
## source:source 第一个source为本地分支 第二个source为远程分支，远程不存在该分支则自动新建
$ git push origin source:source
Counting objects: 88, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (78/78), done.
Writing objects: 100% (88/88), 890.55 KiB | 1.01 MiB/s, done.
Total 88 (delta 0), reused 0 (delta 0)
remote:
remote: Create a pull request for 'source' on GitHub by visiting:
remote:      https://github.com/SJshenjian/SJshenjian.github.io/pull/new/source
remote:
To github.com:SJshenjian/SJshenjian.github.io.git
 * [new branch]      source -> source

# 再次查看所有分支状态(仅显示刚才新push的分支，说明远程分支仅在我们pull或者push后才可看到,且仅能看到相关联的，如远程master不涉及，就没有看到)
$ git branch -a
* source
  remotes/origin/source

# 删除误提交的远程文件夹(不删除本地)
$ git rm -r --cached 1
rm '1/1.txt'

$ git commit -m '删除错误提交'
[source 5726033] 删除错误提交
 1 file changed, 1 deletion(-)
 delete mode 100644 1/1.txt

$ git push origin source:source
Counting objects: 2, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 244 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:SJshenjian/SJshenjian.github.io.git
   007ae29..5726033  source -> source

# 删除远程分支(为了测试，新建一个分支test1)
$ git push origin source:test1

$ git push origin -d test1
To github.com:SJshenjian/SJshenjian.github.io.git
 - [deleted]         test1
```

