---
title: 如何在 Azure 门户中监视 Azure 存储帐户 | Microsoft Docs
description: 了解如何使用 Azure 门户和 Azure 存储分析在 Azure 中监视存储帐户。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bfecae4775655f732df8fd6cffea613cd39fb828
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782002"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>监视 Azure 门户中的存储帐户

[Azure 存储分析](storage-analytics.md)提供所有存储服务的指标，以及 Blob、队列和表的日志。 可以使用 [Azure 门户](https://portal.azure.com)来配置要为帐户记录哪些指标和日志，并配置图表来提供指标数据的可视表示形式。 

建议查阅[用于存储的 Azure Monitor](../../azure-monitor/insights/storage-insights-overview.md)（预览）。 它是 Azure Monitor 的一项功能，通过提供 Azure 存储服务性能、容量和可用性的统一视图，提供对 Azure 存储帐户的全面监视。 无需启用或配置任何设置，即可立即从预定义的交互式图表和包含的其他可视化效果中查看这些指标。

> [!NOTE]
> 在 Azure 门户中检查监视数据会产生相关的费用。 有关详细信息，请参阅[存储分析](storage-analytics.md)。
>
> Azure 文件目前支持存储分析指标，但尚不支持日志记录。
>
> 高级性能块 blob 存储帐户不支持存储分析指标，但支持日志记录。 可以通过 REST API 或客户端库以编程方式启用日志记录。 若要使用高级性能 blob 存储帐户查看指标，不妨参阅 [Azure Monitor 中的 Azure 存储指标](../blobs/monitor-blob-storage.md)。
>
> 有关使用存储分析及其他工具来识别、诊断和排查 Azure 存储相关问题的深入指导，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](storage-monitoring-diagnosing-troubleshooting.md)。
>

## <a name="configure-monitoring-for-a-storage-account"></a>为存储帐户配置监视

1. 在 [Azure 门户](https://portal.azure.com)中选择“存储帐户”，并单击存储帐户名称打开帐户仪表板。
1. 在菜单边栏选项卡的“监视”部分选择“诊断”。

    ![屏幕截图，突出显示了 "监视 (经典) " 部分下 (经典) "选项的诊断设置。](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. 选择要监视的每个服务的指标数据类型，以及数据的保留策略。 还可以通过将“状态”设置为“关闭”来禁用监视。

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   若要设置数据保留策略，请移动“保留期(天)”滑块，或输入数据的保留天数（1 到 365 天）。 新存储帐户的默认保留期为 7 天。 如果不需要设置保留策略，请输入零。 如果没有保留策略，则由用户自行决定是否删除监视数据。

   > [!WARNING]
   > 手动删除指标数据会产生费用。 陈旧的分析数据（超过保留策略的数据）将被系统删除，不会产生费用。 建议根据要将帐户的存储分析数据保留多长时间来设置保留策略。 有关详细信息，请参阅[按存储指标计费](storage-analytics-metrics.md#billing-on-storage-metrics)。
   >

1. 完成监视配置后，选择“保存”。

随后，一组默认的指标将显示在存储帐户边栏选项卡上的图表中，以及各个服务边栏选项卡（Blob、队列、表和文件）中。 启用服务的指标后，最长可能需要一小时，数据才会显示在其图表中。 可以在任何指标图表中选择“编辑”，配置要在图表中显示的指标。

将“状态”设置为“关闭”可以禁用指标收集和日志记录。

> [!NOTE]
> Azure 存储使用[表存储](storage-introduction.md#table-storage)来存储存储帐户的指标，将指标存储在帐户中的表内。 有关详细信息，请参阅： [指标的存储方式](storage-analytics-metrics.md#how-metrics-are-stored)。
>

## <a name="customize-metrics-charts"></a>自定义指标图表

使用以下过程选择要在指标图表中查看哪些存储指标。

1. 首先在 Azure 门户中显示存储指标图表。 可以在存储帐户边栏选项卡以及各个服务（Blob、队列、表和文件）的“指标”边栏选项卡中找到图表。

   本示例使用“存储帐户边栏选项卡”上显示的以下图表：

   ![在 Azure 门户中选择图表](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. 单击图表中的任意位置以进行编辑。

1. 接下来，选择要在图表中显示的指标“时间范围”，以及要显示其指标的“服务”（Blob、队列、表或文件） 。 此处已选择要显示 Blob 服务在过去一周的指标：

   ![在“编辑图表”边栏选项卡中选择时间范围和服务](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. 选择要在图表中显示的各个指标，然后单击“确定”。

   ![在“编辑图表”边栏选项卡选择单个指标](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

图表设置不会影响存储帐户中监视数据的收集、聚合或存储。

### <a name="metrics-availability-in-charts"></a>图表中可用的指标

可用指标列表根据在下拉列表中选择的服务，以及要编辑的图表单位类型而异。 例如，仅当所要编辑的图表显示百分比单位时，才能选择 PercentNetworkError 和 PercentThrottlingError 等百分比指标：

![Azure 门户中的请求错误百分比图表](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>指标解析

在“诊断”中选择的指标决定了可用于帐户的指标的解析：

* “聚合”监视提供入口/出口、可用性、延迟和成功百分比等指标。 系统将从 Blob、表、文件和队列服务聚合这些指标。
* “按 API”除了提供服务级别的聚合外，还提供更精细的解析，包括可用于单个存储操作的指标。

## <a name="configure-metrics-alerts"></a>配置指标警报

可以创建警报，以便在达到存储资源指标的阈值时收到通知。

1. 若要打开“警报规则”边栏选项卡，请向下滚动到“菜单”边栏选项卡的“监视”部分，并选择“警报(经典)”   。
2. 选择“添加指标警报(经典)”以打开“添加警报规则”边栏选项卡 
3. 为新的警报规则输入“名称”和“描述” 。
4. 选择要为其添加警报的指标，以及警报条件和阈值。 阈值单位类型根据所选的指标而异。 例如，“计数”是 ContainerCount 的单位类型，而 PercentNetworkError 指标的单位是百分比。
5. 选择“时间段”。 在该时间段内达到或超过阈值的指标将触发警报。
6. （可选）配置电子邮件和 Webhook 通知。 有关 Webhook 的详细信息，请参阅[针对 Azure 指标警报配置 Webhook](../../azure-monitor/platform/alerts-webhooks.md)。 如果未配置电子邮件或 Webhook 通知，警报只会显示在 Azure 门户中。

![Azure 门户中的“添加警报规则”边栏选项卡](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>将指标图表添加到门户仪表板

可将任何存储帐户的 Azure 存储指标图表添加到门户仪表板。

1. 在 [Azure 门户](https://portal.azure.com)中查看仪表板时，单击“编辑仪表板”。
1. 在“磁贴库”中，选择“查找磁贴依据” > “类型”。
1. 选择“类型” > “存储帐户”。
1. 在“资源”中，选择要将其指标添加到仪表板的存储帐户。
1. 选择“类别” > “监视”。
1. 将图表磁贴拖放到要显示的指标所在的仪表板中。 针对要在仪表板上显示的所有指标重复上述步骤。 在下图中，为了方便演示，已突出显示“Blob - 请求总数”图表，但可将所有图表放置在仪表板上。

   ![在 Azure 门户中的磁贴库](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. 添加完图表后，请选择仪表板顶部附近的“完成自定义”。

将图表添加到仪表板后，可以根据“自定义指标图表”所述进一步自定义这些图表。

## <a name="configure-logging"></a>配置日志记录

可以指示 Azure 存储保存针对 Blob、表和队列服务发出的读取、写入和删除请求的诊断日志。 设置的数据保留策略也适用于这些日志。

> [!NOTE]
> Azure 文件目前支持存储分析指标，但尚不支持日志记录。
>

1. 在 [Azure 门户](https://portal.azure.com)中选择“存储帐户”，然后单击存储帐户的名称打开存储帐户边栏选项卡。
1. 在菜单边栏选项卡的“监视(经典)”部分中选择“诊断设置(经典)”。

    ![Azure 门户中“监视”下面的诊断菜单项。](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. 确保“状态”设置为“打开”，选择要为其启用日志记录的服务。

    ![在 Azure 门户中配置日志记录。](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. 单击“ **保存** ”。

诊断日志保存在存储帐户下名为 $logs 的 Blob 容器中。 可以使用 [Microsoft Azure 存储资源管理器](https://storageexplorer.com)等存储资源管理器来查看日志数据，也可以使用存储客户端库或 PowerShell 以编程方式这样做。

有关如何访问 $logs 容器的信息，请参阅[存储分析日志记录](storage-analytics-logging.md)。

## <a name="next-steps"></a>后续步骤

* 了解有关存储分析的 [指标、日志记录和计费](storage-analytics.md) 的详细信息。