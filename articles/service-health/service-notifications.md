---
title: 使用 Azure 门户查看服务运行状况通知
description: 在 Azure 门户中查看服务运行状况通知。 服务运行状况通知由 Azure 基础结构发布到 Azure 活动日志中。
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 9f9f3e7b10d9aa0014e4e00e7bfa72c9dc66e142
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588006"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>使用 Azure 门户查看服务运行状况通知

服务运行状况通知由 Azure 基础结构发布到 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)中。  这些通知包含有关订阅下的资源的信息。 鉴于活动日志中存储的信息量可能很大，因此有一个单独的用户界面，以便更轻松地查看和设置有关服务运行状况通知的警报。 

服务运行状况通知可能仅提供信息，也可能提示执行某个操作，具体取决于类别。

有关各种类别的服务运行状况通知的详细信息，请参阅[服务运行状况通知属性](service-health-notifications-properties.md)。

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>在 Azure 门户中查看服务运行状况通知

1. 在 [Azure 门户](https://portal.azure.com)中，选择“监视”  。

    ![Azure 门户菜单的屏幕快照，其中“监视器”处于选中状态](./media/service-notifications/home-monitor.png)

    Azure Monitor 将所有监视设置和数据汇聚到一个合并视图中。 首次打开的是“活动日志”  部分。

1. 选择“**警报**”。

    ![监视活动日志的屏幕快照，其中“警报”处于选中状态](./media/service-notifications/service-health-summary.png)

1. 选择“+添加活动日志警报”  并设置警报，以确保以后可收到服务通知。 有关详细信息，请参阅[创建有关服务通知的活动日志警报](./alerts-activity-log-service-notifications-portal.md)。

## <a name="next-steps"></a>后续步骤

* 详细了解[活动日志警报](../azure-monitor/alerts/activity-log-alerts.md)。
