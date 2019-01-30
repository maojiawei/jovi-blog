---
title: "DevOps入门"
date: 2019-01-28T11:18:32+08:00
draft: false
categories: "devops"
---
&emsp;出于对devops的浓厚兴趣，我开始考虑写一系列博客，这些博客将教导你在平时的软件开发中采用这种新文化，并帮助你理解它的全部内容。这是这系列博客的开篇—DevOps入门。

## DevOps入门

&emsp;DevOps入门系列博客将会让你了解DevOps方法论以及相关的工具。在这篇博客中，我将会介绍一下内容，这些也会是后续博客的基础。

- 什么使得DevOps成为可能
- DevOps介绍

## 瀑布模型
&emsp;让我们一起来看软件开发中的传统方式—瀑布模型。
 ![瀑布模型](../images/devops/Waterfall-Model-Devops-Tutorial-Edureka.png)

- 阶段1 — 收集需求并完成SRS（需求规格说明书）
- 阶段2 — 根据SRS完成系统设计与规划。
- 阶段3 — 系统实施。
- 阶段4 — 测试系统，评估质量。
- 阶段5 — 系统部署，终端用户开始使用。
- 阶段6 — 定期进行系统维护。

### 瀑布模型挑战
&emsp;瀑布模型已经运用了较长的时间，并且一直运行得非常好。然而，它面临了许多挑战。在下图中重点介绍了瀑布模型所面临的挑战。
 ![瀑布模型面临的挑战](../images/devops/WaterFall-Model-Challenges-DevOps-Tutorial-Edureka-2.png)
&emsp;在上图中，你可以看到如今在瀑布模型中，开发和运维都面临了众多的挑战。从开发者的角度，主要有两个挑战：


1. 在开发之后，部署需要耗费大量的时间。
2. 旧代码、正在开发中的代码以及新代码的压力是非常巨大的，因为开发部署的周期很长。

&emsp;另一方面，运维也不是很令人满意。有四个主要的挑战：

1. 生产环境难以保持100%运行。
2. 基础的自动化工具并不是非常有效。
3. 服务器的数量不断增长并且更复杂。
4. 在生产问题难以得到响应或诊断。

&emsp;在下图中，重点介绍了这些问题的解决方案。
 ![瀑布模型挑战的解决方法](../images/devops/Possible-solutions-to-address-the-challenges-faced-with-WaterFall-Model-DevOps-Tutorial-Edureka-1-1.png)

&emsp;在上图中，我们用蓝色表示开发人员与运维人员所面临问题的解决方案。这位理想的软件开发模型提供了指导方针。


&emsp;从开发人员的角度：

1. 一个可以使部署没有等待或延迟时间的系统。
2. 一个只需要关注的当前代码的系统，即开发周期很短并且可以安排计划。

&emsp;从运维人员的角度：

1. 系统至少在99%的时间里运行。
2. 工具和系统易于管理和使用。
3. 有效监控和响应。
4. 开发人员和运维人员能够更好的合作。

&emsp;我认为我们可以开始谈论什么是DevOps以及它是如何克服这些困难的。

&emsp;DevOps结合了开发团队和运维团队来促进合作以提高工作效率。

&emsp;根据DevOps文化，只有一个小组(开发人员、系统管理员、QA测试等等转变成DevOps工程师)。从收集需求到开发，再到测试，再到基础架构部署，再到应用程序部署，最后监控和收集来自最终用户的反馈，然后再次实现更改，这些都是应用程序(软件)的端到端的责任。
 ![devops周期环](../images/devops/devops-lifeycle-devops-tutorial-Edureka.png)

&emsp;这是个永不停止的循环，DevOps的logo对我来说意义重大。看看上图—还有什么能比无穷大的符号更符合DevOps？

&emsp;现在，我们一起来看看DevOps是如何处理开发和运维所面临的挑战的。在下面的表格中描述了DevOps如何解决开发的挑战。
![DevOps面向开发人员挑战](../images/devops/Above table states-how-DevOps-solves-Dev-Challenges.png)
下面的表格中描述了DevOps如何解决运维的挑战。
![DevOps面向运维人员挑战](../images/devops/Above table states how-DevOps-solves-Ops-Challenges.png)
&emsp;现在你可能想知道，如何实现DevOps。为了实现DevOps，除了需要在文件上认同它，还需要以一些DevOps工具，例如：Puppet,Jenkins,GIT,Chef,Docker,Selenium,AWS等等来完成自动化。它们可以在不同阶段帮助你们完成持续开发、持续集成、持续测试、持续部署和持续监控，帮助让你快速完成一个合格软件交付给客户。
&emsp;现在我们可以来看看下方的DevOps图，它介绍了许多DevOps工具。
![DevOps工具](../images/devops/DevOps-Tools-DevOps-Tutorial-Edureka-1.png)
&emsp;这些工具可以根据DevOps的不同阶段来进行分类。因此，我首先会介绍下DevOps的各个阶段，然后再介绍各类工具。
&emsp;DevOps的生命周期可以划分为以下几个阶段：

- 持续开发
- 持续集成
- 持续测试
- 持续监控
- 虚拟化与容器化

&emsp;这些阶段构成了一个循环来达到DevOps。
&emsp;这就是DevOps入门系列的第一部分。

注：这篇文章翻译自:https://www.edureka.co/blog/devops-tutorial