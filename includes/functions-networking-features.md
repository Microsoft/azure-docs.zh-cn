---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020973"
---


| 功能 |[消耗计划](../articles/azure-functions/functions-scale.md#consumption-plan)|[高级计划](../articles/azure-functions/functions-scale.md#premium-plan)|[专用计划](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[入站 IP 限制和专用站点访问](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅是|✅是|✅是|✅是|✅是|
|[虚拟网络集成](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌否|✅是（区域）|✅是（区域和网关）|✅是| ✅是|
|[虚拟网络触发器（非 HTTP）](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌否| ✅是 |✅是|✅是|✅是|
|[混合连接](../articles/azure-functions/functions-networking-options.md#hybrid-connections)（仅限 Windows）|❌否|✅是|✅是|✅是|✅是|
|[出站 IP 限制](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌否| ✅是|✅是|✅是|✅是|