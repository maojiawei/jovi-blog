---
title: "Git入门-命令与操作"
date: 2019-04-03T20:26:00+08:00
draft: true
categories: "devops"
tags: ["git"]
toc: false
---
&emsp;Git是非常简单，易学的。这篇文章的主旨是为了排除你对先前对Git的恐惧。我确定在阅读这篇文章之后，你可以了解Git的基本使用。  
&emsp;我希望你已经了解了Git的一些基本概念和术语，在之前的[文章](http://jovi.io/post/what-is-git/)中学习到了关于版本控制的内容。  
&emsp;在本文中，你将会学习:

- Git的命令
- Git的操作
- 一些关于高效使用Git的窍门

&emsp;在进入命令和操作之前，我们先了解Git的初衷。  
&emsp;Git的初衷是为了管理项目，跟踪文件的修改。Git使用Git仓库来存储这些信息。这也是Git的核心。  
&emsp;简明来说，Git仓库是一个文件夹，你可以存储所有项目文件和相关元数据更改。  
&emsp;Git依靠索引构建了树图，以用来记录当前项目的当前状态。这也被称为无回路有向图（Directed Acyclic Graph (DAG)）。  

## Git入门—操作与命令
&emsp;Git有一些基本的操作:

- Initialize（初始化）
- Add（添加）
- Commit（提交）
- Pull（拉取代码）
- Push（上传代码）

&emsp;另外一些高级用法包括:

- Branching（分支）
- Merging（合并）
- Rebasing（衍合）

&emsp;我们通过下图看看，这些操作是如何在Git仓库中使用的。

 ![Git架构](../images/devops/Git-Architechture-Git-Tutorial-Edureka-2-768x720.png)

&emsp;如果你不明白上图的内容，不需要担心，我将详细介绍这些操作。让我们开始学习这些。  
&emsp;你需要在电脑上安装Git，具体请查看[安装操作](https://git-scm.com/)。  
&emsp;在本文中，我将通过Git Bash来作演示。Git Bash是一中纯文本命令行，可以在Windows中使用Git。  
&emsp;在安装Git之后，你只需要打开你的文件夹，右键并选择`Git Bash here`。  
 ![Git-Bash](../images/devops/Git-Bash-Here-Git-Tutorial-Edureka.png)
&emsp;Git Bash命令行会打开，你可以在命令行中输入Git命令来进行相关的操作。  
&emsp;现在，我们来介绍如何初始化仓库。  

## Initialize
&emsp;为了能够初始化仓库，需要使用命令`git init`。请看下图。  

 ![Git-Initialize](../images/devops/Git-Initialize-Git-Tutorial-Edureka.png)
 
 &emsp;**git init**可以创建一个Git的空仓库或者重新初始化一个已存在的仓库。它会创建一个**.git**的子目录和模板文件。运行**git init**后，如果仓库已经存在，将不会覆盖已经存在的任何文件，取而代之的是新的模板。  
 &emsp;现在仓库已经初始化了，我们可以在该目录下创建一些文件。例如，我已经创建了两个文件，edureka1.txt和edureka2.txt。  
 &emsp;我们可以使用`git status`来查看这些文件是否已加入索引。索引包含了这些文件的快照，并且这些快照会成为本地仓库中进行的下一个更改的内容。  
 
### git status
&emsp;`git status`命令列出了所有修改过的文件，这些文件可以加至本地仓库中。  
&emsp;让我们输入该命令，来看看发生了什么：

 ![Git-push](../images/devops/Git-Bash-Here-Git-Tutorial-Edureka.png)
 
 &emsp;上图展示了还有两个文件尚未加至索引中。这意味着我不能commit这些文件的修改，除非我可以明确地加入索引。  
 
## Add
&emsp;这个命令将当前的内容加入索引，然后准备临时区域中的内容，为了下次的commit。  
&emsp;然而，在修改文件之后，运行**commit**命令之前，你必须使用**add**命令来添加任何新的或者修改过的文件进入索引。正因此，可以使用下列命令:  
`git add <directory>`   
or  
`git add <file>`  
&emsp;我演示`git add`。  
&emsp;我又创建了两个文件**edureka3.txt**和**edureka4.txt**。我们可以通过`git add -A`命令来为文件添加索引。这些命令可以将所有修改或新增的文件加入索引。
 ![Git-Add](../images/devops/Git-Add-All-Git-Tutorial-Edureka.png)
 &emsp;现在我们将文件加入索引了，你可以commit它们。  

## Commit
&emsp;这个命令可以在给定的时间内记录仓库快照。除非明确完成，否则提交的快照将不会改变。下图将介绍commit操作是如何工作的。  

 ![Git-Commit工作流](../images/devops/Git-Commit-Workflow-Git-Tutorial-10-Edureka-768x327.png)

&emsp;然而，C1是一个初始提交，即第一次更改的快照，而另一个快照C2创建提交了。请注意，master指向最新一次的提交。  
&emsp;现在，我有提交了，另一个快照C3创建了，现在master从C2转向了C3。  
&emsp;Git的目的是为了使commit更轻量化。所以，每次提交都不会盲目地复制整个目录；它包含了一系列的改变，或从仓库的一个版本到另一个版本的“增量”。简单来说，它只会在仓库中复制更改的文件。  

### git commit
&emsp;这个命令将会提交一个暂存的快照，并启动一个文本编辑器，提示您输入提交的消息。  
&emsp;你可以使用：
`git commit -m “<message>”`
&emsp;如图所示:
 ![Git-Commit](../images/devopsGit-Commit-Git-Tutorial-Edureka.png)
&emsp;正如上图，`git commit`命令可以在本地仓库中提交四个文件的变更。  
&emsp;现在，如果你想要在你的工作目录中提交所有的变更，你可以使用以下命令:

### git commit -a
&emsp;我已经在自己的工作目录中创建了另外两个文件，它们是**edureka5.txt**和**edureka6.txt**，但是他们没有加入索引。  
&emsp;我可以通过以下命令来把**edureka5.txt**加入索引:
```shell
git add edureka5.txt
```
&emsp;我已经将**edureka5.txt**加入索引了，并且对之前的文件做了修改。我现在想要提交这些变更。可以通过下图的方式。
 ![Git-Commit-All](../images/Git-Commit-All-Git-Tutorial-Edureka.png)
&emsp;这个命令可以提交在工作目录中所有变更的快照，但是只包含加入索引的文件，即这些文件通过`git add`加入索引。因此，**edureka6.txt**没有被提交，因为它没有加入索引。但是更改的其他文件都已经提交了，即**edureka1.txt,edureka2.txt**等。  
&emsp;现在我已经在本地仓库中提交了我想要提交的内容。  
&emsp;请注意，在你的变更影响中央仓库之前，你需要从中央仓库pull最新的代码至本地仓库，这样可以获得所有合作者的最新变更。因此，我们将使用**pull**命令。