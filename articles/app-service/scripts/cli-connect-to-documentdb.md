---
title: CLI：将应用连接到 Cosmos DB
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何将应用连接到 MongoDB (Cosmos DB)。
author: msangapu-msft
tags: azure-service-management
ms.assetid: bbbdbc42-efb5-4b4f-8ba6-c03c9d16a7ea
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: fa971bbb8526f1b0bc081e61ed5e7a7f2cf76d1f
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006353"
---
# <a name="connect-an-app-service-app-to-cosmos-db-using-cli"></a>使用 CLI 将应用服务应用连接到 Cosmos DB

此示例脚本使用 Azure Cosmos DB 的用于 MongoDB 的 API 和应用服务应用来创建一个 Azure Cosmos DB 帐户。 然后，它将使用应用设置将 MongoDB 连接字符串链接到 Web 应用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，需要 Azure CLI 2.0 版或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-documentdb/connect-to-documentdb.sh "Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用、Cosmos DB 和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | 创建应用服务应用。 |
| [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) | 创建 Cosmos DB 帐户。 |
| [`az cosmosdb list-connection-strings`](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | 列出指定 Cosmos DB 帐户的连接字符串。 |
| [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | 创建或更新应用服务应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
