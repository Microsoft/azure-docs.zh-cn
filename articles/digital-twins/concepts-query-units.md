---
title: Azure 数字孪生中的查询单位
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生中查询单元的计费概念
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 33c572719d76a2add39aec37329679113fcddb76
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146324"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure 数字孪生中的查询单位 

Azure 数字孪生 **Query unit (QU)** 是一个按需计算单元，用于使用 [查询 API](/rest/api/digital-twins/dataplane/query)执行 [Azure 数字孪生查询](how-to-query-graph.md)。 

它提取了执行 Azure 数字孪生所支持的查询操作所需的系统资源（如 CPU、IOPS 和内存），使您可以改为跟踪查询单位的使用情况。

用于执行查询的查询单位量受 .。。

* 查询的复杂性
* 结果集的大小 (因此，返回10个结果的查询将消耗更多的 QUs，而不是仅返回一个结果的类似复杂度的查询) 

本文介绍如何了解查询单位并跟踪查询单位消耗量。

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>查找 Azure 数字孪生中的查询单位消耗量

使用 Azure 数字孪生 [查询 API](/rest/api/digital-twins/dataplane/query)运行查询时，可以检查 response 标头以跟踪查询使用的 QUs 数。 在从 Azure 数字孪生发送回的响应中查找 "查询费用"。

Azure 数字孪生 [sdk](how-to-use-apis-sdks.md) 允许从可分页的响应中提取查询费用标头。 本部分介绍如何查询数字孪生，以及如何循环访问可分页响应来提取查询费用标头。 

下面的代码段演示了如何提取调用查询 API 时产生的查询费用。 它首先循环访问响应页面以访问查询费用标头，然后循环访问每个页面中的数字硬输出结果。 

```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins");
int pageNum = 0;

// The "await" keyword here is required, as a call is made when fetching a new page.

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages())
{
    Console.WriteLine($"Page {++pageNum} results:");

    // Extract the query-charge header from the page

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge))
    {
        Console.WriteLine($"Query charge was: {queryCharge}");
    }

    // Iterate over the twin instances.

    // The "await" keyword is not required here, as the paged response is local.

    foreach (string response in page.Values)
    {
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response);
        Console.WriteLine($"Found digital twin '{twin.Id}'");
    }
}
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何查询 Azure 数字孪生，请访问：

* [*概念：查询语言*](concepts-query-language.md)
* [*操作方法：查询双子图形*](how-to-query-graph.md)
* [查询 API 参考文档](/rest/api/digital-twins/dataplane/query/querytwins)

可在引用中找到 Azure 数字孪生与查询相关的限制 [*：服务限制*](reference-service-limits.md)。