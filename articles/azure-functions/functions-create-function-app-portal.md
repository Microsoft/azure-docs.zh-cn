---
title: 通过 Azure 门户创建 Function App
description: 通过门户在 Azure 中创建新的函数应用。
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 8d19a269903de309bf219c2546fa70c3abe7be10
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093582"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>通过 Azure 门户创建 Function App

本主题演示如何使用 Azure Functions 在 Azure 门户中创建函数应用。 Function App 是托管各个函数执行的容器。 

## <a name="create-a-function-app"></a>创建函数应用

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

创建 Function App 后，可以使用一种或多种不同的语言创建各个函数。 [通过使用门户](functions-create-first-azure-function.md#create-function)、[连续部署](functions-continuous-deployment.md)，或通过[使用 FTP 上传](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp)来创建函数。

## <a name="service-plans"></a>服务计划

Azure Functions 有三个不同的服务计划：消耗计划、高级计划和专用 (应用服务) 计划。 必须在创建函数应用时选择服务计划，并且随后无法更改。 有关详细信息，请参阅[选择 Azure Functions 托管计划](functions-scale.md)。

如果计划在专用（应用服务）计划上运行 JavaScript 函数，则应选择具有较少核心的计划。 有关详细信息，请参阅[函数的 JavaScript 参考](functions-reference-node.md#choose-single-vcpu-app-service-plans)。

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>存储帐户要求

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 Azure Functions 内部使用存储以进行管理触发器和记录函数执行等操作。 某些存储帐户不支持队列和表，例如仅限 blob 的存储帐户、Azure 高级存储和使用 ZRS 复制的常规用途的存储帐户。 

在 Azure 门户中创建函数应用时，将筛选出不受支持的类型的帐户。 仅当该帐户与要创建的函数应用位于同一区域中时，该门户还允许使用现有的存储帐户。 如果由于某种原因而需要违反在同一区域中使用函数应用所使用的存储帐户的性能最佳做法，则必须在门户外创建函数应用。 

>[!NOTE]
>使用消耗托管计划时，函数代码和绑定配置文件存储在主存储帐户的 Azure 文件存储中。 删除主存储帐户时，此内容将随之删除且无法恢复。 

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#core-storage-services)。 

## <a name="next-steps"></a>后续步骤

虽然 Azure 门户可以轻松创建和试用 Functions，但我们建议[本地开发](functions-develop-local.md)。 在门户中创建函数应用后，仍然需要添加一个函数。 

> [!div class="nextstepaction"]
> [添加 HTTP 触发的函数](functions-create-first-azure-function.md#create-function)
