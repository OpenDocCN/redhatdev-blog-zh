# 如何选择正确的容器基础映像

> 原文：<https://developers.redhat.com/blog/2021/04/13/how-to-pick-the-right-container-base-image>

挑选正确的[容器](/topics/containers/)基础映像对很多人来说都很难。每个主要的 [Linux](/topics/linux) 发行版都提供了一个基础映像。Python、Ruby 和 [Node.js](/topics/nodejs) 等编程语言的开源项目提供了自己的基础映像。许多开源项目和供应商也为 MariaDB、Redis、Elastic 和 MySQL 等服务提供自己的映像。虽然编程语言和服务在技术上并不是基础映像，但是大多数人认为它们是基础映像，并在选择标准化基础映像时将它们包含在分析中。

## 为什么容器基础图像很重要

根据 Red Hat 欧洲、中东和非洲(EMEA)团队进行的一项调查，即将发布的一份报告显示，很大一部分开发人员使用由其运营团队选择的标准化基础映像。然而，调查发现，几乎同样多的团队允许开发人员在生产中使用他们想要的任何东西。这是一个很大的迹象，表明团队很难找到正确的方法来处理基于容器的映像。

你可能会问自己，这有什么关系？在同一调查中，大多数被调查的开发人员将*安全性*列为容器基础映像的三大要求之一。开发者将*保证运行*排在第二位，*多年生命周期*排在第三位。

如果这些需求中的任何一个是你和你的团队的头等大事，那么[Red Hat Universal Base Image](/products/rhel/ubi)(UBI)就是一个简单的选择。UBI 是帮助构建容器的[红帽企业 Linux 8](/products/rhel/download) 的一个组件。它是 Red Hat Enterprise Linux 用户空间的一个轻量级版本，只保留了最基本的内容。

请记住，基本容器映像中的内容仍然是一个操作系统，它的安全性、可靠性和生命周期来自于构建它的底层操作系统。UBI 是一个维护良好、最新的企业级容器映像。它也可以自由使用和自由分发。你甚至不需要成为一个红帽客户来使用它。

## 通用基础映像入门

开发和运营团队应该深入思考他们的容器基础形象。为了帮助你做出这个战略决策，我们出版了一本新的电子书， [*红帽通用基础图片*](https://developers.redhat.com/books/red-hat-universal-base-images-ubi) 。本书包含帮助您理解和开始使用 UBI 的信息，包括:

*   如何使用免费提供的集装箱图像，这些图像基于 RHEL 对质量、安全性、可靠性和性能的长期支持和承诺。
*   为什么选择基本容器映像是一个战略决策，为什么它们是您的标准操作环境的一个关键部分。
*   哪些类型的 UBI 图像可用，以及如何在它们之间进行选择。
*   如何找到可用的 UBI 映像，包括语言和其他准备好添加代码的运行时映像。
*   在哪里可以找到与 UBI 映像一起使用的其他软件包。

[阅读电子书](https://developers.redhat.com/books/red-hat-universal-base-images-ubi)了解为什么基础映像中的内容很重要，并了解如何在开发项目中使用 UBI。

*Last updated: October 14, 2022*