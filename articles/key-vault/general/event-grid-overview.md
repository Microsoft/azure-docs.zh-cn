---
title: 通过 Azure 事件网格监视 Key Vault
description: 使用 Azure 事件网格订阅 Key Vault 事件
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 4fb6d57bb84f4a3b4c5c138be9306489191bfce8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753355"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>通过 Azure 事件网格监视 Key Vault

通过将 Key Vault 与事件网格集成，用户可以在密钥保管库中存储的机密的状态发生更改时收到通知。 状态更改将定义为即将到期的机密（到期前 30 天）、已过期的机密或具有可用新版本的机密。 支持所有三种机密类型（密钥、证书和机密）的通知。

应用程序可以响应这些使用新式无服务器体系结构的事件，无需采用复杂代码或昂贵低效的轮询服务。 可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向事件处理程序（如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)），甚至是向自己的 Webhook 推送事件，且仅需为使用的内容付费。 有关定价的详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="key-vault-events-and-schemas"></a>Key Vault 事件和架构

事件网格使用[事件订阅](../../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 Key Vault 事件包含响应数据更改所需的所有信息。 可以识别 Key Vault 事件，因为 eventType 属性以“Microsoft.KeyVault”开头。

有关详细信息，请参阅 [Key Vault 事件架构](../../event-grid/event-schema-key-vault.md)。

> [!WARNING]
> 通知事件仅在新版本的机密、密钥和证书上触发，并且你必须先在密钥保管库中订阅该事件才能接收这些通知。

## <a name="practices-for-consuming-events"></a>使用事件的做法

处理 Key Vault 事件的应用程序应遵循以下建议的做法：

* 可以配置多个订阅，将事件路由至同一事件处理程序。 重要的是不要假设事件来自特定源，但要检查消息的主题，以确保它来自于你所期望的 Key Vault。
* 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
* 忽略不了解的字段。  此做法有助于适应将来可能添加的新功能。
* 使用“subject”前缀和后缀匹配项，将事件限制为特定事件。

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 概述](overview.md)
- [Azure 事件网格概述](../../event-grid/overview.md)
- 操作说明：[将 Key Vault 事件路由到自动化 Runbook](event-grid-tutorial.md)。
- 如何：[Key Vault 机密发生更改时接收电子邮件](event-grid-logicapps.md)
- [Azure Key Vault 的 Azure 事件网格事件架构](../../event-grid/event-schema-key-vault.md)
- [Azure 自动化概述](../../automation/index.yml)
