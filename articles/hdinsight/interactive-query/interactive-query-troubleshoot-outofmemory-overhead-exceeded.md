---
title: Apache Hive 中的联接导致 OutOfMemory 错误-Azure HDInsight
description: 处理 OutOfMemory 错误“超过 GC 开销限制”错误
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c227758e31b3b17768b8140475872245b2f34e52
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532945"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>方案：Apache Hive 中的联接导致 Azure HDInsight 中出现 OutOfMemory 错误

本文介绍在 Azure HDInsight 群集中使用交互式查询组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

Apache Hive 联接的默认行为是将表的全部内容加载到内存中，以便无需执行 Map/Reduce 步骤即可执行联接。 如果 Hive 表太大而无法放入内存中，则查询可能会失败。

## <a name="cause"></a>原因

在足够大的 Hive 中运行联接时，会遇到以下错误：

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>解决方法

通过设置以下 Hive 配置值，防止 Hive 在联接时将表加载到内存中（而不是执行 Map/Reduce 步骤）：

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>后续步骤

如果设置此值不能解决问题，请访问以下项之一。

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区与适当的资源（解答、支持人员和专家）相关联来改善客户体验。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。