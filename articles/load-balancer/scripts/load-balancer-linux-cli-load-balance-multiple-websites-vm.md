---
title: 对多个网站进行负载均衡 - Azure CLI - Azure 负载均衡器
description: 本 Azure CLI 脚本示例演示如何对指向同一虚拟机的多个网站进行负载均衡
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77dfc42164419d86e174bc47297e2f596e757ffe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790088"
---
# <a name="azure-cli-script-example-load-balance-multiple-websites"></a>Azure CLI 脚本示例：对多个网站进行负载均衡

本 Azure CLI 脚本示例会使用可用性集中的两个虚拟机 (VM) 创建虚拟网络。 负载均衡器会将两个单独 IP 地址的流量定向到两台 VM。 运行脚本后，可将 Web 服务器软件部署到 VM 上，并可承载多个网站，其中每个网站都有其自身的 IP 地址。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本


[!code-azurecli-interactive[main](../../../cli_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.sh  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟网络、负载均衡器和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | 创建 Azure 虚拟网络和子网。 |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | 使用静态 IP 地址和关联的 DNS 名称创建公共 IP 地址。 |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | 创建 Azure 负载均衡器。 |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | 创建负载均衡器探测。 负载均衡器探测用于监视负载均衡器集中的每个 VM。 如果任何 VM 无法访问，流量将不会路由到该 VM。 |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | 创建负载均衡器规则。 在此示例中，为端口 80 创建一个规则。 当 HTTP 流量到达负载均衡器时，它会路由到负载均衡器集中某个 VM 的端口 80。 |
| [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) | 为负载均衡器创建前端 IP 地址。 |
| [az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) | 创建后端地址池。 |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | 创建虚拟网卡并将其连接到虚拟网络和子网。 |
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | 创建可用性集。 可用性集通过将虚拟机分布到各个物理资源上（以便发生故障时，不会影响整个集）来确保应用程序运行时间。 |
| [az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) | 创建 IP 配置。 必须为订阅启用 Microsoft.Network/AllowMultipleIpConfigurationsPerNic 功能。 对于每个 NIC，仅能使用 --make-primary 标志将一个配置指定为主要 IP 配置。 |
| [az vm create](/cli/azure/vm/availability-set#az_vm_availability_set_create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure 网络概述文档](../cli-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)中找到其他网络 CLI 脚本示例。