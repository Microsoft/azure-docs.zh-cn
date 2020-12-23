---
title: 数据流活动
description: 如何在数据工厂管道中执行数据流。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 11/24/2020
ms.openlocfilehash: 1c0ed7cf38cc01623169216ec45e88d198ede3d2
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095077"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure 数据工厂中的数据流活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用数据流活动可以通过映射数据流来转换和移动数据。 如果你不熟悉数据流，请参阅 [映射数据流概述](concepts-data-flow-overview.md)

## <a name="syntax"></a>语法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
数据流 | 对正在执行的数据流的引用 | DataFlowReference | 是
integrationRuntime | 运行数据流的计算环境。 如果未指定，将使用自动解析 Azure 集成运行时。 | IntegrationRuntimeReference | 否
coreCount | Spark 群集中使用的内核数。 仅当使用自动解析 Azure 集成运行时，才能指定 | 8、16、32、48、80、144、272 | 否
computeType | Spark 群集中使用的计算类型。 仅当使用自动解析 Azure 集成运行时，才能指定 | "常规"、"ComputeOptimized"、"MemoryOptimized" | 否
暂存。 linkedService | 如果使用的是 Azure Synapse Analytics 源或接收器，请指定用于 PolyBase 暂存的存储帐户。<br/><br/>如果 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。 还分别了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 的所需配置。<br/> | LinkedServiceReference | 仅当数据流读取或写入 Azure Synapse 分析时
暂存。 folderPath | 如果你使用的是 Azure Synapse Analytics 源或接收器，则用于 PolyBase 暂存的 blob 存储帐户中的文件夹路径 | 字符串 | 仅当数据流读取或写入到 Azure Synapse Analytics 时
traceLevel | 设置数据流活动执行的日志记录级别 | 精细、粗、无 | 否

![执行数据流](media/data-flow/activity-data-flow.png "执行数据流")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>在运行时动态调整数据流的大小

可以动态设置核心计数和计算类型属性，以便在运行时调整传入源数据的大小。 使用查找或获取元数据等管道活动来查找源数据集数据的大小。 然后，使用 "数据流活动属性" 中的 "添加动态内容"。

![动态数据流](media/data-flow/dyna1.png "动态数据流")

[下面是介绍此方法的简短视频教程](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>数据流集成运行时

选择要用于数据流活动执行的 Integration Runtime。 默认情况下，数据工厂将使用带有四个辅助角色的自动解析 Azure 集成运行时，不会实时 (TTL) 。 此 IR 具有常规用途计算类型，并与工厂在同一区域中运行。 你可以创建自己的 Azure 集成运行时，用于定义数据流活动执行的特定区域、计算类型、核心计数和 TTL。

对于管道执行，群集是作业群集，在执行开始之前需要几分钟时间启动。 如果未指定 TTL，则每次运行管道时都需要此启动时间。 如果指定 TTL，则在上一次执行之后指定的时间，温群集池将保持活动状态，从而缩短启动时间。 例如，如果 TTL 为60分钟，并且一小时运行一次数据流，则群集池将保持活动状态。 有关详细信息，请参阅 [Azure 集成运行时](concepts-integration-runtime.md)。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> 数据流活动中的 Integration Runtime 选择仅适用于管道的已触发执行。 在调试会话中指定的群集上运行数据流时，调试管道。

### <a name="polybase"></a>PolyBase

如果使用 Azure Synapse 分析作为接收器或源，则必须为 PolyBase 批处理负载选择一个暂存位置。 PolyBase 允许批量加载，而不是逐行加载数据。 PolyBase 大大降低了 Azure Synapse 分析的加载时间。

## <a name="logging-level"></a>日志记录级别

如果你不要求每个管道执行的数据流活动完全记录所有详细遥测日志，则可以选择将日志记录级别设置为 "基本" 或 "无"。 如果在 "详细" 模式下执行数据流 (默认值) ，则在数据转换过程中，请求 ADF 以完全记录每个分区级别的活动。 这可能是一种代价高昂的操作，因此，仅在进行故障排除时启用详细操作可以提高总体数据流和管道性能。 "基本" 模式将仅记录转换持续时间，而 "无" 将仅提供持续时间的摘要。

![日志记录级别](media/data-flow/logging.png "设置日志记录级别")

## <a name="parameterizing-data-flows"></a>参数化数据流

### <a name="parameterized-datasets"></a>参数化数据集

如果数据流使用参数化数据集，则在 " **设置** " 选项卡中设置参数值。

![执行数据流参数](media/data-flow/params.png "parameters")

### <a name="parameterized-data-flows"></a>参数化数据流

如果您的数据流已参数化，则在 " **参数** " 选项卡中设置数据流参数的动态值。您可以使用 ADF 管道表达式语言或数据流表达式语言来分配动态或文本参数值。 有关详细信息，请参阅 [数据流参数](parameters-data-flow.md)。

### <a name="parameterized-compute-properties"></a>参数化计算属性。

如果使用自动解析 Azure Integration runtime 并指定 coreCount 和 computeType 的值，则可以参数化核心计数或计算类型。

![Execute 数据流参数示例](media/data-flow/parameterize-compute.png "参数示例")

## <a name="pipeline-debug-of-data-flow-activity"></a>数据流活动的管道调试

若要执行带有数据流活动的调试管道运行，必须通过顶部栏上的 "数据流 **调试** " 滑块来切换数据流调试模式。 调试模式允许您针对活动的 Spark 群集运行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

![“调试”按钮](media/data-flow/debugbutton.png "“调试”按钮")

调试管道针对活动调试群集运行，而不是在数据流活动设置中指定的集成运行时环境。 启动调试模式时，可以选择调试计算环境。

## <a name="monitoring-the-data-flow-activity"></a>监视数据流活动

数据流活动具有特殊的监视体验，你可以在其中查看分区、阶段时间和数据沿袭信息。 通过 " **操作**" 下的 "眼镜" 图标打开 "监视" 窗格。 有关详细信息，请参阅 [监视数据流](concepts-data-flow-monitoring.md)。

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>在后续活动中使用数据流活动结果

数据流活动输出有关写入每个接收器的行数和从每个源读取的行数的指标。 在活动运行结果的节中返回这些结果 `output` 。 返回的度量值的格式为以下 json。

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

例如，若要获取写入到名为 "dataflowActivity" 的活动中名为 "sink1" 的接收器的行数，请使用 `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` 。

若要获取从该接收器中使用的名为 "source1" 的源中读取的行数，请使用 `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` 。

> [!NOTE]
> 如果接收器写入的行数为零，则它不会显示在指标中。 可以使用函数来验证是否存在 `contains` 。 例如， `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` 将检查是否有任何行写入 sink1。

## <a name="next-steps"></a>后续步骤

请参阅数据工厂支持的控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
