---
title: Azure Migrate 中的新增功能
description: 了解 Azure Migrate 服务中的新增功能和最新更新。
ms.topic: overview
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: cc207522048e3f0491e3aa54f2ae21d2e7e63bc2
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834028"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate 中的新增功能

[Azure Migrate](migrate-services-overview.md) 可帮助发现和访问本地服务器、应用和数据并将其迁移到 Microsoft Azure 云。 本文汇总了 Azure Migrate 中的新版本和功能。

## <a name="update-january-2021"></a>更新（2021 年 1 月）
-  借助 Azure Migrate 的服务器迁移工具，你现可将 VMware 虚拟机、物理服务器和其他云中的虚拟机迁移到 Azure 虚拟机，其中后者带有用客户管理的密钥 (CMK) 进行服务器端加密的磁盘。

## <a name="update-december-2020"></a>更新（2020 年 12 月）
- Azure Migrate 现在会自动将 Azure VM 代理安装在 VMware VM 上，同时使用 VMware 迁移的无代理方法将其迁移到 Azure。
- 现可通过 Azure 门户使用 Azure Migrate 服务器迁移（无代理复制）将 VMware VM 迁移到 Azure 虚拟机，后者具有使用服务器端加密 (SSE) 和客户管理的密钥 (CMK) 进行加密的磁盘。

## <a name="update-september-2020"></a>更新（2020 年 9 月）
- 现支持将服务器迁移到可用性区域。
- 现支持将基于 UEFI 的虚拟机 (VM) 和物理服务器迁移到 Azure 第二代 VM。 在此版本中，Azure Migrate：服务器迁移工具将不会在迁移过程中执行从第二代 VM 到第一代 VM 的转换。
- 现提供一个新的 Azure Migrate Power BI 评估仪表板来帮助你比较不同评估设置的费用。 该仪表板覆盖了一个 PowerShell 实用工具，它会自动创建要插入到 Power BI 仪表板中的评估。 [了解详细信息。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/assessment-utility)
- 依赖项分析（无代理）现可在 1000 个 VM 上并发运行。
- 现可使用 PowerShell 脚本大规模地启用或禁用依赖项分析（无代理）。 [了解详细信息。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- 通过用依赖项分析（无代理）收集的数据在 Power BI 中直观呈现网络连接 [了解详细信息。](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale)
- 现在，可使用 Azure Migrate 支持迁移数据磁盘大小高达 32 TB 的 VMware VM：服务器迁移无代理 VMware 迁移方法。

## <a name="update-august-2020"></a>更新（2020 年 8 月）

- 改进了载入体验，其中 Azure Migrate 项目密钥是从门户生成的，用于完成设备注册。
- 从门户下载 OVA/VHD 文件或安装程序脚本以便分别设置 VMware 和 Hyper-V 设备的选项。
- 已通过增强的用户体验刷新设备配置管理器。
- 多个凭据支持 Hyper-V VM 发现。

## <a name="update-july-2020"></a>更新（2020 年 7 月）

- 无代理 VMware 迁移现支持按每个 vCenter 并发复制 300 个 VM

## <a name="update-june-2020"></a>更新（2020 年 6 月）

- 现在支持将本地 VMware VM 迁移到 [Azure VMware 解决方案 (AVS)](./concepts-azure-vmware-solution-assessment-calculation.md) 的评估。 [了解详细信息](how-to-create-azure-vmware-solution-assessment.md)
- 支持在设备上用多个凭据来发现物理服务器。
- 支持允许租户从设备登录 Azure，其中租户限制已配置。


## <a name="update-april-2020"></a>更新（2020 年 4 月）

Azure Migrate 支持在 Azure 政府中部署。 

- 你可以发现和评估 VMware VM、Hyper-V VM 和物理服务器。
- 你可以将 VMware VM、Hyper-V VM 和物理服务器迁移到 Azure。
- 对于 VMware 迁移，可以使用无代理或基于代理的迁移。 [了解详细信息](server-migrate-overview.md)。
- [查看](migrate-support-matrix.md#supported-geographies-azure-government) Azure 政府支持的地理位置和区域。
- Azure 政府不支持[基于代理的依赖关系分析](concepts-dependency-visualization.md#agent-based-analysis)。
- Azure 政府支持预览版中的功能，特别是[无代理依赖关系分析](concepts-dependency-visualization.md#agentless-analysis)和[应用程序发现](how-to-discover-applications.md)。


## <a name="update-march-2020"></a>更新（2020 年 3 月）

基于脚本的安装现在可用于设置 [Azure Migrate 设备](migrate-appliance.md)：

- 基于脚本的安装可替代设备的 .OVA (VMware)/VHD (Hyper-V) 安装。
- 它提供 PowerShell 安装程序脚本，该脚本可用于在运行 Windows Server 2016 的现有计算机上为 VMware/Hyper-V 设置设备。

## <a name="update-november-2019"></a>更新（2019 年 11 月）

Azure Migrate 增加了许多新功能：

- **物理服务器评估**。 除了已支持的物理服务器迁移外，现在还支持对本地物理服务器进行评估。
- **基于导入的评估**。 现在支持使用 CSV 文件中提供的元数据和性能数据评估计算机。
- **应用程序发现**：Azure Migrate 现在支持使用 Azure Migrate 设备实现应用、角色和功能的应用程序级发现。 目前仅适用于 VMware VM，并且仅限于发现（目前不支持评估）。 [了解详细信息](how-to-discover-applications.md)
- **无代理依赖项可视化效果**：不再需要为依赖项可视化效果显式安装代理。 现在同时支持无代理和基于代理。
- **虚拟桌面**：使用 ISV 工具评估本地虚拟桌面基础结构 (VDI) 并将其迁移到 Azure 中的 Windows 虚拟桌面。
- **Web 应用**：用于评估和迁移 Web 应用的 Azure 应用服务迁移助手现已集成到 Azure Migrate 中。

Azure Migrate 中提供了新的评估和迁移工具：

- **Rackware**：提供云迁移功能。
- **Movere**：提供评估功能。

[详细了解](migrate-services-overview.md)如何使用 Azure Migrate 中用于评估与迁移的工具和 ISV 产品/服务。

## <a name="azure-migrate-current-version"></a>Azure Migrate 当前版本

Azure Migrate 的当前版本（在 2019 年 7 月发布）提供许多新功能：

- **统一迁移平台**：Azure Migrate 现在提供单个门户，可以集中、管理和跟踪到 Azure 的迁移旅程，并改进了部署流和门户体验。
- **评估和迁移工具**：Azure Migrate 提供本机工具，并与其他 Azure 服务以及独立软件供应商 (ISV) 工具集成。 [详细了解](migrate-services-overview.md#isv-integration) ISV 集成。
- **Azure Migrate 评估**：Azure Migrate 服务器评估可评估 VMware VM 和 Hyper-V VM，以便迁移到 Azure。 还可以使用其他 Azure 服务和 ISV 工具评估迁移。
- **Azure Migrate 迁移**：使用 Azure Migrate 服务迁移工具，可以将本地 VMware VM、Hyper-v VM、物理服务器、其他虚拟服务器和专用/公有云 VM 迁移到 Azure。 此外，还可以使用 ISV 工具迁移到 Azure。
- **Azure Migrate 设备**：Azure Migrate 部署轻型设备，用于发现和评估本地的 VMware VM 和 Hyper-V VM。
    - Azure Migrate 服务器评估和 Azure Migrate 服务器迁移使用此设备进行无代理迁移。
    - 设备持续发现服务器元数据和性能数据，以进行评估和迁移。  
- **VMware VM 迁移**：Azure Migrate 服务器迁移提供几种将本地 VMware Vm 迁移到 Azure 的方法。  使用 Azure Migrate 设备的无代理迁移，以及使用复制设备且在要迁移的每个 VM 上部署代理的基于代理的迁移。 [了解详细信息](server-migrate-overview.md)
 - **数据库评估和迁移**：在 Azure Migrate 中，可使用 Azure 数据库迁移助手来评估要迁移到 Azure 的本地数据库。 使用 Azure 数据库迁移服务来迁移数据库。
- **Web 应用迁移**：可以通过 Azure 应用服务，使用公共终结点 URL 来评估 Web 应用。 若要迁移内部 .NET 应用，可以下载并运行应用服务迁移助手。
- **Data Box**：使用 Azure Migrate 中的 Azure Data Box 将大量脱机数据导入到 Azure。

## <a name="azure-migrate-previous-version"></a>Azure Migrate 旧版本

如果你使用的是以前版本的 Azure Migrate（仅支持对本地 VMware VM 的评估），则现在应使用当前版本。 在以前的版本中，已无法创建新的 Azure Migrate 项目，也无法执行新发现。 仍可以访问现有项目。 在“Azure 门户”>“所有服务”中执行此操作，搜索“Azure Migrate” 。 Azure Migrate 通知中提供了一个用于访问旧 Azure Migrate 项目的链接。



## <a name="next-steps"></a>后续步骤

- [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
- 试用我们的教程来评估 [VMware VM](./tutorial-assess-vmware-azure-vm.md) 和 [Hyper-V VM](tutorial-assess-hyper-v.md)。