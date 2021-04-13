---
title: 文档详细信息 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 此文档列表页面显示你的工作区中的前 10 个文档。 对于每个文档，它显示名称、配对、类型、语言、上传时间戳，以及上传了此文档的用户的电子邮件地址。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 56093204516e156d670097c22b4edab42db54bde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895605"
---
# <a name="view-document-details"></a>查看文档详细信息

此文档列表页面显示你的工作区中的前 10 个文档。 对于每个文档，它显示名称、配对、类型、语言、上传时间戳，以及上传了此文档的用户的电子邮件地址。

单击单个文档可查看文档详细信息页。 文档详细信息页显示从文档提取的语句的列表。

- 默认情况下，下拉字段中会选中“并行”源和目标语言，但可以切换为以源或目标语言查看语句。
- 默认情况下，每页显示 20 个语句。 可以使用分页控件在页面之间进行浏览。

![文档详细信息](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>删除文档

用户必须是工作区所有者才能删除文档。 此外，如果文档由翻译过程或部署过程中的某个模型使用，则无法删除该文档。

1. 转到文档页
2. 将鼠标指针悬停在任何文档记录上，并单击垃圾桶图标。

    ![删除文档](media/how-to/how-to-delete-document-1.png)

3. 确认删除。

    ![确认删除](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>后续步骤

- 了解[如何训练模型](how-to-train-model.md)。
