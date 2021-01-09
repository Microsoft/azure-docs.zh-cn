---
title: 支持的 Microsoft Graph 操作
titleSuffix: Azure AD B2C
description: 支持的用于管理 Azure AD B2C 资源（包括用户、用户流、标识提供者、自定义策略、策略密钥，等等）的 Microsoft Graph 操作的索引。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: fed1e31380381b864530b3fa0b9e8c0886737d04
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033602"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>可用于 Azure AD B2C 的 Microsoft Graph 操作

支持用于管理 Azure AD B2C 资源（包括用户、标识提供者、用户流、自定义策略和策略密钥）的下列 Microsoft Graph API 操作。

以下各部分中的每个链接对应于 Microsoft Graph API 参考中该操作的相应页面。

## <a name="user-management"></a>用户管理

- [列出用户](/graph/api/user-list)
- [创建使用者用户](/graph/api/user-post-users)
- [获取用户](/graph/api/user-get)
- [更新用户](/graph/api/user-update)
- [删除用户](/graph/api/user-delete)

有关使用 Microsoft Graph API 管理 Azure AD B2C 用户帐户的详细信息，请参阅[使用 Microsoft Graph 管理 Azure AD B2C 用户帐户](manage-user-accounts-graph-api.md)。

## <a name="user-phone-number-management"></a>用户电话号码管理

- [添加](/graph/api/authentication-post-phonemethods)
- [Get](/graph/api/b2cauthenticationmethodspolicy-get)
- [更新](/graph/api/b2cauthenticationmethodspolicy-update)
- [删除](/graph/api/phoneauthenticationmethod-delete)

有关使用 Microsoft Graph API 管理用户登录电话号码的详细信息，请参阅 [B2C 身份验证方法](/graph/api/resources/b2cauthenticationmethodspolicy)。

## <a name="identity-providers-user-flow"></a>标识提供者（用户流）

管理 Azure AD B2C 租户中的用户流可用的标识提供者。

- [列出 Azure AD B2C 租户中注册的标识提供者](/graph/api/identityprovider-list)
- [创建标识提供者](/graph/api/identityprovider-post-identityproviders)
- [获取标识提供者](/graph/api/identityprovider-get)
- [更新标识提供者](/graph/api/identityprovider-update)
- [删除标识提供者](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>用户流

配置用于注册、登录、组合式注册和登录、密码重置和配置文件更新的预先生成的策略。

- [列出用户流](/graph/api/identitycontainer-list-b2cuserflows)
- [创建用户流](/graph/api/identitycontainer-post-b2cuserflows)
- [获取用户流](/graph/api/b2cidentityuserflow-get)
- [删除用户流](/graph/api/b2cidentityuserflow-delete)

## <a name="custom-policies"></a>自定义策略

下列操作可用于管理你的 Azure AD B2C 信任框架策略（称为[自定义策略](custom-policy-overview.md)）。

- [列出在租户中配置的所有信任框架策略](/graph/api/trustframework-list-trustframeworkpolicies)
- [创建信任框架策略](/graph/api/trustframework-post-trustframeworkpolicy)
- [读取现有信任框架策略的属性](/graph/api/trustframeworkpolicy-get)
- [更新或创建信任框架策略](/graph/api/trustframework-put-trustframeworkpolicy)
- [删除现有信任框架策略](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>策略密钥

Identity Experience Framework 存储着自定义策略中引用的机密，以在组件之间建立信任关系。 这些机密可以是对称或非对称密钥/值。 在 Azure 门户中，这些实体显示为 **策略密钥**。

Microsoft Graph API 中策略密钥的顶层资源是[信任的框架密钥集](/graph/api/resources/trustframeworkkeyset)。 每个 **密钥集** 都包含至少一个 **密钥**。 若要创建密钥，请先创建一个空的密钥集，然后在密钥集中生成一个密钥。 你可以创建手动机密、上传证书或 PKCS12 密钥。 密钥可以是生成的机密、你定义的字符串 (如 Facebook 应用程序机密) 或上传的证书。 如果密钥集具有多个密钥，则只有其中一个密钥处于活动状态。

### <a name="trust-framework-policy-keyset"></a>信任框架策略密钥集

- [列出信任框架密钥集](/graph/api/trustframework-list-keysets)
- [创建信任框架密钥集](/graph/api/trustframework-post-keysets)
- [获取密钥集](/graph/api/trustframeworkkeyset-get)
- [更新信任框架密钥集](/graph/api/trustframeworkkeyset-update)
- [删除信任框架密钥集](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>信任框架策略密钥

- [获取密钥集中当前处于活动状态的密钥](/graph/api/trustframeworkkeyset-getactivekey)
- [在密钥集中生成密钥](/graph/api/trustframeworkkeyset-generatekey)
- [上传基于字符串的机密](/graph/api/trustframeworkkeyset-uploadsecret)
- [上传 X.509 证书](/graph/api/trustframeworkkeyset-uploadcertificate)
- [上传 PKCS12 格式的证书](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>应用程序

- [列出应用程序](/graph/api/application-list)
- [创建应用程序](/graph/api/resources/application)
- [更新应用程序](/graph/api/application-update)
- [创建 ServicePrincipal](/graph/api/resources/serviceprincipal)
- [创建 oauth2Permission 授权](/graph/api/resources/oauth2permissiongrant)
- [删除应用程序](/graph/api/application-delete)

## <a name="application-extension-properties"></a>应用程序扩展属性

- [列出扩展属性](/graph/api/application-list-extensionproperty)

Azure AD B2C 提供一个目录，其中可以保存每个用户的 100 个自定义属性。 对于用户流，将 [使用 Azure 门户管理](user-flow-custom-attributes.md)这些扩展属性。 对于自定义策略，Azure AD B2C 会为你创建属性，这是首次向扩展属性写入值。

## <a name="audit-logs"></a>审核日志

- [列出审核日志](/graph/api/directoryaudit-list)

有关通过 Microsoft Graph API 访问 Azure AD B2C 审核日志的详细信息，请参阅 [访问 Azure AD B2C 审核日志](view-audit-logs.md)。