---
title: 使用连接的存储创建函数应用 - Azure CLI
description: Azure CLI 脚本示例 - 创建用于连接到 Azure 存储的 Azure Function
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: b95ec2da96e385ba4595a5552e64d6cc039c0e16
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565324"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>创建具有命名存储帐户连接的函数应用 

此 Azure Functions 示例脚本先创建一个函数应用，然后将该函数连接到 Azure 存储帐户。 创建的应用设置（包含连接）可以与[存储触发器或绑定](../functions-bindings-storage-blob.md)配合使用。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

此示例创建 Azure Function app，并将存储连接字符串添加到应用设置。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 使用相关位置创建资源组。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 创建存储帐户。 |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | 在无服务器[消耗计划](../functions-scale.md#consumption-plan)中创建函数应用。 |
| [az storage account show-connection-string](/cli/azure/storage/account#az-storage-account-show-connection-string) | 获取帐户的连接字符串。 |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | 将连接字符串设置为函数应用中的应用设置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure Functions 文档](../functions-cli-samples.md)中找到其他 Azure Functions CLI 脚本示例。
