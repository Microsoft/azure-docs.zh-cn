---
title: Azure MFA 服务器与第三方 VPN - Azure Active Directory
description: 将 Azure MFA 服务器与 Cisco、Citrix 和 Juniper 集成的分步配置指南。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88fe09199cb50d2a3796c3b638dca1a723016dc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742011"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Azure MFA 服务器和第三方 VPN 解决方案的高级场景

Azure 多重身份验证服务器（Azure MFA 服务器）可用于与各种第三方 VPN 解决方案无缝连接。 本文重点介绍 Cisco&reg; ASA VPN 设备、Citrix NetScaler SSL VPN 设备和 Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN 设备。 我们创建了配置指南来处理这三个常用设备。 Azure MFA 服务器还可与将 RADIUS、LDAP、IIS 或基于声明的身份验证用于 AD FS 的大多数其他系统集成。 可以在 [Azure MFA 服务器配置](howto-mfaserver-deploy.md#next-steps)中找到更多详细信息。

> [!IMPORTANT]
> 从 2019 年 7 月 1 日开始，Microsoft 不再为新部署提供 MFA 服务器。 如果新客户希望在登录事件期间要求进行多重身份验证 (MFA)，则应使用基于云的 Azure AD 多重身份验证。
>
> 若要开始进行基于云的 MFA，请参阅[教程：使用 Azure AD 多重身份验证保护用户登录事件](tutorial-enable-azure-mfa.md)。
>
> 如果使用基于云的 MFA，请参阅[将 VPN 基础结构与 Azure MFA 集成](howto-mfa-nps-extension-vpn.md)。
>
> 在 2019 年 7 月 1 日之前激活了 MFA 服务器的现有客户可以像平时一样下载最新版本、将来的更新以及生成激活凭据。

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN 设备和 Azure MFA 服务器
Azure MFA 服务器可以与 Cisco&reg; ASA VPN 设备集成，以便为 Cisco AnyConnect&reg; VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Cisco ASA VPN 设备与 Azure MFA 集成 |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Cisco ASA VPN 设备与 Azure MFA 集成 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN 与 Azure MFA 服务器
Azure MFA 服务器可以与 Citrix NetScaler SSL VPN 设备集成，以便为 Citrix NetScaler SSL VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Citrix NetScaler SSL VPN 与 Azure MFA 设备集成 |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Citrix NetScaler SSL VPN 设备与 Azure MFA 集成 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN 设备与 Azure MFA 服务器
Azure MFA 服务器可以与 Juniper/Pulse Secure SSL VPN 设备集成，以便为 Juniper/Pulse Secure SSL VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Juniper/Pulse Secure SSL VPN 与 Azure MFA 设备集成 |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Juniper/Pulse Secure SSL VPN 设备与 Azure MFA 集成 |

## <a name="next-steps"></a>后续步骤

- [通过适用于 Azure 多重身份验证的 NPS 扩展增强现有的身份验证基础结构](howto-mfa-nps-extension.md)

- [配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)
