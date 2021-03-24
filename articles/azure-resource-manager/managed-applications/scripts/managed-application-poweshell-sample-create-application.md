---
title: Azure PowerShell 脚本示例 - 部署托管应用程序
description: 提供 Azure PowerShell 示例脚本，该脚本将托管应用程序定义部署到订阅。
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: a2687e9c943df8454ff42a17f44866dcdb7f4730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "86055879"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>使用 PowerShell 为服务目录部署托管应用程序

此脚本从服务目录部署托管应用程序定义。


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令部署托管应用程序。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzManagedApplication](/powershell/module/az.resources/new-azmanagedapplication) | 创建托管应用程序。 提供模板的定义 ID 和参数。 |


## <a name="next-steps"></a>后续步骤

* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](../overview.md)。
* 有关 PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/get-started-azureps)。
