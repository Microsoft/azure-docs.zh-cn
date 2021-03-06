---
title: Azure 点虚拟机和规模集实例的错误代码
description: 了解使用 Azure 点虚拟机和规模集实例时可能看到的错误代码。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 9bea9978f1755e5a40b5fb3ff967eb7f32384d19
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557742"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Azure 点虚拟机和规模集的错误消息

下面是使用 Azure 点虚拟机和规模集时可能会收到的一些错误代码。


| 密钥 | Message | 说明 |
|-----|---------|-------------|
| SkuNotAvailable | 资源 "" 的请求层 \<resource\> 当前在订阅 "" 的位置 "" 中不可用 \<location\> \<subscriptionID\> 。 尝试另一层或部署到其他位置。 | 此位置没有足够的 Azure 点虚拟机容量来创建 VM 或规模集实例。 |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  只能在 Azure 点虚拟机上设置逐出策略。 | 此 VM 不是 Azure 点虚拟机，因此无法设置逐出策略。 |
| AzureSpotVMNotSupportedInAvailabilitySet  |  可用性集中不支持 Azure 点虚拟机。 | 需要选择使用 Azure 点虚拟机或使用可用性集中的 VM，而不能同时选择两者。 |
| AzureSpotFeatureNotEnabledForSubscription  |  未启用 Azure 点虚拟机功能的订阅。 | 使用支持 Azure 点虚拟机的订阅。 |
| VMPriorityCannotBeApplied  |  指定的优先级值 " {0} " 无法应用于虚拟机 ""， {1} 因为创建虚拟机时未指定优先级。 | 指定创建 VM 时的优先级。 |
| SpotPriceGreaterThanProvidedMaxPrice  |  无法执行操作 " {0} "，因为提供的最大价格 " {1} usd" 低于 {2} Azure 点虚拟机大小 "" 的当前点焊价格 "usd" {3} 。 | 选择较高的最大价格。 有关详细信息，请参阅 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 或 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定价信息。|
| MaxPriceValueInvalid  |  最大价格值无效。 最大价格的唯一支持值为-1 或大于零的小数。 最大价格值-1 表示出于价格原因，不会逐出 Azure 点虚拟机。 | 请输入有效的最大价格。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 或 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的定价。 |
| MaxPriceChangeNotAllowedForAllocatedVMs | 当前分配 VM "" 时，不允许进行最大价格更改 {0} 。 解除分配，然后重试。 | Stop\Deallocate VM，以便可以更改最大价格。 |
| MaxPriceChangeNotAllowed | 不允许最大价格更改。 | 不能更改此 VM 的最大价格。 |
| AzureSpotIsNotSupportedForThisAPIVersion  |  此 API 版本不支持 Azure 点虚拟机。 | API 版本应为2019-03-01。 |
| AzureSpotIsNotSupportedForThisVMSize  |  此 VM 大小不支持 Azure 点虚拟机 {0} 。 | 选择另一个 VM 大小。 有关详细信息，请参阅 [Azure 点虚拟机](./spot-vms.md)。 |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  仅 Azure 点虚拟机支持最大价格。 | 有关详细信息，请参阅 [Azure 点虚拟机](./spot-vms.md)。 |
| MoveResourcesWithAzureSpotVMNotSupported  |  移动资源请求包含一个 Azure 点虚拟机。 不支持。 查看虚拟机 Id 的错误详细信息。 | 不能移动 Azure 点虚拟机。 |
| MoveResourcesWithAzureSpotVmssNotSupported  |  移动资源请求包含一个 Azure 点虚拟机规模集。 不支持。 查看虚拟机规模集 Id 的错误详细信息。 | 不能移动 Azure 点虚拟机规模集实例。 |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | 虚拟机规模集不支持 Azure 点虚拟机与 VM 业务流程模式。 | 将业务流程模式设置为虚拟机规模集，以便使用 Azure 点虚拟机实例。 |


**后续步骤** 有关详细信息，请参阅 " [发现虚拟机](./spot-vms.md)"。