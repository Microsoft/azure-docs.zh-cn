---
title: 使用 Azure CLI 创建 Azure 专用链接服务
description: 了解如何使用 Azure CLI 创建 Azure 专用链接服务
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: cfffafaab2e2d4ef6b165ef03beb827342c94608
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "96018046"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>使用 Azure CLI 创建专用链接服务
本文介绍了如何使用 Azure CLI 在 Azure 中创建专用链接服务。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 的最新版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-private-link-service"></a>创建专用链接服务
### <a name="create-a-resource-group"></a>创建资源组

在创建虚拟网络之前，必须创建一个资源组用于托管该虚拟网络。 使用 [az group create](/cli/azure/group) 创建资源组。 此示例在 *westcentralus* 位置创建名为 *myResourceGroup* 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>创建虚拟网络
使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 创建虚拟网络。 此示例将创建一个名为 *myVirtualNetwork* 的默认虚拟网络，其中包含一个名为 *mySubnet* 的子网：

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>创建子网
使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 为虚拟网络创建子网。 此示例在 *myVirtualNetwork* 虚拟网络中创建名为 *mySubnet* 的子网：

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>创建内部负载均衡器 
使用 [az network lb create](/cli/azure/network/lb#az-network-lb-create) 创建内部负载均衡器。 此示例在名为 *myResourceGroup* 的资源组中创建名为 *myILB* 的内部负载均衡器。 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>创建负载均衡器运行状况探测

运行状况探测会检查所有虚拟机实例，以确保它们可以接收网络流量。 探测器检查失败的虚拟机实例将从负载均衡器中删除，直到它恢复联机状态并且探测器检查确定它运行正常。 使用 [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest) 创建运行状况探测，以监视虚拟机的运行状况。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义传入流量的前端 IP 配置和用于接收流量的后端 IP 池，以及所需源和目标端口。 使用 [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest) 创建负载均衡器规则 *myHTTPRule*，以便侦听前端池 *myFrontEnd* 中的端口 80，并且将经过负载均衡的网络流量发送到也使用端口 80 的后端地址池 *myBackEndPool*。 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>创建后端服务器

在此示例中，我们未包括虚拟机创建。 可以按照 [快速入门：使用 Azure CLI 创建内部负载均衡器来对 vm 进行负载均衡](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) ，以创建两个虚拟机用作负载均衡器的后端服务器。 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>在子网上禁用专用链接服务网络策略 
专用链接服务需要虚拟网络中你选择的任何子网中的一个 IP。 目前，我们不支持这些 Ip 上的网络策略。  因此，我们必须禁用子网上的网络策略。 请使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 更新子网以禁用专用链接服务网络策略。

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>使用标准负载均衡器创建专用链接服务 
 
通过 [az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create) 使用标准负载均衡器前端 IP 配置创建专用链接服务。 此示例使用 *myResourceGroup* 资源组中的标准负载均衡器 *myLoadBalancer* 创建名为 *myPLS* 的专用链接服务。 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
在创建后，记下专用链接服务 ID。 稍后，你将需要请求连接到此服务。  
 
在此阶段，专用链接服务已成功创建，并已准备好接收流量。 请注意，上面的示例仅演示了如何使用 Azure CLI 创建专用链接服务。  我们尚未配置负载均衡器后端池，也未在后端池上配置任何应用程序来侦听流量。 如果你想查看端到端流量流，则强烈建议你在标准负载均衡器后面配置应用程序。  
 
接下来，我们将演示如何使用 Azure CLI 将此服务映射到不同虚拟网络中的专用终结点。 同样，示例仅限于使用 Azure CLI 创建专用终结点并连接到上面创建的专用链接服务。 另外，你还可以在虚拟网络中创建虚拟机，来为专用终结点发送/接收流量。        
 
## <a name="private-endpoints"></a>专用终结点

### <a name="create-the-virtual-network"></a>创建虚拟网络 
使用  [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 创建虚拟网络。 此示例  *myPEVNet*   在名为 *myResourcegroup* 的资源组中创建名为 myPEVNet 的虚拟网络： 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>创建子网 
使用  [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 在虚拟网络中创建子网。 此示例将在名为  *mySubnet*   *myResourcegroup* 的资源组中名为 *myPEVnet* 的虚拟网络中创建名为 mySubnet 的子网： 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>在子网上禁用专用终结点网络策略 
可以在虚拟网络中你选择的任何子网中创建专用终结点。 目前，我们不支持专用终结点上的网络策略。  因此，我们必须禁用子网上的网络策略。 请使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 更新子网以禁用专用终结点网络策略。 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>创建专用终结点并连接到专用链接服务 
创建一个专用终结点，用于使用上面在虚拟网络中创建的专用链接服务：
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
可以在专用链接服务上使用 `az network private-link-service show` 获取 private-connection-resource-id。 ID 将如下所示：   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>显示专用链接服务连接 
 
使用 [az network private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show) 查看专用链接服务上的连接请求。    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure 专用链接服务](private-link-service-overview.md)
