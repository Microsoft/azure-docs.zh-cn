---
title: 使用 Azure CLI 创建工作区
titleSuffix: Azure Machine Learning
description: 了解如何使用适用于机器学习的 Azure CLI 扩展创建新的 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: b6b23e792aaef4d70e9ffc9be3667f0abef49e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102489542"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>使用 Azure CLI 创建 Azure 机器学习工作区


本文介绍如何使用 Azure CLI 创建 Azure 机器学习工作区。 Azure CLI 提供用于管理 Azure 资源的命令。 CLI 的机器学习扩展提供用于处理 Azure 机器学习资源的命令。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 如果没有订阅，可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。

* 若要从本地环境使用本文档中的 CLI 命令，需要使用 [Azure CLI](/cli/azure/install-azure-cli)。

    如果使用的是 [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)，则可通过浏览器访问 CLI（它位于云端）。

## <a name="limitations"></a>限制

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>将 CLI 连接到 Azure 订阅

> [!IMPORTANT]
> 如果使用的是 Azure Cloud Shell，可跳过此部分。 Cloud Shell 会使用你登录 Azure 订阅所用的帐户自动对你进行身份验证。

可使用多种方法通过 CLI 对 Azure 订阅进行身份验证。 最简单的方法是使用浏览器进行交互式身份验证。 若要以交互方式进行身份验证，请打开命令行或终端，然后使用以下命令：

```azurecli-interactive
az login
```

如果 CLI 可以打开默认的浏览器，则它会打开该浏览器并加载登录页。 否则，需要打开浏览器并按照命令行中的说明操作。 按说明操作时，需要浏览到 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 并输入授权代码。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

有关其他身份验证方法，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

## <a name="install-the-machine-learning-extension"></a>安装机器学习扩展

若要安装机器学习扩展，请使用以下命令：

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区依赖于以下 Azure 服务或实体：

> [!IMPORTANT]
> 如果未指定现有 Azure 服务，则将在创建工作区期间自动创建一个。 必须始终指定资源组。 附加自己的存储帐户时，请确保它满足以下条件：
>
> * 存储帐户不是高级帐户（Premium_LRS 和 Premium_GRS）
> * Azure Blob 和 Azure 文件功能同时启用
> * 分层命名空间（ADLS 第 2 代）已禁用
>
> 这些要求仅适用于工作区使用的默认存储帐户。

| 服务 | 用于指定现有实例的参数 |
| ---- | ---- |
| **Azure 资源组** | `-g <resource-group-name>`
| **Azure 存储帐户** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure 容器注册表** | `--container-registry <service-id>` |

Azure 容器注册表 (ACR) 当前不支持在资源组名称中使用 unicode 字符。 若要缓解此问题，请使用不包含这些字符的资源组。

### <a name="create-a-resource-group"></a>创建资源组

必须在资源组中创建 Azure 机器学习工作区。 可以使用现有资源组，也可以创建新组。 若要创建新的资源组，请使用以下命令。 将 `<resource-group-name>` 替换为此资源组使用的名称。 将 `<location>` 替换为此资源组使用的 Azure 区域：

> [!TIP]
> 应选择可使用 Azure 机器学习的区域。 有关详细信息，请参阅 [各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

此命令的响应类似于以下 JSON：

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

要详细了解如何使用资源组，请参阅 [az group](/cli/azure/group)。

### <a name="automatically-create-required-resources"></a>自动创建所需的资源

若要创建将在其中自动创建服务的新工作区，请使用以下命令：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> 工作区名称不区分大小写。

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>虚拟网络和专用终结点

> [!IMPORTANT]
> Azure 政府区域不支持使用具有专用链接的 Azure 机器学习工作区。

如果希望仅允许虚拟网络访问工作区，可以使用以下参数：

* `--pe-name`：创建的专用终结点的名称。
* `--pe-auto-approval`：是否应自动批准专用终结点与工作区的连接。
* `--pe-resource-group`：用于创建专用终结点的资源组。 必须是包含虚拟网络的同一个组。
* `--pe-vnet-name`：用于创建专用终结点的现有虚拟网络。
* `--pe-subnet-name`：用于创建专用终结点的子网的名称。 默认值为 `default`。

有关将专用终结点和虚拟网络与工作区结合使用的详细信息，请参阅[虚拟网络隔离和隐私概述](how-to-network-security-overview.md)。

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>客户管理的密钥和极大影响业务的工作区

默认情况下，工作区的元数据存储在 Microsoft 维护的 Azure Cosmos DB 实例中。 该数据是使用 Microsoft 管理的密钥加密的。

> [!NOTE]
> Azure Cosmos DB 不用于存储信息，例如模型性能、试验记录的信息或模型部署中记录的信息。 有关监视这些项的详细信息，请参阅体系结构和概念一文的[监视和日志记录](concept-azure-machine-learning-architecture.md)部分。

可以使用自己的密钥，而不使用 Microsoft 管理的密钥。 这样做会创建在 Azure 订阅中存储元数据的 Azure Cosmos DB 实例。 使用 `--cmk-keyvault` 参数指定包含密钥的 Azure Key Vault，并使用 `--resource-cmk-uri` 指定保管库中密钥的 URL。

必须先执行以下操作才能使用 `--cmk-keyvault` 和 `--resource-cmk-uri` 参数：

1. 授予机器学习应用（在“标识和访问管理”中）对订阅的参与者权限。
1. 按照[配置客户管理的密钥](../cosmos-db/how-to-setup-cmk.md)中的步骤完成以下操作：
    * 注册 Azure Cosmos DB 提供程序
    * 创建和配置 Azure Key Vault
    * 生成密钥

你无需手动创建 Azure Cosmos DB 实例，系统会在创建工作区期间为你创建一个实例。 将使用一个基于 `<your-resource-group-name>_<GUID>` 模式的名称在单独的资源组中创建此 Azure Cosmos DB 实例。

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

若要限制 Microsoft 在你的工作区中收集的数据，请使用 `--hbi-workspace` 参数。 

> [!IMPORTANT]
> 只能在创建工作区时选择高业务影响。 在创建工作区后，不能更改此设置。

有关客户管理的密钥和极大影响业务的工作区的详细信息，请参阅 [Azure 机器学习的企业安全性](concept-data-encryption.md#encryption-at-rest)。

### <a name="use-existing-resources"></a>使用现有资源

若要创建使用现有资源的工作区，必须提供这些资源的 ID。 使用以下命令获取服务的 ID：

> [!IMPORTANT]
> 无需指定所有现有资源。 可以指定一个或多个资源。 例如，可以指定某个现有存储帐户，工作区会创建其他资源。

+ **Azure 存储帐户**：`az storage account show --name <storage-account-name> --query "id"`

    此命令的响应类似于以下文本，它是存储帐户的 ID：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > 若要使用现有 Azure 存储帐户，则该帐户不能是高级帐户（Premium_LRS 和 Premium_GRS）。 它也不能具有分层命名空间（与 Azure Data Lake Storage Gen2 一起使用）。 工作区的默认存储帐户不支持高级存储和分层命名空间。 可以将高级存储或分层命名空间用于非默认存储帐户。

+ **Azure Application Insights**：

    1. 安装 Application Insights 扩展：

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. 获取 Application Insights 服务的 ID：

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        此命令的响应类似于以下文本，它是 Application Insights 服务的 ID：

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure 密钥保管库**：`az keyvault show --name <key-vault-name> --query "ID"`

    此命令的响应类似于以下文本，它是密钥保管库的 ID：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure 容器注册表**：`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    此命令的响应类似于以下文本，它是容器注册表的 ID：

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > 必须先为容器注册表启用[管理员帐户](../container-registry/container-registry-authentication.md#admin-account)，然后才能将其用于 Azure 机器学习工作区。

获取要用于工作区的资源的 ID 后，请使用基础 `az workspace create -w <workspace-name> -g <resource-group-name>` 命令，并添加现有资源的参数和 ID。 例如，以下命令创建一个使用现有容器注册表的工作区：

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>列出工作区

若要列出 Azure 订阅的所有工作区，请使用以下命令：

```azurecli-interactive
az ml workspace list
```

此命令的输出类似于以下 JSON：

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

有关详细信息，请参阅 [az ml workspace list](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-list) 文档。

## <a name="get-workspace-information"></a>获取工作区信息

若要获取有关工作区的信息，请使用以下命令：

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

有关详细信息，请参阅 [az ml workspace show](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-show) 文档。

## <a name="update-a-workspace"></a>更新工作区

若要更新工作区，请使用以下命令：

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

此命令的输出类似于以下 JSON：

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

有关详细信息，请参阅 [az ml workspace update](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-update) 文档。

## <a name="share-a-workspace-with-another-user"></a>与另一用户共享工作区

若要与订阅中的另一用户共享工作区，请使用以下命令：

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

如需详细了解 Azure 机器学习的 Azure 基于角色的访问控制 (Azure RBAC)，请参阅[管理用户和角色](how-to-assign-roles.md)。

有关详细信息，请参阅 [az ml workspace share](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-share) 文档。

## <a name="sync-keys-for-dependent-resources"></a>同步依赖资源的密钥

如果更改工作区使用的资源之一的访问密钥，则工作区需要大约一个小时才能与新密钥同步。 若要强制工作区立即同步新密钥，请使用以下命令：

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

有关更改密钥的详细信息，请参阅[重新生成存储访问密钥](how-to-change-storage-access-key.md)。

有关详细信息，请参阅 [az ml workspace sync-keys](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-sync-keys) 文档。

## <a name="delete-a-workspace"></a>创建工作区

若要删除不再需要的工作区，请使用以下命令：

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> 删除某个工作区不会删除该工作区使用的 Application Insights、存储帐户、密钥保管库或容器注册表。

也可以删除资源组，这会删除该资源组中的工作区和所有其他 Azure 资源。 若要删除资源组，请使用以下命令：

```azurecli-interactive
az group delete -g <resource-group-name>
```

有关详细信息，请参阅 [az ml workspace delete](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-delete) 文档。

## <a name="troubleshooting"></a>疑难解答

### <a name="resource-provider-errors"></a>资源提供程序错误

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>移动工作区

> [!WARNING]
> 不支持将 Azure 机器学习工作区移动到另一个订阅，或将拥有的订阅移到新租户。 这样做可能会导致错误。

### <a name="deleting-the-azure-container-registry"></a>删除 Azure 容器注册表

Azure 机器学习工作区使用 Azure 容器注册表 (ACR) 执行某些操作。 首次需要 ACR 实例时，它会自动创建一个。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>后续步骤

有关适用于机器学习的 Azure CLI 扩展的详细信息，请参阅 [az ml](/cli/azure/ext/azure-cli-ml/ml) 文档。