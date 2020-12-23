---
title: 重新激活已禁用的 Azure 订阅
description: 说明何时会禁用用户的 Azure 订阅，以及如何重新激活它。
keywords: azure 订阅已禁用
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: cad3082981bcfc699bc230badf44e2ffc2e1bed3
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744419"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>重新激活已禁用的 Azure 订阅

Azure 订阅被禁用可能是因为额度已过期、达到了支出限制、账单过期未付、达到了信用卡限制，或者订阅已被帐户管理员取消。 了解自己的情况属于什么样的问题，按照本文所述步骤重新激活订阅。

## <a name="your-credit-is-expired"></a>信用已过期

注册 Azure 免费帐户时，将获得免费试用订阅，它提供为期 30 天、价值 $200 的 Azure 信用额度以及 12 个月的免费服务。 30 天结束后，Azure 会禁用订阅。 禁用订阅是为了防止你在意外情况下因使用量超出订阅的信用额度和免费服务而产生相应的费用。 若要继续使用 Azure 服务，则必须[升级订阅](upgrade-azure-subscription.md)。 升级之后，你的订阅仍可使用 12 个月的免费服务。 只需对超过免费服务及用量的使用量付费即可。

## <a name="you-reached-your-spending-limit"></a>已达到支出限制

具有用于免费试用和 Visual Studio Enterprise 等功能的信用额度的 Azure 订阅对这些服务具有支出限制。 这意味着只能在包含的信用额度内使用这些服务。 当使用量达到支出限制后，Azure 将在此计费周期的剩余时间内禁用订阅。 禁用订阅是为了防止你在意外情况下因使用量超出订阅的信用额度而产生相应的费用。 若要移除支出限制，请参阅[移除帐户中心中的支出限制](spending-limit.md#remove)。

> [!NOTE]
> 如果使用的是免费试用版订阅且移除了支出限制，则在免费试用结束时会将订阅转换为采用即用即付费率的个人订阅。 创建订阅后，可保留剩余信用额度整整 30 天。 仍可使用 12 个月的免费服务。

要监视和管理 Azure 的计费活动，请参阅[计划管理 Azure 成本](../understand/plan-manage-costs.md)。


## <a name="your-bill-is-past-due"></a>帐单已过期

若要处理逾期未付余额，请参阅[从 Azure 收到电子邮件后处理 Azure 订阅的逾期未付余额问题](resolve-past-due-balance.md)。

## <a name="the-bill-exceeds-your-credit-card-limit"></a>帐单金额超出了信用卡限制

为了解决此问题，请[改用其他信用卡](change-credit-card.md)。 或者，如果代表的是公司，则可[改用发票付款](pay-by-invoice.md)。

## <a name="the-subscription-was-accidentally-canceled"></a>订阅被意外取消

如果你是帐户管理员，并且意外取消了采用即用即付费率的个人订阅，则可以在帐户中心重新激活它。

1. 登录到[帐户中心](https://account.windowsazure.com/Subscriptions)。
1. 选择已取消的订阅。
1. 单击“重新激活”。 

    ![屏幕截图，右侧窗格中显示重新激活链接](./media/subscription-disabled/reactivate-sub.png)

对于其他订阅类型，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以重新激活订阅。

## <a name="after-reactivation"></a>重新激活后

重新激活订阅后，创建或管理资源可能存在延迟。 如果延迟超过 30 分钟，请联系 [Azure 计费支持](https://go.microsoft.com/fwlink/?linkid=2083458)以获取帮助。 大多数 Azure 资源可自动恢复，无需任何操作。 但建议检查 Azure 服务资源，并重启所有无法自动恢复的资源。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- 了解如何[计划管理 Azure 成本](../understand/plan-manage-costs.md)。
