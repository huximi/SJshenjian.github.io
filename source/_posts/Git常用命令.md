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
git add .

# 提交所有的改变至本地仓库
git commit -m '保存博客原文至source分支'

# 将本地仓库与远程仓库相关联，以便push(origin为关联时设置的名称,可任意取值)
git remote add origin git@github.com:SJshenjian/SJshenjian.github.io.git

# 查看关联好的远程分支状态
git branch -r

# 将本质内容push进远程仓库source分支
git remote push blog source # 该blog为与远程关联时设置的名称

# 将本质内容push进远程仓库source分支(若不存在，则会自动创建分支）
git 

```

