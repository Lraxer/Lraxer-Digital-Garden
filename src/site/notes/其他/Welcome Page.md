---
{"dg-publish":true,"permalink":"/其他/Welcome Page/","tags":"gardenEntry"}
---


## Hi There!

这是由 Obsidian 个人知识库中抽取一部分内容，出于知识共享的目的进行发布的网站。

可以通过左侧边栏，通过传统的树形结构，寻找对应的条目。也可以通过右侧的本地网络图谱，寻找与本条目关联的更多内容。

网站构建采用了 [GitHub - oleeskild/obsidian-digital-garden](https://github.com/oleeskild/Obsidian-Digital-Garden) 的主题和 Digital Garden 插件，目前还有一些小问题，比如似乎不支持 tag 的显示和搜索，有一些需求也需要自己 DIY，但总体来说是一个很有潜力的工具。

## 使用注意

避免出现类似连续两个 `{` 的情况，包括代码块中。根据 [Troubleshooting - Escape Contents | Hexo](https://hexo.io/docs/troubleshooting.html#Escape-Contents) 的解释，这会导致 Nunjucks 的渲染出问题，要想避免这种渲染，还需要加 raw tag。因此最好的办法是在 `{` 和 `%` 之间加空格。例如 `{ {` 和 `{ %` 就不会构建失败了。