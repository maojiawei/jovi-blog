---
title: "Service Mesh是什么？以及为什么我们需要他"
date: 2019-08-07T21:40:35+08:00
draft: true
tags: ["service mesh"]
categories: "microservice"
toc: false
---
&emsp;Service Mesh是一个专用的基础设施层，用于在服务与服务之间建立安全、快速和可靠的通信。如果你正在构建一个云原生应用，你就需要service Mesh。  

&emsp;在过去的一年里，Service Mesh已经成为了云原生栈中重要的组成部分。一些高流量公司，例如：Paypal、Ticketmaster和Credit Karma已经将Service Mesh应用在生产环境中，在今年一月（2017年1月），Linkerd和其他一些开源的云原生应用已经成为了CNCF的正式项目。但是什么是Service Mesh？另外，为什么他们变得相关？ 

&emsp;在这篇文章中，我将会定义Service Mesh，并通过过去十年中应用架构的变化来追踪。我将分别将Service Mesh与API网关、边缘代理和企业服务总线作对比，介绍他们的相关性与不同。最终，我将描述Service Mesh的发展方向，以及他是如何随着云原生而发展的。  

### 什么是Service Mesh

&emsp;Service Mesh是一个专用的基础设施层，用于解决服务与服务之间的通信。它负责在复杂的云原生服务拓扑之间进行可靠的请求传输。事实上，Service Mesh是由一系列轻量级的网络代理组成，这些代理与应用程序部署在一起，而应用程序本身不需要去关心这些。

&emsp;Service Mesh的作为一个单独层与云原生应用的兴起有关，在云原生模型中，一个单一应用可以有数百个服务组成，每个服务可能存在数千个实例，并且每个实例都处在不段变化的状态，因为他们可以被像kubernetes这种协调器动态调度。这不仅仅是因为当前世界上的服务通信复杂，而是这也是运行时行为中最普遍和基本的一种。因此管理它们，保证服务端到端的性能和可靠性是至关重要的。