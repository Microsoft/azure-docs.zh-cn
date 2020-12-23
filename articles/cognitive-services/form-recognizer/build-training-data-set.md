---
title: 如何为自定义模型窗体识别器生成定型数据集
titleSuffix: Azure Cognitive Services
description: 了解如何确保对定型数据集进行优化，以便为窗体识别器模型定型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 513d98ae274c668197b066c742119055248f37f9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911709"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>为自定义模型构建定型数据集

使用窗体识别器自定义模型时，您可以向 [定型自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) 操作提供您自己的定型数据，以使该模型能够定型您特定于行业的窗体。 遵循本指南了解如何收集和准备数据以有效地训练模型。

如果你是在没有手动标签的情况下定型，则可以使用五个填充窗体或空窗体 (必须在文件名中包含单词 "empty") 加上两种填充形式。 即使您具有足够的填充窗体，将空的窗体添加到定型数据集也可以提高模型的准确性。

如果要使用手动标记的定型数据，则必须从至少五个相同类型的填充窗体开始。 除了所需的数据集之外，还可以使用未标记的窗体和空窗体。

## <a name="custom-model-input-requirements"></a>自定义模型输入要求

首先，请确保定型数据集符合窗体识别器的输入要求。

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>训练数据提示

请按照以下附加提示操作，进一步优化你的数据集以进行培训。

* 如果可能，请使用基于文本的 PDF 文档而不是基于图像的文档。 扫描的 Pdf 作为图像处理。
* 对于填充窗体，请使用填充了所有字段的示例。
* 在每个字段中使用具有不同值的表格。
* 如果你的表单图像质量较低，请使用较大的数据集 (10-15 图像，例如) 。

## <a name="upload-your-training-data"></a>上传定型数据

将用于定型的窗体文档集中在一起后，需要将其上传到 Azure blob 存储容器。 如果你不知道如何使用容器创建 Azure 存储帐户，请遵循 [适用于 Azure 门户的 Azure 存储快速入门](../../storage/blobs/storage-quickstart-blobs-portal.md)。 使用标准性能层。

如果要使用手动标记的数据，则还必须上传与训练文档对应的文件上的 *.labels.js* 和 *.ocr.js* 。 您可以使用 [示例标签工具](./quickstarts/label-tool.md) (或自己的 UI) 来生成这些文件。

### <a name="organize-your-data-in-subfolders-optional"></a>在子文件夹中组织数据 (可选) 

默认情况下， [定型自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) API 将仅使用位于存储容器根目录下的窗体文档。 但是，如果在 API 调用中指定数据，则可以训练子文件夹中的数据。 通常， [定型自定义模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) 调用的正文采用以下格式，其中 `<SAS URL>` 是容器的共享访问签名 URL：

```json
{
  "source":"<SAS URL>"
}
```

如果将以下内容添加到请求正文，则 API 将针对位于子文件夹中的文档进行训练。 此 `"prefix"` 字段是可选的，它将定型数据集限制为其路径以给定字符串开头的文件。 例如，的值 `"Test"` 会导致 API 仅查看以 "Test" 一词开头的文件或文件夹。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>后续步骤

现在，您已经了解了如何构建定型数据集，接下来请学习快速入门教程来训练自定义窗体识别器模型，并在窗体上开始使用它。

* [使用客户端库为模型定型和提取窗体数据](./quickstarts/client-library.md)
* [使用曲线对模型进行定型和提取窗体数据](./quickstarts/curl-train-extract.md)
* [使用 REST API 和 Python 为模型定型和提取窗体数据](./quickstarts/python-train-extract.md)
* [使用示例标签工具训练标签](./quickstarts/label-tool.md)
* [使用 REST API 和 Python 通过标签进行训练](./quickstarts/python-labeled-data.md)

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)