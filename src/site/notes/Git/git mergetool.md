---
{"dg-publish":true,"permalink":"/Git/git mergetool/"}
---


这个配置用来选择分支合并时的冲突处理工具。

## Meld

Meld 是一款图形化的冲突合并工具，因此它不太适用于命令行环境。另外我在 i3 环境下使用的时候，发现启用 `git mergetool` ，编辑完成退出以后，它的退出过程似乎有点久，不知道为什么，KDE 没有这个问题。

### 配置

修改本地的~/. gitconfig 配置文件，加入以下几行配置

```
[merge]
    tool = meld
		conflictstyle = diff3
[mergetool "meld"]
    cmd = meld $LOCAL $BASE $REMOTE --output=$MERGED --auto-merge
```

### 可选配置

配置 git 的 diff 工具, 新建可执行脚本~/meld. sh, 内容如下:

```
#!/bin/sh
meld $2 $5
```

增加可执行权限

```
chmod u+x ~/meld.sh
```

配置 git

```
  git config --global diff.external ~/meld.sh
```

### 使用 meld 解决冲突

冲突产生后，通过 `git mergetool` 打开 meld。

可见界面一共分为 3 栏，左边一栏为你本地当前文件内容，右面一栏为远端服务器上当前文件内容。中一栏为本地当前文件原始内容。修改中间那一栏，保存后就是合并的结果了。

## vimdiff

这是一个命令行的冲突合并工具。`vimdiff` 命令和 `vim -d` 应该是等价的。

`.gitconfig` 配置如下：

```
[merge]
	tool = vimdiff
		conflictstyle = diff3
[mergetool]
	prompt = false
```

在遇到冲突的时候同样通过 `git mergetool` 进入编辑界面。vimdiff 的编辑界面分为 4 部分，分别是：

```
| LOCAL | BASE | REMOTE |
|        MERGED         |
```

简单说，LOCAL 是 current branch 的内容，BASE 是 LOCAL 和 REMOTE 的共同父节点，MERGED 就是合并的结果，最后保存在 repo 里的内容。

### 简单使用

在 MERGED  里，用 `[c` 和 `]c` 分别跳转到前一个、后一个冲突，然后输入 `:diffg <cmd>` 或者 `diffget <cmd>` 来用某一块的内容作为 MERGED 的内容：

```
:diffg RE  " get from REMOTE
:diffg BA  " get from BASE
:diffg LO  " get from LOCAL
```

然后还可以在 MERGED 里面做一些手动的修改。最后用 `:wqa` 退出即可。