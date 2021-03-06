---
title: 排查 Azure 负载均衡器常见问题
description: 了解如何对 Azure 负载均衡器的常见问题进行故障排除。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: cddaf1bde84d7e60eb59bd4c58c65fa889e06ae3
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028805"
---
# <a name="troubleshoot-azure-load-balancer"></a>排查 Azure 负载均衡器问题

此页介绍基本和标准 Azure 负载均衡器的常见问题的疑难解答信息。 有关标准负载均衡器的详细信息，请参阅[标准负载均衡器概述](load-balancer-standard-diagnostics.md)。

当负载均衡器连接不可用时，最常见的症状如下：

- 负载均衡器后的 Vm 不响应运行状况探测 
- 负载均衡器后端的 Vm 不响应已配置端口上的流量

当后端 VM 的外部客户端通过负载均衡器时，将使用客户端的 IP 地址进行通信。 请确保将客户端的 IP 地址添加到 NSG 允许列表中。

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>标准内部负载均衡器的出站连接 (ILB) 

验证及解决方法

标准 ILB 在默认情况下是安全的。 基本 ILB 允许通过隐藏的公共 IP 地址连接到 Internet。 对于生产工作负荷，不建议这样做，因为 IP 地址既不是静态的，也不是通过你拥有的 Nsg 锁定的。 如果最近从基本 ILB 移到了标准 ILB，则应通过 " [仅出站](egress-only.md) " 配置显式创建公共 IP，这会通过 NSG 锁定 IP。 你还可以在子网上使用 [NAT 网关](../virtual-network/nat-overview.md)。

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>无法为已在后端池中部署了虚拟机规模集的负载均衡器的现有 LB 规则更改后端端口。

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>原因：无法为虚拟机规模集引用的负载均衡器所用的负载均衡规则修改后端端口

**解决方法** 若要更改端口，可以通过更新虚拟机规模集来删除运行状况探测，更新端口，然后重新配置运行状况探测。

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>从负载均衡器的后端池中删除 Vm 后，小型流量仍会经历负载均衡器。

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>原因：从后端池删除的 Vm 不应再接收流量。 少量的网络流量可能与 Azure 中的存储、DNS 和其他功能有关。

若要进行验证，可以执行网络跟踪。 用于 blob 存储帐户的 FQDN 会列在每个存储帐户的属性中。  在 Azure 订阅中的虚拟机上，你可以执行 nslookup 来确定分配给该存储帐户的 Azure IP。

## <a name="additional-network-captures"></a>附加网络捕获

如果决定打开支持案例，请收集下列信息，以更快获得解决方案。 选择单个后端 VM 执行下列测试：

- 使用 VNet 中某个后端 Vm 的 ps ping 测试探测端口响应 (例如： ps ping 10.0.0.4： 3389) 并记录结果。 
- 如果这些 ping 测试未收到响应，请在运行 PsPing 时，在后端 VM 和 VNet 测试 VM 上同时运行 Netsh 跟踪，并停止 Netsh 跟踪。

## <a name="load-balancer-in-failed-state"></a>处于失败状态的负载均衡器

**分辨率**

- 确定处于失败状态的资源后，请跳到 [Azure 资源浏览器](https://resources.azure.com/) 并在此状态下确定资源。
- 将右侧角上的切换更新为 "读/写"。
- 单击 "编辑" 以使资源处于 "失败" 状态。
- 单击 "开始"，然后单击 "获取" 以确保已将预配状态更新为 "成功"。
- 然后，你可以在资源超出失败状态时继续执行其他操作。

## <a name="next-steps"></a>后续步骤

如果上述步骤无法解决问题，请开具[支持票证](https://azure.microsoft.com/support/options/)。
