---
title: 欧洲客户的标识数据存储 - Azure AD
description: 了解 MAzure Active Directory 在哪个位置存储其欧洲客户的标识相关数据。
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836931"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Azure Active Directory 中的欧洲客户标识数据存储
Azure AD 根据组织在订阅 Microsoft 联机服务（如 Microsoft 365 和 Azure）时提供的地址，将标识数据存储在相关地理位置。 有关标识数据存储位置的信息，可以使用“Microsoft 信任中心”的“[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)”部分。

对于在欧洲提供地址的客户，Azure AD 将大部分标识数据都保留在欧洲数据中心之内。 本文档提供有关 Azure AD 服务存储在欧洲之外的任何数据的信息。

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD 多重身份验证

对于基于云的 Azure AD 多重身份验证，在最接近用户的数据中心完成身份验证。 北美、欧洲和亚太存在用于进行 Azure AD 多重身份验证的数据中心。

* 使用电话进行的多重身份验证通常源自美国数据中心，由全球提供商进行路由。
* 使用 SMS 的多重身份验证由全球提供商进行路由。
* 源自欧洲数据中心的使用 Microsoft Authenticator 应用推送通知的多重身份验证请求，在欧洲数据中心处理。
    * 设备供应商特定的服务（如 Apple 推送通知）可能在欧洲之外处理。
* 源自欧洲数据中心的使用 OATH 代码的多重身份验证请求，在欧洲进行验证。

要详细了解 Azure 多重身份验证服务器（MFA 服务器）和基于云的 Azure AD MFA 收集的用户信息，请参阅 [Azure 多重身份验证用户数据收集](../authentication/howto-mfa-reporting-datacollection.md)。

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>适用于企业应用程序的基于密码的单一登录
 
如果客户创建了一个新的企业应用程序（无论通过 Azure AD 库或非库）并启用了基于密码的 SSO，则应用程序登录 URL 和自定义捕获登录字段将存储在美国。 有关此功能的详细信息，请参阅[配置基于密码的单一登录](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C 策略配置数据和密钥容器存储在美国数据中心。 它们不包含任何用户个人数据。 有关策略配置的详细信息，请参阅 [Azure Active Directory B2C：内置策略](../../active-directory-b2c/user-flow-overview.md)一文。

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B 在美国数据中心存储邀请及兑换链接，并在美国数据中心重定向 URL 信息。 此外，取消订阅接收 B2B 邀请的用户的电子邮件地址也存储在美国数据中心。

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory 域服务 (Azure AD DS)

Azure AD DS 将用户数据存储在客户选择的 Azure 虚拟网络所在的同一位置。 因此，如果该网络位于欧洲外部，则会复制数据并将其存储在欧洲外部。

## <a name="federation-in-microsoft-exchange-server-2013"></a>Microsoft Exchange Server 2013 中的联合身份验证
    
- 应用程序标识符 (AppID) - Azure Active Directory 身份验证系统生成的唯一编号，用于标识 Exchange 组织。
- 应用程序的已批准联合域列表
- 应用程序的令牌签名公钥 

有关 Microsoft Exchange 服务器中联合身份验证的详细信息，请参阅《[联合身份验证：Exchange 2013 帮助](/exchange/federation-exchange-2013-help)》一文。


## <a name="other-considerations"></a>其他注意事项

与 Azure AD 集成的服务和应用程序有权访问标识数据。 评估使用的每项服务和应用程序，以确定该特定服务和应用程序对标识数据的处理方式，以及它们是否符合公司的数据存储要求。

有关 Microsoft 服务的数据存放的详细信息，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

## <a name="next-steps"></a>后续步骤
有关上述任何功能的详细信息，请参阅以下文章：
- [什么是多重身份验证？](../authentication/concept-mfa-howitworks.md)

- [Azure AD 自助密码重置](../authentication/concept-sspr-howitworks.md)

- [什么是 Azure Active Directory B2C？](../../active-directory-b2c/overview.md)

- [什么是 Azure AD B2B 协作？](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) 域服务](../../active-directory-domain-services/overview.md)