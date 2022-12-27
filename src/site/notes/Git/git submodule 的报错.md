---
{"dg-publish":true,"permalink":"/Git/git submodule 的报错/"}
---


## `transport 'file' not allowed` 报错

在使用 `git submodule add` 出现类似下面的错误：

```
	  $ git submodule add /tmp/bar the_bar
	  # or
	  $ git submodule update
	  
	  fatal: transport 'file' not allowed
	  fatal: clone of '/tmp/bar' into submodule path '/tmp/foo/the_bar' failed
```

出现这个错误的原因是，由于 git 2.38.1 中被发现的一个漏洞，`protocol.file.allow` 默认参数被从 `always` 改成了 `user` 。解决这个问题的方式就是在确保安全的情况下临时调整这个参数：

``` shell
	  git config --global protocol.file.allow always
	  #or
	  git -c protocol.file.allow=always submodule update
	  git -c protocol.file.allow=always submodule add ...
```

相关参考：

1. [Bug #1993586 “Cannot add submodule using file transport” : Bugs : git package : Ubuntu](https://bugs.launchpad.net/ubuntu/+source/git/+bug/1993586)
2. [https://twitter.com/jpetazzo/status/1583112279012257797](https://twitter.com/jpetazzo/status/1583112279012257797)
3. [transport: make `protocol.file.allow` be "user" by default · git/git@a1d4f67 · GitHub](https://github.com/git/git/commit/a1d4f67c12ac172f835e6d5e4e0a197075e2146b)
4. [\[Solved\]Installing AUR pkgs (Cemu and Wine-ge-custom) prepare() fails](https://bbs.archlinux.org/viewtopic.php?id=280571)
5. [FS#76255 : Handling of git submodules in PKGBUILDs broken as of git 2.38.1 update](https://bugs.archlinux.org/task/76255)
6. [Git security vulnerabilities announced | The GitHub Blog](https://github.blog/2022-10-18-git-security-vulnerabilities-announced/#cve-2022-39253)
