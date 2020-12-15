---
title: 设置令牌的生存期
titleSuffix: Microsoft identity platform
description: 了解如何设置由 Microsoft 标识平台颁发的令牌的生存期。 了解如何管理组织的默认策略、为 Web 登录创建策略、为调用 Web API 的本机应用创建策略，以及如何管理高级策略。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e663cdd3846e804d1dcf96076c07b9a3db84272c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507738"
---
# <a name="configure-token-lifetime-policies-preview"></a>配置令牌生存期策略（预览版）
如果能够创建和管理应用、服务主体和整个组织的令牌生存期，就可以在 Azure AD 中实现各种新的方案。  

> [!IMPORTANT]
> 在5月 2020 5 日后，租户将无法再配置刷新和会话令牌生存期。  在2021年1月30日后，Azure Active Directory 将停止在策略中遵守现有的刷新和会话令牌配置。 在弃用之后，你仍然可以配置访问令牌生存期。  若要了解详细信息，请参阅 [Microsoft 标识平台中的可配置令牌生存期](active-directory-configurable-token-lifetimes.md)。
> 已在 Azure AD 条件访问中实现 [身份验证会话管理功能](../conditional-access/howto-conditional-access-session-lifetime.md)   。 你可以使用此新功能，通过设置登录频率来配置刷新令牌生存期。


本部分逐步讲解一些常见的策略方案，帮助你针对以下属性实施新规则：

* 令牌生存期
* 令牌最大非活动时间
* 令牌最长时间

通过这些示例，可以了解如何执行以下操作：

* 管理组织的默认策略
* 为 Web 登录创建策略
* 为调用 Web API 的本机应用创建策略
* 管理高级策略

## <a name="prerequisites"></a>先决条件
以下示例演示如何创建、更新、链接和删除应用、服务主体和整个组织的策略。 如果不熟悉 Azure AD，建议在使用这些示例之前，先了解[如何获取 Azure AD 租户](quickstart-create-new-tenant.md)。  

若要开始，请执行以下步骤：

1. 下载最新的 [Azure AD PowerShell 模块公共预览版](https://www.powershellgallery.com/packages/AzureADPreview)。
2. 运行 `Connect` 命令登录到 Azure AD 管理员帐户。 每次启动新会话都需要运行此命令。

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 若要查看组织中创建的所有策略，请运行以下命令。 执行以下方案中的大多数操作之后，都要运行此命令。 运行此命令还可帮助获取策略的 ** **。

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>管理组织的默认策略
本示例将创建一个策略，使用户以更低的频率在整个组织中登录。 为此，可以为单因素刷新令牌创建一个令牌生存期策略，该策略应用于整个组织。 此策略将应用到组织中的每个应用程序，以及尚未设置策略的每个服务主体。

1. 创建令牌生存期策略。

    1. 将单因素刷新令牌设置为“until-revoked”。 在吊销访问权限之前该令牌不会过期。 创建以下策略定义：

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. 若要创建该策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要删除任何空格，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. 若要查看新策略并获取其 **ObjectId**，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 更新策略。

    假设在本示例中创建的第一个策略不像服务要求的那样严格。 若要将单因素刷新令牌设置为在两天后过期，请运行以下命令：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>为 Web 登录创建策略

本示例创建一个要求用户更频繁地在 Web 应用中进行身份验证的策略。 此策略会针对 Web 应用的服务主体设置访问/ID 令牌的生存期以及多因素会话令牌的最大期限。

1. 创建令牌生存期策略。

    这个用于 Web 登录的策略将访问/ID 令牌生存期和单因素会话令牌最大期限设置为 2 小时。

    1. 若要创建该策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略并获取策略 ObjectId，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到服务主体。 还需要获取服务主体的 ObjectId。

    1. 请使用 [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet 来查看组织的所有服务主体或某一个服务主体。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. 如果你有服务主体，请运行 [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>为调用 Web API 的本机应用创建策略
本示例创建一个不要求用户太频繁进行身份验证的策略。 该策略还可延长用户可保持非活动状态、不必再次身份验证的时间。 该策略将应用到 Web API。 当本机应用以资源形式请求 Web API 时，将应用此策略。

1. 创建令牌生存期策略。

    1. 若要为 Web API 创建严格策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略，请运行以下命令：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到 Web API。 还需要获取应用程序的 **ObjectId**。 请使用 [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) cmdlet 来查找应用的 ObjectId，或使用 [Azure 门户](https://portal.azure.com/)。

    获取应用的 ObjectId 并分配该策略：

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>管理高级策略
本示例创建几个策略来演示优先级系统的工作原理。 此外，你还可以了解如何管理应用于多个对象的多个策略。

1. 创建令牌生存期策略。

    1. 若要创建一个将单因素刷新令牌生存期设置为 30 天的组织默认策略，请运行 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 若要查看新策略，请运行 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 将策略分配到服务主体。

    现已创建一个要应用到整个组织的策略。 可能想要为特定的服务主体保留这个 30 天策略，但要将组织默认策略更改为上限“直到吊销”。

    1. 若要查看组织的所有服务主体，请使用 [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet。

    1. 如果你有服务主体，请运行 [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet：

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. 将 `IsOrganizationDefault` 标志设置为 false：

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. 创建新的组织默认策略：

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    现在，已将原始策略链接到服务主体，已将新策略设置为组织默认策略。 请务必记住，应用到服务主体的策略优先级高于组织默认策略。

## <a name="next-steps"></a>后续步骤
了解 Azure AD 条件访问中的 [身份验证会话管理功能](../conditional-access/howto-conditional-access-session-lifetime.md) 。