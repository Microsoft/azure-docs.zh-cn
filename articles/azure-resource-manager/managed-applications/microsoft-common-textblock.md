---
title: TextBlock UI 元素
description: 介绍了 Azure 门户的 Microsoft.Common.TextBlock UI 元素。 用于向界面添加文本。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: ba2c4d410891910c29ee1fda1065f8230ab171bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063875"
---
# <a name="microsoftcommontextblock-ui-element"></a>Microsoft.Common.TextBlock UI 元素

可用于向门户界面添加文本的控件。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textblock.png)

## <a name="schema"></a>架构

```json
{
  "name": "text1",
  "type": "Microsoft.Common.TextBlock",
  "visible": true,
  "options": {
    "text": "Please provide the configuration values for your application.",
    "link": {
      "label": "Learn more",
      "uri": "https://www.microsoft.com"
    }
  }
}
```

## <a name="sample-output"></a>示例输出

```json
"Please provide the configuration values for your application. Learn more"
```

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
