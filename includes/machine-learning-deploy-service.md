---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 04/21/2021
ms.author: gopalv
ms.openlocfilehash: 69b7e28c6b873f2e16df115b7095a7c543e73983
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107952132"
---
# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

将 `bidaf_onnx:1` 替换为模型的名称及其版本号。

```azurecli-interactive
az ml model deploy -n myservice -m bidaf_onnx:1 --ic inferenceconfig.json --dc deploymentconfig.json
az ml service get-logs -n myservice
```

# <a name="python"></a>[Python](#tab/python)

```python

service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output=True)
print(service.get_logs())
```

有关详细信息，请参阅[Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)和[Webservice](/python/api/azureml-core/azureml.core.webservice.webservice)文档。

---
