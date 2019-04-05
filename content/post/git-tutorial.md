---
title: "Git入门-命令与操作"
date: 2019-04-03T20:26:00+08:00
draft: false
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
 ![Git-Commit](../images/devops/Git-Commit-Git-Tutorial-Edureka.png)
&emsp;正如上图，`git commit`命令可以在本地仓库中提交四个文件的变更。  
&emsp;现在，如果你想要在你的工作目录中提交所有的变更，你可以使用以下命令:

### git commit -a
&emsp;我已经在自己的工作目录中创建了另外两个文件，它们是**edureka5.txt**和**edureka6.txt**，但是他们没有加入索引。  
&emsp;我可以通过以下命令来把**edureka5.txt**加入索引:
```shell
git add edureka5.txt
```
&emsp;我已经将**edureka5.txt**加入索引了，并且对之前的文件做了修改。我现在想要提交这些变更。可以通过下图的方式。
 ![Git-Commit-All](../images/devops/Git-Commit-All-Git-Tutorial-Edureka.png)
&emsp;这个命令可以提交在工作目录中所有变更的快照，但是只包含加入索引的文件，即这些文件通过`git add`加入索引。因此，**edureka6.txt**没有被提交，因为它没有加入索引。但是更改的其他文件都已经提交了，即**edureka1.txt,edureka2.txt**等。  
&emsp;现在我已经在本地仓库中提交了我想要提交的内容。  
&emsp;请注意，在你的变更影响中央仓库之前，你需要从中央仓库pull最新的代码至本地仓库，这样可以获得所有合作者的最新变更。因此，我们将使用**pull**命令。

## Pull
&emsp;**git pull**能够从远程仓库接收变更至本地仓库。它在你的本地仓库中合并更改，这个在Git中称为合作。  
&emsp;但是，首先你需要先设置中央仓库作为源地址，可以使用以下命令:  
```shell
git remote add origin <link of your central repository>
```
 ![Git添加远程仓库](../images/devops/Git-Add-Remote-Origin-Git-Tutorial-8-Edureka-1.png)
&emsp;现在你已经设置了源仓库的地址，我们可以使用pull来进行文件拉取。可以使用以下命令:   
```aidl
git pull origin master
```
&emsp;这个命令可以从中央仓库的master分支上复制所有的文件至本地仓库。
 ![Git远程仓库拉取代码](../images/devops/Git-Pull-Origin-Master-Git-Tutorial-Edureka.png)
&emsp;你的本地仓库的文件会随着这些变更而更新。现在你可以使用**push**命令将变更中央仓库的文件。

## Push
&emsp;这个命令能够将你在本地仓库提交的内容提交至远程中央仓库。这个是**pull**的一种相反的操作。  
&emsp;依靠**Pull**从远程仓库拉取代码库并更新本地代码库，而**Push**则会推送本地仓库的更新至远程仓库。  
&emsp;**git push**的作用就是将你的本地变更会推送至远程仓库。在你累计了多个本地提交，并准备分享给团队的其他人，你可以推送他们至远程仓库，具体的命令如下:
```aidl
git push <remote>
```
&emsp;**注意:**这个remote指的是先前指定的镜像仓库。  
&emsp;这个命令可以将本地的所有的变更和提交都推送至远程仓库。这也会在目标仓库中创建一个本地分支。 
&emsp;下面我将会为你展示:
 ![Push之前本地仓库的文件](../images/devops/Local-Repository-Files-Before-Push-Git-Tutorial-Edureka-2.png) 
&emsp;以上文件都是我之前已经提交过的。我将通过命令命令`git push origin master`来推送这些提交至中央仓库。
 ![Git-push命令](../images/devops/Git-Push-Origin-Master-Git-Tutorial-Edureka.png) 
&emsp;让我们一起来检查，我的中央仓库是不是真的已经发生变化了。
 ![GitHub在push之后的变化](../images/devops/GitHub-after-Git-Push-Git-Tutorial-Edureka.png) 
&emsp;成功了!
&emsp;为了防止覆盖，当Git在目标仓库进行非快速合并(non-fast-forward，即别人在你push之前已经对该文件进行了push)时，不允许进行提交。  
&emsp;为了确保合并成功，需要使用以下命令:
```aidl
git push <remote> -force
```
&emsp;以上命令可以强制提交即使在进行非快速合并。  
&emsp;在这篇文章中，我希望你能了解Git的所有命令。现在我们来深入了解Git的分支以及如何在Git中进行合并。

## 分支(Branching)
&emsp;分支在Git中就是个指针，他会指向一个特别的提交。Git的目的是为了使分支更轻量化。  
&emsp;在初始情况下，一般有两个分支:本地分支和远程跟踪分支。  
&emsp;本地分支仅仅是一个工作树的一个路径。另一方面，远程跟踪分支有特别的目的:

- 他们连接了你的本地仓库和远程仓库。
- 他们自动探测远程分支来获取变更，当你使用**pull**的时候。

&emsp;你可以使用一下命令确认当前分支。
```aidl
git branch
```
&emsp;你可以使用一下命令来创建一个分支:
```aidl
git branch <branch-name>
```
 ![分支创建工作流](../images/devops/Creating-A-Branch-Workflow-Git-Tutorial-19-Edureka-768x294.png) 
&emsp;上图显示了一个新分支创建时的工作流。当我们创建一个新的分支的时候，他们基于master分支创建一个新的分支。  
&emsp;由于没有存储或内存可以支持那么多的分支，因此GIT会在逻辑上划分工作区域，而不是拥有大量的实际分支。  
&emsp;现在，我们来看看如何提交分支。  
 ![在分支中提交](../images/devops/Commit-Using-Branches-Workflow-Git-Tutorial-20-Edureka-768x327.png) 
&emsp;创建分支的时候他会连父集上所有的提交都会包含在内。基于上图你可以发现，新分支是从master上切换出来的，因此他创建了一个不同的路径。  
&emsp;通过以下命令:
```aidl
git checkout <branch_name> 
或者
git commit
```
 ![Git分支](../images/devops/Branching-Git-Tutorial-Edureka.png) 
&emsp;因此，我创建了一个**EdurekaImages**的分支，并且通过`git checkout`切换至一个新的分支。  
&emsp;可以通过一个简单的命令完成这两件事情:
```aidl
git checkout -b[ branch_name]
```
&emsp;这个命令可以创建一个新的分支，并在同时切换至一个新的分支。  
&emsp;现在我们已经在分支**EdurekaImages**中，可以添加并提交一个文件**edureka6.txt**,命令如下:
```aidl
git add edureka6.txt

git commit -m”adding edureka6.txt” 
```
## 合并(Merging)
&emsp;合并能够结合两种不同的分支。这就可以允许我们进行分支，创建新的特性并结合。
 ![Git合并](../images/devops/Merging-Workflow-Git-Tutorial-22-Edureka-768x520.png)   
&emsp;上图中显示了两个不同的分支:newBranch和master。现在，当我们讲newBranch合并至master中，他创建了一个新的提交，包含了所有的master和newBranch的内容。  
&emsp;我们对两个分支进行合并:
```aidl
git merge <branch_name>
```
&emsp;上面的branch_name代表的是需要合并进入的分支名称。所以，确认你需要进入目标分支。  
&emsp;现在我们需要将EdurekaImages分支合并至master分支。因此，我们需要先进入master分支，我们可以使用`git checkout master`命令来实现。然后使用` git merge EdurekaImages`来进行合并。  
 ![Git合并](../images/devops/Git-Merge-Git-Tutorial-Edureka.png)   
 &emsp;正如上图所示，所有的数据从EdurekaImages分支合并至master分支。现在，edureka6.txt已经被添加至master分支。  
 &emsp;Git的合并创建了一个特别的提交，它包含了两个特别的父类。  

## 衍合（Rebasing）
&emsp;在多个分支之间合并是一个非常常见的事情。衍合可以使保存一系列的提交，复制，然后保存在你们仓库之外。  
&emsp;衍合的好处是可以用来提交按照线性排列。衍合之后，提交日志或历史可以保持干净。  
&emsp;我们来看看发生了什么:
 ![Git衍合](../images/devops/Rebasing-Git-Tutorial-24-Edureka-1-768x451.png)   
 &emsp;现在，我们的新分支就在右边，并且有了一个线性的提交。  
 &emsp;**注意:**衍合可以阻止向上合并，这意味着无法在新分支之后，修改master。  
 &emsp;现在，为了衍合master分支，可以输入以下命令:
 ```aidl
git rebase master
```
 ![Git衍合-14](../images/devops/Rebase-Git-Tutorial-14-Edureka.png)   
&emsp;这个命令可以将当前分支移至master分支。虽然他们是并行开发的，但看上去他们是串行的。  

## Git入门—使用窍门
&emsp;现在，你已经学会了所有的操作，但是有一些小贴士你需要知道。  

### 仓库存档

&emsp;可以使用一下命令:
```aidl
git archive master –format=zip  –output= ../name-of-file.zip
```
&emsp;这个可以保存是有的文件与数据，以.zip压缩文件的方式，而不是.git目录。  
&emsp;这个方式创建了一个简单的快照来完整的保存所有的版本。这种适用于当你把文件传给一个没有安装Git的客户端的时候。  

### 捆绑仓库

&emsp;这可以使仓库变成一个单一的文件。  
&emsp;可以使用一下命令:
```aidl
git bundle create ../repo.bundler master
```
&emsp;这可以将master分支提交至远程仓库，仅仅一个文件而不是一个仓库。  
&emsp;另一种代替的方式:
```aidl
cd..

git clone repo.bundle repo-copy -b master

cd repo-copy

git log

cd.. /my-git-repo
```

### 隐藏没有提交的变更
&emsp;当你需要取消添加一个新特性或短暂的添加一个数据的时候，你可以暂时使用`stash`。  
&emsp;可以使用以下命令:
```aidl
git status

git stash

git status
```
&emsp;当你想重新申请变更的时候，你可以使用以下命令:
```aidl
git stash apply
```
&emsp;我希望你可以了解Git的操作和命令。本文翻译自:https://www.edureka.co/blog/git-tutorial/