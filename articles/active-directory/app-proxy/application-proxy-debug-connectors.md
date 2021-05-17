---
title: 调试应用程序代理连接器 - Azure Active Directory
description: 调试 Azure Active Directory (Azure AD) 应用程序代理连接器的问题。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 730fba1b1a936a6277289b816412750764930469
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108186040"
---
# <a name="debug-application-proxy-connector-issues"></a>调试应用程序代理连接器问题 

本文将帮助你排查 Azure Active Directory (Azure AD) 应用程序代理连接器的问题。 如果你正在使用应用程序代理服务远程访问本地 Web 应用程序，但连接到应用程序时遇到问题，请使用此流程图调试连接器问题。 

## <a name="before-you-begin"></a>准备阶段

本文假设你已安装应用程序代理连接器并且遇到问题。 对应用程序代理问题进行故障排除时，我们建议你从故障排除流程开始，以确定应用程序代理连接器的配置是否正确。 如果仍然无法连接到应用程序，请按照[调试应用程序代理应用程序问题](application-proxy-debug-apps.md)中的故障排除流程进行操作。  


有关应用程序代理和使用其连接器的详细信息，请参阅：

- [通过应用程序代理远程访问本地应用程序](application-proxy.md)
- [应用程序代理连接器](application-proxy-connectors.md)
- [安装并注册连接器](application-proxy-add-on-premises-application.md)
- [应用程序代理问题和错误消息故障排除](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>连接器问题的流程图

此流程图指导你完成调试某些更常见连接器问题的步骤。 有关每个步骤的详细信息，请参阅流程图后面的表。

![显示调试连接器步骤的流程图](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| 步骤 | 操作 | 描述 |
|---------|---------|---------|
|1 | 查找分配给应用的连接器组 | 你可能在多台服务器上安装了连接器，在这种情况下，应将连接器[分配给连接器组](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。 有关连接器组的详细信息，请参阅[使用连接器组在单独的网络和位置上发布应用程序](application-proxy-connector-groups.md)。 |
|2 | 安装连接器并分配组 | 如果尚未安装连接器，请参阅[安装和注册连接器](application-proxy-add-on-premises-application.md#install-and-register-a-connector)。<br></br> 如果在安装连接器时遇到问题，请参阅[安装连接器时出现问题](application-proxy-connector-installation-problem.md)。<br></br> 如果未将连接器分配给组，请参阅[将连接器分配给组](application-proxy-connector-groups.md#create-connector-groups)。<br></br>如果未将应用程序分配到连接器组，请参阅[将应用程序分配到连接器组](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups)。|
|3 | 在连接器服务器上运行端口测试 | 在连接器服务器上，使用 [telnet](/windows-server/administration/windows-commands/telnet) 或其他端口测试工具运行端口测试，以检查端口 [443 和 80 是否已打开](application-proxy-add-on-premises-application.md#open-ports)。|
|4 | 配置域和端口 | [请确保正确配置了你的域和端口](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) 为了让连接器正常工作，必须打开某些端口，并且必须是服务器必须能够访问的 URL。 |
|5 | 检查后端代理是否正在使用中 | 检查连接器是正在使用后端代理服务器还是绕过了这些服务器。 有关详细信息，请参阅[排查连接器代理问题和服务连接问题](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues)。 |
|6 | 更新连接器和更新程序以使用后端代理 | 如果正在使用后端代理，则需要确保连接器使用相同的代理。 有关故障排除和配置连接器以与代理服务器一起使用的详细信息，请参阅[使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
|7 | 在连接器服务器上加载应用的内部 URL | 在连接器服务器上，加载应用的内部 URL。 |
|8 | 检查内部网络连接 | 内部网络存在此调试流无法诊断的连接问题。 应用程序必须在内部进行访问，连接器才能工作。 可以按[应用程序代理连接器](application-proxy-connectors.md#under-the-hood)中所述来启用和查看连接器事件日志。 |
|9 | 延长后端的超时值 | 在应用程序的“其他设置”中，将“后端应用程序超时”设置更改为“长”  。 请参阅[将本地应用添加到 Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。 |
|10 | 如果问题仍然存在，则针对特定的流问题，查看应用和 SSO 调试流程 | 使用[调试应用程序代理应用程序问题](application-proxy-debug-apps.md)诊断流程。 |

## <a name="next-steps"></a>后续步骤


* [使用连接器组在单独的网络和位置上发布应用程序。](application-proxy-connector-groups.md)
* [使用现有的本地代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)
* [对应用程序代理和连接器错误进行故障排除](application-proxy-troubleshoot.md)
* [如何以无提示方式安装 Azure AD 应用程序代理连接器](application-proxy-register-connector-powershell.md)