---
title: 使用 Azure 门户针对新建议创建 Azure 顾问警报
description: 为新建议创建 Azure 顾问警报
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 3c51479821914ef34edcd13d8708344169f17aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590109"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>使用 Azure 门户针对新建议创建 Azure 顾问警报 

本文介绍如何使用 Azure 门户在 Azure 顾问中针对新建议设置警报。 

当 Azure 顾问检测到针对某项资源的新建议时，将在 [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)中存储一个事件。 可以使用特定于建议的警报创建体验，为来自 Azure 顾问的这些事件设置警报。 可以选择订阅和资源组（可选）来指定想要接收其警报的资源。 

还可以使用以下属性来确定建议类型：

* 类别
* 影响级别
* 建议类型

还可通过以下方式配置触发警报时将发生的操作：  

* 选择现有操作组
* 创建新的操作组

若要了解有关操作组的详细信息，请参阅[创建和管理操作组](../azure-monitor/alerts/action-groups.md)。

> [!NOTE] 
> 顾问警报目前仅适用于高可用性、性能和成本建议。 不支持安全建议。 

## <a name="create-alert-rule"></a>创建警报规则
1. 在门户中选择“Azure 顾问” 。

    ![门户中的“Azure 顾问”](./media/advisor-alerts/create1.png)

2. 在左侧菜单的“监视”部分选择“警报” 。 

    ![顾问中的“警报”](./media/advisor-alerts/create2.png)

3. 选择“新建顾问警报”。

    ![新建顾问警报](./media/advisor-alerts/create3.png)

4. 在“范围”部分，选择要对其发出警报的订阅和（可选）资源组。 

    ![顾问警报范围](./media/advisor-alerts/create4.png)

5. 在“条件”部分，选择用于配置警报的方法。 如果要针对特定类别和/或影响级别的所有建议发出警报，请选择“类别和影响级别”。 如果要针对特定类型的所有建议发出警报，请选择“建议类型”。

    ![Azure 顾问警报条件](./media/advisor-alerts/create5.png)

6. 根据所选的“配置依据”选项，可以指定条件。 如果要将条件指定为所有建议，只需将剩余字段留空即可。 

    ![顾问警报操作组](./media/advisor-alerts/create6.png)

7. 在“操作组”部分，选择“添加现有项”以使用已创建的操作组，或选择“新建”以设置新的[操作组](../azure-monitor/alerts/action-groups.md)  。 

    ![顾问警报 - 添加现有项](./media/advisor-alerts/create7.png)

8. 在“警报详细信息”部分，为警报提供名称和简短说明。 如果你要启用警报，请将“创建后启用规则”选项设置为“是” 。 然后选择要将警报保存到的资源组。 这不会影响建议的目标范围。 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="“警报详细信息”部分的屏幕截图。":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>将建议警报配置为使用 Webhook
本部分介绍如何将 Azure 顾问警报配置为通过 Webhook 向现有系统发送建议数据。 

可以设置警报，以便在提供了有关你的某个资源的新顾问建议时收到通知。 这些警报可以通过电子邮件或短信向你发出通知，但也可以使用这些警报通过 Webhook 来与现有系统集成。 


### <a name="using-the-advisor-recommendation-alert-payload"></a>使用顾问建议警报有效负载
如果你要使用 Webhook 将顾问警报集成到自己的系统中，需要分析从通知发送的 JSON 有效负载。 

为此警报设置操作组时，请选择是否要使用通用警报架构。 如果选择通用警报架构，则有效负载如下所示： 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

如果不使用通用架构，则有效负载如下所示： 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

在任一架构中，都可以通过检查 eventSource 是否为 `Recommendation`，operationName 是否为 `Microsoft.Advisor/recommendations/available/action`，来识别顾问建议事件 。

可以使用的其他一些重要字段包括： 

* alertTargetIDs（在通用架构中）或 resourceId（在传统架构中） 
* recommendationType
* recommendationName
* recommendationCategory
* recommendationImpact
* recommendationResourceLink


## <a name="manage-your-alerts"></a>管理警报 

在 Azure 顾问中，可以编辑、删除或者禁用和启用建议警报。 

1. 在门户中选择“Azure 顾问” 。

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Azure 门户菜单的屏幕截图，其中显示已选择“Azure 顾问”。":::

2. 在左侧菜单的“监视”部分选择“警报” 。

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Azure 门户菜单的屏幕截图，其中显示已选择“警报”。":::

3. 若要编辑某个警报，请单击“警报名称”打开该警报，然后编辑所需的字段。

4. 若要删除、启用或禁用某个警报，请单击其所在行末尾的省略号图标，然后选择要执行的操作。
 

## <a name="next-steps"></a>后续步骤
- 获取[活动日志警报概述](../azure-monitor/alerts/alerts-overview.md)，了解如何接收警报。
- 详细了解[操作组](../azure-monitor/alerts/action-groups.md)。
