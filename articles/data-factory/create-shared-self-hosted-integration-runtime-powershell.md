---
title: 使用 PowerShell 创建共享自承载集成运行时
description: 了解如何在 Azure 数据工厂中创建共享自承载集成运行时，从而允许多个数据工厂访问集成运行时。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: anansub
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 8734247a913bdf6a44a9156f6f87705b618f7228
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632883"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建共享自承载集成运行时

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本指南介绍如何在 Azure 数据工厂中创建共享自承载集成运行时。 然后可在另一个数据工厂中使用共享自托管集成运行时。

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>使用 Azure 数据工厂 UI 创建共享自承载 IR

若要使用 Azure 数据工厂 UI 创建共享自承载 IR，可以执行以下步骤：

1. 在要共享的自承载 IR 中，选择“向另一个数据工厂授予权限”，然后在“集成运行时设置”页中，选择要在其中创建链接 IR 的数据工厂。
      
    ![“共享”选项卡上的授予权限按钮](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. 记下并复制上面要共享的自承载 IR 的“资源 ID”。
         
3. 在已授予权限的数据工厂中，创建新的自承载 IR（链接），并输入资源 ID。
      
    ![用于创建自承载集成运行时的按钮](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![用于创建链接的自承载集成运行时的按钮](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![用于输入名称和资源 ID 的框](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>使用 Azure PowerShell 创建共享自承载 IR

若要使用 Azure PowerShell 创建共享自承载 IR，可以执行以下步骤： 
1. 创建数据工厂。 
1. 创建自我托管的集成运行时。
1. 与其他数据工厂共享自承载集成运行时。
1. 创建链接的集成运行时。
1. 撤消共享。

### <a name="prerequisites"></a>先决条件 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 订阅** 。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。 

- **Azure PowerShell** 。 请遵循[使用 PowerShellGet 在 Windows 上安装 Azure PowerShell](/powershell/azure/install-az-ps) 中的说明。 可使用 PowerShell 运行脚本来创建可与其他数据工厂共享的自承载集成运行时。 

> [!NOTE]  
> 若要查看目前提供数据工厂的 Azure 区域列表，请选择你感兴趣的区域：[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)。

### <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 Windows PowerShell 集成脚本环境 (ISE)。

1. 创建变量。 复制并粘贴以下脚本。 将变量（例如 **SubscriptionName** 和 **ResourceGroupName** ）替换为实际值： 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. 登录并选择一个订阅。 在脚本中添加以下代码，以登录并选择 Azure 订阅：

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. 创建资源组和数据工厂。

    > [!NOTE]  
    > 此步骤是可选的。 若已有数据工厂，请跳过此步骤。 

    使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 以下示例在 WestEurope 位置创建名为 `myResourceGroup` 的资源组： 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    运行以下命令以创建数据工厂： 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime

> [!NOTE]  
> 此步骤是可选的。 若已经拥有要与其他数据工厂共享的自承载集成运行时，请跳过此步骤。

运行以下命令以创建自承载集成运行时：

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>获取集成运行时身份验证密钥并注册节点

运行以下命令以获取自承载集成运行时的身份验证密钥：

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

响应包含此自承载集成运行时的身份验证密钥。 注册集成运行时节点时使用此密钥。

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>安装并注册自承载集成运行时

1. 从 [Azure 数据工厂集成运行时](https://aka.ms/dmg)下载自承载集成运行时安装程序。

2. 运行安装程序以在本地计算机上安装自承载集成。

3. 使用你在上一步中检索的身份验证密钥注册新的自承载集成。

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>与另一个数据工厂共享自承载集成运行时

#### <a name="create-another-data-factory"></a>创建另一个数据工厂

> [!NOTE]  
> 此步骤是可选的。 若已拥有要共享的数据工厂，请跳过此步骤。 但若要向/从其他数据工厂添加或删除角色分配，你必须具有 `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)或[所有者](../role-based-access-control/built-in-roles.md#owner)。

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>授予权限

向需要访问你创建和注册的自承载集成运行时的数据工厂授予权限。

> [!IMPORTANT]  
> 请勿跳过此步骤！

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>创建链接的自承载集成运行时

运行以下命令以创建链接的自承载集成运行时：

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

现可在任何链接服务中使用此链接的集成运行时。 链接的集成运行时使用共享集成运行时来运行活动。

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>撤消数据工厂中的集成运行时共享

若要从共享集成运行时中撤销数据工厂的访问权限，请运行以下命令：

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

若要删除现有的链接集成运行时，请对共享集成运行时运行以下命令：

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="next-steps"></a>后续步骤

- 查看 [Azure 数据工厂中的集成运行时概念](./concepts-integration-runtime.md)。

- 了解如何[在 Azure 门户中创建自承载集成运行时](./create-self-hosted-integration-runtime.md)。