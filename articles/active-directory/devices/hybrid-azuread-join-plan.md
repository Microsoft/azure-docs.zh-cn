---
title: 规划混合 Azure Active Directory 联接-Azure Active Directory
description: 了解如何配置联接到混合 Azure Active Directory 的设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f13dfa4221f8f09c24cce3a451f3180d15ee3b99
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435751"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：规划混合 Azure Active Directory 加入的实施

与用户类似，设备是要保护的，并且随时随时要用来保护资源的另一个核心标识。 若要实现此目的，可以使用下述某种方法将设备标识引入 Azure AD 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用 [条件性访问](../conditional-access/overview.md)来保护对云和本地资源的访问。

如果你有 Active Directory (AD) 环境的本地，并且想要将已加入 AD 域的计算机加入 Azure AD，则可以通过混合 Azure AD 联接来完成此操作。 本文提供了在环境中实现混合 Azure AD 加入的相关步骤。 

## <a name="prerequisites"></a>先决条件

本文假设你熟悉 [Azure Active Directory 中设备标识管理的简介](./overview.md)。

> [!NOTE]
> Windows 10 混合 Azure AD 联接所需的最少域控制器版本为 Windows Server 2008 R2。

## <a name="plan-your-implementation"></a>规划实施

若要规划混合 Azure AD 实现，应做好以下准备：

> [!div class="checklist"]
> - 查看支持的设备
> - 查看应该知道的事项
> - 查看混合 Azure AD 联接的受控验证
> - 基于标识基础结构选择方案
> - 查看本地 AD UPN 支持混合 Azure AD 联接

## <a name="review-supported-devices"></a>查看支持的设备

混合 Azure AD 加入支持多种 Windows 设备。 由于运行旧版 Windows 的设备的配置需要额外或不同的步骤，支持的设备划分为两个类别：

### <a name="windows-current-devices"></a>Windows 当前设备

- Windows 10
- Windows Server 2016
  - **注意**： Azure 国内云客户需要1803版
- Windows Server 2019

对于运行 Windows 桌面操作系统的设备，受支持的版本将在 [Windows 10 版本信息](/windows/release-information/)中列出。 最佳做法是，Microsoft 建议升级到最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>Windows 下层设备

- Windows 8.1
- Windows 7 支持已于 2020 年 1 月 14 日结束。 有关详细信息，请参阅对 [Windows 7 的支持已结束](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)。
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2。 有关 Windows Server 2008 和 2008 R2 的支持信息，请参阅 [准备 Windows server 2008 支持结束](https://www.microsoft.com/cloud-platform/windows-server-2008)。

第一个规划步骤是审查环境，并确定是否需要支持 Windows 下层设备。

## <a name="review-things-you-should-know"></a>查看应该知道的事项

### <a name="unsupported-scenarios"></a>不支持的方案
- 如果你的环境包含将标识数据同步到多个 Azure AD 租户的单个 AD 林，则当前不支持混合 Azure AD 联接。

- 运行域控制器 (DC) 角色的 Windows Server 不支持混合 Azure AD 联接。

- 使用凭据漫游或用户配置文件漫游或强制配置文件时，在 Windows 下层设备上不支持混合 Azure AD 联接。

- 服务器核心操作系统不支持任何类型的设备注册。

### <a name="os-imaging-considerations"></a>OS 映像注意事项
- 如果你依赖于系统准备工具 (Sysprep) ，并且你使用 **Windows 之前的 Windows 10 1809** 映像进行安装，请确保映像不是从已注册 Azure AD 作为混合 Azure AD 联接的设备进行的。

- 如果依赖于虚拟机 (VM) 快照来创建其他 Vm，请确保快照不是从已注册 Azure AD 作为混合 Azure AD 联接的 VM 使用。

- 如果使用 [统一写入筛选器](/windows-hardware/customize/enterprise/unified-write-filter) 和类似的技术，在重新启动时清除对磁盘的更改，则必须在设备混合 Azure AD 加入设备后应用。 在完成混合 Azure AD 联接之前启用此类技术将导致设备在每次重新启动时进行脱离

### <a name="handling-devices-with-azure-ad-registered-state"></a>处理 Azure AD 注册状态的设备
如果已加入 Windows 10 域的设备 [Azure AD 注册](overview.md#getting-devices-in-azure-ad) 到你的租户，则可能会导致混合 Azure AD 加入和 Azure AD 注册设备的双重状态。 建议升级到) 或更高版本应用了 KB4489894 的 Windows 10 1803 (来自动处理此方案。 在1803之前的版本中，你将需要手动删除 Azure AD 注册状态，然后才能启用混合 Azure AD join。 在1803及更高版本中，已进行了以下更改，以避免这种双重状态：

- 在 <i>设备混合 Azure AD 加入并且同一用户登录后</i>，将自动删除用户的任何现有 Azure AD 注册状态。 例如，如果用户 A 在设备上有 Azure AD 的注册状态，则只有当用户 A 登录到设备时，才会清除用户 A 的双重状态。 如果同一设备上有多个用户，则当用户登录时，会单独清除双重状态。 除了删除 Azure AD 注册状态，Windows 10 还会从 Intune 或其他 MDM 取消注册该设备，前提是该注册是通过自动注册在 Azure AD 注册过程中进行的。
- 可以通过将以下注册表值添加到 HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin 来阻止已加入域的设备 Azure AD 注册： "BlockAADWorkplaceJoin" = dword：00000001。
- 在 Windows 10 1803 中，如果已配置 Windows Hello 企业版，则用户需要在双重状态清理后重新设置 Windows Hello 企业版。此问题已通过 KB4512509 解决。

> [!NOTE]
> 即使 Windows 10 会在本地自动删除 Azure AD 的注册状态，如果 Azure AD 中的设备对象由 Intune 管理，则不会立即将其删除。 可以通过运行 dsregcmd.exe/status 来验证 Azure AD 已注册状态的删除，并考虑设备不会根据该设备注册 Azure AD。

### <a name="additional-considerations"></a>其他注意事项
- 如果你的环境使用 (VDI) 的虚拟桌面基础结构，请参阅 [设备标识和桌面虚拟化](./howto-device-identity-virtual-desktop-infrastructure.md)。

- 对于符合 FIPS 的 TPM 2.0，支持混合 Azure AD 联接，不适用于 TPM 1.2。 如果设备具有符合 FIPS 标准的 TPM 1.2，则必须先将其禁用，然后才能继续混合 Azure AD 联接。 Microsoft 不提供任何工具用于为 Tpm 禁用 FIPS 模式，因为它依赖于 TPM 制造商。 请联系你的硬件 OEM 以获得支持。 

- 从 Windows 10 1903 版本开始，Tpm 1.2 不与混合 Azure AD 联接一起使用，具有这些 Tpm 的设备将视为没有 TPM。

- UPN 更改仅受 Windows 10 2004 更新的支持。 对于 Windows 10 2004 更新之前的设备，用户将在其设备上出现 SSO 和条件性访问问题。 若要解决此问题，需要将设备从 Azure AD 中脱离 (以提升的权限运行 "dsregcmd.exe/leave") ，并自动重新加入 () 。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>查看混合 Azure AD 联接的受控验证

当所有先决条件都准备就绪后，Windows 设备将自动作为 Azure AD 租户中的设备进行注册。 Azure AD 中这些设备标识的状态称为 "混合 Azure AD 联接"。 有关本文中所述概念的详细信息，请参阅 [Azure Active Directory 中的设备标识管理简介](overview.md)。

在整个组织中同时启用混合 Azure AD 联接之前，组织可能需要对其进行控制验证。 查看对 [混合 Azure AD 联接的受控验证一](hybrid-azuread-join-control.md) 文，了解如何实现它。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>基于标识基础结构选择方案

混合 Azure AD 联接适用于、托管和联合环境，具体取决于 UPN 是可路由还是不可路由。 有关支持的方案，请参阅表的底部。  

### <a name="managed-environment"></a>托管环境

可使用[无缝单一登录](../hybrid/how-to-connect-sso.md)通过[密码哈希同步 (PHS)](../hybrid/whatis-phs.md) 或[直通身份验证 (PTA)](../hybrid/how-to-connect-pta.md) 来部署托管环境。

这些方案不需要配置联合服务器进行身份验证。

> [!NOTE]
> 仅支持[使用分阶段推出的云身份验证](../hybrid/how-to-connect-staged-rollout.md)启动 Windows 10 1903 更新

### <a name="federated-environment"></a>联合环境

联合环境应具有支持以下要求的标识提供者。 如果已有使用 Active Directory 联合身份验证服务 (AD FS) 的联合环境，则已经支持以下要求。

- **WIAORMULTIAUTHN 声明：** 此声明是为 Windows 下层设备执行混合Azure AD 加入所必需的。
- **WS-Trust 协议：** 使用 Azure AD 对当前已加入混合 Azure AD 的 Windows 设备进行身份验证时需要此协议。 使用 AD FS 时，需要启用以下 WS-Trust 终结点：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 只能作为面向 Intranet 的终结点启用，不能通过 Web 应用程序代理作为面向 Extranet 的终结点公开。 若要详细了解如何禁用 WS-Trust Windows 终结点，请参阅[在代理上禁用 WS-Trust Windows 终结点](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 可以通过 AD FS 管理控制台中的“服务” > “终结点”查看已启用哪些终结点。

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

从版本 1.1.819.0 开始，Azure AD Connect 提供了配置混合 Azure AD 联接的向导。 该向导可让你显著简化配置过程。 如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置设备注册](hybrid-azuread-join-manual.md)。 

根据与标识基础结构匹配的方案，请参阅：

- [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>查看本地 AD 用户的 UPN 支持混合 Azure AD 联接

有时，本地 AD 用户 Upn 可能与 Azure AD Upn 有所不同。 在此类情况下，Windows 10 混合 Azure AD 加入根据[身份验证方法](../hybrid/choose-ad-authn.md)、域类型和 Windows 10 版本对本地 AD UPN 提供有限支持。 环境中可以存在两种类型的本地 AD UPN：

- 可路由用户 UPN：可路由的 UPN 具有已向域注册机构注册的有效验证域。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.org 是 Contoso 拥有的且[已在 Azure AD 中验证](../fundamentals/add-custom-domain.md)的本地 AD 中的主域
- 不可路由的用户 UPN：不可路由的 UPN 没有经过验证的域。 它仅在组织的专用网络内适用。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.local 是本地 AD 中的主域，但在 Internet 中不是可验证的域，且仅可在 Contoso 的网络内使用。

> [!NOTE]
> 本部分中的信息仅适用于本地用户 UPN。 它不适用于本地计算机域后缀 (例如： computer1) 。

下表提供了 Windows 10 混合 Azure AD 加入中对这些本地 AD UPN 的支持情况的详细信息

| 本地 AD UPN 类型 | 域类型 | Windows 10 版本 | 说明 |
| ----- | ----- | ----- | ----- |
| 可路由的 | 联合 | 从 1703 版本开始 | 正式发布 |
| 非可路由的 | 联合 | 从 1803 版本开始 | 正式发布 |
| 可路由的 | Managed | 从 1803 版本开始 | Azure AD 不支持 Windows 锁屏上的 SSPR |
| 非可路由的 | Managed | 不支持 | |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为联合环境](hybrid-azuread-join-federated-domains.md) 
>  配置混合 Azure Active Directory 联接[为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
