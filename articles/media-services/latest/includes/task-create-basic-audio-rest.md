---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/19/2020
ms.author: inhenkel
ms.custom: REST
ms.openlocfilehash: def2553ddc71f008c5d2b902d04f587ef8d26889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996880"
---
<!--Create a basic audio transform REST-->

以下 Azure REST 命令创建基本音频转换。 将 `subscriptionID`、`resourceGroup` 和 `accountName` 值替换为当前正在使用的值。 通过设置 `transformName` 为转换命名。

```REST

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>正文

```json
{
    "properties": {
        "description": "Transform for Audio Analyzer Basic Mode",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>示例响应
```json
{
    "name": "audioAnalyzerTransform",
    "id": "/subscriptions/35c2594a-xxxx-4fce-b59c-f6fb9513eeeb/resourceGroups/myresourcegroup/providers/Microsoft.Media/mediaservices/mymediaservicesaccount/transforms/audioAnalyzerTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2020-10-23T18:03:37.4793962Z",
        "description": "Transform for Audio Analyzer Basic Mode",
        "lastModified": "2020-10-23T18:03:37.4793962Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic",
                    "experimentalOptions": {
                        "basicAudioEnabled": "true"
                    }
                }
            }
        ]
    }
}
```