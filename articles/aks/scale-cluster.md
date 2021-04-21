---
title: 缩放 Azure Kubernetes 服务 (AKS) 群集
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数。
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: 1468f9a0a23935022ed14488dfb65d789828d310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782880"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes 服务 (AKS) 群集中缩放节点数

如果资源需要更改应用程序，可以手动缩放 AKS 群集以运行不同数量的节点。 节点数减少时，节点会被仔细[封锁和排除][kubernetes-drain]，尽量避免对正在运行的应用程序造成中断。 纵向扩展时，AKS 会一直等待，直到节点被 Kubernetes 群集标记为 `Ready`，然后才在这些节点上计划 Pod。

## <a name="scale-the-cluster-nodes"></a>缩放群集节点

首先，使用 [az aks show][az-aks-show] 命令获取节点池的名称。 以下示例获取 myResourceGroup 资源组中名为 myAKSCluster 的群集的节点池名称：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

以下示例输出表明名称为 nodepool1：

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

使用 [az aks scale][az-aks-scale] 命令缩放群集节点。 以下示例将名为 *myAKSCluster* 的群集缩放为单个节点。 提供前一个命令中你自己的 `--nodepool-name`，如 nodepool1：

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

以下示例输出显示群集已成功缩放为一个节点，如 agentPoolProfiles 部分中所示：

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```


## <a name="scale-user-node-pools-to-0"></a>将 `User` 节点池缩放为 0

与始终需要运行节点的 `System` 节点池不同，`User` 节点池允许缩放为 0。 若要详细了解系统节点池和用户节点池之间的差异，请参阅[系统节点池和用户节点池](use-system-pools.md)。

若要将用户池缩放为 0，可以使用 [az aks nodepool scale][az-aks-nodepool-scale] 来替代上面的 `az aks scale` 命令，并将 0 设置为节点计数。


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

还可以通过将[群集自动缩放程序](cluster-autoscaler.md)的 `--min-count` 参数设置为 0，将 `User` 节点池自动缩放为 0 个节点。

## <a name="next-steps"></a>后续步骤

在本文中，你手动缩放了 AKS 群集以增加或减少节点数量。 还可以使用[群集自动缩放程序][cluster-autoscaler]自动缩放群集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale