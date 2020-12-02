---
title: 大规模监视委托的资源
description: 了解如何在你管理的客户租户之间以可伸缩方式有效地使用 Azure Monitor 日志。
ms.date: 10/26/2020
ms.topic: how-to
ms.openlocfilehash: 96ca05faf2b3da8f214c14ae57eb186c7b71e1b3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461515"
---
# <a name="monitor-delegated-resources-at-scale"></a>大规模监视委托的资源

作为服务提供商，你可能已将多个客户租户载入 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

本主题说明如何在你管理的客户租户之间以可伸缩方式使用 [Azure Monitor 日志](../../azure-monitor/platform/data-platform-logs.md) 。 尽管我们指的是本主题中的服务提供商和客户，但本指南也适用于 [使用 Azure Lighthouse 管理多个租户的企业](../concepts/enterprise.md)。

> [!NOTE]
> 请确保已向管理租户中的用户授予在委派的客户订阅上 [管理 Log Analytics 工作区所需的角色](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) 。

## <a name="create-log-analytics-workspaces"></a>创建 Log Analytics 工作区

为了收集数据，需要创建 Log Analytics 工作区。 这些 Log Analytics 工作区是 Azure Monitor 收集的数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。

建议直接在客户租户中创建这些工作区。 这样，它们的数据将保留在其租户中，而不是导出到您的租户中。 这也允许对 Log Analytics 支持的任何资源或服务进行集中监视，从而更灵活地了解所监视的数据类型。

您可以通过使用 [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)、 [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)或使用 [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md)来创建 Log Analytics 工作区。

> [!IMPORTANT]
> 即使在客户租户中创建了所有工作区，也必须在管理租户中的订阅上注册 Microsoft Insights 资源提供程序。

## <a name="deploy-policies-that-log-data"></a>部署日志数据的策略

创建 Log Analytics 工作区后，可以在客户层次结构中部署 [Azure 策略](../../governance/policy/index.yml) ，以便将诊断数据发送到每个租户中的相应工作区。 根据要监视的资源类型，部署的确切策略可能会有所不同。

若要了解有关创建策略的详细信息，请参阅 [教程：创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)。 此 [社区工具](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) 提供了一个脚本，用于帮助你创建策略来监视你选择的特定资源类型。

确定要部署的策略后，可以 [大规模将它们部署到委托订阅](policy-at-scale.md)。

## <a name="analyze-the-gathered-data"></a>分析收集的数据

部署策略后，数据将记录在每个客户租户中创建的 Log Analytics 工作区中。 若要深入了解所有托管客户，可以使用 [Azure Monitor 工作簿](../../azure-monitor/platform/workbooks-overview.md) 之类的工具从多个数据源收集和分析信息。 

## <a name="next-steps"></a>后续步骤

- 探索这 [一通过 MVP 构建的示例工作簿](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)，通过在多个 Log Analytics 工作区中 [查询更新管理日志](../../automation/update-management/query-logs.md) 来跟踪修补程序相容性报告。 
- 了解有关 [Azure Monitor](../../azure-monitor/index.yml) 的信息。
- 了解 [Azure Monitor 日志](../../azure-monitor/platform/data-platform-logs.md)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。