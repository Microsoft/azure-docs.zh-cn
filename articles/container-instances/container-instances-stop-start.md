---
title: 手动停止或启动容器组
description: 了解如何手动停止或启动 Azure 容器实例中的容器组。
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 1801dad463d478c754e621dad0ae9406899ae7e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198109"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>手动停止或启动 Azure 容器实例中的容器

容器组的[重启策略](container-instances-restart-policy.md)设置确定默认情况下容器实例如何启动或停止。 可以通过手动停止或启动容器组来替代默认设置。

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>停止

通过特定方式（例如，使用 [az container stop][az-container-stop] 命令或 Azure 门户）手动停止正在运行的容器组。 对于某些容器工作负载，可能需要在规定的一段时间后停止长时间运行的容器组，以便节省成本。 

容器组进入“已停止”状态时，会终止并回收组中的所有容器。它不保留容器状态。

回收容器后，会解除分配[资源](container-instances-container-groups.md#resource-allocation)，并停止容器组的计费。

如果容器组已终止（处于“成功”或“失败”状态），则停止操作无效。 例如，其一次性运行的容器任务已成功运行的容器组会以“成功”状态终止。 尝试停止处于该状态的组不会更改状态。 

## <a name="start"></a>开始

当容器组停止时（因为容器自行终止或者你手动停止了组），你可以启动容器。 例如，使用 [az container start][az-container-start] 命令或 Azure 门户手动启动组中的容器。 如果更新了任何容器的容器映像，则会拉取一个新映像。 

启动容器组会使用相同的容器配置开始一个新部署。 此操作可帮助你快速重复使用按预期方式工作的已知容器组配置。 你无需创建新的容器组便可运行相同的工作负荷。

容器组中的所有容器都通过此操作启动。 无法启动组中的特定容器。

手动启动或重启容器组后，容器组将根据所配置的重启策略运行。
  
## <a name="restart"></a>重启

可以通过特定方式（例如，使用 [az container restart][az-container-restart] 命令）在容器组正在运行时将其重启。 此操作会重启容器组中的所有容器。 如果更新了任何容器的容器映像，则会拉取一个新映像。 

如果你想要排查部署问题，则重启容器组会有所帮助。 例如，如果临时资源限制阻止了你的容器成功运行，则重启组可能会解决此问题。

容器组中的所有容器都通过此操作重启。 无法重启组中的特定容器。

手动重启容器组后，容器组会根据所配置的重启策略运行。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 容器实例中的[重启策略设置](container-instances-restart-policy.md)。

除了使用现有配置手动停止和启动容器组外，还可以对正在运行的容器组执行[更新设置](container-instances-update.md)操作。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az-container-restart
[az-container-start]: /cli/azure/container#az-container-start
[az-container-stop]: /cli/azure/container#az-container-stop
