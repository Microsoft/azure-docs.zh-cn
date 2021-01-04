---
title: 将 Azure AD 应用限制为仅供一组用户使用 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将在 Azure AD 中注册的应用限制为仅供所选的一组用户访问。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f5a5242cb9448b3d11e0921b2272cf00bef8f6c1
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97722260"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>如何：将 Azure AD 应用限制为仅供 Azure AD 租户中的一组用户使用

默认情况下，在 Azure Active Directory (Azure AD) 租户中注册的应用程序可供租户的所有已成功进行身份验证的用户使用。

类似地，在使用[多租户](howto-convert-app-to-be-multi-tenant.md)应用时，如果此应用在 Azure AD 租户中预配，则该租户中的所有用户在相应的租户中成功进行身份验证以后，都能够访问此应用程序。

租户管理员和开发人员通常会要求一个应用只能供特定的一组用户使用。 开发人员可以使用 Azure 基于角色的访问控制 (Azure RBAC) 之类的常用授权模式来完成此操作，但这种方法要求开发人员完成大量的工作。

租户管理员和开发人员也可以使用 Azure AD 的此内置功能，将应用限制为仅供租户中特定的一组用户或安全组使用。

## <a name="supported-app-configurations"></a>支持的应用配置

将应用限制为仅供租户中特定的一组用户或安全组使用这一选项适用于以下类型的应用程序：

- 经配置后可以使用基于 SAML 的身份验证进行联合单一登录的应用程序
- 使用 Azure AD 预身份验证的应用程序代理应用程序
- 直接在 Azure AD 应用程序平台上生成且使用 OAuth 2.0/OpenID Connect 身份验证的应用程序（前提是用户或管理员已认可该应用程序）。

     > [!NOTE]
     > 此功能仅适用于 Web 应用/Web API 和企业应用程序。 注册为[本机](./quickstart-register-app.md)的应用不能只限租户中的一组用户或安全组使用。

## <a name="update-the-app-to-enable-user-assignment"></a>更新应用，允许用户分配

可通过两种方式创建启用了用户分配的应用程序。 一种方式需要 **全局管理员** 角色，第二种方式不需要。

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>企业应用程序（需要全局管理员角色）

1. 转到 [**Azure 门户**](https://portal.azure.com/)，以“全局管理员”身份登录。
1. 在顶栏中选择登录的帐户。 
1. 在“目录”下，选择要在其中注册应用的 Azure AD 租户。
1. 在左侧导航栏中，选择“Azure Active Directory”。 如果 Azure Active Directory 在导航窗格中不可用，请执行以下步骤：

    1. 在左侧的主导航菜单顶部选择“所有服务”。
    1. 在筛选器搜索框中键入“Azure Active Directory”，然后从结果中选择“Azure Active Directory”项。

1. 使用 **Azure Active Directory** 左侧导航菜单，在“Azure Active Directory”窗格中选择“企业应用程序” 。
1. 选择“所有应用程序”，查看所有应用程序的列表。

     如果看不到希望其显示在这里的应用程序，请使用“所有应用程序”列表顶部的各种筛选器来限制此列表，或者在列表中向下滚动，以便找到应用程序。

1. 从列表中选择要向其分配用户或安全组的应用程序。
1. 在应用程序的“概述”页上，从应用程序的左侧导航菜单中选择“属性” 。
1. 找到设置“需要进行用户分配?”，将其设置为“是”。  将此选项设置为“是”时，必须先将租户中的用户分配到此应用程序，否则用户无法登录到此应用程序。
1. 选择“保存”以保存此配置更改。

### <a name="app-registration"></a>应用注册

1. 转到 [**Azure 门户**](https://portal.azure.com/)。
1. 在顶栏中选择登录的帐户。 
1. 在“目录”下，选择要在其中注册应用的 Azure AD 租户。
1. 在左侧导航栏中，选择“Azure Active Directory”。
1. 在 **Azure Active Directory** 窗格中，从 **Azure Active Directory** 左侧导航菜单中选择“应用注册”。
1. 创建或选择要管理的应用。 你需要成为此应用注册的 **所有者**。
1. 在应用程序的“概述”页上，单击页面顶部“概要”下的“本地目录中的托管应用程序”链接。 这将带你进入应用注册的 _托管企业应用程序_。
1. 从左侧的导航边栏选项卡中，选择“属性”。
1. 找到设置“需要进行用户分配?”，将其设置为“是”。  将此选项设置为“是”时，必须先将租户中的用户分配到此应用程序，否则用户无法登录到此应用程序。
1. 选择“保存”以保存此配置更改。

## <a name="assign-users-and-groups-to-the-app"></a>将用户和组分配到应用

将应用配置为允许用户分配以后，即可继续操作，将用户和组分配到应用。

1. 选择企业应用程序的左侧导航菜单中的 " **用户和组** " 窗格。
1. 在“用户和组”列表顶部选择“添加用户”按钮，以便打开“添加分配”窗格。  
1. 在“添加分配”窗格中，选择“用户”选择器。 

     将会显示用户和安全组的列表和一个文本框，后者用于搜索和查找特定用户或组。 此屏幕允许一次选择多个用户和组。

1. 选择好用户和组以后，按底部的“选择”按钮即可转到下一部分。
1. （可选）如果已在应用程序中定义了应用角色，则可以使用“选择角色”选项将选定的用户和组分配给应用程序的角色之一。 
1. 按底部的“分配”按钮即可完成将用户和组分配到应用的操作。 
1. 确认已添加的用户和组显示在更新的“用户和组”列表中。

## <a name="more-information"></a>详细信息

- [如何：在应用程序中添加应用角色](./howto-add-app-roles-in-azure-ad-apps.md)
- [将使用应用角色和角色声明的授权添加到 ASP.NET Core Web 应用](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM)（在应用中使用安全组和应用程序角色（视频））
- [Azure Active Directory 现在可以与组声明和应用程序角色配合使用](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory 应用清单](./reference-app-manifest.md)
