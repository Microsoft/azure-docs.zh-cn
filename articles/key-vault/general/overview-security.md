---
title: Azure Key Vault 安全性
description: 管理 Azure Key Vault、密钥和机密的访问权限。 介绍 Key Vault 的身份验证和授权模型以及如何保护 Key Vault。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: dc08df7390285f9b6e4701bb1ca5c4227b19f1da
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445024"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 安全性

使用 Azure Key Vault 可以保护云中的加密密钥和机密，例如证书、连接字符串和密码。 存储敏感数据和关键业务数据时，需要采取措施来最大限度地提高保管库及其存储的数据的安全性。

## <a name="identity-and-access-management"></a>标识和访问管理

在 Azure 订阅中创建密钥保管库时，该密钥保管库自动与订阅的 Azure AD 租户关联。 尝试管理或检索保管库内容的任何人都必须通过 Azure AD 进行身份验证。

- 身份验证可确定调用方的身份。
- 授权可确定调用方能够执行的操作。 Key Vault 中的授权结合使用 [azure 基于角色的访问控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 和 Azure Key Vault 访问策略。

### <a name="access-model-overview"></a>访问模型概述

可以通过两个接口或平面访问保管库。 这些平面为管理平面和数据平面。

- 管理平面是管理 Key Vault 本身的地方，也是用于创建和删除保管库的接口。 还可以读取密钥保管库属性并管理访问策略。
- 数据平面支持处理密钥保管库中存储的数据。 可以添加、删除和修改密钥、机密及证书。

若要在任一平面中访问密钥，所有调用方（用户或应用程序）都必须进行身份验证并获得授权。 对于身份验证，这两个平面都使用 Azure Active Directory (Azure AD)。 对于授权，管理平面使用 Azure RBAC)  (Azure 基于角色的访问控制，而数据平面使用 Key Vault 访问策略。

对这两种平面使用单一身份验证机制模型具有多个优点：

- 组织可以集中控制对其组织中的所有密钥保管库的访问。
- 离职的用户会立即失去对组织中所有密钥保管库的访问权限。
- 组织可以通过 Azure AD 中的选项自定义身份验证（例如，启用多重身份验证以提高安全性）。

### <a name="managing-administrative-access-to-key-vault"></a>管理对 Key Vault 的管理访问权限

在资源组中创建密钥保管库时，使用 Azure AD 管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 可以通过分配适当的 Azure 角色在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 Azure 角色：

- **订阅** ：在订阅级别分配的 Azure 角色适用于该订阅中的所有资源组和资源。
- **资源组** ：在资源组级别分配的 Azure 角色适用于该资源组中的所有资源。
- **特定资源** ：为特定资源分配的 Azure 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅 [AZURE RBAC：内置角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果用户具有密钥保管库管理平面的 `Contributor` 权限，则该用户可以通过设置密钥保管库访问策略来授予自己对数据平面的访问权限。 应严格控制对密钥保管库具有 `Contributor` 角色访问权限的用户。 请确保仅授权的人员才能访问和管理 Key Vault、密钥、机密和证书。

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>控制对 Key Vault 数据的访问

Key Vault 访问策略单独授予对密钥、机密或证书的权限。 可以仅授予用户对密钥的访问权限，而不授予对机密的访问权限。 在保管库级别管理密钥、机密或证书的访问权限。

> [!IMPORTANT]
> 密钥保管库访问策略不支持粒度、对象级别权限，例如特定的密钥、机密或证书。 如果授予某个用户创建和删除密钥的权限，该用户可以针对该密钥保管库中的所有密钥执行这些操作。

可以使用 [Azure 门户](assign-access-policy-portal.md)、[Azure CLI](assign-access-policy-cli.md)、[Azure PowerShell](assign-access-policy-powershell.md) 或[密钥保管库管理 REST API](/rest/api/keyvault/) 为密钥保管库设置访问策略。

可以通过使用[适用于 Azure 密钥保管库的虚拟网络服务终结点](overview-vnet-service-endpoints.md)来限制数据平面访问权限）。 可以配置[防火墙和虚拟网络规则](network-security.md)以提供额外的安全层。

## <a name="network-access"></a>网络访问

可以通过指定哪些 IP 地址有权访问来减少保管库的曝光。 通过 Azure Key Vault 的虚拟网络服务终结点可将访问限制为指定虚拟网络。 此外，还可通过这些终结点将访问限制为一系列 IPv4（Internet 协议版本 4）地址范围。 任何从外部连接到 Key Vault 的用户都无法访问这些资源。

防火墙规则生效后，仅当用户请求来自允许的虚拟网络或 IPv4 地址范围时，才能读取 Key Vault 中的数据。 从 Azure 门户访问 Key Vault 时，这同样适用。 虽然用户可从 Azure 门户浏览到 Key Vault，但如果其客户端计算机不在允许列表中，则可能无法列出密钥、机密或证书。 这也会影响其他 Azure 服务的 Key Vault 选取器。 如果防火墙规则阻止了用户的客户端计算机，则用户可以查看 Key Vault 列表，但不能查看列表密钥。

若要详细了解 Azure Key Vault 网络地址，请查看 [Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)）

## <a name="tls-and-https"></a>TLS 和 HTTPS

*   Key Vault 前端 (数据平面) 是一个多租户服务器。 这意味着来自不同客户的密钥保管库可以共享相同的公共 IP 地址。 为了实现隔离，每个 HTTP 请求都将进行身份验证和授权，而与其他请求无关。
*   你可以确定 TLS 的旧版本来报告漏洞，但由于公共 IP 地址是共享的，因此密钥保管库服务团队无法在传输级别为单独的密钥保管库禁用旧版本的 TLS。
*   HTTPS 协议允许客户端参与 TLS 协商。 **客户端可以强制使用最新版本的 TLS** ，只要客户端这样做，整个连接就会使用相应的级别保护。 但 Key Vault 仍支持旧的 TLS 版本，则不会影响使用较新的 TLS 版本的连接的安全性。
*   尽管 TLS 协议中存在已知的漏洞，但当攻击者使用具有漏洞的 TLS 版本启动连接时，不会有任何已知的攻击允许恶意代理从密钥保管库中提取任何信息。 攻击者仍需要对自身进行身份验证和授权，只要合法的客户端始终使用最新的 TLS 版本进行连接，就无法从旧的 TLS 版本泄露凭据。

## <a name="logging-and-monitoring"></a>日志记录和监视

Key Vault 日志记录会保存保管库中所执行活动的相关信息。 有关完整详细信息，请参阅 [Key Vault 日志记录](logging.md)。

有关如何安全地管理存储帐户的建议，请查看 [Azure 存储安全指南](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>后续步骤

- [Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)
- [Azure RBAC：内置角色](../../role-based-access-control/built-in-roles.md)