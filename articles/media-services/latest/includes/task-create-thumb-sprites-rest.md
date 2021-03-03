---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 2/17/2020
ms.author: inhenkel
ms.custom: REST
ms.openlocfilehash: d34cfd31f9648ce6f401c3936a0a28be4729f8e9
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101705415"
---
<!--Create a thumbnail sprites REST-->

以下 Azure REST 命令创建了一个缩略动画动画转换。 将 `subscriptionID`、`resourceGroup` 和 `accountName` 值替换为当前正在使用的值。 

通过设置 `transformName` 为转换命名。

```REST

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version={{api-version}}

```

## <a name="body"></a>正文

```json
{
    "properties": {
    "description": "Basic Transform using the Standard Encoder, JpgImage, JpgLayer, and JpgFormat ",
    "outputs": [{
        "onError": "StopProcessingJob",
        "relativePriority": "Normal",
        "preset": {
            "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
            "Codecs": [{
                "@odata.type": "#Microsoft.Media.JpgImage",
                "start": "1",
                "step": "5%",
                "range": "100%",
                "spriteColumn": 10,
                "layers": [{
                    "@odata.type" : "#Microsoft.Media.JpgLayer",
                    "width": "20%",
                    "height": "20%",
                    "quality" : 90
                }]
                }],
            "formats":[{
                "@odata.type" : "#Microsoft.Media.JpgFormat",
                "filenamePattern":"ThumbnailSprite-{Basename}-{Index}{Extension}"
                }]
            }
        }]
    }
}
```

## <a name="sample-response"></a>示例响应
```json
{
    "name": "thumbnailsprites",
    "id": "/subscriptions/<your subscription ID>/resourceGroups/<your resource group name/providers/Microsoft.Media/mediaservices/<your account name>/transforms/thumbnailsprites",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2021-02-17T21:56:06.6213416Z",
        "description": "Basic Transform using the Standard Encoder, JpgImage, JpgLayer, and JpgFormat ",
        "lastModified": "2021-02-17T21:57:17.3241984Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.JpgImage",
                            "stretchMode": "AutoSize",
                            "syncMode": "Auto",
                            "start": "1",
                            "step": "5%",
                            "range": "100%",
                            "layers": [
                                {
                                    "width": "20%",
                                    "height": "20%",
                                    "quality": 90
                                }
                            ],
                            "spriteColumn": 10
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.JpgFormat",
                            "filenamePattern": "ThumbnailSprite-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}
```
