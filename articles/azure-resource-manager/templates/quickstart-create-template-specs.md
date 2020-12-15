---
title: 创建和部署模板规格
description: 了解如何通过 ARM 模板创建模板规格。 然后，将模板规格部署到订阅中的资源组。
author: tfitzmac
ms.date: 12/01/2020
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: 03cf2013f1cec9722af5d7e72285d9f11d8a6bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518951"
---
# <a name="quickstart-create-and-deploy-template-spec-preview"></a>快速入门：创建和部署模板规格（预览）

本快速入门介绍如何将 Azure 资源管理器模板（ARM 模板）打包为[模板规格](template-specs.md)，然后部署该模板规格。模板规格包含用于部署存储帐户的 ARM 模板。

## <a name="prerequisites"></a>先决条件

具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 模板规格当前提供预览版。 若要将其与 Azure PowerShell 一起使用，必须安装[版本 5.0.0 或更高版本](/powershell/azure/install-az-ps)。 若要将其与 Azure CLI 一起使用，请使用[版本 2.14.2 或更高版本](/cli/azure/install-azure-cli)。

## <a name="create-template-spec"></a>创建模板规格

模板规格是名为 Microsoft.Resources/templateSpecs 的资源类型。 若要创建模板规格，可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 ARM 模板。 在所有选项中，你都需要打包在模板规格中的 ARM 模板。

使用 PowerShell 和 CLI 时，ARM 模板作为参数传递给命令。 对于 ARM 模板，要打包在模板规格中的 ARM 模板嵌入在模板规格定义中。

这些选项如下所示。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在屏幕顶部的“搜索资源、服务和文档”中，输入“模板规格”，然后选择“模板规格”  。
1. 选择“创建模板规格”。
1. 选择或输入以下值：

    - **名称**：输入模板规格的名称。例如 storageSpec
    - **订阅**：选择用于创建模板规格的 Azure 订阅。
    - **资源组**：选择“新建”，然后输入新的资源组名称。  例如 templateSpecRG。
    - **位置**：选择资源组的位置。 例如美国西部 2。
    - **版本**：输入模板规格的版本。例如 1.0 或 v1.0 。

1. 在完成时选择“下一步:编辑模板”。
1. 将模板内容替换为以下 JSON：

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

    这是将在模板规格中打包的模板。
1. 选择“查看 + 创建”  。
1. 选择“创建”  。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 使用 PowerShell 创建模板规格时，可以传入本地模板。 复制以下模板并将其本地保存到名为 azuredeploy.json 的文件中。 本快速入门假设你已将模板保存到路径 c:\Templates\azuredeploy.json，不过你可以使用任何路径。

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. 创建新的资源组以包含模板规格。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

1. 然后，在该资源组中创建模板规格。 将新的模板规格命名为 storageSpec。

    ```azurepowershell
    New-AzTemplateSpec `
      -Name storageSpec `
      -Version "1.0" `
      -ResourceGroupName templateSpecRG `
      -Location westus2 `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 使用 CLI 创建模板规格时，可以传入本地模板。 复制以下模板并将其本地保存到名为 azuredeploy.json 的文件中。 本快速入门假设你已将模板保存到路径 c:\Templates\azuredeploy.json，不过你可以使用任何路径。

    :::code language="json" source="~/quickstart-templates/101-storage-account-create/azuredeploy.json":::

1. 创建新的资源组以包含模板规格。

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 然后，在该资源组中创建模板规格。 将新的模板规格命名为 storageSpec。

    ```azurecli
    az ts create \
      --name storageSpec \
      --version "1.0" \
      --resource-group templateSpecRG \
      --location "westus2" \
      --template-file "c:\Templates\azuredeploy.json"
    ```

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 使用 ARM 模板创建模板规格时，该模板将嵌入资源定义。 复制以下模板并将其本地保存为 azuredeploy.json。 本快速入门假设你已将模板保存到路径 c:\Templates\azuredeploy.json，不过你可以使用任何路径。

    > [!NOTE]
    > 在嵌入的模板中，必须使用第二个左括号对所有[模板表达式](template-expressions.md)进行转义。 请使用 `"[[`，而不是 `"[`。 JSON 数组仍使用单个左括号。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/templateSpecs",
          "apiVersion": "2019-06-01-preview",
          "name": "storageSpec",
          "location": "westus2",
          "properties": {
            "displayName": "Storage template spec"
          },
          "tags": {},
          "resources": [
            {
              "type": "versions",
              "apiVersion": "2019-06-01-preview",
              "name": "1.0",
              "location": "westus2",
              "dependsOn": [ "storageSpec" ],
              "properties": {
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "parameters": {
                    "storageAccountType": {
                      "type": "string",
                      "defaultValue": "Standard_LRS",
                      "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS",
                        "Premium_LRS"
                      ],
                      "metadata": {
                        "description": "Storage Account type"
                      }
                    },
                    "location": {
                      "type": "string",
                      "defaultValue": "[[resourceGroup().location]",
                      "metadata": {
                        "description": "Location for all resources."
                      }
                    }
                  },
                  "variables": {
                    "storageAccountName": "[[concat('store', uniquestring(resourceGroup().id))]"
                  },
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[[variables('storageAccountName')]",
                      "location": "[[parameters('location')]",
                      "sku": {
                        "name": "[[parameters('storageAccountType')]"
                      },
                      "kind": "StorageV2",
                      "properties": {}
                    }
                  ],
                  "outputs": {
                    "storageAccountName": {
                      "type": "string",
                      "value": "[[variables('storageAccountName')]"
                    }
                  }
                }
              },
              "tags": {}
            }
          ]
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 创建新的资源组。

    ```azurepowershell
    New-AzResourceGroup `
      -Name templateSpecRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name templateSpecRG \
      --location westus2
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName templateSpecRG `
      -TemplateFile "c:\Templates\azuredeploy.json"
    ```

    ```azurecli
    az deployment group create \
      --name templateSpecRG \
      --template-file "c:\Templates\azuredeploy.json"
    ```

---

## <a name="deploy-template-spec"></a>部署模板规格

现在即可部署模板规格。部署模板规格就像部署它包含的模板一样，只不过你在 Azure PowerShell 或 Azure CLI 中传入了模板规格的资源 ID。 使用相同的部署命令，并在需要时为模板规格传递参数值。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 从 Azure 门户中打开在上一个过程中创建的资源组。  例如 templateSpecRG。
1. 选择已创建的模板规格。 例如 storageSpec。
1. 选择“部署”。
1. 选择或输入以下值：

    - **订阅**：选择用于创建资源的 Azure 订阅。
    - **资源组**：选择“新建”，然后输入“storageRG” 。
    - **存储帐户类型**：选择“Standard_GRS”。

    创建新的资源组，并将模板规格中的模板部署到新的资源组。

1. 选择“查看 + 创建”。
1. 选择“创建”  。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建资源组以包含新的存储帐户。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

1. 获取模板规格的资源 ID。

    ```azurepowershell
    $id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name storageSpec -Version "1.0").Versions.Id
    ```

1. 部署模板规格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG
    ```

1. 提供的参数与 ARM 模板的完全一样。 使用存储帐户类型的参数重新部署模板规格。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -TemplateSpecId $id `
      -ResourceGroupName storageRG `
      -storageAccountType Standard_GRS
    ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

1. 创建资源组以包含新的存储帐户。

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. 获取模板规格的资源 ID。

    ```azurecli
    id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")
    ```

    > [!NOTE]
    > 获取模板规格 ID 并将其分配到 Windows PowerShell 中的变量时存在一个已知问题。

1. 部署模板规格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id
    ```

1. 提供的参数与 ARM 模板的完全一样。 使用存储帐户类型的参数重新部署模板规格。

    ```azurecli
    az deployment group create \
      --resource-group storageRG \
      --template-spec $id \
      --parameters storageAccountType='Standard_GRS'
    ```

# <a name="arm-template"></a>[ARM 模板](#tab/azure-resource-manager)

1. 复制以下模板并将其本地保存到名为 storage.json 的文件中。

    ```json
       {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "functions": [],
      "variables": {},
      "resources": [
        {
          "type": "Microsoft.Resources/deployments",
          "apiVersion": "2020-06-01",
          "name": "demo",
          "properties": {
            "templateLink": {
              "id": "[resourceId('templateSpecRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
            },
            "parameters": {
            },
            "mode": "Incremental"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. 使用 Azure CLI 或 PowerShell 为存储帐户创建新的资源组。

    ```azurepowershell
    New-AzResourceGroup `
      -Name storageRG `
      -Location westus2
    ```

    ```azurecli
    az group create \
      --name storageRG \
      --location westus2
    ```

1. 使用 Azure CLI 或 PowerShell 部署模板。

    ```azurepowershell
    New-AzResourceGroupDeployment `
      -ResourceGroupName storageRG `
      -TemplateFile "c:\Templates\storage.json"
    ```

    ```azurecli
    az deployment group create \
      --name storageRG \
      --template-file "c:\Templates\storage.json"
    ```

---

## <a name="grant-access"></a>授予访问权限

如果要让组织中的其他用户部署模板规格，你需要向他们授予读取权限。 对于包含要共享的模板规格的资源组，你可以将读取者角色分配给相应的 Azure AD 组。 有关详细信息，请参阅[教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

## <a name="clean-up-resources"></a>清理资源

若要清理本快速入门中部署的资源，请删除创建的两个资源组。

1. 在 Azure 门户上的左侧菜单中选择“资源组”。

1. 在“按名称筛选”字段中输入资源组名称（templateSpecRG 和 storageRG）。

1. 选择资源组名称。

1. 在顶部菜单中选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

若要了解有关创建包含关联模板的模板规格的信息，请参阅[创建关联模板的模板规格](template-specs-create-linked.md)。
