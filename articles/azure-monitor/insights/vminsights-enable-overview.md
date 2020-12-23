---
title: 启用用于 VM 的 Azure Monitor 概述
description: 了解如何部署和配置用于 VM 的 Azure Monitor。 了解系统要求。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/27/2020
ms.custom: references_regions
ms.openlocfilehash: f5e774e9b7327d4b403f6a09187e97082a77aa78
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186792"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>启用用于 VM 的 Azure Monitor 概述

本文概述了可用于启用用于 VM 的 Azure Monitor 来监视以下各项的运行状况和性能的选项：

- Azure 虚拟机 
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机
- 本地虚拟机
- 托管在其他云环境中的虚拟机。  

若要设置用于 VM 的 Azure Monitor：

* 通过在 Azure 门户中直接从菜单中选择 " **见解** "，启用单个 azure VM、azure VMSS 或 azure Arc 计算机。
* 使用 Azure 策略启用多个 Azure Vm、Azure VMSS 或 Azure Arc 计算机。 此方法可确保在现有和新的 VM 与规模集上安装并正确配置所需的依赖项。 系统会报告不合规的 VM 和规模集，因此你可以决定是否要启用和修正它们。
* 使用 PowerShell 在指定的订阅或资源组中启用多个 Azure Vm、Azure Arc Vm、Azure VMSS 或 Azure Arc 计算机。
* 启用用于 VM 的 Azure Monitor，以监视企业网络或其他云环境中托管的 VM 或物理计算机。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保理解以下部分中的信息。 

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](service-map.md)。  

### <a name="log-analytics-workspace"></a>Log Analytics 工作区

用于 VM 的 Azure Monitor 支持以下区域中的 Log Analytics 工作区：

- 非洲
  - 南非北部
- 亚太
  - 东亚
  - 东南亚
- 澳大利亚
  - 澳大利亚东部
  - 澳大利亚东南部
- Azure Government
  - US Gov Az
  - US Gov Va
- Canada
  - 加拿大中部
- 欧洲
  - 北欧
  - 西欧
- 印度
  - 印度中部
- 日本
  - 日本东部
- 英国
  - 英国南部
- 美国
  - 美国中部
  - 美国东部
  - 美国东部 2
  - 美国中北部
  - 美国中南部
  - 美国中西部
  - 美国西部
  - 美国西部 2


>[!NOTE]
>可以在任何区域监视 Azure Vm。 Vm 本身并不限于 Log Analytics 工作区所支持的区域。
>

如果没有 Log Analytics 工作区，则可以使用以下资源之一创建一个工作区：
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure 门户](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../samples/resource-manager-workspace.md)

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机

## <a name="supported-operating-systems"></a>支持的操作系统

用于 VM 的 Azure Monitor 支持支持 Log Analytics 代理和依赖项代理的任何操作系统。 有关完整列表，请参阅 [Azure Monitor 代理概述 ](../platform/agents-overview.md#supported-operating-systems) 。

有关支持用于 VM 的 Azure Monitor 的依赖项代理的 Linux 支持，请参阅以下注意事项列表：

- 仅默认版本和 SMP Linux 内核版本受支持。
- 任何 Linux 发行版都不支持非标准内核版本（例如物理地址扩展 [PAE] 和 Xen）。 例如，不支持版本字符串为 *2.6.16.21-0.8-xen* 的系统。
- 不支持自定义内核（包括标准内核的重新编译）。
- 对于除版本9.4 以外的 Debian 发行版，不支持地图功能，并且只能通过 Azure Monitor 菜单来使用性能功能。 无法直接在 Azure VM 的左窗格中使用此功能。
- 支持 CentOSPlus 内核。
- 必须为 Spectre 漏洞修补 Linux 内核。 有关更多详细信息，请咨询 Linux 发行版供应商。



## <a name="supported-azure-arc-machines"></a>支持的 Azure Arc 计算机
用于 VM 的 Azure Monitor 可用于可用 Arc 扩展服务的区域中启用了 Azure Arc 的服务器。 您必须运行版本0.9 或更高版本的 Arc 代理。

| 连接的源 | 支持 | 说明 |
|:--|:--|:--|
| Windows 代理 | 是 | 除[适用于 Windows 的 Log Analytics 代理](../platform/log-analytics-agent.md)以外，Windows 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](../platform/agents-overview.md#supported-operating-systems)。 |
| Linux 代理 | 是 | 除[适用于 Linux 的 Log Analytics 代理](../platform/log-analytics-agent.md)以外，Linux 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |

## <a name="agents"></a>代理
用于 VM 的 Azure Monitor 要求在要监视的每个虚拟机或虚拟机规模集上安装以下两个代理。 安装这些代理并将其连接到工作区是加入资源的唯一要求。

- [Log Analytics 代理](../platform/log-analytics-agent.md)。 从虚拟机或虚拟机规模集中收集事件和性能数据，并将其传送到 Log Analytics 工作区。 适用于 Azure 资源上的 Log Analytics 代理的部署方法使用适用于 [Windows](../../virtual-machines/extensions/oms-windows.md) 和 [Linux](../../virtual-machines/extensions/oms-linux.md)的 VM 扩展。
- 依赖关系代理。 收集有关在虚拟机上运行的进程和外部进程依赖关系的发现的数据， [用于 VM 的 Azure Monitor 中的映射功能](vminsights-maps.md)使用这些依赖关系。 依赖关系代理依赖于 Log Analytics 代理将其数据传递到 Azure Monitor。 Azure 资源上的依赖关系代理的部署方法使用适用于 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 和 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的 VM 扩展。

> [!NOTE]
> Log Analytics 代理是 System Center Operations Manager 使用的同一代理。 用于 VM 的 Azure Monitor 可以监视通过 Operations Manager 监视的代理（如果它们直接连接），并在这些代理上安装依赖项代理。 用于 VM 的 Azure Monitor 无法监视通过 [管理组连接](../tform/../platform/om-agents.md) 连接到 Azure Monitor 的代理。

下面是用于部署这些代理的多种方法。 

| 方法 | 说明 |
|:---|:---|
| [Azure 门户](./vminsights-enable-portal.md) | 在单个虚拟机、虚拟机规模集或使用 Azure Arc 连接的混合虚拟机上安装这两个代理。 |
| [资源管理器模板](vminsights-enable-resource-manager.md) | 使用任意支持的方法安装两个代理，以部署包括 CLI 和 PowerShell 的资源管理器模板。 |
| [Azure Policy](./vminsights-enable-policy.md) | 分配 Azure 策略计划，以便在创建虚拟机或虚拟机规模集时自动安装代理。 |
| [手动安装](./vminsights-enable-hybrid.md) | 在 Azure 外部托管的计算机上的来宾操作系统中安装代理，包括在数据中心或其他云环境中。 |




## <a name="management-packs"></a>管理包
为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区时，会将两个管理包转发到连接到该工作区的所有 Windows 计算机。 管理包名为 *microsoft.intelligencepacks.updateassessment. microsoft.intelligencepacks.applicationdependencymonitor* 和 *microsoft.intelligencepacks.updateassessment* ，并写入 *%Programfiles%\Microsoft 监视 Agent\Agent\Health Service state\management packs pack \* 。 

*Microsoft.intelligencepacks.applicationdependencymonitor* 管理包使用的数据源是 **% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*。 *VMInsights* 管理包使用的数据源是 *% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据来改进服务的质量、安全性和完整性。 

为了提供准确高效的故障排除功能，映射功能包含有关软件配置的信息。 这些数据提供操作系统和版本、IP 地址、DNS 名称及工作站名称等信息。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

若要了解如何使用性能监视功能，请参阅 [查看用于 VM 的 Azure Monitor 性能](vminsights-performance.md)。 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
