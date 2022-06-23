---
title: Git Submodule的一点点小心得
tags:
  - Git
categories:
  - Coding
date: 2022-06-04 19:54:48
---


最近的一次任务中，在项目A需要用到项目B编译出来的二进制文件。

因为项目A不关心项目B的具体实现方式，但又期望能够每次打包时都能把项目B最新的实现打出来。

所以想到了使用Git子模块来管理。

### 如何使用子模块（常用的场景）

子模块是submodule，命令以git submodule开头

#### 添加模块

```shell
git submodule add <origin_url> <submodule_path>
```

敲下命令之后就会把子仓库克隆下来了。

拉下来之后有两个文件需要commit的
- `.gitsubmodules`，它记录了子模块的来源
- 和子模块路径同名的"文件夹"，里面有当前子模块的commit id

#### 更新模块

```shell
cd <submodule_path> # 切换到子模块路径下
git pull # 更新子模块
cd <project_path> # 切回项目路径
git add <submodule_path> # 提交 子模块记录的git commit id
```

注意，如果不提交更新的子模块commit id，在别的地方重新初始化子模块，并不能拉到最新的代码

#### 初始化模块

当主仓库刚拉下来的时候，子模块对应的文件夹是空的，这时候需要初始化一下下

```shell
git submodule init # 用来初始化本地配置文件
git submodule update # 从该项目中抓取所有数据并检出父项目中列出的合适的提交
```

当更新代码时，如果子模块有新的提交记录，也需要手动`git submodule update`一下来获取最新的子模块代码

#### 删除子模块

```shell
git rm --cached <submodule_path> # 删除子模块缓存
rm .gitsubmodules # 删除子模块配置
# 然后删除 .git/config 下对应的子模块信息 "[submodule " 开头的行
# 最后再提交本次修改
```
