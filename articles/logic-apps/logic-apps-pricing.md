---
title: 定价和计费模型
description: 概述 Azure 逻辑应用的定价和计费模型的工作原理
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 520b4a0e87f27a90a604947ae0b558066b4ab82f
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937587"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure 逻辑应用的定价模型

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)可以创建和运行可在云中缩放的自动化集成工作流。 本文介绍 Azure 逻辑应用的计费和定价方式。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>消耗量定价模型

对于在公共的“全局”多租户 Azure 逻辑应用服务中运行的新逻辑应用，只需根据实际使用的资源付费。 这些逻辑应用使用基于消耗量的计划和定价模型。 在逻辑应用中，每一步都是操作，而 Azure 逻辑应用会对逻辑应用中运行的所有操作进行计量。

例如，操作包括：

* [触发器](#triggers)（特殊的操作）。 所有逻辑应用需将一个触发器用作第一个步骤。

* [“内置”或本机操作](../connectors/apis-list.md#built-in)，例如 HTTP、对 Azure Functions 和 API 管理的调用，等等

* 调用[托管连接器](../connectors/apis-list.md#managed-connectors)，例如 Outlook 365、Dropbox 等

* [控制工作流操作](../connectors/apis-list.md#control-workflow)，例如循环、条件语句等

[标准连接器](../connectors/apis-list.md#managed-connectors)按[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费。 正式发布的[企业连接器](../connectors/apis-list.md#managed-connectors)按[企业连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费，而公共预览版企业连接器则按[标准连接器价格](https://azure.microsoft.com/pricing/details/logic-apps)收费。

详细了解如何在[触发器](#triggers)和[操作](#actions)级别计费。 或者参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md)，了解有关限制的信息。

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>固定定价模型

[ *Integration service 环境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)提供一种隔离方式，用于创建和运行可访问 Azure 虚拟网络中的资源的逻辑应用。 在 ISE 中运行的逻辑应用不会产生数据保留成本。 创建 ISE 时，仅在创建过程中，可以选择一个具有不同[定价费率](https://azure.microsoft.com/pricing/details/logic-apps)的[ise 级别或 "SKU"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)：

* **高级** ISE：此 SKU 的基本单元具有固定容量，但如果需要更大的吞吐量，则可以在 ISE 创建过程中或之后 [添加更多缩放单位](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) 。 有关 ISE 限制，请参阅 [Azure 逻辑应用的限制和配置](logic-apps-limits-and-config.md#integration-service-environment-ise)。

* **开发人员** ISE：此 SKU 没有任何服务级别协议 (SLA) ，而且没有发布的限制。 此 SKU 仅可用于试验、开发和测试，不可用于生产和性能测试。

对于在 ISE 中创建和运行的逻辑应用，将按 [固定价格](https://azure.microsoft.com/pricing/details/logic-apps) (与按使用付费) ：

* [内置](../connectors/apis-list.md#built-in) 触发器和操作

  在 ISE 中，内置触发器和操作显示 " **核心** " 标签，并在与逻辑应用相同的 ISE 中运行。

* [标准](../connectors/apis-list.md#managed-connectors) 连接器和 [企业](../connectors/apis-list.md#enterprise-connectors) 连接器，可让你拥有任意数量的企业连接

   显示 **ise** 标签的标准和企业连接器与逻辑应用在同一 ISE 中运行。 不显示 ISE 标签的连接器在公共、"全局"、多租户逻辑应用服务中运行。 当你将固定定价用于在 ISE 中运行的逻辑应用时，它还适用于在多租户服务中运行的连接器。

* 基于[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)，无需额外付费即可使用[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)：

  * **高级** ISE SKU：单一 [标准层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 集成帐户

  * **开发人员** ISE SKU：单一 [免费层](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 集成帐户

  不管 SKU 如何，每个 ISE 都可以有 [有限数量的集成帐户](logic-apps-limits-and-config.md#integration-account-limits)。 你可以提高此限制，增加成本：

  * **高级** ISE SKU：最多4个标准帐户。 无免费帐户或基本帐户。

  * **开发人员** ISE SKU：最多4个标准帐户或最多5个标准帐户。 无基本帐户。

  有关集成帐户限制的详细信息，请参阅 [Azure 逻辑应用的限制和配置](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 可以在本主题的后面部分了解有关 [集成帐户层及其定价模型](#integration-accounts) 的详细信息。

<a name="connectors"></a>

## <a name="connectors"></a>连接器

Azure 逻辑应用连接器通过提供[触发器](#triggers)和/或[操作](#actions)，帮助逻辑应用访问云中或本地的应用、服务和系统。 连接器分类为“标准”或“企业”连接器。 有关这些连接器的概述，请参阅[适用于 Azure 逻辑应用的连接器](../connectors/apis-list.md)。 如果没有预生成的连接器可用于要在逻辑应用中使用的 REST API，则可以创建[自定义连接器](/connectors/custom-connectors)，这些连接器只是这些 REST API 的包装器。 自定义连接器按标准连接器计费。 以下部分提供有关触发器和操作的计费方式的详细信息。

<a name="triggers"></a>

## <a name="triggers"></a>触发器

触发器始终是逻辑应用工作流中的第一步，是在满足特定条件或发生特定事件时创建并运行逻辑应用实例的特殊操作。 触发器以不同方式起作用，从而影响逻辑应用的计量方式。 下面是 Azure 逻辑应用中存在的各种触发器：

* **定期触发器**：你可以使用不特定于任何服务或系统的此泛型触发器来启动任何逻辑应用工作流，并创建一个基于在触发器中设置的重复间隔运行的逻辑应用实例。 例如，可以设置每隔三天运行的，或者根据更复杂的计划运行的定期触发器。

* **轮询触发器**：你可以使用这种更为专用的重复触发器，该触发器通常与特定服务或系统的托管连接器相关联，以根据在触发器中设置的重复间隔来检查符合用于创建和运行逻辑应用实例的条件的事件或消息。 即使未创建任何逻辑应用实例，例如，跳过触发器时，逻辑应用服务会将每个轮询请求作为一个执行。 若要指定轮询间隔，请通过逻辑应用程序设计器设置触发器。

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 触发器**：可使用 webhook 触发器等待客户端将请求发送到特定终结点 URL 中的逻辑应用，而不使用轮询触发器。 发送到 webhook 终结点的每个请求计为一个操作执行。 例如，请求和 HTTP Webhook 触发器都是泛型 Webhook 触发器。 某些服务或系统连接器还具有 webhook 触发器。

<a name="actions"></a>

## <a name="actions"></a>操作

Azure 逻辑应用将 HTTP 等“内置”操作作为本机操作进行计量。 例如，内置操作包括 HTTP 调用、来自 Azure Functions 或 API 管理的调用，以及条件、循环和开关语句等控制流步骤。 每个操作具有自身的操作类型。 例如，调用[连接器](/connectors)的操作为“ApiConnection”类型。 这些连接器分类为“标准”或“企业”连接器，根据各自的[定价](https://azure.microsoft.com/pricing/details/logic-apps)进行计量。 预览版的企业连接器按标准连接器计费。

Azure 逻辑应用将所有成功和不成功的操作作为执行进行计量。 但是，逻辑应用不会计量以下操作：

* 由于未满足条件而跳过的操作
* 由于逻辑应用在完成之前停止而未运行的操作

对于在循环内部运行的操作，Azure 逻辑应用会对循环中每个周期的每个操作进行计数。 例如，假设有一个处理列表的“每个”循环。 逻辑应用通过将列表项的数量乘以循环中的操作数来计量该循环中的操作，并加上启动循环的操作。 因此，包含 10 个项的列表的计算公式为 (10 * 1) + 1，即 11 个操作执行。

## <a name="disabled-logic-apps"></a>禁用的逻辑应用

禁用的逻辑应用在禁用期间不会产生费用，因为它们无法创建新实例。 禁用逻辑应用后，当前正在运行的实例可能需要在一段时间之后才会完全停止。

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>集成帐户

[固定定价模型](https://azure.microsoft.com/pricing/details/logic-apps)适用于[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)，此类帐户可用于免费浏览、开发和测试 Azure 逻辑应用中的 [B2B 和 EDI](logic-apps-enterprise-integration-b2b.md) 和 [XML 处理](logic-apps-enterprise-integration-xml.md)功能。 每个 Azure 订阅最多可以有一项[集成帐户的特定限制](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)。 每个集成帐户最多可存储项目的特定 [限制](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)，包括贸易合作伙伴、协议、地图、架构、程序集、证书、批配置等。

Azure 逻辑应用提供免费、基本和标准集成帐户。 逻辑应用服务级别协议 (SLA) 支持“基本”和“标准”层级，而“免费”层级则不受 SLA 支持并有区域可用性、吞吐量和使用方面的限制。 每个 Azure 区域中可以有多个集成帐户，“免费”层级集成帐户除外。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps/)。

如果你的 [ *integration service 环境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)，无论使用哪种 [SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)，你的 ise 都可以有 [有限数量的集成帐户](logic-apps-limits-and-config.md#integration-account-limits)，但你可以 [提高此限制以增加成本](#fixed-pricing)。 若要了解如何为 ISE 使用固定定价模型，请参阅本主题前面的 [固定定价模型](#fixed-pricing) 部分。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

若要在免费、基本或标准集成帐户之间进行选择，请查看这些用例说明：

* **免费**：要尝试探索方案，而不是生产方案。 此级别仅适用于 Azure 中的公共区域，例如 "美国西部" 或 "东南亚"，但不适用于 [Azure 中国世纪互联](/azure/china/overview-operations) 或 [azure 政府](../azure-government/documentation-government-welcome.md)版。

* **基本**：适用于只需处理消息或充当与大型企业实体建立贸易合作关系的小型企业合作伙伴的情况

* **标准**：适用于 B2B 关系更复杂且需要管理的实体数增加的情况

<a name="data-retention"></a>

## <a name="data-retention"></a>数据保留

存储在逻辑应用的运行历史记录中的所有输入和输出都将根据逻辑应用的[运行保留期](logic-apps-limits-and-config.md#run-duration-retention-limits)进行计费，在集成服务环境 (ISE) 中运行的逻辑应用除外。 在 ISE 中运行的逻辑应用不会产生数据保留成本。 有关定价费率，请参阅[逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)。

若要更好地监视逻辑应用的存储消耗，可以执行以下操作：

* 查看逻辑应用每月使用的存储单元数（以 GB 为单位）。

* 在逻辑应用的运行历史记录中查看特定操作的输入和输出的大小。

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>查看逻辑应用存储消耗

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中的“监视”下，选择“指标”。 

1. 从右侧窗格的“图表标题”下的“指标”列表中，选择“按消耗存储的执行操作的使用情况计费”。  

   此指标表示每月计费的存储消耗单元数 (GB)。

   > [!NOTE]
   > 在存储中使用小于 500 MB 的运行可能不会出现在 "监视" 视图中，但仍会计费。

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>查看操作输入和输出大小

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中，选择“概述”。

1. 在右侧窗格的“运行历史记录”下，选择具有要检查的输入和输出的运行。

1. 在“逻辑应用运行”下，选择“运行详细信息”。

1. 在“逻辑应用运行详细信息”窗格的“操作表”中（其中列出了每个操作的状态和持续时间），选择要查看的操作。

1. 在 " **逻辑应用操作** " 窗格中，查找该操作的输入和输出的大小。 在 " **输入链接** 和 **输出链接**" 下，找到这些输入和输出的链接。

   > [!NOTE]
   > 对于循环，仅顶级操作显示其输入和输出的大小。 对于嵌套循环内的操作，输入和输出显示零大小和无链接。

## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 逻辑应用](logic-apps-overview.md)
* [创建第一个逻辑应用](quickstart-create-first-logic-app-workflow.md)
