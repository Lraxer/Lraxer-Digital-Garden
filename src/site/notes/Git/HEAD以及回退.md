---
{"dg-publish":true,"permalink":"/Git/HEAD以及回退/","tags":["git"]}
---


## HEAD 是什么

> **`HEAD` names the commit on which you based the changes in the working tree.**
> 
> `FETCH_HEAD` records the branch which you fetched from a remote repository with your last `git fetch` invocation.
> 
> `ORIG_HEAD` is created by commands that move your `HEAD` in a drastic way, to record the position of the `HEAD` before their operation, so that you can easily change the tip of the branch back to the state before you ran them.
> 
> `MERGE_HEAD` records the commit(s) which you are merging into your branch when you run `git merge`.
> 
> `CHERRY_PICK_HEAD` records the commit which you are cherry-picking when you run `git cherry-pick`.
> 
> `@` alone is a shortcut for `HEAD`.
{ #dfb31f}


## `HEAD~` 与 `HEAD^`

这两个符号都表示从当前版本开始，以前的某个版本。也都能结合一个数字使用，例如 `HEAD~2` ，`HEAD^3` 。

### git history 的组织结构

首先需要简单了解 git 历史提交的组织方式，git 的历史是非线性的一个有向无环图（directed acyclic graph, DAG），或者说是一个树。一个 commit 就是一个节点，每个节点都可能有多个父节点，这是 merge 导致的结果。

`HEAD~` 指的是 `HEAD` 的第一代祖先的第一个。`HEAD~n` 也就是 `HEAD` 第 n 代祖先的第一个。

`HEAD^` 指的是 `HEAD` 的第一个父节点，`HEAD^` 是 `HEAD^1` 的简写，因此第 n 个父节点就是 `HEAD^n` 。

因此，如果我们想找到 `HEAD` 的第 `n` 代祖先里第一个 commit 的第 `k` 个 parent ，可以先用 `HEAD~n` 找到第 `n` 代祖先的第一个节点，然后再用 `HEAD~n^k` 找到它的第 `k` 个父节点。

在线性的提交历史中，这两个符号的作用是相同的。而且可以注意到， `^` 其实已经覆盖了全部的使用场景。可能是为了更方便才设计了 `~` .

### 例子

父节点的排序是从左到右。

```
G   H   I   J
 \ /     \ /
  D   E   F
   \  |  / \
    \ | /   |
     \|/    |
      B     C
       \   /
        \ /
         A

A =      = A^0
B = A^   = A^1     = A~1
C = A^2
D = A^^  = A^1^1   = A~2
E = B^2  = A^^2
F = B^3  = A^^3
G = A^^^ = A^1^1^1 = A~3
H = D^2  = B^^2    = A^^^2  = A~2^2
I = F^   = B^3^    = A^^3^
J = F^2  = B^3^2   = A^^3^2
```

在 `git log --graph` 里显示是这样的：

```
*   29392c8 (HEAD -> master, tag: A) A
|\
| * a1ef6fd (tag: C) C
| |
|  \
*-. \   8ae20e9 (tag: B) B
|\ \ \
| | |/
| | *   03160db (tag: F) F
| | |\
| | | * 9df28cb (tag: J) J
| | * 2afd329 (tag: I) I
| * a77cb1f (tag: E) E
*   cd75703 (tag: D) D
|\
| * 3043d25 (tag: H) H
* 4ab0473 (tag: G) G
```

注意到 `I=A^^3^` ，也可以用 `~` 和 `^` 混合表示：`I=A~^3~` 。

### 参考资料

[Git - git-rev-parse Documentation](https://git-scm.com/docs/git-rev-parse#_specifying_revisions)

[What's the difference between HEAD^ and HEAD~ in Git? - Stack Overflow](https://stackoverflow.com/a/2222920/19484138)

## 快速查找 parents

如果想要找到 commit ID 为 `e86d7...` 的父节点，可以用下面的命令：

```shell
git log -1 --pretty=%P e86d7
```

用 `%p` 或 `%P` 都可以，区别只是显示的 hash value 更简略还是更全。

更多 `pretty formats` 的用法，参考 [Git - git-log Documentation](https://git-scm.com/docs/git-log#_pretty_formats)