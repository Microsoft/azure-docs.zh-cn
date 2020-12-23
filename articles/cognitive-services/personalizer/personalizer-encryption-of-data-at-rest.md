---
title: 静态数据的 Personalizer 服务加密
titleSuffix: Azure Cognitive Services
description: Microsoft 提供了 Microsoft 托管的加密密钥，还可让你使用自己的密钥（称为客户管理的密钥 (CMK)）管理你的认知服务订阅。 本文介绍了 Personalizer 的数据加密，以及如何启用和管理 CMK。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 1a27199930587c1a096dd99462ebd0c9d65054ee
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369352"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>静态数据的 Personalizer 服务加密

当你将数据保存到云时，Personalizer 服务会自动对其进行加密。 Personalizer 服务加密可以保护数据，并帮助你满足组织的安全性和符合性承诺。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> 客户托管的密钥仅可用于 E0 定价层。 若要请求使用客户管理的密钥的功能，请填写并提交 [Personalizer 服务 Customer-Managed 密钥请求窗体](https://aka.ms/cogsvc-cmk)。 大约需要3-5 个工作日内就会收到请求的状态。 根据需要，你可以将置于队列中并在空间可用时进行批准。 批准使用 CMK 与 Personalizer 服务后，将需要创建新的 Personalizer 资源，并选择 E0 作为定价层。 创建具有 E0 定价层的 Personalizer 资源后，可以使用 Azure Key Vault 来设置托管标识。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>后续步骤

* [Personalizer 服务 Customer-Managed 密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)