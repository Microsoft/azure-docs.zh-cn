---
title: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373738"
---
向安全主体分配 Azure RBAC 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。 在较广范围内中定义的 Azure RBAC 角色由其下的资源继承。

以下列表描述可以限定 Azure blob 和队列资源访问权限范围的等级，从最窄的范围开始：

- **单个容器。** 在此范围内，角色分配将应用于容器中的所有 blob，以及容器属性和元数据。
- **单个队列。** 在此范围内，角色分配将应用于队列中的消息，以及队列属性和元数据。
- **存储帐户。** 在此范围内，角色分配将应用于所有容器及其 blob，或者所有队列及其消息。
- **资源组。** 在此范围内，角色分配适用于资源组中所有存储帐户内的所有容器或队列。
- **订阅。** 在此范围内，角色分配适用于订阅中所有资源组内的所有存储帐户中的所有容器或队列。
- **管理组。** 在此范围内，角色分配将应用于管理组中所有订阅的所有资源组中的所有存储帐户中的所有容器或队列。

有关 Azure 角色分配和范围的详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)？](../articles/role-based-access-control/overview.md)。
