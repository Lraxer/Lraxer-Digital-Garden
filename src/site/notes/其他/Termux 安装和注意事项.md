---
{"dg-publish":true,"permalink":"/其他/Termux 安装和注意事项/","tags":["android"]}
---


## Termux 是什么

Termux 是运行在 Android 上的 terminal。不需要root，运行于内部存储（不在SD卡上）。它自带了一个包管理器，可以安装许多现代化的开发和系统维护工具。

我个人使用中，主要就是安装 `git` 进行一些同步，使用 Termux: Widget 创建快捷命令。
## 安装

**不要通过 Google Play 商店安装。** Termux 的本体及其插件，全部通过 [F-Droid](https://f-droid.org/en/packages/com.termux/) 安装或 Github release 安装，不要本体使用 Github 版本，插件使用 F-Droid版本，否则签名对不上会导致无法使用。

## 包管理器和其他配置

安装完成后可以通过以下命令更新 package:

``` shell
pkg update
```

参考教程更换镜像源：[Termux 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/termux/)

安装软件

``` shell
pkg install git openssh termux-tools
```

获取存储访问权限
{ #b25a76}


``` shell
termux-setup-storage
```

## 使用 `git` 时要注意的问题

如果把 repo 直接建在 Termux 的 home 中，安卓系统的其他 app 往往是难以访问 termux 自己的那个空间的。所以最好 [[其他/Termux 安装和注意事项#^b25a76\|获取存储访问权限]] 后把 repo 建在手机存储中，例如 `~/storage/shared/data` 目录下。 clone repo 后，可能会遇到无法 pull, push, commit 的问题，报告如下错误：

```
fatal: detected dubious ownership in repository at '/storage/emulated/0/data/<repo_name>'
To add an exception for this directory, call:
		git config --global --add safe.directory /storage/emulated/0/data/<repo_name>
```

产生这个问题的原因是当前用户和 repo 中文件的所有者不一样。这个是 Termux 和系统进行交互产生的问题，只要可以确保你的系统中没有恶意程序通过这种方式破坏你的 repo ，就可以按照建议的命令做。

``` shell
git config --global --add safe.directory /storage/emulated/0/data/<repo_name>
```

## 使用 Termux Widget

按照 [[其他/Termux 安装和注意事项#安装\|安装]] 所讲的，选择正确的插件来源进行安装。

在家目录中新建 `.shortcuts` 文件夹。然后在里面添加想用的 shell 脚本。

以我进行 logseq 同步的脚本为例。

使用 `read -p` 起到暂停的效果。否则脚本运行完成自动退出，看不到结果。参考 [[其他/如何在 bash 中暂停\|如何在 bash 中暂停]] 。

- 从远程仓库拉取到本地 `pull-graph.sh`

	```shell
	#!/usr/bin/bash
	set -xe
	cd ~/storage/shared/data/LogseqNote
	git pull
	read -p "pull repo"
	``` 

- 检查本地仓库的更改 `repo_status.sh`

	``` shell
	#!/usr/bin/bash
	set -xe
	cd ~/storage/shared/data/LogseqNote
	git status
	read -p ""
	```

- 清空本地的更改 `restore-graph.sh` 。因为我只用手机阅读，不用来写作，为了避免出现冲突使用这个脚本。不过这个脚本只能清除新建的文件，不能清除经过修改的文件。最好还是直接 `git reset --hard HEAD` 比较好，反正移动端不会 push 代码。

	``` shell
	#!/usr/bin/bash
	set -xe
	cd ~/storage/shared/data/LogseqNote
	git clean -fd
	read -p "clean repo"
	```

在桌面上添加这个桌面小工具。下面是显示效果。

![Pasted image 20221106150507.png|400](/img/user/00%20Attachments/Pasted%20image%2020221106150507.png)