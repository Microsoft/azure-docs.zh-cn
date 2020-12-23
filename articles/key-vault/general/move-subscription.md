---
title: Azure Key Vault 将保管库移动到不同的订阅 | Microsoft Docs
description: 有关将密钥保管库移动到不同订阅的指南。
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: e0cd4cad74257dbf83ec8d30405eacca341a8d31
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289530"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>将 Azure Key Vault 移动到另一个订阅

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>概述

> [!IMPORTANT]
> **将密钥保管库移动到另一个订阅会导致环境发生中断性变更。**
> 在决定将密钥保管库移动到新订阅之前，请确保你了解此变更的影响，并仔细遵循本文中的指导。
> 如果使用的是托管服务标识 (MSI)，请阅读此文档末尾的移动后说明。 

创建密钥保管库时，它会自动绑定到创建它的订阅的默认 Azure Active Directory 租户 ID。 所有访问策略条目也都绑定到此租户 ID。 如果将 Azure 订阅从租户 A 移到租户 B，租户 B 中的服务主体（用户和应用程序）将无法访问现有的密钥保管库。若要解决此问题，需执行以下操作：

* 将与订阅中所有现有密钥保管库关联的租户 ID 更改到租户 B。
* 删除所有现有的访问策略条目。
* 添加与租户 B 关联的新访问策略条目。

## <a name="limitations"></a>限制

> [!IMPORTANT]
> **无法移动用于磁盘加密的密钥保管库** 如果你使用的是 VM 的磁盘加密的密钥保管库，则在启用磁盘加密时，无法将密钥保管库移到其他资源组或订阅。 将密钥保管库移动到新的资源组或订阅之前，必须先禁用磁盘加密。 

某些服务主体（用户和应用程序）绑定到特定的租户。 如果将密钥保管库移动到其他租户中的订阅，则可能无法还原对特定服务主体的访问权限。 请进行检查，确保在要将密钥保管库移动到其中的租户中存在所有基本的服务主体。

## <a name="design-considerations"></a>设计注意事项

你的组织可能已在订阅级别实施了带有强制执行或排除机制的 Azure Policy。 在密钥保管库当前所在的订阅与要将密钥保管库移动到其中的订阅之间，可能存在一组不同的策略分配。 如果策略要求冲突，可能会破坏应用程序。

### <a name="example"></a>示例

你有一个连接到密钥保管库的应用程序，该密钥保管库创建的证书有效期为两年。 你尝试将密钥保管库移动到其中的订阅有一个策略分配，该策略分配阻止创建有效期超过一年的证书。 将密钥保管库移动到新订阅后，创建有效期为两年的证书的操作会被 Azure 策略分配阻止。

### <a name="solution"></a>解决方案

请确保转到 Azure 门户上的“Azure Policy”页，查看当前订阅的策略分配以及要移动到其中的订阅的策略分配，并确保没有不匹配项。

## <a name="prerequisites"></a>先决条件

* 对密钥保管库所在的当前订阅具有参与者级别或更高级别的访问权限。
* 对要将密钥保管库移动到其中的订阅具有参与者级别或更高级别的访问权限。
* 新订阅中有一个资源组。

## <a name="procedure"></a>过程

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>将 Key Vault 移到同一租户中的新订阅

1. 登录到 Azure 门户
2. 导航到你的密钥保管库
3. 单击“概览”选项卡
4. 选择“移动”按钮
5. 从下拉选项中选择“移动到另一个订阅”
6. 选择要将密钥保管库移动到其中的资源组
7. 确认有关移动资源的警告
8. 选择“确定”

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>附加步骤（如果将密钥保管库移到了新租户中的订阅）

如果将密钥保管库移到了新租户中的订阅，则需要手动更新租户 ID 并删除旧的访问策略。 下面是通过 PowerShell 和 Azure CLI 执行这些步骤的教程。 如果使用的是 PowerShell，可能需要运行下面所述的 Clear-AzContext 命令，这样就能查看当前所选范围外的资源。 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

既然保管库已与正确的租户 ID 关联，并且旧的访问策略条目已删除，请使用 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet 或 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令设置新的访问策略条目。

如果使用的是 Azure 资源的托管标识，则还需要将其更新为新的 Azure Active Directory 租户。 有关托管标识的详细信息，请参阅[托管标识概述](../../active-directory/managed-identities-azure-resources/overview.md)。

如果使用的是托管标识，则还必须更新标识，因为旧标识将不再位于相应的 Azure Active Directory 租户中。 参阅下述有助于解决此问题的文档。 

* [更新 MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [将订阅转移到新目录](../../role-based-access-control/transfer-subscription.md)