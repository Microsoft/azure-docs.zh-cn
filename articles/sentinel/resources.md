---
title: 使用 Azure Sentinel 时有用的资源 |Microsoft Docs
description: 本文档提供使用 Azure Sentinel 时可用资源的列表。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2465cf74dbf4fbf074aed8f8e4097a236e628425
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660638"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>使用 Azure Sentinel 的有用资源



本文列出了可帮助你获取有关使用 Azure Sentinel 的详细信息的资源。

Azure 逻辑应用连接器： <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>审核和报告
Azure Sentinel 的审核日志保留在 [Azure 活动日志](../azure-monitor/platform/platform-logs-overview.md)中。

可以审核以下受支持的操作。

|操作名称|    资源类型|
|----|----|
|创建或更新工作簿  |Microsoft Insights/工作簿|
|删除工作簿    |Microsoft Insights/工作簿|
|设置工作流   |Microsoft.Logic/workflows|
|删除工作流    |Microsoft.Logic/workflows|
|创建保存的搜索    |Microsoft.operationalinsights/工作区/savedSearches|
|删除保存的搜索    |Microsoft.operationalinsights/工作区/savedSearches|
|更新警报规则 |Microsoft.SecurityInsights/alertRules|
|删除警报规则 |Microsoft.SecurityInsights/alertRules|
|更新警报规则响应操作 |SecurityInsights/alertRules/actions|
|删除警报规则响应操作 |SecurityInsights/alertRules/actions|
|更新书签   |Microsoft.SecurityInsights/bookmarks|
|删除书签   |Microsoft.SecurityInsights/bookmarks|
|更新事例   |SecurityInsights/事例|
|更新案例调查  |SecurityInsights/案例/调查|
|创建案例注释   |SecurityInsights/案例/注释|
|更新数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|删除数据连接器 |Microsoft.SecurityInsights/dataConnectors|
|更新设置    |Microsoft.SecurityInsights/settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>在 Azure Sentinel 中查看审核和报告数据

你可以查看此数据，方法是将其从 Azure 活动日志流式传输到 Azure Sentinel，然后你可以在其中对其执行研究和分析。

1. 连接 [Azure 活动](connect-azure-activity.md) 数据源。 执行此操作后，审核事件会在名为 AzureActivity 的 **日志** 屏幕上流式传输到新的表中。

1. 然后，使用 KQL 查询数据，就像对任何其他表一样。

    例如，若要找出上次编辑特定分析规则的用户，请使用以下查询 (替换 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 为要检查) 的规则的规则 ID：

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>供应商文档

| **供应商**  | **在 Azure Sentinel 中使用事件** | **链接**|
|----|----|----|
| GitHub| 用于访问社区页面| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| 配置 CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto 查询语言课程| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>博客和论坛

将你的问题发布到 Azure Sentinel 的 [TechCommunity 空间](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) 。

查看 [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 和 [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)的 Azure Sentinel 博客文章。


## <a name="next-steps"></a>后续步骤
在本文档中，你获得了在使用 Azure Sentinel 时非常有用的资源列表。 你会在 [Microsoft Azure 安全和合规性博客](/archive/blogs/azuresecurity/)上找到有关 Azure 安全性和符合性的其他信息。