---
title: 获取用于应用身份验证的值
description: 创建服务主体以便从代码访问 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1 , devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: mathoma
ms.date: 03/12/2019
ms.openlocfilehash: 923e218768e10fcf31c5dd1f3408c6dfc1af27b2
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110705704"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-azure-sql-database-from-code"></a>获取对应用程序进行身份验证所需的值以便从代码访问 Azure SQL 数据库
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

要在代码中创建并管理 Azure SQL 数据库，必须在创建 Azure 资源的订阅中的 Azure Active Directory (Azure AD) 域内注册应用。

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>创建服务主体以便从应用程序访问资源

以下示例将创建对 C# 应用进行身份验证所需的 Active Directory (AD) 应用程序和服务主体。 该脚本输出我们需要用于前面 C# 示例的值。 有关详细信息，请参阅[使用 Azure PowerShell 创建服务主体以访问资源](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> SQL 数据库仍然支持 PowerShell Azure 资源管理器 (RM) 模块，但所有后续开发都针对 Az.Sql 模块。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

```powershell
# sign in to Azure
Connect-AzAccount

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#Set-AzContext -SubscriptionId $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = New-AzADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

# create a Service Principal for the app
$svcprincipal = New-AzADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause here for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (Get-AzContext).Subscription.SubscriptionId
Write-Output "_tenantId:" (Get-AzContext).Tenant.TenantId
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
# sign in to Azure
az login

# for multiple subscriptions, uncomment and set to the subscription you want to work with
#$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
#az account set --subscription $subscriptionId

$appName = "{app-name}" # display name for your app, must be unique in your directory
$uri = "http://{app-name}" # does not need to be a real uri
$secret = "{app-password}"

# create an AAD app
$azureAdApplication = az ad app create --display-name $appName --homepage $Uri --identifier-uris $Uri --password $secret

# create a Service Principal for the app
$svcprincipal = az ad sp create --id $azureAdApplication.ApplicationId

Start-Sleep -s 15 # to avoid a PrincipalNotFound error, pause for 15 seconds

# if you still get a PrincipalNotFound error, then rerun the following until successful.
$roleassignment = az role assignment create --role "Contributor" --assignee $azureAdApplication.ApplicationId.Guid

# output the values we need for our C# application to successfully authenticate
Write-Output "Copy these values into the C# sample app"

Write-Output "_subscriptionId:" (az account show --query "id")
Write-Output "_tenantId:" (az account show --query "tenantId")
Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
Write-Output "_applicationSecret:" $secret
```

* * *

## <a name="see-also"></a>另请参阅

[在 Azure SQL 数据库中使用 C# 创建数据库](design-first-database-csharp-tutorial.md)  
[使用 Azure Active Directory 身份验证连接到 Azure SQL 数据库](authentication-aad-overview.md)
