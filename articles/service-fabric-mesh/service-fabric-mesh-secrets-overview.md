---
title: 存储和使用 Azure Service Fabric 网格应用程序机密
description: Service Fabric 网格支持将机密作为 Azure 资源。 下面介绍了如何使用 Service Fabric 网格应用程序存储和管理机密。
author: erikadoyle
ms.author: edoyle
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 85b4bb6ee4d76a7115c9ebdd1466049afe6683c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625526"
---
# <a name="service-fabric-mesh-application-secrets"></a>Service Fabric 网格应用程序机密

> [!IMPORTANT]
> Azure Service Fabric 网格的预览版已停用。 不允许再通过 Service Fabric 网格 API 进行新的部署。 对现有部署的支持将会持续到 2021 年 4 月 28 日。
> 
> 有关详细信息，请参阅 [Azure Service Fabric 网格预览版停用](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)。

Service Fabric 网格支持将机密作为 Azure 资源。 Service Fabric 网格机密可以是任何敏感文本信息，例如存储连接字符串、密码或应该安全存储和传输的其他值。

![网格机密概述][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>网格机密资源
网格应用程序机密包括：
* 一个 **机密** 资源，它是一个用于存储文本机密的容器。 **机密** 资源中包含的机密将安全地进行存储和传输。
* 存储在 **机密** 资源容器中的一个或多个 **机密/值** 资源。 每个 **机密/值** 资源都由版本号予以区分。

## <a name="next-steps"></a>后续步骤 
若要详细了解 Service Fabric 网格机密，请参阅：
- [管理 Service Fabric 网格应用程序机密](service-fabric-mesh-howto-manage-secrets.md)
- [Service Fabric 资源模型简介](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
