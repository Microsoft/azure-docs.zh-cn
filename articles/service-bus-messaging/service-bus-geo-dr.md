---
title: Azure 服务总线异地灾难恢复 | Microsoft 文档
description: 如何使用地理区域进行故障转移并在 Azure 服务总线中执行灾难恢复
ms.topic: article
ms.date: 01/04/2021
ms.openlocfilehash: c07721c07923a40da9fe28e0e3116bfd6a52210f
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862362"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure 服务总线异地灾难恢复

对于许多企业来说，甚至在某些情况下，在某些情况下，对数据处理资源灾难性中断的复原能力是必需的。 

Azure 服务总线已在跨数据中心的多个故障域的各个群集之间提高了个别计算机的灾难性故障的风险，甚至跨群集实现了整个机架的风险，并实现了透明的故障检测和故障转移机制，使服务能够继续在有保证的服务级别内操作，并且通常在发生此类故障时不会发生明显中断。 如果已使用 [可用性区域](../availability-zones/az-overview.md)的 enabled 选项创建了服务总线命名空间，则风险会进一步分散到三个物理上分离的设备，并且该服务有足够的容量预留，可立即应对整个设施的整个灾难性损失。 

对于任何本地消息代理产品，具有可用性区域支持的全部活动 Azure 服务总线群集模型优于每个本地消息代理的产品，无论是出于重 尽管如此，还可能出现一些大的物理析构，甚至这些度量值无法充分防范。 

服务总线异地灾难恢复功能旨在更轻松地从灾难中恢复，并放弃发生故障的 Azure 区域，而无需更改应用程序配置。 放弃 Azure 区域通常会涉及到几项服务，此功能主要旨在帮助保持复合应用程序配置的完整性。 此功能在全球范围内可用于服务总线高级 SKU。 

"Geo-Disaster 恢复" 功能可确保在配对时，对命名空间 (队列、主题、订阅、筛选器) 的整个配置会连续从主命名空间复制到辅助命名空间，并允许你随时启动从主命名空间到辅助命名空间的一次故障转移。 故障转移移动会将命名空间的所选别名重新指向辅助命名空间，然后中断配对。 故障转移几乎是启动后立即发生的。 

> [!IMPORTANT]
> 此功能可以实现具有相同配置的操作的即时连续性，但不 **会复制保留在队列或主题订阅或死信队列中的消息**。 若要保留队列语义，此类复制不仅需要消息数据的复制，还需要对代理中每次状态的更改。 对于大多数服务总线命名空间，所需的复制流量远远超过了应用程序流量，并且具有高吞吐量队列，大多数消息仍会在从主副本中删除的情况下复制到辅助副本，从而导致流量过浪费。 对于适用于异地灾难恢复的多个配对的高延迟复制路由，即使延迟引发的限制影响，复制流量也可能持续与应用程序流量保持同步。
 
> [!TIP]
> 若要复制队列和主题订阅的内容，并在主动/主动配置中操作相应的命名空间以应对中断和灾难，请不要对此地域灾难恢复功能集进行进一步了解，但请遵循 [复制指南](service-bus-federation-overview.md)。  

## <a name="outages-and-disasters"></a>中断和灾难

请务必注意“中断”和“灾难”的区别。 

中断是指 Azure 服务总线暂时不可用，可能会影响服务的某些组件，如消息存储，甚至是整个数据中心。 但在问题解决后，服务总线将恢复可用。 通常情况下，中断不会导致消息或其他数据丢失。 例如，数据中心的电源故障可能会导致此类中断。 某些中断由于暂时性故障或网络问题只是短时间连接丢失。 

根据定义，*灾难* 是指永久或长期丢失服务总线群集、Azure 区域或数据中心。 该区域或数据中心不一定会恢复可用，或可能停用数小时或数天。 例如，火灾、洪灾或地震等可能导致此类灾难。 永久性灾难可能会导致一些消息、事件或其他数据丢失。 不过，在大多数情况下，都不应该会有数据丢失，并且在数据中心备份后，便可以恢复消息。

Azure 服务总线的异地灾难恢复功能是一项面向灾难恢复的解决方案。 本文中所述的概念和工作流适用于灾难方案，而不适用于暂时或临时中断。 有关 Microsoft Azure 中的灾难恢复的详细讨论，请参阅[此文](/azure/architecture/resiliency/disaster-recovery-azure-applications)。   

## <a name="basic-concepts-and-terms"></a>基本概念和术语

灾难恢复功能可实现元数据灾难恢复，并且依赖于主要和次要灾难恢复命名空间。 请注意，异地灾难恢复功能仅适用于[高级 SKU](service-bus-premium-messaging.md)。 不需要对连接字符串进行任何更改，因为连接是通过别名建立的。

本文涉及以下术语：

-  *别名*：所设置的灾难恢复配置的名称。 别名提供单个稳定的完全限定域名 (FQDN) 连接字符串。 应用程序使用此别名连接字符串连接到命名空间。 使用别名可确保连接字符串在触发故障转移后保持不变。

-  *主要/次要命名空间*：别名所对应的命名空间。 主要命名空间是“主动”的，并且会接收消息（该命名空间可以是现有的命名空间，也可以是新的命名空间）。 次要命名空间是“被动”的，不接收消息。 这两者之间的元数据保持同步，因此这两者可以无缝接受消息，而不必更改任何应用程序代码或连接字符串。 若要确保只有主动命名空间接收消息，必须使用别名。 

-  *元数据*：队列、主题、订阅等实体及其与命名空间关联的服务的属性。 请注意，仅自动复制实体及其设置。 不会复制消息。

-  *故障转移*：激活辅助命名空间的过程。

## <a name="setup"></a>设置

以下部分概述了命名空间之间的设置配对。

![1][]

设置过程如下所述 -

1. 预配“主要”服务总线高级命名空间。

2. 在与预配了主要命名空间的位置不同的区域中预配“次要”服务总线高级命名空间。 这将有助于跨不同的数据中心区域进行故障隔离。

3. 在主要命名空间与次要命名空间之间创建配对以获取别名。

    >[!NOTE] 
    > 如果已[将 Azure 服务总线标准命名空间迁移到 Azure 服务总线高级命名空间](service-bus-migrate-standard-premium.md)，则必须使用预先存在的别名（即服务总线标准命名空间连接字符串）通过 PS/CLI 或 REST API 创建灾难恢复配置。
    >
    >
    > 这是因为，在迁移过程中，Azure 服务总线标准命名空间连接字符串/DNS 名称本身会成为 Azure 服务总线高级命名空间的别名。
    >
    > 客户端应用程序必须利用此别名（即 Azure 服务总线标准命名空间连接字符串）连接到已设置灾难恢复配对的高级命名空间。
    >
    > 如果使用门户来设置灾难恢复配置，则由你在门户中提供此注意事项。


4. 使用在步骤 3 中获取的别名将你的客户端应用程序连接到启用了异地灾难恢复的主要命名空间。 最初，别名指向主要命名空间。

5. [可选] 添加一些监视功能，以检测是否有必要进行故障转移。

## <a name="failover-flow"></a>故障转移流程

故障转移由客户手动触发（通过命令显式触发，或者通过客户拥有的触发了该命令的业务逻辑），从不会由 Azure 触发。 这为客户提供了对 Azure 主干的中断解决方法的完整所有权和可见性。

![4][]

在故障转移触发后 -

1. “别名”连接字符串更新为指向次要高级命名空间。

2. 客户端（发送方和接收方）自动连接到次要命名空间。

3. 主要高级命名空间与次要高级命名空间之间的现有配对将被破坏。

在故障转移启动后 -

1. 如果又出现其他中断，则需要能够再次进行故障转移。 因此，请设置另一个被动命名空间，并更新配对。 

2. 一旦以前的主要命名空间恢复可用，请从该命名空间拉取消息。 在此之后，请使用该命名空间在异地恢复设置之外进行常规消息传送，或删除旧的主要命名空间。

> [!NOTE]
> 仅支持失败转发语义。 在此方案中，先进行故障转移，然后与新的命名空间重新配对。 不支持故障回复；例如，在 SQL 群集中不支持这样做。 

可以使用监视系统或定制监视解决方案自动执行故障转移。 但是，这种自动执行需要额外的规划和工作，它超出了本文的讨论范围。

![2][]

## <a name="management"></a>管理

如果出了错，例如在初始设置过程中将错误的区域配对，则随时可以中断这两个命名空间的配对。 如果想要使用配对命名空间作为常规命名空间，请删除别名。

## <a name="use-existing-namespace-as-alias"></a>使用现有的命名空间作为别名

如果遇到不能更改生产者和使用者连接的情况，则可将命名空间名称作为别名重用。 请参阅[此处提供的 GitHub 上的示例代码](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)。

## <a name="samples"></a>示例

[GitHub 上的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/)演示如何设置和启动故障转移。 这些示例演示以下概念：

- 在 Azure Active Directory 中将 Azure 资源管理器与服务总线配合使用所需的 .NET 示例和设置，用来设置和启用异地灾难恢复。
- 执行示例代码所需的步骤。
- 如何使用现有的命名空间作为别名。
- 改用 PowerShell 或 CLI 启用异地灾难恢复的步骤。
- 使用别名从当前的主要或次要命名空间进行[发送和接收](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

## <a name="considerations"></a>注意事项

此版本需要注意以下事项：

1. 在故障转移规划中，还应考虑时间因素。 例如，如果失去连接的时间超过 15 到 20 分钟，你可能会决定启动故障转移。

2. 未复制数据是指未复制当前处于活动状态的会话。 此外，重复检测和计划消息可能无法正常工作。 新会话、新计划消息和新的重复项可以正常工作。 

3. 故障转移复杂的分布式基础结构应至少[演练](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)一次。

4. 同步实体可能需要一些时间，每分钟大约 50-100 个实体。 订阅和规则也计为实体。

## <a name="availability-zones"></a>可用性区域

服务总线高级 SKU 还支持[可用性区域](../availability-zones/az-overview.md)，在 Azure 区域内提供故障隔离位置。

> [!NOTE]
> Azure 服务总线高级版的可用性区域支持仅适用于存在可用性区域的 [Azure 区域](../availability-zones/az-region.md)。

可以使用 Azure 门户仅在新的命名空间上启用可用性区域。 服务总线不支持迁移现有命名空间。 在命名空间上启用区域冗余之后，不能将其禁用。

![3][]

## <a name="private-endpoints"></a>专用终结点
本部分提供了将异地灾难恢复与使用专用终结点的命名空间一起使用时的其他注意事项。 若要总体了解有关将专用终结点与服务总线一起使用的信息，请参阅[将 Azure 服务总线与 Azure 专用链接集成](private-link-service.md)。

### <a name="new-pairings"></a>新建配对
如果尝试在具有专用终结点的主命名空间与没有专用终结点的辅助命名空间之间创建配对，则配对会失败。 仅当主命名空间和辅助命名空间都具有专用终结点时，配对才会成功。 建议在主命名空间和辅助命名空间以及创建了专用终结点的虚拟网络上使用相同的配置。 

> [!NOTE]
> 尝试将具有专用终结点的主命名空间与某个辅助命名空间配对时，验证过程仅检查辅助命名空间上是否存在专用终结点。 它不会检查在故障转移后终结点是否正常工作或是否将正常工作。 你需要负责确保在故障转移后，具有专用终结点的辅助命名空间能够按预期工作。
>
> 若要测试专用终结点配置是否相同，请从虚拟网络外部向辅助命名空间发送[获取队列](/rest/api/servicebus/stable/queues/get)请求，并验证是否收到来自服务的错误消息。

### <a name="existing-pairings"></a>现有配对
如果主命名空间和辅助命名空间之间已存在配对，则在主命名空间上创建专用终结点将失败。 若要解决此问题，请首先在辅助命名空间上创建专用终结点，然后为主命名空间创建专用终结点。

> [!NOTE]
> 尽管我们允许对辅助命名空间进行只读访问，但也允许对专用终结点配置进行更新。 

### <a name="recommended-configuration"></a>建议配置
在为应用程序和服务总线创建灾难恢复配置时，必须针对承载应用程序的主实例和辅助实例的虚拟网络，为主要和辅助服务总线命名空间创建专用终结点。

假设你有两个虚拟网络：VNET-1、VNET-2 以及以下主命名空间和辅助命名空间：ServiceBus-Namespace1-Primary、ServiceBus-Namespace2-Secondary。 需要执行以下步骤： 

- 在 ServiceBus-Namespace1-Primary 上创建两个专用终结点，这两个专用终结点使用 VNET-1 和 VNET-2 中的子网
- 在 ServiceBus-Namespace2-Secondary 上创建两个专用终结点，这两个专用终结点使用 VNET-1 和 VNET-2 中的相同子网 

![专用终结点和虚拟网络](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


此方法的优点是，可以在独立于服务总线命名空间的应用程序层上进行故障转移。 请考虑下列情形： 

仅限应用程序的故障转移：此处，应用程序不会在 VNET-1 中，而是会移到 VNET-2 中。 由于主命名空间和辅助命名空间的 VNET-1 和 VNET 2 上都同时配置了这两个专用终结点，因此该应用程序将正常工作。 

**仅限服务总线命名空间的故障转移**：此处再次说明，因为在主命名空间和辅助命名空间的两个虚拟网络上都同时配置了这两个专用终结点，因此该应用程序将正常工作。 

> [!NOTE]
> 如需虚拟网络异地灾难恢复的相关指导，请参阅[虚拟网络 - 业务连续性](../virtual-network/virtual-network-disaster-recovery-guidance.md)。

## <a name="next-steps"></a>后续步骤

- 请参阅此处的异地灾难恢复 [REST API 参考](/rest/api/servicebus/stable/disasterrecoveryconfigs)。
- 在 GitHub 上运行异地灾难恢复[示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)。
- 请参阅异地灾难恢复[将消息发送到别名的示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)。

若要了解有关服务总线消息传送的详细信息，请参阅以下文章：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
