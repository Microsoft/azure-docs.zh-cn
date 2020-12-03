---
title: 选择一个定价层
titleSuffix: Azure Cognitive Search
description: 可在以下各层中预配 Azure 认知搜索：免费版、基本版、标准版、标准版和标准版均提供各种资源配置和容量级别。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 3f2cbd7afe206866ae4d5b7c0925c8f3be9ab785
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558804"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>选择 Azure 认知搜索的定价层

[创建搜索服务](search-create-service-portal.md)时，请选择在服务生存期内固定的定价层。 选择的层决定：

+ 索引数量和其他对象 (最大限制) 
+ 分区（物理存储）的大小和速度
+ 可计费费率，每月固定费用，但如果添加分区或副本，则还会增加成本

此外，还提供了一些级别要求的 [高级功能](#premium-features) 。

## <a name="tier-descriptions"></a>层说明

层包括 **免费**、 **基本**、 **标准** 和 **优化存储**。 “标准”和“存储优化”提供多种配置和容量。

以下 Azure 门户屏幕截图显示了可用的层，其中不包括定价层（可在门户中和[定价页](https://azure.microsoft.com/pricing/details/search/)上找到该层）。 

![Azure 认知搜索的定价层](media/search-sku-tier/tiers.png "Azure 认知搜索的定价层")

**免费** 为较小的项目创建受限的搜索服务，如运行教程和代码示例。 在内部，副本和分区可被多个订阅者共享。 不能缩放免费服务，也不能运行繁重的工作负荷。

“基本”和“标准”层是最常用的计费层，其中“标准”层是默认的层。   通过控制下的专用资源，你可以部署更大的项目、优化性能和增加容量。

某些层已针对特定类型的工作进行优化。 例如，“标准 3 高密度(S3 HD)”是 S3 的托管模式，其中的底层硬件已针对大量的较小索引进行优化，适用于多租户方案。 S3 HD 的每单位费用与 S3 相同，但硬件经过优化，可基于大量的小型索引快速读取文件。

与“标准”层相比，“存储优化”层以更低的每 TB 价格提供更大的存储容量。 主要弊端是查询延迟更高，应根据具体的应用程序要求确认这种延迟。 若要详细了解此层的性能注意事项，请参阅[性能和优化注意事项](search-performance-optimization.md)。

预配服务时，可以在[定价页](https://azure.microsoft.com/pricing/details/search/)、[Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)以及门户页上找到有关各个层的详细信息。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>按层划分的功能可用性

下表描述了与层相关的功能约束。

| 功能 | 限制 |
|---------|-------------|
| [索引器](search-indexer-overview.md) | 索引器在 S3 HD 上不可用。  |
| [AI 扩充](search-security-manage-encryption-keys.md) | 在免费层上运行，但不建议这样做。 |
| [客户托管的加密密钥](search-security-manage-encryption-keys.md) | 在免费层上不可用。 |
| [IP 防火墙访问](service-configure-firewall.md) | 在免费层上不可用。 |
| [专用终结点 (与 Azure Private Link 集成) ](service-create-private-endpoint.md) | 对于到搜索服务的入站连接，不能用于免费层。 对于通过索引器连接到其他 Azure 资源的出站连接，在免费或 S3 HD 上不可用。 对于使用技能集的索引器，不适用于免费、基本、S1 或 S3 HD 的索引器。|

大多数功能都可在每个层（包括免费层）上使用，但如果不为其提供足够的容量，则资源密集型功能可能无法正常工作。 例如，[AI 扩充](cognitive-search-concept-intro.md)包含长时间运行的技能，除非数据集较小，否则这些技能在免费服务中会超时。

## <a name="billable-events"></a>计费事件

基于 Azure 认知搜索构建的解决方案可能会在以下方面产生成本：

+ [服务](#service-costs) 本身的成本，以最低配置 (每个分区和副本) 以基本速率运行

+  (副本或分区) 添加容量，其中成本以计费费率的增量递增

+ 带宽费用（出站数据传输）

+ 特定功能或特性所需的附加服务：

  + AI 扩充（需要[认知服务](https://azure.microsoft.com/pricing/details/cognitive-services/)）
  + 知识存储（需要 [Azure 存储](https://azure.microsoft.com/pricing/details/storage/)）
  + 增量扩充（需要 [Azure 存储](https://azure.microsoft.com/pricing/details/storage/)，适用于 AI 扩充）
  + 客户管理的密钥和双加密（需要 [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)）
  + 无 internet 访问模型的专用终结点 (需要 [Azure 专用链接](https://azure.microsoft.com/pricing/details/private-link/)) 

### <a name="service-costs"></a>服务成本

与可以“暂停”以避免产生费用的虚拟机或其他资源不同，Azure 认知搜索服务在专门供你使用的硬件上始终可用。 因此，创建服务是一个计费事件，该事件在创建该服务时开始，在删除该服务时结束。 

最低费用是采用计费费率的第一个搜索单位（1 个副本 x 1 个分区）。 在服务的整个生命周期内，此最低费用都是固定的，因为服务不能在低于此配置的组件上运行。 超出最低费用时，可以单独添加副本和分区。 通过副本和分区递增容量会增大费用，其计算公式如下：[(副本数 x 分区数 x 费率)](#search-units)，其中，收费费率取决于所选的定价层。

在估算搜索解决方案的费用时，请记住，定价和容量不是线性的。 （容量翻倍不是支付两倍的费用，而是要支付更高的费用）有关该公式的工作方式示例，请参阅[如何分配副本和分区](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)。

### <a name="bandwidth-charges"></a>带宽费用

使用[索引器](search-indexer-overview.md)可能会影响计费，具体取决于服务的位置。 如果在数据所在的同一区域中创建 Azure 认知搜索服务，则可以完全消除数据流出费用。 下面是摘自[带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中的一些信息：

+ Microsoft 不会对入站到 Azure 上的任何服务的任何数据收费，也不会对 Azure 认知搜索的任何出站数据收费。
+ 在多服务解决方案中，如果所有服务都位于同一区域，则不会对跨越网络的数据收费。

如果服务在不同的区域中，则会针对出站数据收费。 这些费用实际上不是 Azure 认知搜索帐单的一部分。 此处之所以提到这些费用，是因为如果你使用数据或 AI 扩充索引器从不同的区域提取数据，将会在总体帐单中看到这些费用。

### <a name="ai-enrichment-with-cognitive-services"></a>使用认知服务的 AI 扩充

对于 [AI 扩充](cognitive-search-concept-intro.md)，应该计划在 S0 定价层上的 Azure 认知搜索所在的同一区域中[附加一个计费的认知服务资源](cognitive-search-attach-cognitive-services.md)，用于即用即付处理。 附加认知服务不会产生固定的费用。 只需支付所需的处理费。

| 操作 | 计费影响 |
|-----------|----------------|
| 文档破解、文本提取 | 免费 |
| 文档破解、图像提取 | 根据从文档中提取的图像数计费。 在 [索引器配置](/rest/api/searchservice/create-indexer#indexer-parameters)中，**imageAction** 是触发图像提取的参数。 如果 **imageAction** 设置为“none”（默认值），则不收取图像提取费用。 Azure 认知搜索的[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上阐述了图像提取费率。|
| [内置认知技能](cognitive-search-predefined-skills.md) | 计费费率与直接使用认知服务执行任务的费率相同。 |
| 自定义技能 | 自定义技能是你提供的功能。 使用自定义技能的费用完全取决于自定义代码是否调用其他计量的服务。 |

可以利用[增量扩充（预览版）](cognitive-search-incremental-indexing-conceptual.md)功能来提供缓存，使得在未来修改技能组后只需运行必要的认知技能，从而使索引器更高效，为你节省时间和金钱。

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>计费公式 (R x P = SU)

对于 Azure 认知搜索操作，要了解的最重要计费概念是搜索单位 (SU)。 由于 Azure 认知搜索必须同时使用副本和分区进行索引编制和查询，因此无法按其中的一个进行计费。 相反，应基于两者的组合来计费。

SU 是服务使用的副本数和分区数的乘积：  **(R x P = SU)** 。

每个服务至少从 1 个 SU（1 个分区乘以 1 个副本）开始。 任何服务的最大 SU 值为 36。 可通过多种方式来实现此最大值：例如，6 个分区 x 6 个副本，或 3 个分区 x 12 个副本。 通常使用小于总容量的值（例如，3 副本、3 分区的服务按 9 个 SU 计费）。 有关有效的组合，请参阅[分区和副本组合](search-capacity-planning.md#chart)图表。

费率按每个 SU、按小时计算。 每个层的费率渐进式提高。 层越高，分区越大且速度越快，因此，每小时的总费率更高。 可以在[定价详细信息](https://azure.microsoft.com/pricing/details/search/)页上查看每个层的费率。

大多数客户只是联机使用一部分总容量，将剩余的容器保持预留状态。 在计费方面，支付的每小时费用取决于联机的分区和副本数量（使用 SU 公式计算）。

## <a name="how-to-manage-costs"></a>如何管理成本

以下建议有助于降低成本或提高成本管理效率：

+ 在同一区域或者在尽可能少的区域中创建所有资源，以最大程度地减少甚至消除带宽费用。

+ 将所有服务（例如 Azure 认知搜索、认知服务，以及解决方案中使用的任何其他 Azure 服务）合并到一个资源组中。 在 Azure 门户中找到该资源组，并使用“成本管理”命令来洞察实际支出和预计支出。

+ 考虑对前端应用程序使用 Azure Web 应用，使请求和响应保留在数据中心边界范围内。

+ 针对索引编制等资源密集型操作纵向扩展，然后针对常规查询工作负荷向下重新调整。 首先对 Azure 认知搜索使用最低的配置（由一个分区和一个副本组成的一个 SU），然后监视用户活动，以识别指示需要更多容量的使用模式。 如果有可预测的模式，也许可以使用活动来同步规模（需要编写代码来自动化此过程）。

此外，请访问[计费和成本管理](../cost-management-billing/cost-management-billing-overview.md)获取与支出相关的内置工具和功能。

不可能临时关闭搜索服务。 专用资源始终运行，是在服务的生存期内专门分配给你使用的。 删除服务这项操作是永久性的，也会删除其关联的数据。

就服务本身而言，降低费用的唯一方法是将副本和分区数减少到一个较低的水平，但仍能提供可接受的性能并[遵从 SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)；或者在较低的层创建一个服务（S1 的每小时费率低于 S2 或 S3 的费率）。 假设你预配了一个面向低端负载预测的服务。若要扩充该服务，可以创建另一个具有较大层的服务，在该服务上重建索引，然后删除第一个服务。

## <a name="how-to-evaluate-capacity-requirements"></a>如何评估容量要求

在 Azure 认知搜索中，容量的结构包括副本和分区 。

+ 副本是搜索服务的实例。 每个副本托管索引的一个负载均衡副本。 例如，包含 6 个副本的服务具有加载到服务中的每个索引的 6 个副本。

+ 分区存储索引，并自动拆分可搜索的数据。 两个分区可将索引拆分成两半，三个分区可将索引拆分为三个部分，依次类推。 在容量方面，分区大小是各层级的主要区别特征。

> [!NOTE]
> 所有“标准”和“存储优化”层都支持[副本和分区的灵活组合](search-capacity-planning.md#chart)，因此你可以通过改变平衡方式来[优化系统以提高速度或存储](search-performance-optimization.md)。 “基本”层最多提供三个副本来实现高可用性，但只有一个分区。 “免费”层不提供专用资源：计算资源由多个订阅者共享。

### <a name="evaluating-capacity"></a>评估容量

容量与运行服务的成本密切相关。 层对两个级别施加限制：存储和内容 (索引数，例如) 。 应该同时考虑到此两者，因为首先达到的限制就是实施的限制。

业务需求通常决定了所需的索引数。 例如，你可能需要对一个较大的文档存储库使用全局索引。 或者，你可能需要多个基于区域、应用或商业利基的索引。

若要确定索引大小，必须[生成一个索引](search-what-is-an-index.md)。 其大小将基于导入的数据和索引配置，例如是否启用建议器、筛选和排序。

对于全文搜索，主数据结构是一个 [反转索引](https://en.wikipedia.org/wiki/Inverted_index) 结构，其特征不同于源数据。 对于倒排索引，大小和复杂度由内容决定，不一定是输入的数据量。 具有高度冗余的大型数据源可能会导致比包含高度可变内容的较小数据集更小的索引。 因此，很难根据原始数据集的大小来推断索引大小。

> [!NOTE] 
> 即使估算将来的索引和存储需求类似于猜测，但也值得一试。 如果层级容量经证实过低，将需要在更高的层级上预配新服务，然后[重新加载索引](search-howto-reindex.md)。 不会将服务从一个层就地升级到另一个层。
>

### <a name="estimate-with-the-free-tier"></a>使用“免费”层进行评估

估算容量的一种方法是从“免费”层开始。 回想一下，“免费”服务最多提供 3 个索引、50 MB 存储和 2 分钟索引时间。 根据这些约束估算预计索引大小可能很有难度，具体步骤如下：

+ [创建免费服务](search-create-service-portal.md)。
+ 准备一个小型的有代表性的数据集。
+ [在门户中生成初始索引](search-get-started-portal.md)并记下其大小。 功能和属性会影响存储。 例如，添加建议器（“边键入边搜索”查询）会提高存储要求。 可以使用同一个数据集尝试创建索引的多个版本，并在每个字段中使用不同的属性，以了解存储要求的变化。 有关详细信息，请参阅[“创建基本索引”中的“存储影响”](search-what-is-an-index.md#index-size)。

估算出粗略的数字后，可将此数量增大一倍来得出两个索引（开发和生产）的预算，然后相应地选择层。

### <a name="estimate-with-a-billable-tier"></a>使用计费层进行评估

专用资源可以适应更大的采样和处理时间，并可以在开发期间对索引数量、大小和查询量进行更贴近实际的估算。 某些客户会直接选择计费层，然后在开发项目成熟后重新进行评估。

1. [检查每个层级的服务限制](./search-limits-quotas-capacity.md#index-limits)以确定较低层级是否可以支持需要的索引数量。 在“基本”、“S1”和“S2”层中，索引数限制分别为 15、50 和 200。 “存储优化”层的索引数限制为 10 个，因为它旨在支持少量的极大型索引。

1. [在可计费层中创建服务](search-create-service-portal.md)：

    + 如果你不确定预计负载有多大，请从较低的“基本”或“S1”层着手。
    + 如果你知道会出现较大的索引和查询负载，请从较高的“S2”甚至“S3”层着手。
    + 如果你要为大量的数据编制索引并且查询负载相对较低（例如，使用与内部商务应用程序时），请从“优化存储”层 L1 或 L2 着手。

1. [生成初始索引](search-what-is-an-index.md)以确定将源数据转换为索引的方式。 这是估计索引大小的唯一方法。

1. 在门户中[监视存储、服务限制、查询量和延迟](search-monitor-usage.md)。 门户会显示每秒查询数、限制的查询数和搜索延迟。 所有这些值可帮助你确定是否选择了合适的层。 

索引数量和大小对于分析而言同等重要。 这是因为，需要通过充分利用存储（分区）或通过最大化对资源（索引、索引器等）的限制来达到最大限制（以先发生者为准）。 门户可帮助你跟踪两者，并在“概述”页面上并排显示当前使用情况和最大限制。

> [!NOTE]
> 如果文档包含无关数据，存储要求可能会过高。 理想情况下，文档仅包含搜索体验所需的数据。 二进制数据不可搜索，因此应单独存储（也许可以存储在 Azure 表或 Blob 存储中）。 然后在索引中添加一个字段，用于保存对外部数据的 URL 引用。 单个文档的最大大小是 16 MB（如果在一次请求中批量上传了多个文档，则小于 16 MB）。 有关详细信息，请参阅 [Azure 认知搜索中的服务限制](search-limits-quotas-capacity.md)。
>

**查询量注意事项**

每秒查询数 (QPS) 在性能优化期间是一个重要的指标，但如果你预期查询量一开始就很高，则通常只需考虑到层。

“标准”层可以提供平衡的副本数和分区数。 可以添加副本来实现负载均衡，或添加分区进行并行处理，以此增大查询周转时间。 然后，可以在预配服务后优化性能。

如果你预期持续查询量一开始就很高，应考虑更高的由更强大硬件支持的“标准”层。 如果不会这些这种查询量，可以使分区和副本脱机，甚至切换到更低层的服务。 有关如何计算查询吞吐量的详细信息，请参阅 [Azure 认知搜索性能和优化](search-performance-optimization.md)。

“存储优化”层可用于大数据工作负荷，当查询延迟要求不太重要时，可以支持更大的总体可用索引存储。 仍应使用更多的副本进行负载均衡，并使用更多的分区进行并行处理。 然后，可以在预配服务后优化性能。

**服务级别协议**

“免费”层和预览版功能不提供[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。 对于所有可计费的层，SLA 将在用户为服务提供足够冗余时生效。 需要为查询（读取）SLA 提供两个或更多个副本。 需要为查询和索引（读写）SLA 提供三个或更多个副本。 分区数不影响 SLA。

## <a name="tips-for-tier-evaluation"></a>层级评估提示

+ 允许围绕查询生成指标，并围绕使用模式收集数据（在营业期间执行查询，在非高峰期执行索引编制）。 使用此数据做出明智的服务预配决策。 尽管这种做法不是在每小时或每日都可行，但可以动态调整分区和资源，以应对查询量的计划内变化。 此外，还可以应对计划外的但持续性的变化，前提是变化程度持续足够长的时间，以致有必要采取措施。

+ 请记住，预配不足的唯一缺点是，如果实际需求超出预测，则可能必须关闭某项服务。 为避免服务中断，可以在更高层级创建新服务，并让其并排运行，直到所有应用和请求都指向新的终结点。

## <a name="next-steps"></a>后续步骤

从“免费”层开始，使用数据子集生成初始索引以了解其特征。 Azure 认知搜索中的数据结构是一种倒排索引结构。 倒排索引的大小和复杂性由内容决定。 请记住，高度冗余的内容往往会导致比高度不规则的内容更小的索引。 因此，确定索引存储要求的是它的内容特征而不是数据集的大小。

初始估算索引大小后，根据本文中所述的某个层[预配可计费的服务](search-create-service-portal.md)：“基本”、“标准”或“存储优化”。 放宽对数据大小的任何人为约束，并[重建索引](search-howto-reindex.md)以包含可搜索的所有数据。

根据需要[分配分区和副本](search-capacity-planning.md)以获取所需性能和规模。

如果性能和容量都合适，那么你已完成操作。 否则，请在与你的需求更接近的不同层级上重新创建搜索服务。

> [!NOTE]
> 如有问题，请在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) 中发贴或[联系 Azure 支持部门](https://azure.microsoft.com/support/options/)。