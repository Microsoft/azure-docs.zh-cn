---
title: Azure Batch 池调整大小开始事件
description: 批处理池调整大小开始事件参考。 示例显示了池调整大小开始事件（即通过手动调整大小将池的大小从 0 调整为 2 个节点）的正文。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 63576b04b06aad024211d0a50225907c88c138ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83723725"
---
# <a name="pool-resize-start-event"></a>池调整大小开始事件

 当池调整大小开始时，会发出此事件。 由于池调整大小是异步事件，预计在调整大小操作完成后会发出池调整大小完成事件。

 以下示例显示了池调整大小开始事件（即通过手动调整大小将池的大小从 0 调整为 2 个节点）的正文。

```
{
    "id": "myPool1",
    "nodeDeallocationOption": "Invalid",
    "currentDedicatedNodes": 0,
    "targetDedicatedNodes": 2,
    "currentLowPriorityNodes": 0,
    "targetLowPriorityNodes": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|元素|类型|说明|
|-------------|----------|-----------|
|`id`|字符串|池的 ID。|
|`nodeDeallocationOption`|字符串|指定何时从池中删除节点（如果池的大小正在减小）。<br /><br /> 可能的值包括：<br /><br /> **requeue** – 终止正在运行的任务并将其重新排队。 当作业启用时，任务将再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **terminate** – 终止正在运行的任务。 任务将不会再次运行。 一旦任务终止，便会立即删除节点。<br /><br /> **taskcompletion** – 允许完成当前正在运行的任务。 等待时不计划任何新任务。 在所有任务完成时，删除节点。<br /><br /> **Retaineddata** - 允许完成当前正在运行的任务，并等待所有任务数据保留期到期。 等待时不计划任何新任务。 在所有任务保留期都已过期时，删除节点。<br /><br /> 默认值为 requeue。<br /><br /> 如果池的大小正在增加，该值将设置为**无效**。|
|`currentDedicatedNodes`|Int32|当前分配到池的计算节点数。|
|`targetDedicatedNodes`|Int32|池请求的计算节点数。|
|`currentLowPriorityNodes`|Int32|当前分配到池的计算节点数。|
|`targetLowPriorityNodes`|Int32|池请求的计算节点数。|
|`enableAutoScale`|Bool|指定池大小是否随时间自动调整。|
|`isAutoPool`|Bool|指定是否已通过作业的 AutoPool 机制创建池。|
