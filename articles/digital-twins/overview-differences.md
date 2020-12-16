---
title: 与第一版之间的差异
titleSuffix: Azure Digital Twins
description: 了解新版本的 Azure 数字孪生中的更改内容
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: fb0d3e3c57a26f7ca14b74edc42cb657ba6074c3
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97604990"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-previous-version-2018"></a>新版 Azure 数字孪生是指什么？ 它与之前的版本 (2018) 有何区别？

Azure 数字孪生的第一个公共预览版已于 2018 年 10 月发布。 尽管第一版中的核心概念已转到新服务，但许多接口和实现细节都已更改，使该服务更灵活和更易于访问。 这些更改是由客户反馈推动的。

> [!IMPORTANT]
> 在新服务的扩展功能中，以前的 Azure 数字孪生服务将停用，其 Api 和关联的数据从2021年1月18日开始不再可用。

如果在首次公开预览期间使用第一版的 Azure 数字孪生，请使用本文中的信息和最佳做法，了解如何使用新的服务并利用其功能。

## <a name="differences-by-topic"></a>差异（按主题）

下图提供了在以前版本的服务和新的（当前）服务之间已更改概念的并排视图。

| 主题 | 在以前版本中 | 在新版本中 |
| --- | --- | --- | --- |
| **建模**<br>*更灵活* | 以前的版本是围绕智能空间设计的，因此它附带了建筑物的内置词汇。 | 新的 Azure 数字孪生与域无关。 可以为解决方案定义自己的自定义词汇和自定义模型，以更灵活的方式表示更多种环境。<br><br>在[概念：自定义模型](concepts-models.md)中了解详细信息。 |
| **拓扑**<br>*更灵活*| 以前的版本支持针对智能空间量身定制的树数据结构。 数字孪生与层次结构关系相关联。 | 通过新版本，数字孪生可以连接到任意图形拓扑，并根据需要进行组织。 这使你可以更加灵活地表达现实世界中的复杂关系。<br><br>在[概念：数字孪生和孪生图](concepts-twins-graph.md)中了解详细信息。 |
| **计算**<br>*更丰富、更灵活* | 在以前的版本中，用于处理事件和遥测的逻辑是在 JavaScript 用户定义的函数 (UDF) 中进行定义的。 使用 UDF 进行调试受到限制。 | 新版本具有开放式计算模型：通过附加外部计算资源（如 [Azure Functions](../azure-functions/functions-overview.md)）即可提供自定义逻辑。 这使你可以使用自己选择的编程语言、不受限制地访问自定义代码库，以及利用外部服务可能具有的开发和调试资源。<br><br>在[如何：设置用于处理数据的 Azure 函数](how-to-create-azure-function.md)中了解详细信息。 |
| **使用 IoT 中心进行设备管理**<br>*更易于访问* | 以前版本的托管设备，其中包含 Azure 数字孪生服务内部的 [IoT 中心](../iot-hub/about-iot-hub.md)实例。 开发人员无法完全访问此集成的中心。 | 在新版本中，通过附加单独创建的 IoT 中心实例（以及它已管理的任何设备）即可“自带”IoT 中心。 这使你可以完全访问 IoT 中心的功能，并可以控制设备管理。<br><br>在[如何：从 IoT 中心引入遥测](how-to-ingest-iot-hub-data.md)中了解详细信息。 |
| **安全性**<br>*更标准* | 以前的版本具有预定义的角色，可使用这些角色来管理对实例的访问。 | 新版本与其他 Azure 服务使用的相同 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 后端服务相集成。 这可能使在解决方案中的其他 Azure 服务（如 IoT 中心、Azure Functions、事件网格等）之前进行身份验证变得更加简单。<br>使用 RBAC 时，仍可以使用预定义的角色，也可以构建和配置自定义角色。<br><br>在[概念：Azure 数字孪生解决方案的安全性](concepts-security.md)中了解详细信息。 |
| **伸缩性**<br>*更强大* | 以前的版本对设备、消息、图形和缩放单元有缩放限制。 每个订阅仅支持一个 Azure 数字孪生实例。  | 新版本依赖于可伸缩性得到提高的新体系结构，并且具有更强大的计算能力。 它还支持每个区域每个订阅 10 个实例。<br><br>有关当前版本中的限制的详细信息，请参阅 [*参考：服务限制*](reference-service-limits.md) 。 |

## <a name="service-limits"></a>服务限制

有关 Azure 数字孪生限制的列表，请参阅[参考：服务限制](reference-service-limits.md)

## <a name="next-steps"></a>后续步骤

接下来，通过第一个教程深入研究如何使用 Azure 数字孪生：

[*教程：* 为客户端应用编写代码](tutorial-code.md)