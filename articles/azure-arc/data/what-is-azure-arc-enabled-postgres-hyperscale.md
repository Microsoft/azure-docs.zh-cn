---
title: 什么是启用了 Azure Arc 的 PostgreSQL 超大规模？
description: 什么是启用了 Azure Arc 的 PostgreSQL 超大规模？
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 17c0e63b02d61b3aecf70624bc97a468d18f66fe
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607501"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>什么是启用了 Azure Arc 的 PostgreSQL 超大规模？

启用 azure Arc 后，PostgreSQL 超大规模是已启用 Azure Arc 的数据服务中提供的一项数据库服务。 借助 Azure Arc，可以使用 Kubernetes 和你选择的基础结构在本地、边缘和公有云环境中运行 Azure 数据服务。 支持 Azure Arc 的数据服务指定了的价值主张：
- 始终最新
- 弹性缩放
- 自助式预配
- 统一管理
- 断开连接的方案支持

有关更多详细信息，请参阅：
- [什么是已启用 Azure Arc 的数据服务](overview.md)
- [连接模式和要求](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>比较解决方案

本部分介绍如何启用 Azure Arc PostgreSQL 超大规模与 Azure Database for PostgreSQL 超大规模 (Citus) 的不同之处？

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL 超大规模 (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database for PostgreSQL 超大规模 (Citus) ":::

这是 Azure (PaaS) 中作为数据库即服务提供的 Postgres 数据库引擎的超大规模外形规格。 它由启用超大规模体验的 Citus 扩展提供支持。 在此外观上，该服务运行在 Microsoft 数据中心，由 Microsoft 操作。

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc PostgreSQL 超大规模

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="已启用 Azure Arc PostgreSQL 超大规模":::

这是可用于启用了 Azure Arc 的数据服务的 Postgres 数据库引擎的超大规模外形规格。 它还由启用超大规模体验的 Citus 扩展提供支持。 按照此外观，我们的客户提供了托管系统和运行系统的基础结构。

## <a name="next-steps"></a>后续步骤
- **创建**
   > **只想尝试执行此项任务呢？你没有可用的 Kubernetes 群集？我们提供了一个沙箱：**  
   > 在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中，通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始操作。

   - **创建**
      - [安装客户端工具](install-client-tools.md)
      - [创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）
      - [在 Azure arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md) ， (需要首先创建 azure arc 数据控制器。 ) 
- [**阅读有关启用了 Azure Arc 的数据服务的详细信息**](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [**了解 Azure Arc**](https://aka.ms/azurearc)
