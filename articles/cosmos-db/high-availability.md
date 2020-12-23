---
title: Azure Cosmos DB 中的高可用性
description: 本文介绍 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 58507703ca3440e73dbc41757e0bc70f56e886c3
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360150"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Azure Cosmos DB 如何提供高可用性
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 提供了两种主要方式的高可用性。 首先，Azure Cosmos DB 跨 Cosmos 帐户中配置的区域复制数据。 其次，Azure Cosmos DB 维护区域内数据的4个副本。

Azure Cosmos DB 是一种全球分布式数据库服务，是 Azure 中的基础服务。 默认情况下，在 [Azure 可用的所有区域](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all)中都提供。 可将任意数量的 Azure 区域与 Azure Cosmos 帐户相关联，并且数据会自动且透明地得到复制。 可随时向 Azure Cosmos 帐户添加或从中删除区域。 Cosmos DB 在提供给客户的所有五种不同的 Azure 云环境中均可使用：

* Azure 公有云，全球通用。

* **Azure 中国世纪互联** 是通过 Microsoft 和世纪互联，其中一个全国最大的 internet 供应商（中国）之一提供的。

* **Azure 德国** 在数据受信者模型下提供服务，这可确保客户数据保留在德国的 T 系统国际 GmbH （德国 Telekom 的子公司）控制下，作为德国数据受信者。

* Azure 政府在美国的四个区域向美国政府机构及其合作伙伴提供服务。

* 适用于美国国防部的 **Azure 政府部门 (DoD)** 在美国到美国国防部的两个区域中提供。

在某个区域内，Azure Cosmos DB 将数据的四个副本作为副本保留在物理分区中，如下图所示：

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="物理分区" border="false":::

* Azure Cosmos 容器中的数据[已水平分区](partitioning-overview.md)。

* 分区集是多个副本集的集合。 在每个区域中，每个分区受副本集的保护，该副本集中的大多数副本将复制并以持久方式提交所有写入内容。 副本分布在最多 10 到 20 个容错域中。

* 将复制所有区域中的每个分区。 每个区域包含某个 Azure Cosmos 容器的所有数据分区，可接受写入并维护读取。  

如果 Azure Cosmos 帐户分布在 N 个 Azure 区域之间，则所有数据至少有 N x 4 个副本。 在超过2个区域的 Azure Cosmos 帐户会提高应用程序的可用性，并在相关区域之间提供低延迟。

## <a name="slas-for-availability"></a>可用性 SLA

作为一种全球分布式数据库，Azure Cosmos DB 提供全面的 Sla，其中包含吞吐量、99% 的延迟、一致性和高可用性。 下表显示 Azure Cosmos DB 针对单区域和多区域帐户提供的高可用性保证。 为实现高可用性，请始终将 Azure Cosmos 帐户配置为具有多个写入区域。

|操作类型  | 单区域 |多区域（单区域写入）|多区域（多区域写入） |
|---------|---------|---------|-------|
|写入    | 99.99    |99.99   |99.999|
|读取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 在实践中，有限过期、会话、一致前缀和最终一致性模型的实际写入可用性明显高于发布的 SLA。 所有一致性级别的实际读取可用性明显高于发布的 SLA。

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>使用 Azure Cosmos DB 在发生区域性服务中断时提供高可用性

对于区域性服务中断的罕见情况，Azure Cosmos DB 可确保你的数据库始终保持高可用性。 下面根据 Azure Cosmos 帐户配置详细介绍 Azure Cosmos DB 在服务中断期间的行为：

* 使用 Azure Cosmos DB 时，在客户端确认写入操作之前，数据将由接受写入操作的区域中的副本仲裁持续提交。 有关更多详细信息，请参阅 [一致性级别和吞吐量](./consistency-levels.md#consistency-levels-and-throughput)

* 配置有多个写入区域的多区域帐户对于写入和读取都将具有高可用性。 在 Azure Cosmos DB 客户端中检测并处理区域故障转移。 它们也是即时的，无需对应用程序进行任何更改。

* 发生区域性服务中断时，单区域帐户可能会失去可用性。 始终建议对 Azure Cosmos 帐户至少设置两个区域（最好至少设置两个写入区域），以确保始终保持高可用性。

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>配置为使用单个写入区域的多区域帐户（写入区域服务中断）

* 在写入区域服务中断期间，如果在 Azure Cosmos 帐户上配置了“启用自动故障转移”，则 Azure Cosmos 帐户会自动将次要区域提升为新的主要写入区域。 当启用后，将按您指定的区域优先级顺序故障转移到其他区域。

* 当上一个受影响的区域重新联机时，可以通过[冲突源](how-to-manage-conflicts.md#read-from-conflict-feed)使用该区域发生故障时未复制的任何写入数据。 应用程序可以读取冲突源，根据应用程序特定的逻辑解决冲突，并相应地将更新后的数据写回 Azure Cosmos 容器。

* 以前受影响的写入区域恢复后，它将自动用作读取区域。 可以切换回到用作写入区域的已恢复区域。 可以使用 [PowerShell、Azure CLI 或 Azure 门户](how-to-manage-database-account.md#manual-failover)来切换区域。 在切换写入区域之前、期间或之后， **不会丢失数据或可用性** ，应用程序将继续保持高可用性。

> [!IMPORTANT]
> 强烈建议将用于生产工作负载的 Azure Cosmos 帐户配置为“启用自动故障转移”  。 手动故障转移要求在辅助写入区域与主要写入区域之间进行连接来完成一致性检查，确保在故障转移期间不会丢失数据。 如果主要区域不可用，则此一致性检查无法完成，手动故障转移将不会成功，从而导致在区域性服务中断期间失去写入可用性。

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>配置为使用单个写入区域的多区域帐户（读取区域服务中断）

* 在读取区域服务中断期间，使用任何一致性级别或强一致性且具有三个或更多读取区域的 Azure Cosmos 帐户仍将对读取和写入保持高可用性。

* 使用强一致性且读取区域（包括读取和写入区域）不超过两个的 Azure Cosmos 帐户会在一个读取区域发生服务中断时失去读写可用性。

* 受影响的区域将自动断开连接，并标记为脱机。 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 会将读取调用重定向到首选区域列表中的下一个可用区域。

* 如果首选区域列表中没有区域可用，则会自动让调用返回到当前的写入区域。

* 处理读取区域服务中断不需要对应用程序代码进行更改。 当受影响的读取区域重新联机时，它会自动与当前写入区域同步，并再次可用于为读取请求提供服务。

* 后续的读取会重定向到恢复的区域，不需更改应用程序代码。 在故障转移和重新加入之前发生故障的区域期间，Azure Cosmos DB 会继续提供读取一致性保证。

* 即使在发生了 Azure 区域永久无法恢复的罕见不幸事件中，如果为多区域 Azure Cosmos 帐户配置了强一致性，也不会丢失数据。 如果出现永久不可恢复的写入区域，对于配置了有限过期一致性的多区域 Azure Cosmos 帐户，潜在的数据丢失时段限制为过期时段（K 或 T），其中 K = 100,000 次更新，T = 5 分钟。 对于会话、一致前缀和最终一致性级别，潜在的数据丢失时段限制为最多 15 分钟。 有关 Azure Cosmos DB 的 RTO 和 RPO 目标的详细信息，请参阅[一致性级别和数据持续性](./consistency-levels.md#rto)

## <a name="availability-zone-support"></a>可用性区域支持

除了跨区域复原功能，现在还可以在选择要与 Azure Cosmos 数据库相关联的区域时启用 **区域冗余** 。

通过可用性区域支持，Azure Cosmos DB 确保将副本放置在给定区域内的多个区域，以便在发生区域性故障时提供高可用性和复原能力。 此配置中不存在对延迟和其他 Sla 的更改。 如果发生单一区域故障，区域冗余将使用 RPO = 0 提供完整的数据持续性，并使用 RTO = 0 实现可用性。

区域冗余是 [在多区域写入功能中进行复制](how-to-multi-master.md)的 *补充功能* 。 只有区域冗余才能实现区域复原。 例如，如果区域中断或跨区域的低延迟访问，则建议除了区域冗余以外，还有多个写区域。

为你的 Azure Cosmos 帐户配置多区域写入时，你可以选择不额外收费的区域冗余。 否则，请参阅下面的说明，了解有关区域冗余支持的定价。 可以通过删除区域并将其重新添加到已启用的区域冗余，来在 Azure Cosmos 帐户的现有区域启用区域冗余。 有关支持可用性区域的区域的列表，请参阅 [可用性区域](../availability-zones/az-region.md) 文档。

下表总结了各种帐户配置的高可用性功能：

|KPI  |无可用性区域 (非 AZ) 的单个区域  |具有可用性区域 (AZ 的单个区域)   |使用可用性区域 (AZ、2个区域) 进行多区域写入–最建议的设置 |
|---------|---------|---------|---------|
|写入可用性 SLA | 99.99% | 99.99% | 99.999% |
|读取可用性 SLA  | 99.99% | 99.99% | 99.999% |
|价格 | 单区域计费率 | 单区域可用性区域计费率 | 多区域计费率 |
|区域故障–数据丢失 | 数据丢失 | 无数据丢失 | 无数据丢失 |
|区域故障–可用性 | 可用性损失 | 无可用性损失 | 无可用性损失 |
|读取延迟 | 跨区域 | 跨区域 | 低 |
|写入延迟 | 跨区域 | 跨区域 | 低 |
|地区性中断–数据丢失 | 数据丢失 |  数据丢失 | 数据丢失 <br/><br/> 在对多个写入区域和多个区域使用有限过期一致性时，数据丢失限制为在帐户上配置的界限过期 <br /><br />通过配置与多个区域的强一致性，可避免在区域性中断期间发生数据丢失。 此选项附带会影响可用性和性能的权衡。 只能对为单区域写入配置的帐户进行配置。 |
|地区性中断–可用性 | 可用性损失 | 可用性损失 | 无可用性损失 |
|吞吐量 | X RU/秒预配吞吐量 | X RU/s 预配吞吐量 * 1.25 | 已预配 2X RU/秒的吞吐量 <br/><br/> 与具有可用性区域的单个区域相比，此配置模式需要两倍的吞吐量，因为有两个区域。 |

> [!NOTE]
> 若要为多区域 Azure Cosmos 帐户启用可用性区域支持，帐户必须启用多区域写入。

将区域添加到新的或现有的 Azure Cosmos 帐户时，可以启用区域冗余。 若要在 Azure Cosmos 帐户上启用区域冗余，应将标志设置为，以 `isZoneRedundant` `true` 指定特定位置。 可以在 "位置" 属性中设置此标志。 例如，以下 PowerShell 代码片段为 "东南亚" 区域启用区域冗余：

可以通过以下方式启用可用性区域：

* [Azure 门户](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

* [Azure PowerShell](manage-with-powershell.md#create-account)

* [Azure CLI](manage-with-cli.md#add-or-remove-regions)

* [Azure Resource Manager 模板](./manage-with-templates.md)

## <a name="building-highly-available-applications"></a>生成高可用性应用程序

* 了解发生这些事件期间的 [Azure Cosmos SDK 预期行为](troubleshoot-sdk-availability.md)以及会影响它的具体配置。

* 若要确保较高的写入和读取可用性，请将 Azure Cosmos 帐户配置为跨越至少两个区域并使用多个写入区域。 对于读取和写入，此配置都可提供由 SLA 作为保障的最高可用性、最低延迟和最佳可伸缩性。 若要了解详细信息，请参阅如何[将 Azure Cosmos 帐户配置为使用多个写入区域](tutorial-global-distribution-sql-api.md)。

* 对于配置为使用单个写入区域的多区域 Azure Cosmos 帐户，请[使用 Azure CLI 或 Azure 门户启用自动故障转移](how-to-manage-database-account.md#automatic-failover)。 启用自动故障转移后，每当发生区域性灾难时，Cosmos DB 都会自动故障转移你的帐户。  

* 即使 Azure Cosmos 帐户具有高可用性，应用程序也不一定能够正常保持高可用性。 若要测试应用程序的端到端高可用性，请在应用程序测试或灾难恢复 (DR) 演练过程中，暂时禁用帐户的自动故障转移功能，[使用 PowerShell、Azure CLI 或 Azure 门户调用手动故障转移](how-to-manage-database-account.md#manual-failover)，然后监视应用程序的故障转移。 完成后，可以故障回复到主区域，然后还原该帐户的自动故障转移。

* 在全球分布式数据库环境中，一致性级别与数据持续性之间的直接关系是在发生区域范围的服务中断。 制定业务连续性计划时，需了解应用程序在中断事件发生后完全恢复之前的最大可接受时间。 应用程序完全恢复所需的时间称为恢复时间目标 (RTO)。 此外，还需要了解从中断事件恢复时，应用程序可忍受最近数据更新丢失的最长期限。 可以承受更新丢失的时限称为恢复点目标 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 和 RTO，请参阅[一致性级别和数据持续性](./consistency-levels.md#rto)

## <a name="next-steps"></a>后续步骤

接下来可以阅读以下文章：

* [各种一致性级别的可用性和性能权衡](./consistency-levels.md)

* [全局缩放预配的吞吐量](./request-units.md)

* [全球分布 - 揭秘](global-dist-under-the-hood.md)

* [Azure Cosmos DB 中的一致性级别](consistency-levels.md)

* [如何将 Cosmos 帐户配置为使用多个写入区域](how-to-multi-master.md)

* [多区域环境上的 SDK 行为](troubleshoot-sdk-availability.md)