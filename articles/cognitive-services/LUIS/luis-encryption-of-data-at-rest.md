---
title: 语言理解服务静态数据加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍语言理解 (LUIS) 的静态数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 2dcfff005eaaac034f5fed13b6d4d18e20d2afae
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018967"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>语言理解服务静态数据加密

语言理解服务在将数据保存到云时会自动加密数据。 语言理解服务加密可保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

使用符合 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [的256位 AES 加密对](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 数据进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 此外，还可以选择用自己的密钥来管理你的订阅，名为客户管理的密钥 (CMK) 。 CMK 提供更大的灵活性来创建、轮换、禁用和撤消访问控制。 此外，你还可以审核用于保护数据的加密密钥。

## <a name="customer-managed-keys-with-azure-key-vault"></a>客户管理的密钥和 Azure Key Vault

还可以选择使用自己的密钥来管理订阅。 客户管理的密钥 (CMK) ，也称为自带密钥 (BYOK) ，提供更高的灵活性来创建、轮换、禁用和撤消访问控制。 此外，你还可以审核用于保护数据的加密密钥。

必须使用 Azure Key Vault 来存储客户管理的密钥。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 认知服务资源和密钥保管库必须位于同一区域，并且在相同的 Azure Active Directory (Azure AD) 租户中，但它们可以位于不同的订阅中。 有关 Azure Key Vault 的详细信息，请参阅[什么是 Azure Key Vault？](../../key-vault/general/overview.md)。

### <a name="customer-managed-keys-for-language-understanding"></a>语言理解的客户托管密钥

若要请求使用客户管理的密钥的功能，请填写并提交 [LUIS 服务 Customer-Managed 密钥请求窗体](https://aka.ms/cogsvc-cmk)。 你大约需要 3-5 个工作日才能收到关于请求状态的回复。 视情况而定，你可能需要排队，并在空间可用时获批。 批准将 CMK 与 LUIS 配合使用后，需要从 Azure 门户创建新的语言理解资源，并选择 E0 作为定价层。 新 SKU 的工作方式与已提供的 F0 SKU 相同（CMK 除外）。 用户无法从 F0 升级到新的 E0 SKU。

![LUIS 订阅图像](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>限制

使用带有现有/以前创建的应用程序的 E0 层时有一些限制：

* 迁移到 E0 资源将被阻止。 用户只能将其应用迁移到 F0 资源。 将现有资源迁移到 F0 后，可以在 E0 层中创建新的资源。 有关迁移的详细信息，请参阅 [此处](./luis-migration-authoring.md)。  
* 阻止将应用程序移到 E0 资源或从 E0 资源移动应用程序。 此限制的解决方法是导出现有的应用程序，并将其作为 E0 资源导入。
* 不支持必应拼写检查功能。
* 如果你的应用程序是 E0，则会禁用日志记录最终用户流量。
* 对于 E0 层中的应用程序，Azure Bot 服务中的语音填充功能不受支持。 此功能通过 Azure Bot 服务提供，该服务不支持 CMK。
* 门户中的语音填充功能需要 Azure Blob 存储。 有关详细信息，请参阅 [自带存储](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)。

### <a name="enable-customer-managed-keys"></a>启用客户管理的密钥

新的认知服务资源始终使用 Microsoft 托管的密钥进行加密。 创建资源时，无法启用客户管理的密钥。 客户管理的密钥存储在 Azure Key Vault 中，并且必须使用访问策略对密钥保管库进行预配，此访问策略向与认知服务资源关联的托管标识授予密钥权限。 只有使用 CMK 定价层创建了资源后，托管标识才可用。

若要了解如何将客户托管的密钥用于认知服务加密的 Azure Key Vault，请参阅：

- [将客户托管的密钥配置 Key Vault 用于认知服务加密的 Azure 门户](../Encryption/cognitive-services-encryption-keys-portal.md)

启用客户托管的密钥还将启用系统分配的托管标识，一项功能 Azure AD。 启用系统分配的托管标识后，此资源将注册 Azure Active Directory。 注册后，将向托管标识授予对客户管理密钥安装过程中选择的 Key Vault 的访问权限。 你可以了解有关 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的详细信息。

> [!IMPORTANT]
> 如果禁用系统分配的托管标识，则将删除对密钥保管库的访问权限，并且任何使用客户密钥加密的数据将无法再访问。 所有依赖于此数据的功能都将停止工作。

> [!IMPORTANT]
> 托管标识当前不支持跨目录方案。 在 Azure 门户中配置客户管理的密钥时，会自动在这些内容下分配托管标识。 如果随后将订阅、资源组或资源从一个 Azure AD 目录移动到另一个目录，则与该资源关联的托管标识不会传输到新租户，因此，客户管理的密钥可能不再有效。 有关详细信息，请参阅 [Azure 资源的常见问题解答和已知问题](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)中的“在 Azure AD 目录之间转移订阅”。  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>将客户管理的密钥存储在 Azure 密钥保管库

若要启用客户管理的密钥，必须使用 Azure Key Vault 来存储密钥。 必须同时启用密钥保管库上的“软删除”和“不清除”属性 。

认知服务加密仅支持大小为2048的 RSA 密钥。 有关密钥的详细信息，请参阅[关于 Azure Key Vault 密钥、机密和证书](../../key-vault/general/about-keys-secrets-certificates.md)中的“Key Vault 密钥”。

### <a name="rotate-customer-managed-keys"></a>轮换客户管理的密钥

可以根据自己的合规性策略，在 Azure 密钥保管库中轮换客户管理的密钥。 当密钥旋转时，必须更新认知服务资源才能使用新密钥 URI。 若要了解如何更新资源以在 Azure 门户中使用新版本的密钥，请参阅 [使用 Azure 门户为认知服务配置客户管理的密钥](../Encryption/cognitive-services-encryption-keys-portal.md)中的 "**更新密钥版本**" 一节。

旋转密钥不会触发重新加密资源中的数据。 用户无需执行任何其他操作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤消对客户管理的密钥的访问权限

若要撤消对客户管理的密钥的访问权限，请使用 PowerShell 或 Azure CLI。 有关详细信息，请参阅 [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) 或 [Azure Key Vault CLI](/cli/azure/keyvault)。 撤消访问权限会有效地阻止访问认知服务资源中的所有数据，因为认知服务无法访问加密密钥。

## <a name="next-steps"></a>后续步骤

* [LUIS 服务 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)
