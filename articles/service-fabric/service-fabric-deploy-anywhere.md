---
title: Azure 和独立 Service Fabric 群集的概述
description: 可以在运行 Windows Server 或 Linux 的任何 VM 或计算机上创建 Service Fabric 群集。 这意味着，可以在包含一组互连 Windows Server 或 Linux 计算机（无论是本地计算机、Microsoft Azure 计算机还是任何云提供商的计算机）的任何环境中部署和运行 Service Fabric 应用程序。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576122"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>将 Azure Service Fabric 群集与 Windows Server 和 Linux 上的独立 Service Fabric 群集进行比较

Service Fabric 群集是通过网络连接在一起的一组虚拟机或物理机，可在其中部署和管理微服务。 群集中的计算机或 VM 称为群集节点。 群集可以扩展到数千个节点。 如果向群集添加新节点，Service Fabric 会在新增加的节点间重新平衡服务分区副本和实例。 应用程序总体性能提高，访问内存的争用减少。 如果没有高效使用群集中的节点，可以减少群集中节点的数量。 Service Fabric 会再次在减少的节点间重新平衡分区副本和实例以更加充分利用每个节点上的硬件。

使用 Service Fabric 可在运行 Windows Server 或 Linux 的任何 VM 或计算机上创建 Service Fabric 群集。 这意味着，可以在包含一组互连 Windows Server 或 Linux 计算机（无论是本地计算机、Microsoft Azure 计算机还是任何云提供商的计算机）的任何环境中部署和运行 Service Fabric 应用程序。

## <a name="benefits-of-clusters-on-azure"></a>Azure 上的群集的优点

在 Azure 上，我们提供与其他 Azure 功能和服务的集成，这样可使群集的操作和管理更容易且更可靠。

* **Azure 门户：** Azure 门户使群集易于创建和管理。
* **Azure 资源管理器：** 使用 Azure 资源管理器可以单元的形式方便地管理群集使用的所有资源，并简化了成本跟踪和计费。
* **用作 Azure 资源的 Service Fabric 群集** Service Fabric 群集是一种 Azure 资源，因此可以像在 Azure 中对其他资源建模一样为它建模。
* **与 Azure 基础结构集成** Service Fabric 与适用于 OS、网络和其他升级的 Azure 基础结构相协调，以提高应用程序的可用性与可靠性。  
* **诊断：** 在 Azure 中，我们提供与 Azure 诊断和 Azure Monitor 日志的集成。
* **自动缩放：** 对于 Azure 上的群集，我们通过虚拟机缩放集提供内置自动缩放功能。 在本地和其他云环境中，必须构建自己的自动缩放功能，或使用 Service Fabric 为缩放群集公开的 API 来手动缩放。

## <a name="benefits-of-standalone-clusters"></a>独立群集的优点

* 可以自由选择任何云提供商来托管群集。
* Service Fabric 应用程序在编写之后，可以在多个宿主环境中运行，只需进行最小程度的更改，甚至无需更改。
* 构建 Service Fabric 应用程序的知识可从一个宿主环境转移到另一个环境。
* 运行和管理 Service Fabric 群集的操作经验可从一个环境转移到另一个环境。
* 广泛的客户范围不会受宿主环境约束的限制。
* 存在针对大范围中断的额外一层可靠性和保护，因为可以在数据中心或云提供商中断时将服务转移到其他部署环境。

## <a name="next-steps"></a>后续步骤

* 阅读 [Azure 上的 Service Fabric 群集](service-fabric-azure-clusters-overview.md)的概述
* 阅读 [Service Fabric 独立群集](service-fabric-standalone-clusters-overview.md)的概述
* 了解 [Service Fabric 支持选项](service-fabric-support.md)