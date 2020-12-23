---
title: 将环境连接到 Power BI - Azure 时序见解 | Microsoft Docs
description: 了解如何将 Azure 时序见解连接到 Power BI，以便在整个组织中共享、绘制图表和显示数据。
author: deepakpalled
ms.author: dpalled
manager: diviso
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 680b3c5a9548fa06d0139bd441b5583c27427a77
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020770"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>在 Power BI 中可视化来自 Azure 时序见解的数据

Azure 时序见解是可在云中存储、管理、查询和可视化时序数据的平台。 [Power BI](https://powerbi.microsoft.com) 是一个业务分析工具，它提供丰富的可视化功能让你在组织中共享见解和结果。 这两个服务现在可以集成到一起，以充分利用 Azure 时序见解和 Power BI 固有的可视化功能。

将了解如何执行以下操作：

* 使用云连接器将 Azure 时序见解连接到 Power BI
* 使用 Power BI 中的数据创建视觉对象
* 将报表发布到 Power BI，然后与组织中的其他人共享

最后，了解如何通过 Azure 时序见解可视化时序数据，然后使用 Power BI 的强大数据可视化功能和轻松共享功能增强这些数据。

如果还没有 Azure 订阅，请确保注册 [免费订阅](https://azure.microsoft.com/free/) 。

## <a name="prerequisites"></a>先决条件

* 下载并安装最新版本的 [Power BI Desktop](https://powerbi.microsoft.com/downloads/)。
* 具有或创建 [Azure 时序见解 Gen2 环境](./how-to-provision-manage.md)

> [!IMPORTANT]
>
> * 当前，仅配置有“Warm 存储”的 Azure 时序见解 Gen2 环境支持该连接器。
> * 如果你有从另一个 Azure AD 租户访问 Azure 时序见解 Gen2 环境的来宾访问权限，则将无法访问该连接器。 请参阅[环境访问策略](./concepts-access-policies.md)。

## <a name="connect-data-from-azure-time-series-insights-to-power-bi"></a>将数据从 Azure 时序见解连接到 Power BI

若要将 Azure 时序见解环境连接到 Power BI，请执行以下步骤：

1. 打开 Azure 时序见解资源管理器
1. 将数据导出为查询或原始数据
1. 打开 Power BI Desktop
1. 从自定义查询加载

### <a name="export-data-into-power-bi-desktop"></a>将数据导出到 Power BI Desktop

开始操作：

1. 打开 Azure 时序见解资源管理器并策展数据。
1. 创建满意的视图后，导航到“更多操作”下拉菜单，然后选择“连接到 Power BI” 。

    [![Azure 时序见解资源管理器导出](media/how-to-connect-power-bi/time-series-insights-export-option.png)](media/how-to-connect-power-bi/time-series-insights-export-option.png#lightbox)

1. 在此选项卡中设置参数：

   1. 指定视图的相对时间范围。 如果对现有视图感到满意，请保留“现有时间范围”。

   1. 选择“聚合”或“原始事件”。

       > [!NOTE]
       > 以后始终可以在 Power BI 中聚合数据，但聚合后无法还原为原始数据。

       > [!NOTE]
       > 原始事件级别数据的事件计数限制为 250,000 个。

       [![连接](media/how-to-connect-power-bi/connect-to-power-bi.png)](media/how-to-connect-power-bi/connect-to-power-bi.png#lightbox)

   1. 如果尚未使用“Warm 存储”配置 Azure 时序见解环境，会收到一条警告。

       [![Warm 存储警告](media/how-to-connect-power-bi/connect-to-power-bi-warning.png)](media/how-to-connect-power-bi/connect-to-power-bi-warning.png#lightbox)

       > [!TIP]
       > 可以在 Azure 门户中为“Warm 存储”配置现有的实例。

1. 选择“将查询复制到剪贴板”。
1. 现在启动 Power BI Desktop。
1. 在 Power BI Desktop 中的“主页”选项卡上，选择左上角的“获取数据”，然后选择“更多”。

    [![“主页”下拉菜单](media/how-to-connect-power-bi/power-bi-home-drop-down.png)](media/how-to-connect-power-bi/power-bi-home-drop-down.png#lightbox)

1. 搜索“Azure 时序见解”，依次选择“Azure 时序见解(Beta)”、“连接”。  

    [![将 Power BI 连接到 Azure 时序见解](media/how-to-connect-power-bi/connect-to-time-series-insights.png)](media/how-to-connect-power-bi/connect-to-time-series-insights.png#lightbox)

    或者，导航到“Azure”选项卡，并依次选择“Azure 时序见解(Beta)”、“连接”。

1. 此时会显示一个消息对话框，要求授予连接到第三方资源的权限。 请选择“继续”。

    [![选择“创建自定义查询”](media/how-to-connect-power-bi/confirm-the-connection.png)](media/how-to-connect-power-bi/confirm-the-connection.png#lightbox)

1. 在“数据源”下面的下拉菜单中，选择“创建自定义查询”。 将剪贴板中的内容粘贴到下面的“自定义查询(可选)”可选字段中，然后按“确定”。

    [![传入自定义查询并选择“确定”](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1. 随即会加载数据表。 按“加载”以载入 Power BI。

    [![检查表中的加载数据并选择“加载”](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

如果已完成这些步骤，请跳到下一部分。

## <a name="create-a-report-with-visuals"></a>使用视觉对象创建报表

将数据导入 Power BI 后，接下来请生成包含视觉对象的报表。

1. 在窗口的左侧，确保已选择“报表”视图。

    [![屏幕截图显示“报表”视图图标。](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. 在“可视化效果”列中，选择所需的视觉对象。 例如，选择“折线图”。 这会在画布中添加一个空白的折线图。

1. 在“字段”列表中选择“_时间戳”，然后将此时间戳拖放到“轴”字段以显示 X 轴上的项。   确保切换到“_时间戳”，作为“轴”的值（默认为“数据层次结构”）  。

    [![屏幕截图显示已选择“时间戳(_T)”的时间戳菜单。](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1. 同样，请在“字段”列表中选择“时序 ID”，然后将此 ID 拖放到“值”字段以显示 Y 轴上的项。

    [![创建折线图](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. 若要将另一个图表添加到画布，请选择画布上折线图外部的任意位置，并重复上述过程。

    [![创建要共享的其他图表](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

创建报表后，可将其发布到 Power BI Reporting Services。

## <a name="advanced-editing"></a>高级编辑

如果已在 Power BI 中加载了一个数据集，但想要修改查询（例如日期/时间或环境 ID 参数），可以通过 Power BI 的高级编辑器功能实现此目的。 有关详细信息，请参阅 [Power BI 文档](/power-bi/desktop-query-overview)。

功能概述：

1. 在 Power BI Desktop 中选择“编辑查询”。
1. 按“高级编辑器”。

    [![在高级编辑器中编辑查询](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png)](media/how-to-connect-power-bi/power-bi-advanced-query-editing.png#lightbox)

1. 根据需要修改 JSON 有效负载。
1. 在 **Power Query 编辑器窗口** 中，依次选择“完成”、“关闭并应用”。

现在接口会反映你应用的所需更改。  

## <a name="next-steps"></a>后续步骤

* 了解 Azure 时序见解的 [Power BI 连接器概念](/power-bi/desktop-query-overview)。

* 详细了解 [Power BI Desktop](/power-bi/desktop-query-overview)。