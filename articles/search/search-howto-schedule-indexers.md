---
title: 计划索引器执行
titleSuffix: Azure Cognitive Search
description: 计划 Azure 认知搜索索引器，以定期或在特定时间为内容编制索引。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 80c3f9aa02680097276f966ce6aea02acf1e40fb
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358790"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>如何计划 Azure 认知搜索中的索引器

通常，在创建索引器后，该索引器会紧接着运行一次。 可以使用门户、REST API 或 .NET SDK 按需再次运行该索引器。 还可以将索引器配置为按计划定期运行。

在某些情况下，计划索引器会很有作用：

* 源数据随时更改，你希望 Azure 认知搜索索引器自动处理更改的数据。
* 索引从多个数据源填充，你想要确保索引器在不同的时间运行，以减少冲突。
* 源数据极大，你想要将索引器的处理负载分散到不同的时间。 有关对大量数据编制索引的详细信息，请参阅[如何在 Azure 认知搜索中为大型数据集编制索引](search-howto-large-index.md)。

计划程序是 Azure 认知搜索的内置功能。 无法使用外部计划程序来控制搜索索引器。

## <a name="define-schedule-properties"></a>定义计划属性

索引器计划有两个属性：
* **间隔** ：定义按计划执行索引器的间隔时间。 允许的最小间隔为 5 分钟，最大间隔为 24 小时。
* **开始时间 (UTC)** ：指示首次运行索引器的时间。

可以在首次创建索引器时指定计划，以后也可以通过更新索引器的属性来指定计划。 可以使用[门户](#portal)、[REST API](#restApi) 或 [.NET SDK](#dotNetSdk) 设置索引器计划。

一次只能运行一个索引器执行。 如果在计划索引器的下一次执行时该索引器已运行，该执行将推迟到下一个计划时间。

让我们考虑更具体的示例。 假设我们要使用每小时 **间隔** 和 **开始时间** 2019 年 6 月 1 日上午 8:00 (UTC) 来配置索引器计划。 下面是索引器运行时间超过一小时时可能出现的情况：

* 第一次索引器执行的开始时间为 2019 年 6 月 1 日上午 8:00 (UTC) 或近似时间。 假设此执行需要 20 分钟（或小于 1 小时的任何时间）。
* 第二次索引器执行的开始时间为 2019 年 6 月 1 日上午 9:00 (UTC) 或近似时间。 假设此执行耗时 70 分钟（超过 1 小时），并且在上午 10:10 (UTC) 之前无法完成。
* 第三次执行的计划开始时间为上午 10:00 (UTC)，但此时上一次执行仍在运行。 那么，将会跳过此计划的执行。 索引器的下一次执行在上午 11:00 (UTC) 之前不会开始。

> [!NOTE]
> 如果将索引器设置为某个计划，但每次运行时一次又一次地在同一文档上反复失败，则索引器将以不那么频繁的时间间隔开始运行（最多每 24 小时至少一次），直到它成功地再次取得进展。  如果你认为你已修复了导致索引器在某一点停滞的任何问题，则可以按需运行索引器，如果成功取得进展，索引器将再次回到其设置的计划间隔。

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>在门户中计划

在创建时，可以使用门户中的“导入数据”向导来定义索引器的计划。 默认的“计划”设置为“小时”，即，索引器在创建后将运行一次，然后每隔一小时再次运行。 

如果你不希望索引器再次自动运行，可将“计划”设置更改为“一次”；或者更改为“每日”以每日运行一次。   若要指定不同的间隔或特定的将来开始时间，请将其设置为“自定义”。 

将计划设置为“自定义”时，会显示相应的字段让你指定“间隔”和“开始时间(UTC)”。    允许的最短时间间隔为 5 分钟，最长为 1440 分钟（24 小时）。

   ![在导入数据向导中设置索引器计划](media/search-howto-schedule-indexers/schedule-import-data.png "在导入数据向导中设置索引器计划")

创建索引器后，可以使用索引器的“编辑”面板更改计划设置。 “计划”字段与“导入数据”向导中的字段相同。

   ![在索引器的“编辑”面板中设置计划](media/search-howto-schedule-indexers/schedule-edit.png "在索引器的“编辑”面板中设置计划")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>使用 REST API 进行计划

可以使用 REST API 定义索引器的计划。 为此，请在创建或更新索引器时包含 **schedule** 属性。 以下示例演示了用于更新现有索引器的 PUT 请求：

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

**间隔** 参数是必需的。 间隔是指开始两个连续的索引器执行之间的时间。 允许的最小间隔为 5 分钟；最长为一天。 必须将其格式化为 XSD“dayTimeDuration”值（[ISO 8601 持续时间](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)值的受限子集）。 它的模式为：`P(nD)(T(nH)(nM))`。 示例：`PT15M` 为每隔 15 分钟，`PT2H` 为每隔 2 小时。

可选的 **startTime** 指示计划的执行何时开始。 如果省略，则使用当前 UTC 时间。 此时间可以是过去的时间，在此情况下，计划的第一次执行的运行方式如同索引器在原始 **startTime** 之后连续运行。

还可以使用“运行索引器”调用随时按需运行索引器。 有关运行索引器和设置索引器计划的详细信息，请参阅“REST API 参考”中的[运行索引器](/rest/api/searchservice/run-indexer)、[获取索引器](/rest/api/searchservice/get-indexer)和[更新索引器](/rest/api/searchservice/update-indexer)。

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>使用 .NET SDK 进行计划

可以使用 Azure 认知搜索 .NET SDK 定义索引器的计划。 为此，请在创建或更新索引器时包括 **Schedule** 属性。

下面的 c # 示例使用预定义的数据源和索引创建一个 Azure SQL 数据库索引器，并将其计划设置为每天运行一次：

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```


如果省略 " **Schedule** " 属性，则索引器在创建后将立即立即运行一次。

**StartTime** 参数可以设置为过去的某个时间。 在这种情况下，将计划第一次执行，就像索引器在给定 **StartTime** 后连续运行一样。

计划是使用 [IndexingSchedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) 类定义的。 **IndexingSchedule** 构造函数需要使用 **TimeSpan** 对象指定的 **间隔** 参数。 允许的最小间隔值为 5 分钟，最大间隔值为 24 小时。 指定为 **DateTimeOffset** 对象的第二个 **StartTime** 参数是可选的。

.NET SDK 允许使用 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)控制索引器操作。 

可以使用 [RunIndexer](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) 或 [RunIndexerAsync](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexerasync) 方法之一随时按需运行索引器。

有关创建、更新和运行索引器的详细信息，请参阅 [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)。