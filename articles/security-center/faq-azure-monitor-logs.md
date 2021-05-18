---
title: Azure 安全中心常见问题解答 - 有关现有 Log Analytics 代理的问题
description: 此常见问题解答为已在使用 Log Analytics 代理并考虑使用 Azure 安全中心（一种可帮助你预防、检测和响应威胁的产品）的客户解答问题。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701954"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>针对已在使用 Azure Monitor 日志的客户的常见问题解答<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>安全中心是否会覆盖 VM 和工作区之间的任何现有连接？

如果 VM 已将 Log Analytics 代理作为 Azure 扩展进行安装，则安全中心不会覆盖现有工作区连接。 相反，安全中心会使用现有工作区。 如果在 VM 向其报告的工作区上安装了“Security”或“SecurityCenterFree”解决方案，则该 VM 将受保护。 

将在“数据收集”屏幕中选择的工作区上安装一个安全中心解决方案（如果尚不存在），而该解决方案只会应用到相关的 VM。 添加解决方案时，默认情况下会自动将它部署到连接到 Log Analytics 工作区的所有 Windows 和 Linux 代理。 [解决方案目标](../azure-monitor/insights/solution-targeting.md)可用于限定解决方案的范围。

> [!TIP]
> 如果 Log Analytics 代理直接安装到 VM 上（而不是作为 Azure 扩展进行安装），那么安全中心不会安装 Log Analytics 代理，并且安全监视也会受限。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>安全中心是否在现有 Log Analytics 工作区上安装解决方案？ 计费会产生什么影响？
安全中心确定 VM 已连接到所创建的工作区时，会根据定价配置启用此工作区上的解决方案。 由于[解决方案目标](../azure-monitor/insights/solution-targeting.md)，解决方案仅应用于相关的 Azure VM，因此计费保持不变。

- Azure Defender 关 - 安全中心在工作区中安装“SecurityCenterFree”解决方案。 系统不会向你收费。
- Azure Defender 开 - 安全中心在工作区中安装“Security”解决方案。

   ![默认工作区上的解决方案](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>环境中已存在工作区，是否可以将其用于收集安全数据？
如果 VM 已将 Log Analytics 代理作为 Azure 扩展进行安装，则安全中心会使用现有的已连接的工作区。 如果没有安全中心解决方案，则会在工作区安装，并且由于[解决方案目标](../azure-monitor/insights/solution-targeting.md)此解决方案仅适用于相关的 VM。

当安全中心在 VM 上安装 Log Analytics 代理时，如果安全中心未指向现有工作区，则代理会使用安全中心创建的默认工作区。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>工作区已存在安全解决方案。 计费会产生什么影响？
安全与审核解决方案用于启用适用于服务器的 Azure Defender。 如果已在工作区上安装“安全性与审核”解决方案，则安全中心会使用现有解决方案。 计费方面没有任何更改。