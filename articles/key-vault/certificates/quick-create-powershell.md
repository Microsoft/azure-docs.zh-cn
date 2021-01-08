---
title: 快速入门：设置和查看 Azure Key Vault 证书 - Azure PowerShell
description: 本快速入门展示了如何使用 Azure PowerShell 在 Azure Key Vault 中设置和检索证书
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: ae53ebac1c2a943a2b1ca98b222a8dbab210bdb5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935115"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 在 Azure Key Vault 中设置和检索证书

在本快速入门中，你将使用 Azure PowerShell 在 Azure Key Vault 中创建一个密钥保管库。 Azure Key Vault 是一项云服务，用作安全的机密存储。 可以安全地存储密钥、密码、证书和其他机密。 有关 Key Vault 的详细信息，可以参阅[概述](../general/overview.md)。 Azure PowerShell 用于通过命令或脚本创建和管理 Azure 资源。 完成该操作后，你将存储证书。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 来创建与 Azure 的连接。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>创建密钥保管库

接下来创建 Key Vault。 执行此步骤时，需要一些信息：

虽然在本快速入门中我们始终使用“Contoso KeyVault2”作为密钥保管库的名称，但你必须使用唯一的名称。

- **保管库名称** Contoso-Vault2。
- 资源组名称 **ContosoResourceGroup**。
- **位置**“美国东部”。

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

此 cmdlet 的输出显示新创建的密钥保管库的属性。 请记下下面列出的两个属性：

* **保管库名称**：在本示例中为 **Contoso-Vault2**。 将在其他密钥保管库 cmdlet 中使用此名称。
* **保管库 URI**：在本示例中为 https://Contoso-Vault2.vault.azure.net/ 。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

创建保管库以后，你的 Azure 帐户是唯一能够对这个新的保管库执行任何操作的帐户。

## <a name="add-a-certificate-to-key-vault"></a>将证书添加到 Key Vault

若要向保管库中添加证书，只需再执行几个步骤即可。 此证书可供应用程序使用。 

键入以下命令，使用名为 ExampleCertificate  的策略创建自签名证书：

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal
Add-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

现在，可以通过 URI 来引用已添加到 Azure Key Vault 的此证书。 使用“https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate”获取当前版本。 

若要查看以前存储的证书，请使用以下命令：

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate"
```

现在，你已创建了一个密钥保管库，存储了一个证书，并检索了该证书。

## <a name="clean-up-resources"></a>清理资源

本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组和所有相关资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。 可以删除资源，如下所示：

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库并在其中存储了一个证书。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 请参阅 [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/) 参考
- 请参阅 [Key Vault 安全性概述](../general/security-overview.md)
