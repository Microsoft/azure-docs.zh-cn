---
title: include 文件
description: include 文件
services: app-service
author: msangapu-msft
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: msangapu
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: a61698a876deb1705e231a627ce6a8cac7586fe5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779413"
---
<!-- Please keep this file set to PHP 7.2, as that's the highest PHP version Laravel supports (as shown in the PHP+MySQL tutorial) -->

在 `myAppServicePlan` 应用服务计划中创建一个 [Web 应用](../articles/app-service/overview.md#app-service-on-linux)。 

在 Cloud Shell 中可以使用 [`az webapp create`](/cli/azure/webapp#az_webapp_create) 命令。 在以下示例中，将 `<app-name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 运行时设置为 `PHP|7.2`。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes --linux`](/cli/azure/webapp#az_webapp_list_runtimes)。 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.2" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.2" --deployment-local-git
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

现在你已经创建了一个新的空 Web 应用并启用了 Git 部署。

> [!NOTE]
> Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 保存此 URL，后续将会用到。
>
