---
title: 关于 Azure 文件共享备份
description: 了解如何在恢复服务保管库中备份 Azure 文件共享
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c4f9dd816ace2c9aec8f48207fbce88acf34e24a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516809"
---
# <a name="about-azure-file-share-backup"></a>关于 Azure 文件共享备份

Azure 文件共享备份是一种基于云的原生备份解决方案，用于保护云中的数据，消除了本地备份解决方案涉及的额外维护开销。 Azure 备份服务与 Azure 文件同步顺畅集成，允许你集中管理文件共享数据和备份。 这个简单、可靠且安全的解决方案使你可以通过几个简单的步骤来配置对企业文件共享的保护，确保你可以在发生任何灾难时恢复数据。

## <a name="key-benefits-of-azure-file-share-backup"></a>Azure 文件共享备份的主要优点

* 零基础结构：配置对文件共享的保护不需要进行部署。
* 自定义保留期：可以根据需要配置保留期为每日/每周/每月/每年的备份。
* 内置的管理功能：你可以计划备份并指定所需的保留期，不会产生额外的数据删除开销。
* 即时还原：Azure 文件共享备份使用文件共享快照，因此，你可以只选择你想要立即还原的文件。
* 警报和报告：你可以针对备份和还原失败的情况配置警报，并使用 Azure 备份提供的报告解决方案来深入了解文件共享中的备份。
* 防止意外删除文件共享：Azure 备份在存储帐户级别启用保留期为 14 天的[软删除功能](../storage/files/storage-files-prevent-file-share-deletion.md)。 即使恶意行动者删除了文件共享，文件共享的内容和恢复点（快照）也会在可配置的保留期内保留，这样就能够成功且完整地恢复源内容和快照，不会丢失数据。

## <a name="architecture"></a>体系结构

![Azure 文件共享备份体系结构](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>备份进程的工作方式

1. 为 Azure 文件共享配置备份的第一步是创建恢复服务保管库。 保管库提供为各种工作负荷配置的备份的合并视图。

2. 当你创建保管库后，Azure 备份服务会发现可向保管库注册的存储帐户。 你可以选择要保护的文件共享所在的存储帐户。

3. 当你选择存储帐户后，Azure 备份服务会列出存储帐户中存在的文件共享集，并将其名称存储在管理层目录中。

4. 然后，请根据需要配置备份策略（计划和保留），并选择要备份的文件共享。 Azure 备份服务会在控制平面中注册计划以执行计划的备份。

5. 根据指定的策略，Azure 备份计划程序会在计划的时间触发备份。 在该作业中，文件共享快照是使用文件共享 API 创建的。 只有快照 URL 存储在元数据存储中。

    >[!NOTE]
    >文件共享数据不会传输到备份服务，因为备份服务会创建并管理属于你的存储帐户的快照，而不会将备份传输到保管库。

6. 你可以从源文件共享上可用的快照还原 Azure 文件共享内容（个别文件或整个共享）。 触发该操作后，将从元数据存储中检索快照 URL，列出数据并将其从源快照传输到所选的目标文件共享。

7. 如果你使用的是 Azure 文件同步，则备份服务会向 Azure 文件同步服务指示要还原的文件的路径，然后会针对这些文件触发后台更改检测过程。 任何已更改的文件都会同步到服务器终结点。 此过程与到 Azure 文件共享的原始还原并行进行。

8. 备份和还原作业监视数据会被推送到 Azure 备份监视服务。 这样你就可以在单个仪表板中监视文件共享的云备份。 此外，你还可以配置备份运行状况受到影响时的警报或电子邮件通知。 电子邮件通过 Azure 电子邮件服务发送。

## <a name="backup-costs"></a>备份成本

Azure 文件共享备份解决方案有两种成本：

1. 快照存储成本：快照产生的存储费用将与 Azure 文件使用量费用一起计算（参照[此文](https://azure.microsoft.com/pricing/details/storage/files/)所述的详细定价）

2. 受保护实例费用：从 2020 年 9 月 1 日开始，我们将根据[此文](https://azure.microsoft.com/pricing/details/backup/)所述的定价详细信息向客户收取受保护实例费用。受保护实例费用取决于存储帐户中受保护文件共享的总大小。

若要对备份 Azure 文件共享时的费用进行详细的估算，可以下载详细的 [Azure 备份定价估算器](https://aka.ms/AzureBackupCostEstimates)。  

## <a name="next-steps"></a>后续步骤

* 了解如何[备份 Azure 文件共享](backup-afs.md)
* 查找[有关如何备份 Azure 文件的问题](backup-azure-files-faq.yml)的解答
