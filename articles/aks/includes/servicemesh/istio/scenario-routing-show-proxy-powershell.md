---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 33c8e7938e3b142e1af932e550c16770355babb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "77594094"
---
```powershell
kubectl describe pod -l "app=voting-analytics, version=1.0" -n voting | Select-String -Pattern "istio-proxy:|voting-analytics:" -Context 0,2
```

Istio 已自动注入 `istio-proxy` 容器，来管理组件的往返网络流量，如以下示例输出所示：

```console
>   voting-analytics:
      Container ID:   docker://35efa1f31d95ca737ff2e2229ab8fe7d9f2f8a39ac11366008f31287be4cea4d
      Image:          mcr.microsoft.com/aks/samples/voting/analytics:1.0
>   istio-proxy:
      Container ID:  docker://1fa4eb43e8d4f375058c23cc062084f91c0863015e58eb377276b20c809d43c6
      Image:         docker.io/istio/proxyv2:1.3.2
```