---
title: 将 CI/CD 与 Azure Dev Spaces 配合使用
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: 了解如何将 Azure DevOps 与 Azure Dev Spaces 配合使用来设置持续集成/持续部署
keywords: Docker, Kubernetes, Azure, AKS, Azure 容器服务, 容器
ms.openlocfilehash: 142f01a04066b919a81e1ad1ea95efa31f51f6ca
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548727"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>结合使用 CI/CD 与 Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

本文逐步介绍了如何在已启用 Dev Spaces 的 Azure Kubernetes 服务 (AKS) 中设置持续集成/持续部署 (CI/CD)。 通过在 AKS 中设置 CI/CD，只要已提交的代码推送到源存储库，就可以自动部署应用更新。 结合使用 CI/CD 与已启用 Dev Spaces 的群集很有用，因为这样可以确保应用基线不断更新，以供团队使用。

![示例 CI/CD 图](../media/common/ci-cd-simple.png)

虽然本文介绍的是如何使用 Azure DevOps，但相同的概念同样适用于 Jenkins、TeamCity 等 CI/CD 系统。

## <a name="prerequisites"></a>先决条件
* 已启用 Azure Dev Spaces 的 Azure Kubernetes 服务 (AKS) 群集
* [已安装 Azure Dev Spaces CLI](upgrade-tools.md)
* [有项目的 Azure DevOps 组织](/azure/devops/user-guide/sign-up-invite-teammates)
* [Azure 容器注册表 (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * 可详细了解 Azure 容器注册表[管理员帐户](../../container-registry/container-registry-authentication.md#admin-account)
* [授权 AKS 群集从 Azure 容器注册表拉取映像](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>下载示例代码
为了节省时间，接下来创建示例代码 GitHub 存储库的分支。 先转到 https://github.com/Azure/dev-spaces，再选择“创建分支”。 分支创建流程完成后，在本地克隆存储库分支版本。 虽然默认签出的是主分支，但我们已在 azds_updates 分支中做出了一些省时更改，这些更改也应该在分支创建过程中传输。 azds_updates 分支包含要求在 Dev Spaces 教程各部分中手动进行的更新，以及一些用于简化 CI/CD 系统部署的预制 YAML 和 JSON 文件。 可使用 `git checkout -b azds_updates origin/azds_updates` 等命令签出本地存储库的 azds_update 分支。

## <a name="dev-spaces-setup"></a>Dev Spaces 设置
使用 `azds space select` 命令新建名为“dev”的空间。 CI/CD 管道将使用 dev 空间来推送代码更改。 它还将用于创建基于 dev 的子空间。

```cmd
azds space select -n dev
```

当系统提示选择父 dev 空间时，请选择 \<none\>。

创建开发空间后，你需要确定主机后缀。 使用 `azds show-context` 命令显示 Azure Dev Spaces 入口控制器的主机后缀。

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

在上面的示例中，主机后缀为 fedcba098.eus.azds.io。 稍后在创建发布定义时将使用此值。

dev 空间始终包含最新状态的存储库（基线），以便开发人员能够通过 dev 创建子空间，从而在较大的应用上下文中测试独立更改。 Dev Spaces 教程更详细地介绍了此概念。

## <a name="creating-the-build-definition"></a>创建生成定义
在 Web 浏览器中，打开 Azure DevOps 团队项目，并转到“管道”部分。 首先，单击 Azure DevOps 网站右上角的个人资料照片，打开“预览功能”窗格，并禁用“新 YAML 管道创建体验”：

![打开“预览功能”窗格](../media/common/preview-feature-open.png)

要禁用的选项：

![“新 YAML 管道创建体验”选项](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps 预览功能“新 YAML 管道创建体验”暂与创建预定义的生成管道冲突。 目前，必须禁用此功能，才能部署预定义的生成管道。

在 azds_updates 分支中，我们已添加简单的 [Azure 管道 YAML](/azure/devops/pipelines/yaml-schema?tabs=schema)，用于定义 mywebapi 和 webfrontend 所需的生成步骤。

管道 YAML 已签入，路径如下（具体视已选择的语言而定）：`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

若要根据此文件创建管道，请执行以下操作：
1. 在 DevOps 项目主页中，导航到“管道”>“生成”。
1. 选择用于新建生成管道的选项。
1. 选择“GitHub”作为源，授权 GitHub 帐户（如有必要），并选择“dev-spaces”示例应用程序存储库分支版本中的“azds_updates”分支。
1. 选择“配置为代码”或“YAML”作为模板。
1. 此时，便会看到生成管道的配置页。 如上所述，使用 ... 按钮导航到 YAML 文件路径的特定语言路径。 例如，`samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`。
1. 转到“变量”选项卡。
1. 手动添加“dockerId”作为变量，这是 [Azure 容器注册表管理员帐户](../../container-registry/container-registry-authentication.md#admin-account)的用户名。 （如本文先决条件中所述）
1. 手动添加“dockerPassword”作为变量，这是 [Azure 容器注册表管理员帐户](../../container-registry/container-registry-authentication.md#admin-account)的密码。 出于安全目的，请务必指定 dockerPassword 作为“机密”（通过选择锁图标）。
1. 选择“保存并排队”。

此时拥有 CI 解决方案，它会自动为推送到 GitHub 分支版本的 azds_updates 分支的任何更新生成 mywebapi 和 webfrontend。 可以通过导航到 Azure 门户，选择“Azure 容器注册表”，并浏览“存储库”选项卡来验证是否已推送 Docker 映像。生成映像并显示在容器注册表中可能需要几分钟时间。

![Azure 容器注册表存储库](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>创建发布定义

1. 在 DevOps 项目主页中，依次转到“管道”>“发布”
1. 如果处理的是尚未包含发布定义的全新 DevOps 项目，必须先创建空白的发布定义，再继续操作。 只有在现有发布定义的情况下，“导入”选项才会显示在 UI 中。
1. 在左侧，依次单击“+ 新建”按钮和“导入管道”。
1. 单击“浏览”，然后从项目中选择 `samples/release.json`。
1. 单击 **“确定”** 。 请注意，“管道”窗格已加载发布定义编辑页。 另请注意，有一些红色警告图标，指明仍必须配置的群集专用详细信息。
1. 在“管道”窗格的左侧，单击“添加项目”气泡。
1. 在“源”下拉列表中，选择之前创建的生成管道。
1. 对于“默认版本”，选择“带标记的生成管道默认分支中的最新内容”。
1. 将“标记”留空。
1. 将“源别名”设置为“`drop`”。 由于预定义的发布任务使用“源别名”，因此必须设置它。
1. 单击“添加” 。
1. 现在，单击新创建的 `drop` 项目源上的闪电图标，如下所示：

    ![发布项目持续部署设置](../media/common/release-artifact-cd-setup.png)
1. 启用“持续部署触发器”。
1. 将鼠标悬停在“管道”旁边的“任务”选项卡之上，然后单击“开发”以编辑“开发”阶段任务。
1. 验证是否已在“连接类型”下选择“Azure 资源管理器”。 你会看到三个以红色突出显示的下拉控件：![发布定义设置](../media/common/release-setup-tasks.png)
1. 选择用于 Azure Dev Spaces 的 Azure 订阅。 还需要单击“授权”。
1. 选择用于 Azure Dev Spaces 的资源组和群集。
1. 单击“代理作业”。
1. 在“代理池”下选择“托管 Ubuntu 1604”。
1. 将鼠标悬停在顶部的“任务”选择器之上，单击“生产”以编辑“生产”阶段任务。
1. 验证是否已在“连接类型”下选择“Azure 资源管理器”。 选择用于 Azure Dev Spaces 的 Azure 订阅、资源组和群集。
1. 单击“代理作业”。
1. 在“代理池”下选择“托管 Ubuntu 1604”。
1. 单击“变量”选项卡以更新发布的变量。
1. 将 DevSpacesHostSuffix 的值从 UPDATE_ME 更新为主机后缀。 以前运行 `azds show-context` 命令时会显示主机后缀。
1. 依次单击右上方的“保存”和“确定”。
1. 依次单击“保存”按钮旁边的“+ 发布”和“创建发布”。
1. 在“项目”下，验证是否已选择生成管道中的最新生成。
1. 单击“创建”。

此时，自动发布过程便会开始，将 mywebapi 和 webfrontend 图表部署到 dev 顶级空间中的 Kubernetes 群集。 可以在 Azure DevOps Web 门户上监视发布进度：

1. 导航到“管道”下的“发布”部分。
1. 单击示例应用程序的发布管道。
1. 单击最新版本的名称。
1. 将鼠标悬停在“阶段”下的“开发”框之上，然后单击“日志”。

完成所有任务时，即完成了发布。

> [!TIP]
> 如果发布失败并看到“升级失败:等待条件超时”等错误消息，请尝试[使用 Kubernetes 仪表板](../../aks/kubernetes-dashboard.md)检查群集中的 Pod。 如果发现 Pod 无法启动，并看到“无法拉取映像 "azdsexample.azurecr.io/mywebapi:122": rpc 错误:代码 = Unknown 说明 = 来自守护程序的错误响应: 获取 https:\//azdsexample.azurecr.io/v2/mywebapi/manifests/122: 未授权: 需要进行身份验证”等错误消息，可能是因为未授权群集从 Azure 容器注册表拉取映像。 请务必已完成[授权 AKS 群集从 Azure 容器注册表拉取映像](../../aks/cluster-container-registry-integration.md)先决条件。

此时，已针对 Dev Spaces 示例应用的 GitHub 分支拥有全自动 CI/CD 管道。 每当你提交和推送代码，生成管道都会生成 mywebapi 和 webfrontend 映像，并将它们推送到自定义 ACR 实例。 然后，发布管道会将每个应用的 Helm 图表部署到已启用 Dev Spaces 的群集上的 dev 空间。

## <a name="accessing-your-_dev_-services"></a>访问 dev 服务
部署后，可使用如下公共 URL 访问 webfrontend 的 dev 版本：`http://dev.webfrontend.fedcba098.eus.azds.io`。 可以通过运行 `azds list-uri` 命令来查找此 URL： 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>部署到生产中

若要使用本教程中创建的 CI/CD 系统手动将特定版本提升到“生产”阶段，请执行以下操作：
1. 导航到“管道”下的“发布”部分。
1. 单击示例应用程序的发布管道。
1. 单击最新版本的名称。
1. 将鼠标悬停在“阶段”下的“生产”框之上，然后单击“部署”。
    ![提升到生产](../media/common/prod-promote.png)
1. 再次将鼠标悬停在“阶段”下的“生产”框之上，然后单击“日志”。

完成所有任务时，即完成了发布。

CI/CD 管道的“生产”阶段使用负载均衡器（而不是 Dev Spaces 入口控制器）来提供对生产服务的访问权限。 部署在“生产”阶段中的服务可作为 IP 地址（而不是 DNS 名称）进行访问。 在生产环境中，可以选择创建自己的入口控制器来根据自己的 DNS 配置托管服务。

若要确定 webfrontend 服务的 IP，请单击“打印 webfrontend 公共 IP”步骤以展开日志输出。 使用日志输出中显示的 IP 访问 webfrontend 应用程序。

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>生产环境中的 Dev Spaces 检测
虽然 Dev Spaces 检测不会妨碍应用的正常操作，但建议在未启用 Dev Spaces 的 Kubernetes 命名空间中运行生产工作负载。 使用这种类型的 Kubernetes 命名空间意味着，应使用 `kubectl` CLI 创建生产命名空间，或允许 CI/CD 系统在第一个 Helm 部署过程中创建它。 选择或以其他方式使用 Dev Spaces 工具创建空间会向相应命名空间添加 Dev Spaces 检测。

下面的示例命名空间结构支持功能开发、“开发”环境和生产环境，所有这些都可以在一个 Kubernetes 群集中完成：

![示例命名空间结构](../media/common/cicd-namespaces.png)

> [!Tip]
> 如果已创建 `prod` 空间，并且仅希望从 Dev Spaces 检测中排除（而不是删除！）它，可运行下面的 Dev Spaces CLI 命令：
>
> `azds space remove -n prod --no-delete`
>
> 执行此操作后，可能需要删除 `prod` 命名空间中的所有 Pod。这样一来，无需 Dev Spaces 检测，即可重新创建它们。

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](../how-dev-spaces-works.md)
