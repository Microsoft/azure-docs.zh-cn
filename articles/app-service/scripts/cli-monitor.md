---
title: CLI：使用 Web 服务器日志监视应用
description: 了解如何使用 Azure CLI 自动部署和管理应用服务应用。 此示例演示如何使用 Web 服务器日志监视应用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: 0887656f-611c-4627-8247-b5cded7cef60
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 3d8442179ecec72d47e770d823bbfd5795f5c4dc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787670"
---
# <a name="monitor-an-app-service-app-with-web-server-logs-using-azure-cli"></a>使用 Azure CLI 通过 Web 服务器日志监视应用服务应用

此示例脚本将创建资源组、应用服务计划和应用，并配置应用以启用 Web 服务器日志。 然后，它将下载日志文件以供查看。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/monitor-with-logs/monitor-with-logs.sh "Monitor Logs")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、应用服务应用和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | 创建应用服务计划。 |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | 创建应用服务应用。 |
| [`az webapp log config`](/cli/azure/webapp/log#az_webapp_log_config) | 配置应用服务应用将持久保留的日志。 |
| [`az webapp log download`](/cli/azure/webapp/log#az_webapp_log_download) | 将应用服务应用的日志下载到本地计算机。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 应用服务文档](../samples-cli.md)中找到其他应用服务 CLI 脚本示例。
