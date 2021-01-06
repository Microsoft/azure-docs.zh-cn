---
title: 模板中的用户定义函数
description: 介绍如何在 Azure 资源管理器模板中定义和使用用户定义的函数 (ARM 模板) 。
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: f428fa3bc827af3820ad9f928f4f92b881c9c84c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934673"
---
# <a name="user-defined-functions-in-arm-template"></a>ARM 模板中的用户定义函数

在模板中，可以创建自己的函数。 这些函数可在模板中使用。 用户定义函数不同于模板中自动可用的[标准模板函数](template-functions.md)。 当有复杂的表达式在模板中重复使用时，请创建自己的函数。

本文介绍如何在 Azure 资源管理器模板 (ARM 模板) 中添加用户定义的函数。

## <a name="define-the-function"></a>定义函数

函数需要一个命名空间值以避免命名与模板函数发生冲突。 下面的示例演示一个返回唯一名称的函数：

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>使用函数

下面的示例演示一个包含用户定义函数的模板。 它使用该函数获取存储帐户的唯一名称。 该模板具有一个名为的参数，该参数将 `storageNamePrefix` 作为参数传递给函数。

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
 "functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>限制

定义用户函数时，存在一些限制：

* 该函数不能访问变量。
* 函数仅可使用函数中定义的参数。 在用户定义函数中使用 [parameters](template-functions-deployment.md#parameters) 函数时，只能使用该函数的参数。
* 该函数不能调用其他用户定义的函数。
* 该函数不能使用 [reference](template-functions-resource.md#reference) 函数或任何 [list](template-functions-resource.md#list) 函数。
* 该函数的参数不能具有默认值。

## <a name="next-steps"></a>后续步骤

* 若要了解有关用户定义函数的可用属性，请参阅 [了解 ARM 模板的结构和语法](template-syntax.md)。
* 有关可用模板函数的列表，请参阅 [ARM 模板函数](template-functions.md)。
