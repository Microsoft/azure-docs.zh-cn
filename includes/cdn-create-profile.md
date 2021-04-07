---
title: include 文件
description: include 文件
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 9a003b5c42a6ef4c699a3768d15ae08f86d56e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367278"
---
## <a name="create-a-new-cdn-profile"></a>创建新的 CDN 配置文件

CDN 配置文件是适用于 CDN 终结点的容器，用于指定定价层。

1. 在 Azure 门户中，选择“创建资源”（在左上角）。 此时会显示“新建”窗格。
   
1. 搜索并选择“CDN”，然后选择“创建”： 
   
    ![选择 CDN 资源](./media/cdn-create-profile/cdn-new-resource.png)

    此时会显示“CDN 配置文件”窗格。

1. 输入以下值：
   
    | 设置  | 值 |
    | -------- | ----- |
    | **名称** | 输入“cdn-profile-123”作为配置文件名称。 |
    | **订阅** | 从下拉列表中选择一个 Azure 订阅。 |
    | **资源组** | 选择“新建”并输入“CDNQuickstart-rg”作为资源组名，或者选择“使用现有”并选择“CDNQuickstart-rg”（如果已有该组）。 | 
    | **资源组位置** | 从下拉列表中选择你附近的位置。 |
    | **定价层** | 从下拉列表中选择“标准 Akamai”选项。 （Akamai 层的部署时间大约为一分钟。 Microsoft 层大约需要 10 分钟，Verizon 层大约需要 30 分钟。） |
    | **立即创建新的 CDN 终结点** | 保持未选中状态。 |  
   
    ![新的 CDN 配置文件](./media/cdn-create-profile/cdn-new-profile.png)

1. 选择“创建”以创建该配置文件。

