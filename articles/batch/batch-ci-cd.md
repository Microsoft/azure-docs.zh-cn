---
title: 使用 Azure Pipelines 生成和部署 HPC 解决方案
description: 了解如何为 Azure Batch 上运行的 HPC 应用程序部署生成/发布管道。
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435539"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>使用 Azure Pipelines 生成和部署 HPC 解决方案

Azure DevOps 提供的工具可用于自动生成和测试高性能计算 (HPC) 解决方案。 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 提供了一系列新式持续集成 (CI) 和持续部署 (CD) 进程，用于生成、部署、测试和监视软件。 这些流程加快了软件交付，使你能够专注于代码，而不是基础结构和操作支持。

本文介绍如何使用 Azure Batch 上部署的 HPC 解决方案的 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 设置 CI/CD 进程。

## <a name="prerequisites"></a>先决条件

若要按照本文中的步骤进行操作，需要一个 [Azure DevOps 组织](/azure/devops/organizations/accounts/create-organization)。 还需要[在 Azure DevOps 中创建一个项目](/azure/devops/organizations/projects/create-project)。

在开始之前，有必要对[源代码管理](/azure/devops/user-guide/source-control)和 [Azure 资源管理器模板语法](../azure-resource-manager/templates/template-syntax.md)有一个基本的了解。

## <a name="create-an-azure-pipeline"></a>创建 Azure 管道

在此示例中，将创建一个生成和发布管道，用于部署 Azure Batch 基础结构并发布应用程序包。 假设代码是在本地开发的，以下是常规部署流：

![显示管道中的部署流的关系图，](media/batch-ci-cd/DeploymentFlow.png)

此示例使用多个 Azure 资源管理器模板和现有二进制文件。 可以将这些示例复制到存储库中，并将其推送到 Azure DevOps。

### <a name="understand-the-azure-resource-manager-templates"></a>了解 Azure 资源管理器模板

此示例使用多个 Azure 资源管理器模板来部署解决方案。 三个功能模板（类似于单位或模块）用于实现特定功能。 然后，使用端到端解决方案模板 (deployment.json) 来部署这些基础功能模板。 此[链接模板结构](../azure-resource-manager/templates/deployment-tutorial-linked-template.md)允许单独测试每个功能模板并跨解决方案重复使用这些模板。

![显示使用 Azure 资源管理器模板的链接模板结构的关系图。](media/batch-ci-cd/ARMTemplateHierarchy.png)

此模板定义用于将应用程序部署到 Batch 帐户所需的 Azure 存储帐户。 有关详细信息，请参阅 [Microsoft.Storage 资源类型的资源管理器模板参考指南](/azure/templates/microsoft.storage/allversions)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

下一个模板定义 [Azure Batch 帐户](accounts.md)。 Batch 帐户充当一种平台，用于跨[池](nodes-and-pools.md#pools)运行多个应用程序。 有关详细信息，请参阅 [Microsoft.Batch 资源类型的资源管理器模板参考指南](/azure/templates/microsoft.batch/allversions)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

下一个模板在 Batch 帐户中创建 Batch 池。 有关详细信息，请参阅 [Microsoft.Batch 资源类型的资源管理器模板参考指南](/azure/templates/microsoft.batch/allversions)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

最终模板充当业务流程协调程序，并部署三个基础功能模板。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>了解 HPC 解决方案

如前所述，此示例使用多个 Azure 资源管理器模板和现有二进制文件。 可以将这些示例复制到存储库中，并将其推送到 Azure DevOps。

对于此解决方案，将使用 ffmpeg 作为应用程序包。 如果尚未安装，可以[下载 ffmpeg 包](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08)。

![存储库结构的屏幕截图。](media/batch-ci-cd/git-repository.jpg)

此存储库主要分为四个部分：

- arm-templates 文件夹，其中包含 Azure 资源管理器模板
- hpc-application 文件夹，其中包含 [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) 的 Windows 64 位版本。
- pipelines 文件夹，其中包含定义生成管道进程的 YAML 文件。
- 可选：client-application 文件夹，这是示例[使用 ffmpeg 进行 Azure Batch .NET 文件处理](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial)的副本。 本文不需要此应用程序。


> [!NOTE]
> 这只是基本代码结构的一个示例。 此方法用于显示应用程序、基础结构和管道代码存储在同一存储库中。

现在已设置源代码，接下来可以开始第一次生成。

## <a name="continuous-integration"></a>持续集成

Azure DevOps Services 内的 [Azure Pipelines](/azure/devops/pipelines/get-started/) 有助于实现应用程序的生成、测试和部署管道。

在管道的此阶段中，通常会运行测试来验证代码并生成软件的相应部分。 测试的数量和类型以及运行的任何其他任务将取决于作用范围更广的生成和发布策略。

## <a name="prepare-the-hpc-application"></a>准备 HPC 应用程序

在本部分中，将使用 hpc-application 文件夹。 此文件夹包含将在 Azure Batch 帐户中运行的软件 (ffmpeg)。

1. 导航到 Azure DevOps 组织中 Azure Pipelines 的生成部分。 创建新管道。

    ![“新管道”屏幕的屏幕截图。](media/batch-ci-cd/new-build-pipeline.jpg)

1. 可使用两个选项创建生成管道：

    a. [使用可视化设计器](/azure/devops/pipelines/get-started-designer)。 为此，请在“新管道”页上单击“使用可视化设计器”。

    b. [使用 YAML 生成](/azure/devops/pipelines/get-started-yaml)。 可以通过单击“新管道”页上的“Azure Repos”或“GitHub”选项来创建新的 YAML 管道。 或者，可以在源代码管理中存储下面的示例，并通过选择“可视化设计器”引用现有的 YAML 文件，然后使用 YAML 模板。

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. 根据需要配置生成后，选择“保存并排队”。 如果已启用持续集成（在触发器部分中），那么在对存储库发起新提交时，将自动触发生成，并满足在生成中设置的条件。

    ![现有生成管道的屏幕截图。](media/batch-ci-cd/existing-build-pipeline.jpg)

1. 通过导航到 Azure Pipelines 的生成部分来查看 Azure DevOps 中生成进度的实时更新。 从生成定义中选择相应的生成。

    ![Azure DevOps 中的生成的实时输出屏幕截图。](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> 如果使用客户端应用程序执行 HPC 解决方案，则需要为该应用程序创建单独的生成定义。 可以在 [Azure Pipelines](/azure/devops/pipelines/get-started/index) 文档中找到一些操作指南。

## <a name="continuous-deployment"></a>连续部署

Azure Pipelines 还用于部署应用程序和底层基础结构。 [发布管道](/azure/devops/pipelines/release)启用持续部署并自动执行发布过程。

### <a name="deploy-your-application-and-underlying-infrastructure"></a>部署应用程序和底层基础结构

部署基础结构涉及多个步骤。 由于此解决方案使用[链接模板](../azure-resource-manager/templates/linked-templates.md)，因此需要能够从公共终结点（HTTP 或 HTTPS）访问这些模板。 该位置可能是 GitHub 上的存储库、Azure Blob 存储帐户或其他存储位置。 上传的模板项目可以保持安全，因为它们可以在专用模式下保存，而使用某种形式的共享访问签名 (SAS) 令牌进行访问。

以下示例演示了如何使用 Azure 存储 blob 中的模板部署基础结构。

1. 创建新的发布定义，然后选择一个空定义。 将新创建的环境重命名为与管道相关的内容。

    ![初始发布管道的屏幕截图。](media/batch-ci-cd/Release-0.jpg)

1. 在生成管道上创建依赖项，以获取 HPC 应用程序的输出。

    > [!NOTE]
    > 请注意源别名，因为在“发布定义”内创建任务时需要它。

    ![屏幕截图显示在适当的生成管道中指向 HPCApplicationPackage 的项目链接。](media/batch-ci-cd/Release-1.jpg)

1. 创建指向其他项目的链接，这一次是 Azure 存储库。 这对于访问存储在存储库中的资源管理器模板是必需的。 由于资源管理器模板不需要编译，因此不需要通过生成管道来推送这些模板。

    > [!NOTE]
    > 同样，请注意源别名，因为稍后需要它。

    ![屏幕截图显示指向 Azure Repos 的项目链接。](media/batch-ci-cd/Release-2.jpg)

1. 导航到变量部分。 想要在管道中创建多个变量，这样就不必再将相同的信息重新输入到多个任务。 此示例使用以下变量：

   - **applicationStorageAccountName**：保存 HPC 应用程序二进制文件的存储帐户的名称
   - **batchAccountApplicationName** Batch 帐户中的应用程序名称
   - **batchAccountName**：Batch 帐户的名称
   - **batchAccountPoolName**：执行处理的 VM 池的名称
   - **batchApplicationId**：Batch 应用程序的唯一 ID
   - **batchApplicationVersion**：Batch 应用程序的语义版本（即 ffmpeg 二进制文件）
   - **location**：要部署的 Azure 资源的位置
   - **resourceGroupName**：要创建的资源组的名称，以及要将资源部署到的位置
   - **storageAccountName**：保存链接资源管理器模板的存储帐户的名称

   ![屏幕截图显示为 Azure Pipelines 发布设置的变量。](media/batch-ci-cd/Release-4.jpg)

1. 导航到开发环境的任务。 在下面的快照中，可以看到六个任务。 这些任务将执行以下操作：下载压缩的 ffmpeg 文件、部署用于托管嵌套资源管理器模板的存储帐户、将这些资源管理器模板复制到存储帐户、部署批处理帐户和所需的依赖项、在 Azure Batch 帐户中创建应用程序并将应用程序包上传到 Azure Batch 帐户。

    ![屏幕截图显示用于将 HPC 应用程序发布到 Azure Batch 的任务。](media/batch-ci-cd/Release-3.jpg)

1. 添加“下载管道工件（预览版）”任务，并设置以下属性：
    - **显示名称：** 将 ApplicationPackage 下载到代理
    - **要下载的项目的名称：** hpc-application
    - **要下载到的路径：** $(System.DefaultWorkingDirectory)

1. 创建存储帐户以存储 Azure 资源管理器模板。 可以使用解决方案中的现有存储帐户，但若要支持此自包含示例和内容隔离，将生成专用存储帐户。

    添加 Azure 资源组部署任务，并设置以下属性：
    - **显示名称：** 为资源管理器模板部署存储帐户
    - **Azure 订阅：** 选择适当的 Azure 订阅
    - **操作**：创建或更新资源组
    - **资源组**：$(resourceGroupName)
    - **位置**：$(location)
    - **模板**：$(System.ArtifactsDirectory)/{YourAzureRepoArtifactSourceAlias}/arm-templates/storageAccount.json
    - **替代模板参数**：-accountName $(storageAccountName)

1. 使用 Azure Pipelines 将源代码管理中的项目上传到存储帐户。 作为此 Azure Pipelines 任务的一部分，可以将存储帐户容器 URI 和 SAS 令牌输出到 Azure Pipelines 中的变量，从而使其可在此代理阶段重复使用。

    添加 Azure 文件复制任务，并设置以下属性：
    - **源：** ：$(System.ArtifactsDirectory)/{YourAzureRepoArtifactSourceAlias}/arm-templates/
    - **Azure 连接类型**：Azure 资源管理器
    - **Azure 订阅：** 选择适当的 Azure 订阅
    - **目标类型**：Azure Blob
    - **RM 存储帐户**：$(storageAccountName)
    - **容器名称**：templates
    - **存储容器 URI**：templateContainerUri
    - **存储容器 SAS 令牌**：templateContainerSasToken

1. 部署业务流程协调程序模板。 此模板包括存储帐户容器 URI 和 SAS 令牌的参数。 资源管理器模板中所需的变量已保存在发布定义的变量部分中，或已在另一 Azure Pipelines 任务（例如，Azure Blob 复制任务的一部分）中进行了设置。

    添加 Azure 资源组部署任务，并设置以下属性：
    - **显示名称：** 部署 Azure Batch
    - **Azure 订阅：** 选择适当的 Azure 订阅
    - **操作**：创建或更新资源组
    - **资源组**：$(resourceGroupName)
    - **位置**：$(location)
    - **模板**：$(System.ArtifactsDirectory)/{YourAzureRepoArtifactSourceAlias}/arm-templates/deployment.json
    - **替代模板参数**：`-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   常见的做法是使用 Azure Key Vault 任务。 如果与 Azure 订阅连接的服务主体设置了适当的访问策略，则它可以从 Azure Key Vault 下载机密，并可在管道中用作变量。 将使用关联值设置机密名称。 例如，可以在发布定义中使用 $(sshPassword) 来引用 sshPassword 的机密。

1. 接下来的步骤调用 Azure CLI。 第一种用于在 Azure Batch 中创建应用程序并上传关联包。

    添加 Azure CLI 任务，并设置以下属性：
    - **显示名称：** 在 Azure Batch 帐户中创建应用程序
    - **Azure 订阅：** 选择适当的 Azure 订阅
    - **脚本位置**：内联脚本
    - **内联脚本**：`az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. 第二步用于将关联包上传到应用程序（在这种情况下为 ffmpeg 文件）。

    添加 Azure CLI 任务，并设置以下属性：
    - **显示名称：** 将包上传到 Azure Batch 帐户
    - **Azure 订阅：** 选择适当的 Azure 订阅
    - **脚本位置**：内联脚本
    - **内联脚本**：`az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > 应用程序包的版本号设置为变量。 这允许覆盖包的早期版本，并允许手动控制推送到 Azure Batch 的包的版本号。

1. 通过选择“发布”>“创建新的发布”来创建新的发布。 触发后，选择指向新发布的链接以查看状态。

1. 通过选择环境下的“日志”按钮，查看代理的实时输出。

    ![屏幕截图显示发布的状态。](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>测试环境

设置环境后，确认以下测试是否可以成功完成。

使用 PowerShell 命令提示符中的 Azure CLI 连接到新的 Azure Batch 帐户。

- 通过 `az login` 登录到 Azure 帐户，并按照说明进行身份验证。
- 现在对 Batch 帐户进行身份验证：`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>列出可用的应用程序

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>检查池是否有效

```azurecli
az batch pool list
```

请注意此命令的输出中 `currentDedicatedNodes` 的值。 此值在下一测试中进行调整。

#### <a name="resize-the-pool"></a>调整池大小

调整池大小，以便有可用于作业和任务测试的计算节点，使用 pool list 命令检查以查看当前状态，直到完成调整大小并且有可用节点为止

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>后续步骤

请参阅以下教程了解如何通过简单应用程序与 Batch 帐户交互。

- [使用 Python API 通过 Azure Batch 运行并行工作负荷](tutorial-parallel-python.md)
- [使用 .NET API 通过 Azure Batch 运行并行工作负荷](tutorial-parallel-dotnet.md)
