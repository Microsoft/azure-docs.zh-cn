---
title: 服务配额和限制
description: 了解默认的 Azure Batch 配额、限制和约束，以及如何请求提高配额
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: seodec18
ms.openlocfilehash: 9f529d388cb883f635b6225801af5ce41b8c997a
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614502"
---
# <a name="batch-service-quotas-and-limits"></a>Batch 服务配额和限制

与其他 Azure 服务一样，与 Batch 服务关联的某些资源存在限制。 其中的许多限制是 Azure 在订阅或帐户级别应用的默认配额。

设计和增加 Batch 工作负荷时，请记住这些配额。 例如，如果池没有达到指定的计算节点目标数量，那么可能是已达到 Batch 帐户的核心配额限制。

可以在单个 Batch 帐户中运行多个 Batch 工作负荷，或者在相同订阅的不同 Azure 区域的 Batch 帐户之间分散工作负荷。

如果打算在 Batch 中运行生产工作负荷，可能需要将一个或多个配额提高到默认值以上。 如果需要提高配额，可以免费提出在线[客户支持请求](#increase-a-quota)。

## <a name="resource-quotas"></a>资源配额

配额是一种限制，不能保证容量。 如果有大规模的容量需求，请联系 Azure 支持。

另请注意，配额不是受保证值。 配额可能因来自 Batch 服务的更改或是用于更改配额值的用户请求而异。

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>核心配额

### <a name="cores-quotas-in-batch-service-mode"></a>Batch 服务模式下的核心配额

正在改进专用核心配额的实施，并在2020年12月结束后，所有批处理帐户的更改都将按阶段提供。

VM 支持的每个 VM 序列都存在核心配额，它们显示在门户的 " **配额** " 页上。 可以使用支持请求更新 VM 序列配额限制，如下所述。

随着现有机制的出现，不会检查 VM 系列的配额限制，只会强制执行该帐户的总配额限制。 这意味着，可以为 VM 系列分配比 VM 系列配额所指示的更多的内核，直到达到总帐户配额限制。

除了帐户配额总计外，更新的机制还将强制实施 VM 序列配额。 在过渡到新机制的过程中，可能会更新 VM 序列配额值以避免分配失败-最近几个月内使用的任何 VM 序列都将更新其 VM 序列配额，以匹配总帐户配额。 此更改不会允许使用比已提供的容量更多的容量。

可以通过检查来确定是否已为批处理帐户启用了 VM 系列配额强制：

* Batch 帐户 [dedicatedCoreQuotaPerVMFamilyEnforced](/rest/api/batchmanagement/batchaccount/get#batchaccount) API 属性。

* 门户中 "Batch 帐户 **配额** " 页上的文本。

### <a name="cores-quotas-in-user-subscription-mode"></a>用户订阅模式中的核心配额

如果创建的 [batch 帐户](accounts.md) 的池分配模式设置为 " **用户订阅**"，则在创建池或调整池大小时，将直接在订阅中创建 batch vm 和其他资源。 Azure Batch 核心配额不会应用，并且将使用和强制执行针对区域计算核心、每系列计算核心和其他资源的订阅中的配额。

若要详细了解这些配额，请参阅 [Azure 订阅和服务的限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。

## <a name="pool-size-limits"></a>池大小限制

池大小限制由 Batch 服务设置。 与[资源配额](#resource-quotas)不同，这些值无法更改。 只有具有节点间通信和自定义映像的池才具有与标准配额不同的限制。

| **资源** | **最大限制** |
| --- | --- |
| **[启用了节点间通信的池](batch-mpi.md)中的计算节点**  ||
| Batch 服务池分配模式 | 100 |
| Batch 订阅池分配模式 | 80 |
| [使用托管映像资源创建的池](batch-custom-images.md)中的计算节点<sup>1</sup> ||
| 专用节点 | 2000 |
| 低优先级节点 | 1000 |

<sup>1</sup> 适用于未启用节点间通信的池。

## <a name="other-limits"></a>其他限制

Batch 服务设置的其他限制。 与[资源配额](#resource-quotas)不同，这些值无法更改。

| **资源** | **最大限制** |
| --- | --- |
| 每个计算节点的[并发任务](batch-parallel-node-tasks.md)数 | 4 x 节点核心数 |
| 每个 Batch 帐户的[应用程序](batch-application-packages.md)数 | 20 |
| 每个应用程序的应用程序包数 | 40 |
| 每个池的应用程序包数 | 10 |
| 最长任务生存期 | 180 天<sup>1</sup> |
| 每个计算节点的[装载](virtual-file-mount.md)数 | 10 |

<sup>1</sup> 最长任务生存期（从添加到作业时算起到任务完成时结束）为 180 天。 已完成的任务保存七天；最长生存期内未完成的任务的数据不可访问。

## <a name="view-batch-quotas"></a>查看 Batch 配额

在 [Azure 门户](https://portal.azure.com)中查看 Batch 帐户配额：

1. 选择“Batch 帐户”，然后选择所需的 Batch 帐户。
1. 在 Batch 帐户的菜单上选择“配额”。
1. 查看当前应用于 Batch 帐户的配额。

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Batch 帐户配额":::

## <a name="increase-a-quota"></a>提高配额

可以使用 [Azure 门户](https://portal.azure.com)请求提高 Batch 帐户或订阅的配额。 可以提高哪种配额取决于批处理帐户的池分配模式。 若要请求增加配额，必须包含要增加其配额的 VM 系列。 当应用了配额增加时，它会应用于所有 VM 系列。

1. 在门户仪表板上选择“帮助 + 支持”磁贴，或单击门户右上角的问号 ( **?** )。
1. 选择“新建支持请求” > “基本”。
1. 在“基本信息”中：
   
    1. “问题类型” > “服务和订阅限制(配额)”
   
    1. 选择订阅。
   
    1. “配额类型” > “Batch”
      
       选择“**下一页**”。
    
1. 在“详细信息”中：
      
    1. 在“提供详细信息”中，指定位置、配额类型和 Batch 帐户。
    
       ![Batch 配额增加][quota_increase]

       配额类型包括：

       * 每个 Batch 帐户  
         特定于单个 Batch 帐户的值，包括专用和低优先级核心以及作业和池的数量。
        
       * 每个区域  
         应用于区域中所有 Batch 帐户的值，包括每个订阅的每个区域的 Batch 帐户数。

       低优先级配额是跨所有 VM 系列的单个值。 如果需要受约束的 SKU，则必须选择“低优先级核心”并包含要请求的 VM 系列。

    1. 根据[业务影响情况](https://aka.ms/supportseverity)选择“严重性”。

       选择“**下一页**”。

1. 在“联系人信息”中：
   
    1. 选择“首选联系方法”。
   
    1. 输入并确认所需的联系人详细信息。
   
       选择“创建”以提交支持请求。

提交支持请求后，Azure 支持人员将与你取得联系。 配额请求可以在几分钟内完成，或在最多两个工作日内完成。

## <a name="related-quotas-for-vm-pools"></a>VM 池的相关配额

部署在 Azure 虚拟网络中的虚拟机配置中的 Batch 池可自动分配其他 Azure 网络资源。 在虚拟网络中，每 50 个池节点需要以下资源：

- 一个[网络安全组](../virtual-network/network-security-groups-overview.md#network-security-groups)
- 一个[公共 IP 地址](../virtual-network/public-ip-addresses.md)
- 一个[负载均衡器](../load-balancer/load-balancer-overview.md)

在包含创建 Batch 池时提供的虚拟网络的订阅中分配这些资源。 这些资源受订阅的[资源配额](../azure-resource-manager/management/azure-subscription-service-limits.md)限制。 如果计划在虚拟网络中部署大型池，请检查订阅的这些资源配额。 如果需要，请在 Azure 门户中选择“帮助和支持”，请求增大配额。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建 Azure Batch 帐户](batch-account-create-portal.md)。
* 了解 [Batch 服务工作流和主要资源](batch-service-workflow-features.md)，例如池、节点、作业和任务。
* 了解 [Azure 订阅和服务的限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)。

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png