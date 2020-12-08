---
title: 教程：将事件中心数据发送到数据仓库 - 事件网格
description: 教程：介绍了如何使用 Azure 事件网格和事件中心将数据迁移到 Azure Synapse Analytics。 它使用 Azure 函数来检索 Capture 文件。
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 42a2f7fd557970328f6d88b08e296317cecd8c66
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462145"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>教程：将大数据流式传输到数据仓库
Azure [事件网格](overview.md)是一项智能事件路由服务，可用于对应用和服务的通知（事件）作出响应。 例如，它可以触发 Azure 函数来处理已捕获到 Azure Blob 存储或 Azure Data Lake Storage 的事件中心数据，并将数据迁移到其他数据存储库。 此[事件中心和事件网格集成示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)展示了如何将事件中心与事件网格结合使用，从而将捕获的事件中心数据从 blob 存储无缝迁移到 Azure Synapse Analytics。

![应用概览](media/event-grid-event-hubs-integration/overview.png)

此图描绘了在本教程中生成的解决方案的工作流： 

1. 在 Azure Blob 存储中捕获发送到 Azure 事件中心的数据。
2. 完成数据捕获后，将生成一个事件并将其发送到 Azure 事件网格。 
3. 事件网格将此事件数据转发到 Azure 函数应用。
4. 函数应用使用事件数据中的 Blob URL 从存储中检索 Blob。 
5. 函数应用将 Blob 数据迁移到 Azure Synapse Analytics。 

在本文中，将执行以下步骤：

> [!div class="checklist"]
> * 使用 Azure 资源管理器模板部署基础结构：事件中心、存储帐户、函数应用、专用 SQL 池。
> * 在专用 SQL 池中创建表。
> * 将代码添加到函数应用。
> * 订阅事件。 
> * 运行将数据发送到事件中心的应用。
> * 查看数据仓库中的已迁移数据。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要完成本教程，必须满足以下先决条件：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* [Visual Studio 2019](https://www.visualstudio.com/vs/)，并包含适用于以下用途的工作负载：.NET 桌面开发、Azure 开发、ASP.NET 和 Web 开发、Node.js 开发和 Python 开发。
* 将 [EventHubsCaptureEventGridDemo 示例项目](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)下载到计算机上。

## <a name="deploy-the-infrastructure"></a>部署基础结构
在此步骤中，使用[资源管理器模板](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)部署所需的基础结构。 部署模板时，将创建以下资源：

* 已启用捕获功能的事件中心。
* 适用于已捕获文件的存储帐户。 
* 用于托管函数应用的应用服务计划
* 用于处理事件的函数应用
* 用于托管数据仓库的 SQL Server
* 用于存储迁移数据的 Azure Synapse Analytics

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>在 Azure 门户中启动 Azure Cloud Shell

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 选择顶部的“Cloud Shell”按钮。

    ![Azure 门户](media/event-grid-event-hubs-integration/azure-portal.png)
3. 会看到 Cloud Shell 在浏览器底部打开。

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. 在 Cloud Shell 中，如果看到在“Bash”和“PowerShell”之间进行选择的选项，请选择“Bash”  。 
5. 如果是第一次使用 Cloud Shell，请选择“创建存储”来创建存储帐户。 Azure Cloud Shell 需要一个 Azure 存储帐户来存储某些文件。 

    ![此屏幕截图显示了选中“创建存储”按钮的“未装载存储”对话框。](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. 等待 Cloud Shell 初始化。 

    ![为 Cloud Shell 创建存储](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>使用 Azure CLI

1. 通过运行以下 CLI 命令创建 Azure 资源组： 
    1. 将以下命令复制并粘贴到 Cloud Shell 窗口中。 如有需要，请更改资源组名称和位置。

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. 按 **Enter**。 

        以下是示例：
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. 通过运行以下 CLI 命令来部署上一部分提到的所有资源（事件中心、存储帐户、函数应用、Azure Synapse Analytics）： 
    1. 将命令复制并粘贴到 Cloud Shell 窗口中。 或者，可能需要复制/粘贴到所选的编辑器中，设置值，然后将该命令复制到 Cloud Shell。 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. 指定以下实体的值：
        1. 之前创建的资源组的名称。
        2. 事件中心命名空间的名称。 
        3. 事件中心的名称。 可以将值保留原样 (hubdatamigration)。
        4. SQL Server 的名称。
        5. SQL 用户名称和密码。 
        6. Azure Synapse Analytics 的名称
        7. 存储帐户的名称。 
        8. 函数应用的名称。 
    3.  在 Cloud Shell 窗口中按 ENTER 以运行该命令。 此过程可能需要一段时间，因为正在创建一系列资源。 在命令的结果中，请确保没有任何故障。 
    

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

1. 在 Azure Cloud Shell 中，切换到 PowerShell 模式。 选择 Azure Cloud Shell 左上角的向下键，然后选择“PowerShell”。

    ![切换到 PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. 通过运行以下命令创建 Azure 资源组： 
    1. 将以下命令复制并粘贴到 Cloud Shell 窗口中。

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. 为资源组指定名称。
    3. 按 Enter。 
3. 通过运行以下命令来部署上一部分提到的所有资源（事件中心、存储帐户、函数应用、Azure Synapse Analytics）：
    1. 将命令复制并粘贴到 Cloud Shell 窗口中。 或者，可能需要复制/粘贴到所选的编辑器中，设置值，然后将该命令复制到 Cloud Shell。 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. 指定以下实体的值：
        1. 之前创建的资源组的名称。
        2. 事件中心命名空间的名称。 
        3. 事件中心的名称。 可以将值保留原样 (hubdatamigration)。
        4. SQL Server 的名称。
        5. SQL 用户名称和密码。 
        6. Azure Synapse Analytics 的名称
        7. 存储帐户的名称。 
        8. 函数应用的名称。 
    3.  在 Cloud Shell 窗口中按 ENTER 以运行该命令。 此过程可能需要一段时间，因为正在创建一系列资源。 在命令的结果中，请确保没有任何故障。 

### <a name="close-the-cloud-shell"></a>关闭 Cloud Shell 
通过选择门户中的“Cloud Shell”按钮（或）Cloud Shell 窗口右上角的“X”按钮来关闭 Cloud Shell 。 

### <a name="verify-that-the-resources-are-created"></a>验证是否已创建资源

1. 在 Azure 门户中的左侧菜单上选择“资源组”。 
2. 通过在搜索框中输入资源组的名称来筛选资源组列表。 
3. 在列表中选择你的资源组。

    ![选择你的资源组](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 确认是否在资源组中看到以下资源：

    ![资源组中的资源](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中创建表
通过运行 [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 脚本在数据仓库中创建表。 若要运行此脚本，可以使用 Visual Studio 或门户中的查询编辑器。 以下步骤显示如何使用查询编辑器： 

1. 在资源组的资源列表中，选择“专用 SQL 池”。 
2. 在 Azure Synapse Analytics 页中，选择左侧菜单中的“查询编辑器(预览)”。 

    ![Azure Synapse Analytics 页](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. 输入 SQL Server 的“用户名”和“密码”，然后选择“确定”  。 需要将客户端 IP 地址添加到防火墙中才能成功登录 SQL Server。 

    ![SQL Server 身份验证](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. 在查询窗口中，复制并运行以下 SQL 脚本： 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![运行 SQL 查询](media/event-grid-event-hubs-integration/run-sql-query.png)
5. 保持此选项卡或窗口处于打开状态，以便可以验证在本教程结束时是否创建了数据。 

### <a name="update-the-function-runtime-version"></a>更新函数运行时版本

1. 在 Azure 门户中的左侧菜单上选择“资源组”。
2. 选择函数应用所在的资源组。 
3. 在资源组的资源列表中，选择类型为“应用服务”的函数应用。
4. 在左侧菜单中的“设置”下选择“配置” 。 
5. 切换到右窗格中的“函数运行时设置”选项卡。 
5. 将运行时版本更新为 ~3 。 

    ![更新函数运行时版本](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>发布 Azure Functions 应用

1. 启动 Visual Studio。
2. 打开作为先决条件的一部分从 [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) 下载的 EventHubsCaptureEventGridDemo.sln 解决方案。
3. 在“解决方案资源管理器”中，右键单击“FunctionEGDWDumper”，再选择“发布”。

   ![发布函数应用](media/event-grid-event-hubs-integration/publish-function-app.png)
4. 如果看到以下屏幕，请选择“启动”。 

   ![显示 Visual Studio 的屏幕截图，其中显示了“发布”部分中的“开始”按钮。](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. 在“发布”对话框中，对于“目标”，请选择“Azure”，然后选择“下一步”   。 

   ![“开始发布”按钮](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. 选择“Azure Function App (Windows)”，然后选择“下一步” 。 

   ![选择 Azure Function App - Windows](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. 在“Functions 实例”选项卡上，选择你的 Azure 订阅，展开资源组，并选择函数应用，然后选择“完成” 。 如果尚未执行该操作，则需要登录到 Azure 帐户。 

   ![选择函数应用](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. 在“服务依赖项”部分中，选择“配置” 。
9. 在“配置依赖项”页上，选择前面创建的存储帐户，然后选择“下一步” 。 
10. 保留连接字符串名称和值的设置，然后选择“下一步”。
11. 清除“机密存储”选项，然后选择“完成” 。  
8. 在 Visual Studio 配置好配置文件后，选择“发布”。

   ![选择发布](media/event-grid-event-hubs-integration/select-publish.png)

在发布函数后，已准备好订阅事件。

## <a name="subscribe-to-the-event"></a>订阅事件

1. 在 Web 浏览器的新选项卡或新窗口中，导航到 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户中的左侧菜单上选择“资源组”。 
3. 通过在搜索框中输入资源组的名称来筛选资源组列表。 
4. 在列表中选择你的资源组。

    ![选择你的资源组](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 在资源组的资源列表中，选择应用服务计划（而不是应用服务）。 
5. 在“应用服务计划”页中，选择左侧菜单中的“应用”，然后选择函数应用。 

    ![选择函数应用](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. 展开函数应用，展开函数，然后选择函数。 
7. 在工具栏上选择“添加事件网格订阅”。 

    ![选择 Azure 函数](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. 在“创建事件网格订阅”页中，请执行以下操作： 
    1. 在“事件订阅详细信息”页中，输入订阅的名称（例如：captureEventSub），然后选择“创建” 。 
    2. 在“主题详细信息”部分中，请执行以下操作：
        1. 对于“主题类型”，请选择“事件中心命名空间” 。 
        2. 选择 Azure 订阅。
        2. 选择 Azure 资源组。
        3. 选择你的事件中心命名空间。
    3. 在“事件类型”部分中，确认对“筛选事件类型”，选择了“创建的捕获文件”  。 
    4. 在“终结点详细信息”部分中，确认“终结点类型”是否设置为“Azure Function”并将“终结点”设置为 Azure 函数   。 
    
        ![创建事件网格订阅](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>运行应用以生成数据
至此，已完成设置事件中心、Azure Synapse Analytics、Azure 函数应用和事件订阅。 需要先配置几个值，然后再运行应用来生成事件中心数据。

1. 在 Azure 门户中，像之前那样导航到资源组。 
2. 选择事件中心命名空间。
3. 在“事件中心命名空间”页中的左侧菜单上选择“共享访问策略” 。
4. 在策略列表中选择 RootManageSharedAccessKey。 
5. 选择“连接字符串 - 主密钥”文本框旁边的“复制”按钮。 

    ![事件中心命名空间的连接字符串](media/event-grid-event-hubs-integration/get-connection-string.png)
1. 返回到 Visual Studio 解决方案。 
2. 在 WindTurbineDataGenerator 项目中，打开 program.cs。
5. 替换两个常数值。 使用复制的 EventHubConnectionString 值。 使用 **hubdatamigration** 作为事件中心名称。 如果为事件中心使用了其他名称，请指定该名称。 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. 生成解决方案。 运行 WindTurbineGenerator.exe 应用程序。 
7. 几分钟后，查询数据仓库中的表，获取已迁移数据。

    ![查询结果](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>事件中心生成的事件数据
事件网格将事件数据分发给订阅者。 以下示例显示了在 Blob 中捕获通过事件中心的数据流时生成的事件数据。 特别要注意 `data` 对象中的 `fileUrl` 属性指向存储中的 Blob。 函数应用使用此 URL 来检索具有捕获数据的 Blob 文件。

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>后续步骤

* 若要了解 Azure 消息传递服务之间的差异，请参阅[在提供消息的 Azure 服务之间进行选择](compare-messaging-services.md)。
* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 有关事件中心捕获的介绍，请参阅[使用 Azure 门户启用事件中心捕获](../event-hubs/event-hubs-capture-enable-through-portal.md)。
* 若要详细了解如何设置和运行示例，请参阅[事件中心捕获和事件网格示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)。
