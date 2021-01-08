---
title: 快速入门：设置和查看 Azure Key Vault 证书 - Azure CLI
description: 以快速入门的方式介绍如何使用 Azure CLI 在 Azure Key Vault 中设置和检索证书
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 2bb718d038dd7b3f5aa6f3bac1ce1de572c8e829
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936356"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>快速入门：使用 Azure CLI 在 Azure Key Vault 中设置和检索证书

在本快速入门中，你将使用 Azure CLI 在 Azure Key Vault 中创建一个密钥保管库。 Azure Key Vault 是一项云服务，用作安全的机密存储。 可以安全地存储密钥、密码、证书和其他机密。 有关 Key Vault 的详细信息，可以参阅[概述](../general/overview.md)。 Azure CLI 用于通过命令或脚本创建和管理 Azure 资源。 完成该操作后，你将存储证书。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本快速入门需要 Azure CLI 版本 2.0.4 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“eastus”位置创建名为“ContosoResourceGroup”的资源组。

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>创建密钥保管库

接下来，将在上一步创建的资源组中创建 Key Vault。 需要提供某些信息：

- 对于本快速入门，请使用 **Contoso-vault2**。 必须在测试中提供唯一名称。
- 资源组名称 **ContosoResourceGroup**。
- 位置“美国东部”  。

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

此 cmdlet 的输出显示新创建的 Key Vault 的属性。 请记下下面列出的两个属性：

- **保管库名称**：在本示例中，此项为 **Contoso-Vault2**。 会在其他 Key Vault 命令中使用此名称。
- **保管库 URI**：在本示例中，此项为 https://contoso-vault2.vault.azure.net/ 。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="add-a-certificate-to-key-vault"></a>将证书添加到 Key Vault

若要向保管库中添加证书，只需再执行几个步骤即可。 此证书可供应用程序使用。 

键入以下命令，使用名为“ExampleCertificate”  的默认策略创建自签名证书：

```azurecli
az keyvault certificate create --vault-name "Contoso-Vault2" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

现在，可以通过 URI 来引用已添加到 Azure Key Vault 的此证书。 使用“https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate”获取当前版本。 

若要查看以前存储的证书，请使用以下命令：

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "Contoso-Vault2"
```

现在，你已创建了一个密钥保管库，存储了一个证书，并检索了该证书。

## <a name="clean-up-resources"></a>清理资源

本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。 可以删除资源，如下所示：

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库并在其中存储了一个证书。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 请参阅 [Azure CLI az keyvault 命令](/cli/azure/keyvault?view=azure-cli-latest)参考
- 请参阅 [Key Vault 安全性概述](../general/security-overview.md)
