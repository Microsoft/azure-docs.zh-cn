---
title: 包含多个文档的索引 blob
titleSuffix: Azure Cognitive Search
description: 使用 Azure 认知搜索 Blob 索引器抓取 Azure Blob 以获取文本内容，该索引器中的每个 blob 可能会生成一个或多个搜索索引文档。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430974"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>为可以生成多个搜索文档的 Blob 编制索引

默认情况下，Blob 索引器将一个 Blob 的内容视为单个搜索文档。 如果要在搜索索引中对 blob 进行更精细的表示，则可以将 parsingMode 值设置为从一个 blob 创建多个搜索文档。 导致许多搜索文档的 parsingMode 值包括 `delimitedText`（对于 [CSV](search-howto-index-csv-blobs.md)）和 `jsonArray` 或 `jsonLines`（对于 [JSON](search-howto-index-json-blobs.md)）。

当你使用这些分析模式中的任何一种时，出现的新搜索文档必须具有唯一的文档键，并在确定值的来源时出现问题。 父 blob 至少具有一个采用 `metadata_storage_path property` 形式的唯一值，但如果它将该值分配给多个搜索文档，则该键在索引中不再唯一。

若要解决此问题，blob 索引器将生成一个 `AzureSearch_DocumentKey`，用于唯一标识从单个 blob 父项创建的每个子搜索文档。 本文介绍此功能的工作原理。

## <a name="one-to-many-document-key"></a>一对多文档键

Azure 认知搜索索引中显示的每个文档由一个文档键唯一标识。 

如果未指定分析模式，并且搜索文档键的索引器定义中没有[显式字段映射](search-indexer-field-mappings.md)，blob 索引器会自动将 `metadata_storage_path property` 映射为文档键。 此映射可确保每个 blob 都显示为一个不同的搜索文档，并省去自行创建此字段映射的步骤（正常情况下，只会自动映射具有相同名称和类型的字段）。

使用上面所列的任一分析模式时，一个 Blob 将映射到“多个”搜索文档，因此，一个文档键仅基于 Blob 元数据是不适当的。 为了克服这种约束，Azure 认知搜索能够为从 Blob 提取的每个单个实体生成“一对多”的文档键。 此属性名为 AzureSearch_DocumentKey，将添加到从 Blob 提取的每个实体。 系统保证此属性的值对于各 Blob 中的每个实体唯一，而实体将显示为独立的搜索文档。 

默认情况下，如果未指定键索引字段的显式字段映射，系统会使用 `base64Encode` 字段映射函数将 `AzureSearch_DocumentKey` 映射到该字段。

## <a name="example"></a>示例

假设某个索引定义包含以下字段：

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob 容器包含采用以下结构的 Blob：

_Blob1.json_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.json_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

创建索引器并将 parsingMode 设置为 `jsonLines`（未指定键字段的任何显式字段映射）时，将隐式应用以下映射。

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

此设置将导致消除文档键的歧义，类似于下图（为简便起见缩短了 base64 编码的 ID）。

| ID | 温度 | 压力 | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ...YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引键字段的自定义字段映射

假设索引定义与前面的示例相同，并且 Blob 容器包含采用以下结构的 Blob：

_Blob1.json_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.json_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

使用 `delimitedText` **parsingMode** 创建索引器时，可能会自然而然地将字段映射函数设置为如下所示的键字段：

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

但是，此映射不会生成索引中显示的 4 个文档，因为 `recordid` 字段在各 Blob 中不是唯一的。   因此，我们建议对“一对多”分析模式，应用从 `AzureSearch_DocumentKey` 属性到键索引字段的隐式字段映射。

如果确实想要设置显式字段映射，请确保 _sourceField_ 对于 **所有 Blob** 中的每个实体都是不同的。

> [!NOTE]
> `AzureSearch_DocumentKey` 用来确保每个提取实体的唯一性的方法可能会发生变化，因此你不应该依赖于使用其值来解决应用程序的需求。

## <a name="next-steps"></a>后续步骤

如果尚未熟悉 blob 索引编制的基本结构和工作流，则应先[使用 Azure 认知搜索为 Azure Blob 存储编制索引](search-howto-index-json-blobs.md)。 请查看以下文章，详细了解不同 blob 内容类型的分析模式。

> [!div class="nextstepaction"]
> [为 CSV blob 编制索引](search-howto-index-csv-blobs.md)
> [为 JSON blob 编制索引](search-howto-index-json-blobs.md)
