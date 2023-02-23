---
{"dg-publish":true,"permalink":"/Git/git rebase 的使用/","tags":["git"]}
---


## 什么是 `git rebase`

用 git 社区教程 [Git - Rebasing](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) 的例子来说明。

假设现在有两个分叉的分支 `master` 和 `experiment` ，如下图。

![Pasted image 20221206215709.png|600](/img/user/00%20Attachments/Pasted%20image%2020221206215709.png)

如果在 `master` 分支 **`git merge`** 了 `experiment` 分支，结果如下图所示。

![Pasted image 20221206220109.png|600](/img/user/00%20Attachments/Pasted%20image%2020221206220109.png)

如果是在 `experiment` 分支上使用 **`git rebase master`** 变基到 `master` 分支：

> 它的原理是首先找到这两个分支（即当前分支 `experiment`、变基操作的目标基底分支 `master`） 的最近共同祖先 `C2`，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件， 然后将当前分支指向目标基底 `C3`, 最后以此将之前另存为临时文件的修改依序应用。

然后再到 `master` 分支执行一次快速合并：

```shell
git checkout master
git merge experiment
```

![Pasted image 20221206220308.png|600](/img/user/00%20Attachments/Pasted%20image%2020221206220308.png)

> 此时，`C4'` 指向的快照就和 merge 中 `C5` 指向的快照一模一样了。 这两种整合方法的最终结果没有任何区别，但是变基使得提交历史更加整洁。 你在查看一个经过变基的分支的历史记录时会发现，尽管实际的开发工作是并行的， 但它们看上去就像是串行的一样，提交历史是一条直线没有分叉。
> 
> 一般我们这样做的目的是为了确保在向远程分支推送时能保持提交历史的整洁——例如向某个其他人维护的项目贡献代码时。 在这种情况下，你首先在自己的分支里进行开发，当开发完成时你需要先将你的代码变基到 `origin/master` 上，然后再向主项目提交修改。 这样的话，该项目的维护者就不再需要进行整合工作，只需要快进合并便可。
> 
> 请注意，无论是通过变基，还是通过三方合并，整合的最终结果所指向的快照始终是一样的，只不过提交历史不同罢了。 变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起。

---

以上就是最基本的 `git rebase` 用法。但需要注意，rebase 对于团队开发来说可能会造成很混乱的 git 分支状态，尤其是当你把一个分支 rebase 到其他分支上，而有其他人正在这两个分支上开发的时候。

我更倾向于使用 `git merge` 去合并不同的分支，只在单分支的 commit 管理上使用 `git rebase` 。

## 单分支 commit 管理

由于种种原因， commit 有时候会比较混乱。我们最好在合并进入重要分支以前，先处理一下自己开发过程中留下的 commit ，尽量做到一个 commit 只处理一件事，回退到任意一个 commit ，代码都能够运行。

要注意的是，rebase 后，被更改的 commit 的提交时间可能也会发生变化。

我们主要使用的命令是 `git rebase -i <commit id>` 。假设最近 5 个 commit ID （从上到下为由新到旧）分别是

```
commit 6ec616...
commit c51838...
commit d7dd5f...
commit 5d052c...
commit 4f5075...
```

如果我们要处理最近 4 个 commit（`6ec616` 到 `5d052c`），那么这里的命令就是 `git rebase -i 4f5075` ，填入的 commit ID 是最早要处理的 commit 前一个的，代表要调整这个 commit 之后的 commit 。

另外，建议最好每一次使用 `git rebase -i` 只做一件事，最好不要同时改 commit message 和 squash 这样。

如果改动后不满意，通过 `git reset --hard ORIG_HEAD` 回到之前的状态。

执行命令后会出现类似下面的界面。

```
pick 47e4d8e commit 1
pick ebd05ab commit 2
pick 0d9e92c commit 3
pick c73fc1f commit 4
pick 8c814bf commit 5
pick 106578d commit 6

# Rebase 22216f7..106578d onto 22216f7 (6 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
#         create a merge commit using the original merge commit's
#         message (or the oneline, if no original merge commit was
#         specified); use -c <commit> to reword the commit message
# u, update-ref <ref> = track a placeholder for the <ref> to be updated
#                       to this position in the new commits. The <ref> is
#                       updated at the end of the rebase
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

最上面是最早的 commit ，最下面是最新的。

### 调整 commit 顺序

如果要调整顺序，只需要改变这些行的顺序即可。

### 删除 commit

将要删除的 commit 前面的 `pick` 改为 `drop` 。

### 修改 commit message

把要修改的 commit 前的 `pick` 改为 `reword` **（注意不要在这时就直接改 commit message）** ，保存并退出，会弹出一个新的编辑器窗口，在这个编辑器中修改 commit message 即可。

### 合并 commit

例如要合并 message 为 `commit 4` 和 `commit 5` 的两个提交，把 commit 5 前面的 `pick` 改为 `squash` ，代表把它和上一个 commit 合并。

```diff
pick 47e4d8e commit 1
pick ebd05ab commit 2
pick 0d9e92c commit 3
pick c73fc1f commit 4
-pick 8c814bf commit 5
+squash 8c814bf commit 5
pick 106578d commit 6
```

保存并退出后，会弹出新的编辑器窗口，把旧的删掉，写一个新的 commit message 即可。

### 拆分 commit

例如要拆分 message 为 `commit 4` 的提交，先把前面的 `pick` 改为 `edit` 。

```diff
pick 47e4d8e commit 1
pick ebd05ab commit 2
pick 0d9e92c commit 3
-pick c73fc1f commit 4
+edit c73fc1f commit 4
pick 8c814bf commit 5
pick 106578d commit 6
```

保存并退出，git 会再次应用 message 为 `commit 1` 到 `commit 4` 的提交，然后停下，不使用后续的提交。此时就可以使用 `git reset HEAD^` 或者 `HEAD~` （当然有人也习惯在 Win 或 OS X 下面用 `@` 代替 `HEAD` ，参考 [[Git/HEAD以及回退#HEAD 是什么\|HEAD以及回退#HEAD 是什么]]）把要修改的提交全都 unstage 回去，然后拆成你想要的好几个 commits 。这个过程可以结合 [[Git/分批提交同一个文件的改动\|分批提交同一个文件的改动]] 。

处理完以后，使用 `git rebase --continue` 继续应用后续的提交。