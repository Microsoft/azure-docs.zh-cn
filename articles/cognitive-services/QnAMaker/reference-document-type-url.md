---
title: 支持进行导入的 URL 类型 - QnA Maker
description: 了解如何使用 URL 的类型来导入和创建 QnA 对。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 8bf50c1ea81cdf5246c47646d1a55926fe7d58d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "91776691"
---
# <a name="urls-supported-for-importing-documents"></a>支持用于导入文档的 URL

了解如何使用 URL 的类型来导入和创建 QnA 对。

## <a name="faq-urls"></a>常见问题解答 URL

QnA Maker 可以支持 3 种不同形式的常见问题解答网页：

* 纯文本常见问题解答页
* 带链接的常见问题解答页
* 带主题主页的常见问题解答页

### <a name="plain-faq-pages"></a>纯文本常见问题解答页

这是最常见的常见问题解答页类型，其中答案会紧跟在同一页中的问题后面。

下面是普通常见问题解答页的示例：

![知识库的纯文本常见问题解答页示例](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>带链接的常见问题解答页

在这种类型的常见问题解答页中，问题聚合在一起，并链接到同一页的不同部分或不同页中的答案。

下面的示例是一个常见问题解答页，其中的链接位于同一页上的不同部分：

 ![知识库的部分链接常见问题解答页示例](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>与子答案页面链接的父主题页面

此类型的常见问题解答有一个主题页面，其中每个主题都链接到不同页面上相应的一组问题和答案。 QnA Maker 会抓取所有链接的页以提取相应的问题与答案。

下面是主题页面的示例，其中包含指向不同页面中常见问题解答部分的链接。

 ![知识库的深层链接常见问题解答页示例](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>支持 URL

QnA Maker 可以处理半结构化支持网页，例如，介绍如何执行给定任务、如何诊断和解决给定问题以及适用于给定流程的最佳做法的网文。 提取最适用于结构清晰且具有分层标题的文档。

> [!NOTE]
> 提取支持文章是一项新功能，并且处于早期阶段。 它最适用于结构良好且未包含复杂页眉/页脚的简单页面。

![QnA Maker 支持从在分层标题中提供了清晰结构的半结构化网页进行提取](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)