---
title: 排查常见的身份验证错误 - Azure 市场
description: 提供帮助以排查 Azure 市场中使用云合作伙伴门户 API 时常见的身份验证错误。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 01af5357e4ae2f4dfb317a0931a8d0bc2b2d54e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89487311"
---
# <a name="troubleshooting-common-authentication-errors"></a>排查常见的身份验证错误

> [!NOTE]
> 云合作伙伴门户 API 已与合作伙伴中心集成，并将继续在其中工作。 本次转换带来了少量更改。 查看[云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md)中列出的更改，确保转换到合作伙伴中心后代码继续正常工作。 CPP API 应仅用于在转换到合作伙伴中心之前已经集成的现有产品；新产品应使用合作伙伴中心提交 API。

此文章提供帮助以排查使用云合作伙伴门户 API 时常见的身份验证错误。

## <a name="unauthorized-error"></a>未授权的错误

如果一直收到 `401 unauthorized` 错误，请验证你访问令牌是否有效。  如果尚未执行此操作，请创建基本 Azure Active Directory (Azure AD) 应用程序和服务主体，如[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)中所述。 然后，使用应用程序或简单的 HTTP POST 请求来验证访问权限。  需要提供租户 ID、应用程序 ID、对象 ID 和密钥以获取访问令牌。

## <a name="forbidden-error"></a>“已禁止”错误

如果收到 `403 forbidden` 错误，请确保已将正确的服务主体添加到云合作伙伴门户中的发布者帐户。 按照[先决条件](./cloud-partner-portal-api-prerequisites.md)页面中的步骤将服务主体添加到门户。

如果添加了正确的服务主体，则验证所有其他信息。 特别注意在门户中输入的对象 ID。 Azure Active Directory 应用注册页中有两个对象 ID，你必须使用本地对象 ID。 通过转到应用的“应用注册”页，然后单击“本地目录中的托管应用程序”下的应用名称，可以找到正确的值。 这会将你转到应用的本地属性，你可以在“属性”页中找到正确的对象 ID，如下图所示。 此外，请确保在添加服务主体并进行 API 调用时使用正确的发布者 ID。
