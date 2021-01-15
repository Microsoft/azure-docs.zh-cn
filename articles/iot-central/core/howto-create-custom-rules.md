---
title: 通过自定义规则和通知扩展 Azure IoT Central | Microsoft Docs
description: 解决方案开发人员将配置一个 IoT Central 应用程序，以便在设备停止发送遥测数据时发送电子邮件通知。 此解决方案使用 Azure 流分析、Azure Functions 和 SendGrid。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: c79367ca8cf9e4a4884c829c675d794b2e734737
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220259"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>使用流分析、Azure Functions 和 SendGrid 通过自定义规则扩展 Azure IoT Central

本操作指南向解决方案开发人员介绍如何使用自定义规则和通知来扩展 IoT Central 应用程序。 本示例演示如何在设备停止发送遥测数据时向操作员发送通知。 该解决方案使用 [Azure 流分析](../../stream-analytics/index.yml)查询来检测设备何时已停止发送遥测数据。 流分析作业使用 [Azure Functions](../../azure-functions/index.yml) 通过 [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/) 发送通知电子邮件。

本操作指南将介绍如何扩展 IoT Central，使其功能超越内置规则和操作的功能。

本操作指南的内容：

* 使用“连续数据导出”从 IoT Central 应用程序流式传输遥测数据。 
* 创建一个流分析查询用于检测设备何时已停止发送数据。
* 使用 Azure Functions 和 SendGrid 服务发送电子邮件通知。

## <a name="prerequisites"></a>必备条件

完成本操作方法指南中的步骤需要有效的 Azure 订阅。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="iot-central-application"></a>IoT Central 应用程序

在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上使用以下设置创建一个 IoT Central 应用程序：

| 设置 | 值 |
| ------- | ----- |
| 定价计划 | Standard |
| 应用程序模板 | 店内分析 – 条件监视 |
| 应用程序名称 | 接受默认设置，或选择自己的名称 |
| 代码 | 接受默认设置，或选择自己的唯一 URL 前缀 |
| 目录 | Azure Active Directory 租户 |
| Azure 订阅 | Azure 订阅 |
| 区域 | 离你最近的区域 |

本文中的示例和屏幕截图使用“美国”区域。  选择靠近你的位置，并确保在同一区域中创建所有资源。

此应用程序模板包含两个可发送遥测数据的模拟恒温器设备。

### <a name="resource-group"></a>资源组

使用 [Azure 门户](https://portal.azure.com/#create/Microsoft.ResourceGroup)创建名为 **DetectStoppedDevices** 的资源组，用于包含创建的其他资源。 在 IoT Central 应用程序所在的同一位置创建 Azure 资源。

### <a name="event-hubs-namespace"></a>事件中心命名空间

在 [Azure 门户中使用以下设置创建一个事件中心命名空间](https://portal.azure.com/#create/Microsoft.EventHub)：

| 设置 | 值 |
| ------- | ----- |
| 名称    | 选择命名空间名称 |
| 定价层 | 基本 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| 位置 | 美国东部 |
| 吞吐量单位 | 1 |

### <a name="stream-analytics-job"></a>流分析作业

在 [Azure 门户中使用以下设置创建一个流分析作业](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)：

| 设置 | 值 |
| ------- | ----- |
| 名称    | 选择作业名称 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| 位置 | 美国东部 |
| 宿主环境 | 云 |
| 流式处理单位 | 3 |

### <a name="function-app"></a>函数应用

在 [Azure 门户中使用以下设置创建一个函数应用](https://portal.azure.com/#create/Microsoft.FunctionApp)：

| 设置 | 值 |
| ------- | ----- |
| 应用程序名称    | 选择函数应用名称 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| OS | Windows |
| 托管计划 | 消耗计划 |
| 位置 | 美国东部 |
| 运行时堆栈 | .NET |
| 存储 | 新建 |

### <a name="sendgrid-account"></a>SendGrid 帐户

在 [Azure 门户中使用以下设置创建一个 SendGrid 帐户](https://portal.azure.com/#create/Sendgrid.sendgrid)：

| 设置 | 值 |
| ------- | ----- |
| 名称    | 选择 SendGrid 帐户名称 |
| 密码 | 创建密码 |
| 订阅 | 订阅 |
| 资源组 | DetectStoppedDevices |
| 定价层 | F1 免费 |
| 联系信息 | 填写所需的信息 |

创建全部所需的资源后，**DetectStoppedDevices** 资源组将如以下屏幕截图所示：

![检测已停止的设备资源组](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>创建事件中心

可将 IoT Central 应用程序配置为向某个事件中心连续导出遥测数据。 在本部分，你将创建一个事件中心用于接收来自 IoT Central 应用程序的遥测数据。 该事件中心将遥测数据传送到流分析作业进行处理。

1. 在 Azure 门户中，导航到你的事件中心命名空间并选择“+ 事件中心”。 
1. 将事件中心命名为 **centralexport**，然后选择“创建”。 

事件中心命名空间如以下屏幕截图所示：

![事件中心命名空间](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>获取 SendGrid API 密钥

函数应用需要使用 SendGrid API 密钥来发送电子邮件。 若要创建 SendGrid API 密钥：

1. 在 Azure 门户中，导航到你的 SendGrid 帐户。 然后选择“管理”以访问你的 SendGrid 帐户。 
1. 在 SendGrid 帐户中，依次选择“设置”、“API 密钥”。   选择“创建 API 密钥”： 

    ![创建 SendGrid API 密钥](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. 在“创建 API 密钥”页上，创建拥有“完全访问权限”的名为 **AzureFunctionAccess** 的密钥。 
1. 请记下该 API 密钥，因为在配置函数应用时需要用到。

## <a name="define-the-function"></a>定义函数

当流分析作业检测到已停止的设备时，此解决方案将使用 Azure Functions 应用来发送电子邮件通知。 若要创建函数应用：

1. 在 Azure 门户中，导航到“DetectStoppedDevices”资源组中的“应用服务”实例。  
1. 选择 **+** 以创建新函数。
1. 在“选择开发环境”页上，依次选择“门户中”、“继续”。   
1. 在“创建函数”页上，依次选择“Webhook + API”、“创建”。   

门户将创建名为 **HttpTrigger1** 的默认函数：

![默认的 HTTP 触发器函数](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>配置函数绑定

若要使用 SendGrid 发送电子邮件，需按如下所述配置函数的绑定：

1. 选择“集成”，选择输出“HTTP ($return)”，然后选择“删除”。   
1. 依次选择“+ 新建输出”、“SendGrid”、“选择”。    选择“安装”以安装 SendGrid 扩展。 
1. 安装完成后，选择“使用函数返回值”。  添加有效的“收件人地址”用于接收电子邮件通知。   添加用作电子邮件发件人的有效“发件人地址”。 
1. 选择“SendGrid API 密钥应用设置”旁边的“新建”。   输入 **SendGridAPIKey** 作为密钥，并输入前面记下的 SendGrid API 密钥作为值。 然后选择“创建”  。
1. 选择“保存”以保存函数的 SendGrid 绑定。 

集成设置如以下屏幕截图所示：

![函数应用集成](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>添加函数代码

若要实现函数，请添加 C# 代码以分析传入的 HTTP 请求并发送电子邮件，如下所示：

1. 在函数应用中选择 **HttpTrigger1** 函数，并将 C# 代码替换为以下代码：

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    在保存新代码之前，可能一直会出现一条错误消息。

1. 选择“保存”以保存函数。 

### <a name="test-the-function-works"></a>测试函数的运行情况

若要在门户中测试函数，请先选择代码编辑器底部的“日志”。  然后选择代码编辑器右侧的“测试”。  使用以下 JSON 作为 **请求正文**：

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

函数日志消息将显示在“日志”面板中： 

![函数日志输出](media/howto-create-custom-rules/function-app-logs.png)

几分钟后，“收件人”电子邮件地址将收到包含以下内容的电子邮件： 

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>添加流分析查询

此解决方案使用流分析查询来检测设备何时停止发送遥测数据超过 120 秒。 该查询使用来自事件中心的遥测数据作为输入。 作业将查询结果发送到函数应用。 在本部分，你将配置流分析作业：

1. 在 Azure 门户中导航到你的流分析作业，在“作业拓扑”下，依次选择“输入”、“+ 添加流输入”、“事件中心”。    
1. 根据下表中的信息使用前面创建的事件中心配置输入，然后选择“保存”： 

    | 设置 | 值 |
    | ------- | ----- |
    | 输入别名 | centraltelemetry |
    | 订阅 | 订阅 |
    | 事件中心命名空间 | 事件中心命名空间 |
    | 事件中心名称 | 使用现有名称 - **centralexport** |

1. 在 " **作业拓扑**" 下，选择 " **输出**"，选择 " **+ 添加**"，然后选择 " **Azure 函数**"。
1. 使用下表中的信息配置输出，然后选择“保存”： 

    | 设置 | 值 |
    | ------- | ----- |
    | 输出别名 | emailnotification |
    | 订阅 | 订阅 |
    | 函数应用 | 你的函数应用 |
    | 函数  | HttpTrigger1 |

1. 在“作业拓扑”下选择“查询”，并将现有查询替换为以下 SQL：  

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. 选择“保存”。 
1. 若要启动流分析作业，请依次选择“概述”、“启动”、“立即”、“启动”：    

    ![流分析](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>在 IoT Central 中配置导出

在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上，导航到从 Contoso 模板创建的 IoT Central 应用程序。 在本部分，你将配置应用程序，以将其模拟设备发来的遥测数据流式传输到事件中心。 若要配置导出：

1. 导航到“数据导出”页，依次选择“+ 新建”、“Azure 事件中心”。   
1. 使用以下设置配置导出，然后选择“保存”： 

    | 设置 | 值 |
    | ------- | ----- |
    | 显示名称 | 导出到事件中心 |
    | 已启用 | 启用 |
    | 事件中心命名空间 | 事件中心命名空间的名称 |
    | 事件中心 | centralexport |
    | 度量 | 启用 |
    | 设备 | 关闭 |
    | 设备模板 | 关闭 |

![连续数据导出配置](media/howto-create-custom-rules/cde-configuration.png)

等到导出状态变为“正在运行”，然后继续。 

## <a name="test"></a>测试

若要测试该解决方案，可以禁用从 IoT Central 到已停止的模拟设备的连续数据导出：

1. 在 IoT Central 应用程序中，导航到“数据导出”页，并选择“导出到事件中心”导出配置。  
1. 将“已启用”设置为“关闭”，然后选择“保存”。   
1. 在至少两分钟后，“收件人”电子邮件地址将收到一封或多封类似于以下示例内容的电子邮件： 

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>整理

若要在完成本操作指南后进行清理并避免不必要的费用，请在 Azure 门户中删除 **DetectStoppedDevices** 资源组。

可以从应用程序中的“管理”页删除 IoT Central 应用程序。 

## <a name="next-steps"></a>后续步骤

通过本操作指南，我们已学会了：

* 使用“连续数据导出”从 IoT Central 应用程序流式传输遥测数据。 
* 创建一个流分析查询用于检测设备何时已停止发送数据。
* 使用 Azure Functions 和 SendGrid 服务发送电子邮件通知。

了解如何创建自定义规则和通知后，建议接下来了解如何[使用自定义分析扩展 Azure IoT Central](howto-create-custom-analytics.md)。
