---
title: 快速入门：使用 cURL 训练模型并提取表单数据 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用表单识别器 REST API 和 cURL 来训练模型，并从表单中提取数据。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: 0738e06fbd26526ed78991d5db18e7f8c5c58ea7
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505335"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>快速入门：使用 REST API 和 cURL 训练表单识别器模型并提取表单数据

在本快速入门中，我们将使用 Azure 表单识别器的 REST API 和 cURL 来训练表单并为其评分，以提取键值对和表。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，必须具备以下条件：
- 已安装 [cURL](https://curl.haxx.se/windows/)。
- 至少有六个相同类型的表单。 你将使用其中五个表单训练模型，然后使用第六个表单对模型进行测试。 表单可以是不同的文件类型，但必须是相同的文档类型。 对于此快速入门，可使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)（下载并提取 sample_data.zip）。 将训练文件上传到标准性能层 Azure 存储帐户中 blob 存储容器的根目录。 可以将测试文件放在单独的文件夹中。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>训练表单识别器模型

首先，你需要 Azure 存储 blob 中的一组训练数据。 应至少有五个类型/结构与主要输入数据相同的已填写表单（PDF 文档和/或图像）。 或者，可以使用一个空表单和两个已填充表单。 空表单的文件名需要包括单词“empty”。 有关整理训练数据的提示和选项，请参阅[为自定义模型生成训练数据集](../build-training-data-set.md)。

> [!NOTE]
> 可以使用标记数据功能来手动预先标记部分或全部训练数据。 这是一个更为复杂的过程，但会生成更好的经过训练的模型。 有关此功能的详细信息，请参阅概述的[通过标签进行训练](../overview.md#train-with-labels)部分。



若要使用 Azure Blob 容器中的文档训练表单识别器模型，请运行下面的 cURL 命令来调用[训练自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) API  。 运行该命令之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅中获取的终结点。
1. 将 `<subscription key>` 替换为从上一步复制的订阅密钥。
1. 将 `<SAS URL>` 替换为 Azure Blob 存储容器的共享访问签名 (SAS) URL。 [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]


    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



你将收到包含“Location”标头的 `201 (Success)` 响应  。 此标头的值是要训练的新模型的 ID。

## <a name="get-training-results"></a>获取训练结果

开始训练操作后，可以使用新操作[获取自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)检查训练状态  。 将模型 ID 传递到此 API 调用以检查训练状态：

1. 将 `<Endpoint>` 替换为从表单识别器订阅密钥中获得的终结点。
1. 将 `<subscription key>` 替换为订阅密钥
1. 将 `<model ID>` 替换为在上一步骤收到的模型 ID



# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.2/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
```
    
---

你将收到以下格式的 JSON 正文的 `200 (Success)` 响应。 请注意 `"status"` 字段。 完成训练后，将出现值 `"ready"`。 如果模型没有完成训练，则需要重新运行该命令以再次查询该服务。 我们建议两次调用间隔一秒或更长时间。

`"modelId"` 字段包含要训练的模型的 ID。 下一步骤需要用到此字段。

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>分析键值对和表的表单

接下来，使用新的经过训练的模型分析文档并从中提取键值对和表。 运行以下 cURL 命令来调用[分析表单](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API  。 运行该命令之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为从表单识别器订阅密钥中获取的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。 
1. 将 `<model ID>` 替换为在上一部分收到的模型 ID。
1. 将 `<SAS URL>` 替换为指向 Azure 存储中文件的 SAS URL。 按照“训练”部分中的步骤操作，但不是获取整个 blob 容器的 SAS URL，而是获取要分析的特定文件的 URL。
1. 将 `<subscription key>` 替换为订阅密钥。

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.2/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



你将收到 `202 (Success)` 响应，其中包含“Operation-Location”标头  。 此标头的值包括用于跟踪“分析”操作结果的结果 ID。 保存此结果 ID 以供下一步使用。

## <a name="get-the-analyze-results"></a>获取分析结果

使用以下 API 查询“分析”操作的结果。

1. 将 `<Endpoint>` 替换为从表单识别器订阅密钥中获取的终结点。 可以在表单识别器资源的“概览”选项卡中找到该终结点。 
1. 将 `<result ID>` 替换为上一部分中收到的 ID。
1. 将 `<subscription key>` 替换为订阅密钥。


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
    
---

你将收到以下格式的 JSON 正文的 `200 (Success)` 响应。 为了简单起见，已缩短输出。 请注意底部附近的 `"status"` 字段。 完成“分析”操作时，会出现 `"succeeded"` 值。 如果“分析”操作尚未完成，则需要重新运行该命令以再次查询该服务。 我们建议两次调用间隔一秒或更长时间。

主键/值对关联和表位于 `"pageResults"` 节点中。 如果还通过 includeTextDetails URL 参数指定了纯文本提取，则 `"readResults"` 节点将显示文档中所有文本的内容和位置  。

为了简单起见，已缩短此示例 JSON 输出。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 

---


## <a name="improve-results"></a>改进结果

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们已使用表单识别器 REST API 和 cURL 训练了一个模型，并在示例案例中运行了该模型。 接下来，请参阅参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
