---
title: 了解你的用例
author: clearab
description: 决定如何分发您的应用程序
ms.topic: conceptual
ms.author: anclear
ms.openlocfilehash: ef1007c21e79cd69155b64f02d2980bcf6a708b9
ms.sourcegitcommit: 4329a94918263c85d6c65ff401f571556b80307b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "41673320"
---
# <a name="understand-your-use-cases"></a>了解你的用例

Microsoft 团队平台提供了您的应用程序可以利用的大量[扩展性点和 UI 元素](~/concepts/extensibility-points.md)。 如果您还没有充分了解团队平台上可能的功能，则应首先阅读本文。

与您的用户交互的每种方法都有自己的优势和弱点。 构建一个非常有兴趣的团队应用程序是为了找到满足用户需求的正确组合。 如果你打算满足这些需求，你首先需要了解它们。

## <a name="what-problem-are-you-trying-to-solve"></a>您尝试解决什么问题？

在开始构建之前，每个有用的应用程序都有核心问题（或需要），您需要清楚地说明该问题是什么。 在这里，团队是一个协作平台，因此寻求解决协作问题的应用程序非常合适。 它也是社交平台，本身跨平台，位于 Office 365 的核心位置，并提供个人画布，供您在上创建应用程序。 可以使用团队应用程序解决的各种需求非常广泛，但请务必了解您要尝试解决的需求。

## <a name="who-are-you-solving-it-for"></a>您对其进行求解的是谁？

有时，这可能很明显-"我的团队的监视系统需要在某个位置发送警报，我们需要能够快速讨论它们，并且我们不需要查看我们的电子邮件。" 有时您的目标受众可以随着时间的推移而增加-"我们的备用团队实际上是我们的 jealous 系统，他们希望在此行动中。 了解您的用户将会帮助您识别正确的分发模型，但更重要的是，将帮助您确定*他们如何使用团队*。 它们主要是移动客户端上的前线工作者吗？ 您是否希望大量来宾用户需要访问您的应用程序？ 他们是使用团队和频道，还是主要聊天组？ 它们在技术上的复杂程度如何？ 您是否需要全面的入职体验，或者很少有一些指针？

有时，答案是 "我们想要为所有团队的用户解决此问题"。 如果是这种情况，你将需要花些时间了解[发布到 AppSource 所需的内容](~/concepts/deploy-and-publish/appsource/prepare/overview.md)。

## <a name="do-you-need-authentication"></a>是否需要身份验证？

如果你需要保护要公开的服务以及在哪个级别进行保护，则应提前确定。 请记住，您在团队应用程序中公开的 web 服务是通过 internet 公开提供的，因此，如果需要保护它们，就可以开始考虑如何实现。

## <a name="should-the-entire-app-be-in-teams"></a>整个应用程序是否应位于团队中？

无论您是要构建全新的内容，还是将现有解决方案引入团队，务必要确定整个应用程序是否在团队客户端内，或者是否有必要只提供部分体验。 您可以将选项卡、邮件扩展、任务模块、交互式卡片和对话 bot 组合在一起，在团队中完全构建复杂的应用程序。 但是，这并不总是有意义。 记住你的用户是谁，以及你尝试解决的问题。 他们是否已有一个系统来解决大部分问题，您只需将一组子功能扩展到团队中？ 通常情况下，如果只打算引入解决方案的一部分，则应重点关注共享、协作和发起和监控工作流。

## <a name="what-will-the-onboarding-experience-be-like"></a>载入体验是什么样子的？

您的载入体验可能是您的应用程序的成功或失败之间的差异。 对于您的应用程序的每个功能，以及可在其中安装功能的每个上下文，应制定一个计划来介绍如何引入您自己。 如果你的对话机器人安装在带一千人的频道中的方式，则可能会与在一对一聊天中安装它的时间不同。 当用户首次在频道中配置选项卡时，会发生什么？ 如果要与邮件扩展共享卡片，则在向 "了解更多" 页面添加小链接以帮助向用户介绍您的应用程序可以执行的操作时，是否有意义？

了解你的用户将会帮助你创建正确的体验。 您是否希望大多数用户都已拥有您的应用程序的上下文，或者已在另一个上下文中使用过您的服务？ 或者，他们是否在不事先知道的情况中来到你的应用？ 在考虑关键用户的同时，创建您的载入体验。

还请记住，用户可以通过多种方式发现您的应用程序-它们可能是安装它的应用程序，也可能会在其他团队成员使用它共享内容时引入到您的应用程序中。 如果您希望您的应用程序传播，应查找向所有人介绍的方法。

在所有其他情况下，请记住，没有人喜欢垃圾邮件。 将个人和频道消息发射出来是快速卸载的好方法！

## <a name="next-steps"></a>后续步骤

* [将用例映射到功能](~/concepts/design/map-use-cases.md)
* [选择分发应用程序的方式](~/concepts/deploy-and-publish/apps-publish.md)

## <a name="learn-more"></a>了解更多信息

* [设计有效的选项卡](~/tabs/design/tabs.md)
* [创建精彩的 bot](~/bots/design/bots.md)

