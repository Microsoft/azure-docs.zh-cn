---
title: 快速入门：使用 REST API 和 Python 通过标签进行训练 - 表单识别器
titleSuffix: Azure Cognitive Services
description: 了解如何将表单识别器标记的数据特征与 REST API 和 Python 配合使用，以便训练自定义模型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: f944a793d721e93d818723eae25a9ce80d9c15bc
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "96005073"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>使用 REST API 和 Python 通过标签训练表单识别器模型

在本快速入门中，我们将使用表单识别器 REST API 和 Python 通过手动标记的数据来训练自定义模型。 有关此功能的详细信息，请参阅概述的[使用标签进行训练](../overview.md#train-with-labels)部分。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，必须具备以下条件：
- 安装 [Python](https://www.python.org/downloads/)（若要在本地运行此示例）。
- 至少有六个相同类型的表单。 你将使用此数据训练模型并测试表单。 在本快速入门中可以使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)。 下载并提取 sample_data.zip。 将训练文件上传到标准性能层 Azure 存储帐户中 blob 存储容器的根目录。

> [!NOTE]
> 此快速入门使用 URL 访问的远程文档。 若要改用本地文件，请参阅[适用于 v2.0 的参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)和[适用于 v2.1 的参考文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/TrainCustomModelAsync)。

## <a name="create-a-form-recognizer-resource"></a>创建表单识别器资源

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>设置训练数据

接下来，需要设置所需的输入数据。 标记的数据特征有特殊的输入要求（而不需要使用标签训练自定义模型所需的输入要求除外）。

确保所有训练文档的格式相同。 如果有多种格式的表单，请根据通用格式将它们组织到子文件夹中。 训练时，需要将 API 定向到子文件夹。

若要使用标记的数据训练模型，需要在子文件夹中将以下文件用作输入。 你将了解如何创建下面这些文件。

* **源表单** – 用于从其中提取数据的表单。 支持的类型为 JPEG、PNG、PDF 或 TIFF。
* **OCR 布局文件** - 这是 JSON 文件，描述每个源表单中所有可读文本的大小和位置。 我们将使用表单识别器布局 API 来生成此数据。 
* **标签文件** - 这些是 JSON 文件，用于描述用户已手动输入的数据标签。

所有这些文件都应占用同一子文件夹，并采用以下格式：

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> 使用表单识别器[示例标记工具](./label-tool.md)来标记表单时，该工具会自动创建这些标签和 OCR 布局文件。

### <a name="create-the-ocr-output-files"></a>创建 OCR 输出文件

需要 OCR 结果文件，这样服务才会考虑使用相应的输入文件进行标记的训练。 若要获取给定源表单的 OCR 结果，请执行以下步骤：

1. 在读取的布局容器上调用 **[分析布局](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** API，使用输入文件作为请求正文的一部分。 保存在响应的 **Operation-Location** 标头中找到的 ID。
1. 调用 **[获取分析布局结果](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** API，使用上一步中的操作 ID。
1. 获取响应并将内容写入文件。 就每个源表单来说，应该在相应的 OCR 文件的原始文件名中追加 `.ocr.json`。 OCR JSON 输出应采用以下格式。 如需完整示例，请参阅[示例 OCR 文件](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json)。 

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    
    # <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)
    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```    


    ---



### <a name="create-the-label-files"></a>创建标签文件

标签文件包含用户已手动输入的键-值关联。 它们是进行标记数据训练所需的，但并不是每个源文件都需要有相应的标签文件。 没有标签的源文件会被视为普通训练文档。 建议使用至少五个标记的文件进行可靠的训练。 可以使用 UI 工具（如[示例标记工具](./label-tool.md)）生成这些文件。

创建标签文件时，可以选择指定区域&mdash;文档中值的确切位置。 这样甚至会提高训练的准确性。 区域的格式是由八个值组成的一组，对应于四个 X,Y 坐标：左上、右上、右下和左下。 坐标值在零和一之间，按页面尺寸进行缩放。

就每个源表单来说，应该在相应的标签文件的原始文件名中追加 `.labels.json`。 标签文件应采用以下格式。 如需完整示例，请参阅[示例标签文件](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json)。

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!IMPORTANT]
> 只能对每个文本元素应用一个标签，并且每个标签在每个页面只能应用一次。 无法跨多个页面应用标签。


## <a name="train-a-model-using-labeled-data"></a>使用标记的数据训练模型

若要使用标记的数据来训练模型，请运行下面的 python 代码来调用 **[训练自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** API。 运行该代码之前，请进行以下更改：

1. 将 `<Endpoint>` 替换为表单识别器资源的终结点 URL。
1. 将 `<SAS URL>` 替换为 Azure Blob 存储容器的共享访问签名 (SAS) URL。 若要检索 SAS URL，请打开 Microsoft Azure 存储资源管理器，右键单击容器，然后选择“获取共享访问签名”  。 确保选中“读取”和“列表”权限，然后单击“创建”。 然后复制 **URL** 部分中的值。 它应当采用 `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 形式。
1. 将 `<Blob folder name>` 替换为输入数据所在的 blob 容器中的文件夹名称。 或者，如果数据位于根目录，请将此项留空，并从 HTTP 请求的正文中删除 `"prefix"` 字段。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```    
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)    
```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.1-preview.2/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```   

---



## <a name="get-training-results"></a>获取训练结果

开始训练操作后，请使用返回的 ID 获取操作状态。 将以下代码添加到 Python 脚本的底部。 这样就可以在新的 API 调用中使用来自训练调用的 ID 值。 该训练操作是异步的，因此此脚本会定期调用 API，直到训练状态为“完成”为止。 我们建议调用间隔为一秒或更长时间。

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

当训练过程完成以后，你会收到 `201 (Success)` 响应，其中的 JSON 内容如下所示。 为了简单起见，响应已缩短。

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

复制 `"modelId"` 值，以便在以下步骤中使用它。

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

当过程完成以后，你会收到 `202 (Success)` 响应，其中的 JSON 内容采用以下格式。 为了简单起见，响应已缩短。 主键/值关联位于 `"documentResults"` 节点中。 `"selectionMarks"` 节点（在 v2.1 预览版中）显示每个选择标记（复选框、单选框），并显示其状态是“已选中”还是“未选中”。 布局 API 结果（文档中所有文本的内容和位置）位于 `"readResults"` 节点中。

# <a name="v20"></a>[v2.0](#tab/v2-0)
```json
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:16:28Z",
  "lastUpdatedDateTime": "2020-08-21T02:16:35Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
            ]
          }
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/14/words/0",
                  "#/readResults/0/lines/14/words/1"
                ]
              },
              ...
            ]
          },
        ]
      }
    ],
    "documentResults": [
      {
        "docType": "custom:form",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Receipt No": {
            "type": "string",
            "valueString": "9876",
            "text": "9876",
            "page": 1,
            "boundingBox": [
              7.615,
              1.245,
              7.915,
              1.245,
              7.915,
              1.35,
              7.615,
              1.35
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/3/words/3"
            ]
          },
          ...
        }
      }
    ],
    "errors": []
  }
}
```
# <a name="v-2"></a>[v 2](#tab/v2-1) 
```json   
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T02:29:42Z",
  "lastUpdatedDateTime": "2020-08-21T02:29:50Z",
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
            "boundingBox": [
              0.5826,
              0.4411,
              2.3387,
              0.4411,
              2.3387,
              0.7969,
              0.5826,
              0.7969
            ],
            "text": "Contoso, Ltd.",
            "words": [
              {
                "boundingBox": [
                  0.5826,
                  0.4411,
                  1.744,
                  0.4411,
                  1.744,
                  0.7969,
                  0.5826,
                  0.7969
                ],
                "text": "Contoso,",
                "confidence": 1
              },
              {
                "boundingBox": [
                  1.8448,
                  0.4446,
                  2.3387,
                  0.4446,
                  2.3387,
                  0.7631,
                  1.8448,
                  0.7631
                ],
                "text": "Ltd.",
                "confidence": 1
              }
            ]
          },
          ...
        ], 
        "selectionMarks": [
          {
            "boundingBox": [
              3.9737,
              3.7475,
              4.1693,
              3.7475,
              4.1693,
              3.9428,
              3.9737,
              3.9428
            ],
            ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "rowIndex": 0,
                "columnIndex": 0,
                "text": "Training Date",
                "boundingBox": [
                  0.5133,
                  4.2167,
                  1.7567,
                  4.2167,
                  1.7567,
                  4.4492,
                  0.5133,
                  4.4492
                ],
                "elements": [
                  "#/readResults/0/lines/12/words/0",
                  "#/readResults/0/lines/12/words/1"
                ]
              },
              ...
            ]
          }
        ] 
      }
    ], 
    "documentResults": [
      {
        "docType": "custom:e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "modelId": "e1073364-4f3d-4797-8cc4-4bdbcd0dab6b",
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "ID #": {
            "type": "string",
            "valueString": "5554443",
            "text": "5554443",
            "page": 1,
            "boundingBox": [
              2.315,
              2.43,
              2.74,
              2.43,
              2.74,
              2.515,
              2.315,
              2.515
            ],
            "confidence": 1,
            "elements": [
              "#/readResults/0/lines/8/words/1"
            ]
          },
          ...
        },
        "docTypeConfidence": 1
      }
    ],
    "errors": []
  }
}
```

---


## <a name="improve-results"></a>改进结果

检查 `"documentResults"` 节点下每个键/值结果的 `"confidence"` 值。 还应查看 `"readResults"` 节点中的置信度分数，这些分数对应于布局操作。 布局结果的置信度不影响键/值提取结果的置信度，因此二者都应检查。
* 如果布局操作的置信度分数低，请尝试提高输入文档的质量（请参阅[输入要求](../overview.md#input-requirements)）。
* 如果键/值提取操作的置信度分数低，请确保所分析的文档与训练集中使用的文档是相同的类型。 如果训练集中的文档在外观上有差异，请考虑将它们拆分成不同的文件夹，针对每项差异训练一个模型。

### <a name="avoid-cluttered-labels"></a>避免使用混乱的标签

有时候，当你在同一文本行中应用不同的标签时，服务会将这些标签合并成一个字段。 例如，在地址中，你可能会将城市、州和邮政编码标记为不同的字段，但在预测过程中，系统不会单独识别这些字段。

我们知道这种情况在客户中很常见，因此会力争在将来进行改进。 目前，我们建议用户将多个混乱的字段标记为一个字段，然后在对提取结果进行后期处理时将相关词条分开。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何使用表单识别器 REST API 和 Python 通过手动标记的数据来训练模型。 接下来，请参阅 API 参考文档来深入了解表单识别器 API。

> [!div class="nextstepaction"]
> [REST API 参考文档](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
