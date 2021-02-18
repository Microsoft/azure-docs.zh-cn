---
title: Azure 服务总线 - 自动更新消息传送单元
description: 本文介绍如何使用自动更新 Service Bus 命名空间的消息传送单元。
ms.topic: how-to
ms.date: 09/15/2020
ms.openlocfilehash: 932c7bb1235cb54aefe67253e38e1683187f4d2c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581647"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>自动更新 Azure 服务总线命名空间的消息传送单元。 
自动缩放是指在处理应用程序负载时让适当数量的资源运行。 当负载增加时，它可以添加资源来处理增加的负载；当资源空闲时，它可以删除资源以节省资金。 请参阅 [Microsoft Azure 中的自动缩放概述](../azure-monitor/autoscale/autoscale-overview.md) ，了解有关 Azure Monitor 的自动缩放功能的详细信息。 

服务总线高级消息传送在 CPU 和内存级别提供资源隔离，以便每个客户工作负荷以隔离方式运行。 此资源容器称为 **消息传送单元**。 若要详细了解消息传送单元，请参阅 [服务总线高级消息传送](service-bus-premium-messaging.md)。 

通过使用服务总线高级命名空间的自动缩放功能，你可以指定最小和最大 [消息传送单元](service-bus-premium-messaging.md) 数，并基于一组规则自动添加或删除消息传送单元。 

例如，你可以使用自动缩放功能为服务总线命名空间实现以下缩放方案。 

- 当命名空间的 CPU 使用率超过75% 时，为服务总线命名空间增加消息传送单元。 
- 如果命名空间的 CPU 使用率低于25%，则减少服务总线命名空间的消息传送单元。 
- 在工作时间内使用更多消息传送单元，在休息时间更少。 

本文介绍如何在 Azure 门户中自动缩放服务总线命名空间 (更新 [消息单元](service-bus-premium-messaging.md)) 。 

> [!IMPORTANT]
> 本文仅适用于 Azure 服务总线的高级层。 

## <a name="autoscale-setting-page"></a>"自动缩放设置" 页
首先，按照以下步骤导航到服务总线命名空间的 " **自动缩放设置** " 页。

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在搜索栏中键入 " **服务总线**"，从下拉列表中选择 " **服务总线** "，然后按 **enter**。 
1. 从命名空间列表中选择 **高级命名空间** 。 
1. 切换到 " **缩放** " 页。 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="服务总线命名空间-缩放页":::

## <a name="manual-scale"></a>手动缩放 
此设置允许您为命名空间设置固定的消息传送单元数。 

1. 在 " **自动缩放设置** " 页上，选择 " **手动缩放** " （如果尚未选择）。 
1. 对于 " **消息单元** " 设置，请从下拉列表中选择消息传送单元数。
1. 在工具栏上选择“保存”，保存该设置。 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="手动缩放消息单元":::       


## <a name="custom-autoscale---default-condition"></a>自定义自动缩放-默认条件
可以通过使用条件来配置消息传送单元的自动缩放。 当没有其他缩放条件匹配时，将执行此缩放条件。 可以通过以下方式之一来设置默认条件：

- 基于指标 (例如 CPU 或内存使用率）进行缩放) 
- 缩放到特定的消息传送单元数

不能将计划设置为在特定日期或日期范围内自动缩放的默认条件。 当具有计划的其他缩放条件都不匹配时，将执行此缩放条件。 

### <a name="scale-based-on-a-metric"></a>基于指标进行缩放
下面的过程演示了如何添加一个条件，以便在 cpu 使用率大于75% 时，自动增加 (scale out) 的消息传送单元，并减小当 CPU 使用率低于25% 时 (缩放) 的消息单元。 增量是从1到2、2到4和4到8完成的。 同样，减量的实现方式为8到4，4到2，2到1。 

1. 在 "**自动缩放设置**" 页上，为 "**选择如何缩放资源**" 选项选择 "**自定义自动缩放**"。 
1. 在页面的 " **默认** " 部分中，指定默认条件的 **名称** 。 选择 **铅笔** 图标以编辑文本。 
1. 选择 "基于 **缩放模式** 的 **指标缩放"** 。 
1. 选择“+ 添加规则”。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="默认值-根据指标缩放":::    
1. 在 " **缩放规则** " 页上，执行以下步骤：
    1. 从 " **指标名称** " 下拉列表中选择一个度量值。 在此示例中，它是 **CPU**。 
    1. 选择运算符和阈值。 在此示例中，它们 **大于** ， **75** 用于 **触发缩放操作的指标阈值**。 
    1. 在 "**操作**" 部分中选择 **操作**。 在此示例中，它设置为 " **增加**"。 
    1. 然后选择 "**添加**"
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="默认-如果 CPU 使用率大于75%，则向外缩放":::       

        > [!NOTE]
        > 如果在此示例中总体 CPU 使用率超过75%，则自动缩放功能将增加命名空间的消息传送单元。 增量是从1到2、2到4和4到8完成的。 
1. 再次选择 " **+ 添加规则** "，然后在 " **缩放规则** " 页上执行以下步骤：
    1. 从 " **指标名称** " 下拉列表中选择一个度量值。 在此示例中，它是 **CPU**。 
    1. 选择运算符和阈值。 在此示例中，它们 **小于** 和 **25** 的 **指标阈值触发缩放操作**。 
    1. 在 "**操作**" 部分中选择 **操作**。 在此示例中，它设置为 " **减小**"。 
    1. 然后选择 "**添加**" 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="默认-如果 CPU 使用率小于25%，则放大":::       

        > [!NOTE]
        > 如果在此示例中总体 CPU 使用率低于25%，则自动缩放功能会减少命名空间的消息传送单元。 减量的实现方式为8到4，4到2，2到1。 
1. 设置消息传送单元的 **最小** 值和 **最大** 值以及 **默认** 数量。

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="基于指标的默认规则":::
1. 选择工具栏上的 " **保存** " 以保存自动缩放设置。 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>缩放到特定的消息传送单元数
请按照以下步骤进行操作，将该规则配置为缩放命名空间以使用特定数量的消息传送单元。 同样，如果其他任何缩放条件都不匹配，则会应用默认条件。 

1. 在 "**自动缩放设置**" 页上，为 "**选择如何缩放资源**" 选项选择 "**自定义自动缩放**"。 
1. 在页面的 " **默认** " 部分中，指定默认条件的 **名称** 。 
1. 选择缩放 **模式** 的 "**缩放到特定消息单元**"。 
1. 对于 " **消息传送单元**"，选择默认消息传送单元数。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="默认值-缩放到特定消息传送单元":::       

## <a name="custom-autoscale---additional-conditions"></a>自定义自动缩放-其他条件
上一部分说明了如何添加自动缩放设置的默认条件。 本部分说明如何将更多条件添加到自动缩放设置。 对于这些额外的非默认条件，可以基于特定的一周或某个日期范围设置计划。 

### <a name="scale-based-on-a-metric"></a>基于指标进行缩放
1. 在 "**自动缩放设置**" 页上，为 "**选择如何缩放资源**" 选项选择 "**自定义自动缩放**"。 
1. 选择 "在 **默认** 块下 **添加缩放条件**"。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="自定义-添加缩放条件链接":::    
1. 指定条件的 **名称** 。 
1. 确认已选择 " **基于指标缩放** " 选项。 
1. 选择 " **+ 添加规则** "，添加一个规则，以便在总体 CPU 使用率超过75% 时增加消息单元。 按照 " [默认条件](#custom-autoscale---default-condition) " 部分中的步骤进行操作。 
5. 设置消息传送单元的 **最小** 值和 **最大** 值以及 **默认** 数量。
6. 您还可以在自定义条件 (上设置计划，而不是在默认条件) 上设置 **计划** 。 您可以为条件指定开始日期和结束日期 (或者) 选择 (星期一、星期二等的特定日期。 ) 一周。 
    1. 如果选择 " **指定开始/结束日期**"，请选择 " **时区**"、" **开始日期** " 和 "时间" 和 " **结束日期和时间** " (，如下图所示) 条件生效。 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="消息传送单元数的最小值、最大值和默认值":::
    1. 如果选择 " **重复特定日期**"，请选择应应用该条件的星期几、时区、开始时间和结束时间。 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="重复特定日":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>缩放到特定的消息传送单元数
1. 在 "**自动缩放设置**" 页上，为 "**选择如何缩放资源**" 选项选择 "**自定义自动缩放**"。 
1. 选择 "在 **默认** 块下 **添加缩放条件**"。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="自定义-添加缩放条件链接":::    
1. 指定条件的 **名称** 。 
2. 选择 **缩放模式** 的 "**缩放到特定消息单元**" 选项。 
1. 从下拉列表中选择 **消息传送单元** 数。 
6. 对于 " **计划**"，为条件指定开始日期和结束日期 (或) 选择 " (" 星期一 "、" 星期二 "等的特定日期。 ) 周和时间。 
    1. 如果选择 " **指定开始/结束日期**"，请选择要生效的条件的 **时区**、 **开始日期和时间** 以及 **结束日期和时间** 。 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="缩放到特定消息传送单元-开始和结束日期":::        
    1. 如果选择 " **重复特定日期**"，请选择应应用该条件的星期几、时区、开始时间和结束时间。
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="缩放到特定消息传送单元-重复特定天数":::

> [!IMPORTANT]
> 若要详细了解自动缩放设置的工作原理，尤其是它如何选择配置文件或条件并评估多个规则的详细信息，请参阅 [了解自动缩放设置](../azure-monitor/autoscale/autoscale-understanding-settings.md)。          

## <a name="next-steps"></a>后续步骤
若要了解消息传送单元，请参阅[高级消息传送](service-bus-premium-messaging.md)

