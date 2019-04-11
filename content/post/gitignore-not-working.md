---
title: ".gitignore 不起作用"
date: 2019-04-11T22:21:04+08:00
draft: false
tags: ["git"]
categories: "devops"
toc: false
---
&emsp;有时候你不想让你的文件提交，git似乎能够"知道"他们，即使你已经把它们加入.gitignore中了。  
&emsp;**注意:**先提交，以免丢失。  
&emsp;然后，运行一下命令，在你的git仓库的根目录。
```shell
git rm -r --cached .
git add .
git commit -m "fixed untracked files"
```

注：这篇文章翻译自:https://dev.to/onurbilgin/gitignore-is-not-working-58c9

