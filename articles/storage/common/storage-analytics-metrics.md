---
title: Azure 存储分析指标（经典）
description: 了解如何在 Azure 存储中使用存储分析指标。 了解事务和容量指标、存储指标的方式、启用指标的方式，等等。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f1ab2be598a24a2448fed44742733633a8e0fc8f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787595"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 存储分析指标（经典）

Azure 存储使用存储分析解决方案存储一些指标，这些指标包括有关存储服务请求的聚合事务统计信息和容量数据。 事务在 API 操作级别以及存储服务级别报告。 容量在存储服务级别报告。 指标数据可用于：
- 分析存储服务使用情况。
- 通过针对存储服务发出的请求诊断问题。
- 提高使用服务的应用程序的性能。

 默认为新存储帐户启用存储分析指标。 可以在 [Azure 门户](https://portal.azure.com/)中配置指标。 有关详细信息，请参阅[在 Azure 门户中监视存储帐户](./storage-monitor-storage-account.md)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用“获取服务属性”操作为每项服务启用存储分析。  

> [!NOTE]
> 存储分析指标可用于 Azure Blob 存储、Azure 队列存储、Azure 表存储以及 Azure 文件。
> 存储分析指标现在是经典指标。 建议你使用 [Azure Monitor 中的存储指标](../blobs/monitor-blob-storage.md)，而不是存储分析指标。

## <a name="transaction-metrics"></a>事务指标  
 对于每个存储服务和请求的 API 操作，将按小时或分钟为间隔记录一组可靠的数据，其中包括入口和出口、可用性、错误和分类请求百分比。 有关事务详细信息的完整列表，请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

 事务数据在服务级别和 API 操作级别记录。 在服务级别，汇总所有请求的 API 操作的统计信息将每小时写入一次表实体，即使未向服务发出请求也是如此。 在 API 操作级别，仅当在该小时内请求操作时才将统计信息写入实体。  

 例如，如果对 Blob 服务执行 GetBlob 操作，则存储分析指标将记录请求并将其包含在 Blob 服务以及 GetBlob 操作的聚合数据中。 如果在一小时内未请求 GetBlob 操作，则不会将实体写入该操作的 $MetricsTransactionsBlob。  

 会为用户请求和存储分析本身发出的请求记录事务指标。 例如，存储分析写入日志和表实体的请求会被记录。

## <a name="capacity-metrics"></a>容量度量值  

> [!NOTE]
>  目前，容量指标仅适用于 blob 服务。

 每天记录存储帐户的 Blob 服务的容量数据，并写入两个表实体。 一个条目提供用户数据的统计信息，另一个条目提供有关存储分析所使用的 `$logs` Blob 容器的统计信息。 *$MetricsCapacityBlob* 表包含以下统计信息：  

- **容量** ：存储帐户的 Blob 服务使用的存储量（以字节为单位）。  
- **ContainerCount** ：存储帐户的 Blob 服务中的 blob 容器数。  
- **ObjectCount** ：存储帐户的 Blob 服务中已提交和未提交的块 blob 或页 blob 数量。  

  有关容量指标的详细信息，请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)。  

## <a name="how-metrics-are-stored"></a>如何存储指标  

 每个存储服务的所有指标数据都存储在为该服务保留的三个表中。 一个表存储事务信息，一个表存储分钟事务信息，还有一个表存储容量信息。 事务和分钟事务信息由请求和响应数据组成。 容量信息由存储使用情况数据组成。 存储帐户的 Blob 服务的小时指标、分钟指标和容量可在按下表所述命名的表中访问。  

|指标级别|表名|支持的版本|  
|-------------------|-----------------|----------------------------|  
|小时指标，主位置|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|仅 2013 年 8 月 15 日之前的版本。 虽然仍然支持这些名称，但还是建议改用下面列出的表。|  
|小时指标，主位置|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|所有版本。 仅在 2015 年 4 月 5 日版及更高版本中提供对文件服务指标的支持。|  
|分钟指标，主位置|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|所有版本。 仅在 2015 年 4 月 5 日版及更高版本中提供对文件服务指标的支持。|  
|小时指标，辅助位置|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|所有版本。 必须启用读访问的地域冗余复制。|  
|分钟指标，辅助位置|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|所有版本。 必须启用读访问的地域冗余复制。|  
|容量（仅限 Blob 服务）|$MetricsCapacityBlob|所有版本。|  

 为存储服务终结点启用存储分析时，会自动创建这些表。 这些指标可通过存储帐户的命名空间进行访问，例如：`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`。 指标表不会显示在列表操作中，必须直接通过表名称进行访问。

## <a name="enable-metrics-by-using-the-azure-portal"></a>使用 Azure 门户启用指标
请按照下列步骤在 [Azure 门户](https://portal.azure.com)中启用指标：

1. 转到存储帐户。
1. 在菜单窗格中选择“诊断设置（经典）”。
1. 确保“状态”设置为“打开”。
1. 选择要监视的服务指标。
1. 指定用来指示保留度量值和日志数据的时间长度的保留期策略。
1. 选择“保存”。

[Azure 门户](https://portal.azure.com)目前不允许你在存储帐户中配置分钟指标。 必须使用 PowerShell 或以编程方式启用分钟指标。

## <a name="enable-storage-metrics-by-using-powershell"></a>使用 PowerShell 启用存储指标  
可以使用本地计算机上的 PowerShell 在存储帐户中配置存储指标，具体方法是：使用 Azure PowerShell cmdlet“Get-AzStorageServiceMetricsProperty” 检索当前设置。 使用 cmdlet“Set-AzStorageServiceMetricsProperty” 更改当前设置。  

控制存储指标的 cmdlet 使用以下参数：  

* ServiceType：可能值为 Blob、Queue、Table 和 File。
* MetricsType：可能值为 Hour 和 Minute。  
* MetricsLevel：可能的值包括：
   * **无** ：禁用监视。
   * **服务** ：收集经过汇总的有关 Blob、队列、表和文件服务的入口和出口、可用性、延迟及成功百分比等指标。
   * ServiceAndApi：除服务指标外，在 Azure 存储服务 API 中为每项存储操作收集一组相同的指标。

例如，以下命令在存储帐户中打开 blob 服务的分钟指标，并将保留期设置为五天： 

> [!NOTE]
> 此命令假设你已使用 `Connect-AzAccount` 命令登录到 Azure 订阅。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* 将 `<resource-group-name>` 占位符值替换为资源组的名称。      
* 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。



以下命令在默认存储帐户中为 Blob 服务检索当前的小时度量值级别和保留天数：  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

若要了解如何配置 Azure PowerShell cmdlet 来使用 Azure 订阅并了解如何选择要使用的默认存储帐户，请参阅：[安装和配置 Azure PowerShell](/powershell/azure/)。  

## <a name="enable-storage-metrics-programmatically"></a>以编程方式启用存储指标  
除了使用 Azure 门户或 Azure PowerShell cmdlet 控制存储指标，你还可以使用 Azure 存储 API 之一。 例如，如果你使用 .NET 语言，则可以使用 Azure 存储客户端库。  

CloudBlobClient、CloudQueueClient、CloudTableClient 和 CloudFileClient 类都使用 SetServiceProperties 和 SetServicePropertiesAsync 等方法，这些方法采用 ServiceProperties 对象作为参数。 你可以使用 ServiceProperties 对象配置存储指标。 例如，下面的 C# 代码片段说明如何更改小时队列指标的指标级别和保留天数：  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

有关使用 .NET 语言配置存储指标的详细信息，请参阅[用于 .NET 的 Azure 存储客户端库](/dotnet/api/overview/azure/storage)。  

有关使用 REST API 配置存储指标的一般信息，请参阅[启用和配置存储分析](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)。  

##  <a name="view-storage-metrics"></a>查看存储指标  
在将存储分析指标配置为监视存储帐户后，存储分析将使用存储帐户在一组已知表中记录指标。 可以将图表配置为每小时查看 [Azure 门户](https://portal.azure.com)中的指标：

1. 在 [Azure 门户](https://portal.azure.com)中转到自己的存储帐户。
1. 在要查看其指标的服务的“菜单”窗格中，选择“指标（经典）”。
1. 选择要配置的图表。
1. 在“编辑图表”窗格中，选择“时间范围”、“图表类型”，以及想要在图表中显示的指标。

在 Azure 门户的存储帐户菜单窗格的“监视（经典）”部分中，你可以配置[警报规则](#metrics-alerts)。 例如，你可以发送电子邮件警报，以在特定指标达到某值时通知你。

如果要为长期存储下载指标或在本地分析这些指标，则需要使用工具或编写一些代码来读取表。 必须下载分析用的分钟指标。 如果在存储帐户中列出所有表，则这些表不会显示，但可以按名称直接访问。 很多存储浏览工具可识别这些表，并可用于直接查看这些表。 有关可用工具的列表，请参阅 [Azure 存储客户端工具](./storage-explorers.md)。

|指标|表名|备注| 
|-|-|-|  
|小时指标|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|在 2013 年 8 月 15 日之前的版本中，这些表称为：<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> 从 2015 年 4 月 5 日的版本开始，可以使用文件服务指标。|  
|分钟度量值|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|只能通过 PowerShell 或以编程方式启用。<br /><br /> 从 2015 年 4 月 5 日的版本开始，可以使用文件服务指标。|  
|容量|$MetricsCapacityBlob|仅限 Blob 服务。|  

有关这些表的完整架构详细信息，请参阅[存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)。 以下示例行仅显示一部分可用列，但也说明了存储指标在采用相应方式保存这些指标时展现的一些重要功能：  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|可用性|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

在这个分钟指标数据示例中，分区键按分钟使用时间。 行键用于标识行中所存储信息的类型。 行键由访问类型和请求类型组成：  

-   访问类型是 user 或 system，其中 user 是指用户对存储服务发出的所有请求，而 system 是指存储分析发出的请求。  
-   请求类型是 all（在这种情况下是摘要行）或可识别特定 API，如 QueryEntity 或 UpdateEntity。  

此示例数据显示一分钟的所有记录（从上午 11:00 开始），因此，QueryEntities 请求数加 QueryEntity 请求数再加 UpdateEntity 请求数的和为 7。 这是显示在 user:All 行上的总数。 同样，通过计算 ((143.8 * 5) + 3 + 9)/7，可以在 user:All 行得到平均端到端延迟为 104.4286。  

## <a name="metrics-alerts"></a>度量警报
考虑在 [Azure 门户](https://portal.azure.com)中设置警报，以在存储服务行为发生重要更改时自动通知你。 如果使用存储资源管理器工具下载这种采用分隔格式的指标数据，则可以使用 Microsoft Excel 分析数据。 有关可用存储资源管理器工具的列表，请参阅 [Azure 存储客户端工具](./storage-explorers.md)。 可以在“警报（经典）”窗格中（可在存储帐户菜单窗格中的“监视（经典）”下进行访问）配置警报。

> [!IMPORTANT]
> 在存储事件与记录对应每小时或分钟指标数据的时间之间可能存在延迟。 对于分钟指标，可能会一次写入几分钟的数据。 此问题可能会导致将前面几分钟的事务聚合到当前分钟的事务中。 发生此问题时，警报服务可能没有已配置警报间隔内的所有可用指标数据，这可能会导致意外触发警报。
>

## <a name="access-metrics-data-programmatically"></a>以编程方式访问指标数据  
以下列表显示示例 C# 代码，该代码用于访问分钟范围的分钟指标，并在控制台窗口中显示结果。 代码示例使用 Azure 存储客户端库版本 4.x 或更高版本，其中包括 CloudAnalyticsClient 类，用于简化访问存储中的指标表的过程。 

> [!NOTE]
> **CloudAnalyticsClient** 类不包括在适用于 .Net 的 Azure Blob 存储客户端库 v12 中。 **2023 年8月 31** 日存储分析度量值，也称为 *经典指标* 。 有关详细信息，请查看[官方公告](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/)。 如果使用经典指标，建议你在该日期之前过渡到 Azure Monitor 中的指标。 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>对存储指标计费
为度量值创建表实体的写入请求，按适用于所有 Azure 存储操作的标准费率收费。  

客户端针对指标数据的读取和删除请求也按标准费率收费。 如果已配置数据保留策略，则当 Azure 存储删除旧的指标数据时，不用付费。 但是，如果删除分析数据，则会针对删除操作向帐户收费。  

指标表使用的容量也要付费。 使用以下信息估算用于存储指标数据的容量：  

-   如果某个服务每小时都会利用每个服务的每个 API，则在启用服务级别和 API 级别摘要的情况下，每小时约有 148KB 的数据存储在指标事务表中。  
-   如果某个服务每小时都会利用服务的每个 API，则在仅启用服务级别摘要的情况下，每小时约有 12KB 的数据存储在指标事务表中。  
-   Blob 的容量表每天添加两行（如果你已为日志选择加入）。 这表示，此表的大小每天最多以约 300 字节的幅度增加。

## <a name="next-steps"></a>后续步骤
* [监视存储帐户](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [存储分析指标表架构](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [存储分析记录的操作和状态消息](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [存储分析日志记录](storage-analytics-logging.md)