---
title: 为 Azure NetApp 文件创建和管理 Active Directory 连接 |Microsoft Docs
description: 本文介绍如何创建和管理 Azure NetApp 文件 Active Directory 连接。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 756bf1cd7a7e9435130a3ad2d3b530b7f2e5b1b4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100605887"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>为 Azure NetApp 文件创建和管理 Active Directory 连接

Azure NetApp 文件的几项功能需要 Active Directory 连接。  例如，在创建 [SMB 卷](azure-netapp-files-create-volumes-smb.md) 或 [双协议卷](create-volumes-dual-protocol.md)之前，需要具有 Active Directory 的连接。  本文介绍如何创建和管理 Azure NetApp 文件 Active Directory 连接。

## <a name="before-you-begin"></a>开始之前  

必须已设置容量池。   
[设置容量池](azure-netapp-files-set-up-capacity-pool.md)   
子网必须委派给 Azure NetApp 文件。  
[将子网委派给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 连接的要求

 Active Directory 连接的要求如下： 

* 你使用的管理帐户必须能够在你将指定的组织单位 (OU) 路径中创建计算机帐户。  

* 必须在适用的 Windows Active Directory (AD) 服务器上打开正确的端口。  
    所需的端口如下： 

    |     服务           |     端口     |     协议     |
    |-----------------------|--------------|------------------|
    |    AD Web 服务    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    空值       |    回显回复    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 名称       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* 目标 Active Directory 域服务的站点拓扑必须遵循指导原则，特别是部署 Azure NetApp 文件的 Azure VNet。  

    必须将部署了 Azure NetApp 文件的虚拟网络的地址空间添加到新的或现有的 Active Directory 站点（其中可由 Azure NetApp 文件访问的域控制器）。 

* 指定的 DNS 服务器必须可以从 Azure NetApp 文件的[委派子网](./azure-netapp-files-delegate-subnet.md)访问。  

    有关支持的网络拓扑，请参阅 [Azure NetApp 文件网络规划指南](./azure-netapp-files-network-topologies.md)。

    网络安全组 (NSG) 和防火墙必须具有适当配置的规则，以允许 Active Directory 和 DNS 流量请求。 

* Azure NetApp 文件委派子网必须能够访问域中的所有 Active Directory 域服务 (ADDS) 域控制器，包括所有本地和远程域控制器。 否则，可能会发生服务中断。  

    如果你的域控制器无法通过 Azure NetApp 文件委派子网访问，则可以在创建 Active Directory 连接期间指定 Active Directory 站点。  Azure NetApp 文件只需要与 Azure NetApp 文件委派子网地址空间所在站点中的域控制器通信。

    关于 AD 站点和服务，请参阅[设计站点拓扑](/windows-server/identity/ad-ds/plan/designing-the-site-topology)。 
    
* 可以通过选中 "[联接 Active Directory](#create-an-active-directory-connection) " 窗口中的 " **aes 加密**" 框为 AD 身份验证启用 aes 加密。 Azure NetApp 文件支持从最低到最安全的)  (DES、Kerberos AES 128 和 Kerberos AES 256 加密类型。 如果启用 AES 加密，则用于联接 Active Directory 的用户凭据必须具有与为 Active Directory 启用的功能匹配的最高对应帐户选项。    

    例如，如果 Active Directory 只有 AES-128 功能，则必须为用户凭据启用 AES-128 帐户选项。 如果 Active Directory 具有 256 AES 功能，则必须启用 "AES-256 帐户" 选项，该选项也支持 AES 128)  (。 如果 Active Directory 没有任何 Kerberos 加密功能，则默认情况下，Azure NetApp 文件使用 DES。  

    你可以在 Active Directory 用户和计算机 "的" 属性 "" 用户和计算机 " (MMC) 中启用帐户选项：   

    ![Active Directory 用户和计算机 MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp 文件支持 [ldap 签名](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server)，这使得可以在 Azure NetApp 文件服务与目标 [Active Directory 域控制器](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)之间安全传输 ldap 通信。 如果遵循 Microsoft [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) for LDAP 签名指南，则应通过选中 "[联接 Active Directory](#create-an-active-directory-connection) " 窗口中的 " **ldap 签名**" 框，在 Azure NetApp 文件中启用 ldap 签名功能。 

    仅限[LDAP 通道绑定](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry)配置对 Azure NetApp 文件服务没有影响。 但是，如果同时使用 LDAP 通道绑定和安全 LDAP (例如，LDAPS 或 `start_tls`) ，则 SMB 卷的创建将失败。

## <a name="decide-which-domain-services-to-use"></a>确定要使用的域服务 

Azure NetApp 文件支持用于 AD 连接的 [Active Directory 域服务](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) 和 Active Directory 域服务 (AADDS)。  在创建 AD 连接之前，需要决定是使用 ADDS 还是 AADDS。  

有关更多详细信息，请参阅[自我管理型 Azure Active Directory 域服务、Azure Active Directory 和托管型 Azure Active Directory 域服务的比较](../active-directory-domain-services/compare-identity-solutions.md)。 

### <a name="active-directory-domain-services"></a>Active Directory 域服务

你可以为 Azure NetApp 文件使用首选的 [Active Directory 站点和服务](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)范围。 此选项允许读取和写入[可由 Azure NetApp 文件访问的](azure-netapp-files-network-topologies.md) Active Directory 域服务 (ADDS) 域控制器。 它还阻止服务与不在指定的 Active Directory 站点和服务站点中的域控制器通信。 

若要在使用 ADDS 时查找站点名称，可以与组织中负责 Active Directory 域服务的管理组联系。 以下示例显示了显示站点名称的 Active Directory 站点和服务插件： 

![Active Directory 站点和服务](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

为 Azure NetApp 文件配置 AD 连接时，在“AD 站点名称”字段的范围中指定站点名称。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务 

有关 Azure Active Directory 域服务 (AADDS) 配置和指导，请参阅 [Azure AD 域服务文档](../active-directory-domain-services/index.yml)。

其他适用于 Azure NetApp 文件的 AADDS 注意事项： 

* 确保部署 AADDS 的 VNet 或子网与 Azure NetApp 文件部署位于同一 Azure 区域中。
* 如果在部署 Azure NetApp 文件的区域中使用另一个 VNet，则应在两个 VNet 之间创建对等互连。
* Azure NetApp 文件支持 `user` 和 `resource forest` 类型。
* 对于同步类型，可以选择 `All` 或 `Scoped`。   
    如果选择 `Scoped`，请确保选择了正确的 Azure AD 组以访问 SMB 共享。  如果不确定，可以使用 `All` 同步类型。
* 需要使用 Enterprise 或 Premium SKU。 标准 SKU 不受支持。

创建Active Directory 连接时，请注意 AADDS 的以下具体信息：

* 可以在 AADDS 菜单中找到“主要 DNS”、“辅助 DNS”和“AD DNS 域名”的信息  。  
对于 DNS 服务器，将使用两个 IP 地址来配置 Active Directory 连接。 
* “组织单位路径”是 `OU=AADDC Computers`。  
此设置是在“NetApp 帐户”下的“Active Directory 连接”中配置的 ：

  ![组织单位路径](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* “用户名”凭据可以是任何属于 Azure AD 组“Azure AD DC 管理员”的用户 。


## <a name="create-an-active-directory-connection"></a>创建 Active Directory 连接器

1. 在 NetApp 帐户中，单击“Active Directory 连接”，然后单击“联接” 。  

    ![Active Directory 连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 在“联接 Active Directory”窗口中，根据要使用的域服务提供以下信息：  

    有关特定于所使用的域服务的信息，请参阅[确定要使用的域服务](#decide-which-domain-services-to-use)。 

    * **主要 DNS**  
        这是 Active Directory 域联接和 SMB 身份验证操作所需的 DNS。 
    * **辅助 DNS**   
        这是用于确保冗余名称服务的辅助 DNS 服务器。 
    * **AD DNS 域名**  
        这是你要联接的 Active Directory 域服务的域名。
    * **AD 站点名称**  
        这是域控制器发现将被限制到的站点名称。 这应与 Active Directory 站点和服务中的站点名称匹配。
    * **SMB 服务器(计算机帐户)前缀**  
        这是 Active Directory 中计算机帐户的命名前缀，Azure NetApp 文件将用于创建新帐户。

        例如，如果你的组织用于文件服务器的命名标准是 NAS-01、NAS-02…、NAS-045，则你可以输入“NAS”作为前缀。 

        该服务将根据需要在 Active Directory 中创建其他计算机帐户。

        > [!IMPORTANT] 
        > 创建 Active Directory 连接后重命名 SMB 服务器前缀会造成中断。 重命名 SMB 服务器前缀后，需要重新装载现有 SMB 共享。

    * **组织单位路径**  
        这是将在其中创建 SMB 服务器计算机帐户的组织单元 (OU) 的 LDAP 路径。 即 OU = 第二级，OU = 第一级。 

        如果将 Azure NetApp 文件与 Azure Active Directory 域服务一起使用，则在为 NetApp 帐户配置 Active Directory 时，组织单位路径为 `OU=AADDC Computers`。

        ![加入 Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES 加密**   
        如果要为 SMB 卷启用 AES 加密，请选中此复选框。 请参阅要求 [Active Directory 连接的要求](#requirements-for-active-directory-connections) 。 

        ![Active Directory AES 加密](../media/azure-netapp-files/active-directory-aes-encryption.png)

        **AES 加密** 功能目前处于预览阶段。 如果这是你第一次使用此功能，请在使用此功能前注册它： 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        检查功能注册的状态： 

        > [!NOTE]
        > 在将 `Registering` 更改为之前，RegistrationState 的状态可能最长为60分钟 `Registered` 。 请等到状态为， `Registered` 然后再继续。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        你还可以使用 [Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 并 `az feature show` 注册功能并显示注册状态。 

    * **LDAP 签名**   
        选中此复选框可启用 LDAP 签名。 此功能在 Azure NetApp 文件服务和用户指定的 [Active Directory 域服务域控制器](/windows/win32/ad/active-directory-domain-services)之间启用安全 LDAP 查找。 有关详细信息，请参阅 [ADV190023 |用于启用 LDAP 通道绑定和 LDAP 签名的 Microsoft 指导](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023)。  

        ![Active Directory LDAP 签名](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        **LDAP 签名** 功能目前处于预览阶段。 如果这是你第一次使用此功能，请在使用此功能前注册它： 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        检查功能注册的状态： 

        > [!NOTE]
        > 在将 `Registering` 更改为之前，RegistrationState 的状态可能最长为60分钟 `Registered` 。 请等到状态为， `Registered` 然后再继续。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        你还可以使用 [Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 并 `az feature show` 注册功能并显示注册状态。 

     * **备份策略用户**  
        你可以包含其他帐户，这些帐户要求对创建用于 Azure NetApp 文件的计算机帐户具有提升的权限。 将允许指定的帐户在文件或文件夹级别更改 NTFS 权限。 例如，你可以指定一个非特权服务帐户，用于将数据迁移到 Azure NetApp 文件中的 SMB 文件共享。  

        ![Active Directory 备份策略用户](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        **备份策略用户** 功能目前处于预览阶段。 如果这是你第一次使用此功能，请在使用此功能前注册它： 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        检查功能注册的状态： 

        > [!NOTE]
        > 在将 `Registering` 更改为之前，RegistrationState 的状态可能最长为60分钟 `Registered` 。 请等到状态为， `Registered` 然后再继续。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        你还可以使用 [Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 并 `az feature show` 注册功能并显示注册状态。 

    * 凭证，包括“用户名”和“密码” 

        ![Active Directory 凭据](../media/azure-netapp-files/active-directory-credentials.png)

3. 单击“加入” 。  

    将显示你创建的 Active Directory 连接。

    ![已创建 Active Directory 连接](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>后续步骤  

* [创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)
* [创建双协议卷](create-volumes-dual-protocol.md)
* [配置 NFSv 4.1 Kerberos 加密](configure-kerberos-encryption.md)
* [使用 Azure CLI 安装新的 Active Directory 林](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
