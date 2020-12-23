---
title: " (预览版在 Azure Cosmos DB 帐户中启用笔记本) "
description: Azure Cosmos DB 的内置笔记本使你可以从门户内分析和可视化数据。 本文介绍如何为 Cosmos 帐户启用此功能。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/22/2019
ms.author: dech
ms.openlocfilehash: 7b52a066f80b686a0e424d8f63d520d46691a72a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187810"
---
# <a name="enable-notebooks-for-azure-cosmos-db-accounts-preview"></a> (预览版为 Azure Cosmos DB 帐户启用笔记本) 
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> 以下 Azure 区域中当前提供了 Azure Cosmos DB 的内置笔记本：澳大利亚东部、美国东部、美国东部2、北欧、美国中南部、东南亚、英国南部、西欧和美国西部2。 若要使用笔记本，请使用 [笔记本创建新帐户](#enable-notebooks-in-a-new-cosmos-account) ，或在其中一个区域中的 [现有帐户上启用笔记本](#enable-notebooks-in-an-existing-cosmos-account) 。

使用 Azure Cosmos DB 中的内置 Jupyter 笔记本可以从 Azure 门户分析和可视化数据。 本文介绍了如何为 Azure Cosmos DB 帐户启用此功能。

## <a name="enable-notebooks-in-a-new-cosmos-account"></a>在新的 Cosmos 帐户中启用笔记本

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择“创建资源” > “数据库” > “Azure Cosmos DB”。
1. 在 " **创建 Azure Cosmos DB 帐户** " 页上，选择 " **笔记本**"。 
 
    :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks.png" alt-text="Azure Cosmos DB 创建边栏选项卡中选择笔记本选项":::

1. 选择“查看 + 创建”。 可以跳过 " **网络** 和 **标记** " 选项。 
1. 检查帐户设置，然后选择“创建”。 创建帐户需要几分钟时间。 等待门户页显示“你的部署已完成”消息。 

   :::image type="content" source="media/enable-notebooks/create-new-account-with-notebooks-complete.png" alt-text="Azure 门户“通知”窗格":::

1. 选择“转到资源”，转到 Azure Cosmos DB 帐户页。

   :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-3.png" alt-text="Azure Cosmos DB 帐户页面":::

1. 导航到 " **数据资源管理器** " 窗格。 现在应会看到 "笔记本" 工作区。

    :::image type="content" source="media/enable-notebooks/new-notebooks-workspace.png" alt-text="新 Azure Cosmos DB 笔记本工作区":::

## <a name="enable-notebooks-in-an-existing-cosmos-account"></a>在现有 Cosmos 帐户中启用笔记本

你还可以在现有帐户上启用笔记本。 每个帐户只需执行此步骤一次。

1. 导航到 Cosmos 帐户中的 " **数据资源管理器** " 窗格。
1. 选择 " **启用笔记本**"。

    :::image type="content" source="media/enable-notebooks/enable-notebooks-workspace.png" alt-text="在数据资源管理器中创建新的 &quot;笔记本&quot; 工作区":::

1. 这会提示你创建新的 "笔记本" 工作区。 选择 " **完成设置"。**
1. 你的帐户现已启用，可以使用笔记本！

## <a name="create-and-run-your-first-notebook"></a>创建并运行第一个笔记本

若要验证是否可以使用笔记本，请在 "示例笔记本" 下选择一个笔记本。 这会将笔记本副本保存到你的工作区并将其打开。

在此示例中，我们将使用 **GettingStarted. ipynb**。 

:::image type="content" source="media/enable-notebooks/select-getting-started-notebook.png" alt-text="查看 GettingStarted ipynb 笔记本":::

运行笔记本：
1. 选择包含 Python 代码的第一个代码单元。 
1. 选择 " **运行** " 以运行该单元。 还可以使用 **Shift + Enter** 来运行单元。
1. 刷新资源窗格，查看已创建的数据库和容器。

    :::image type="content" source="media/enable-notebooks/run-first-notebook-cell.png" alt-text="运行入门笔记本":::

你还可以选择 "**新建笔记本**"，以通过从 "**我的笔记本**" 菜单中选择 "**上传文件**"，来创建新笔记本或) 文件上传现有笔记本 (。 

:::image type="content" source="media/enable-notebooks/create-or-upload-new-notebook.png" alt-text="创建或上传新笔记本":::

## <a name="next-steps"></a>后续步骤

- 了解[Azure Cosmos DB Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)的优点
