---
title: 设置 QnA Maker 服务-QnA Maker
description: 在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 83917214705546b21553e997ccab11a7511f77fd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353300"
---
# <a name="manage-qna-maker-resources"></a>管理 QnA Maker 资源

在创建任何 QnA Maker 知识库之前，必须先在 Azure 中设置 QnA Maker 服务。 任何有权在订阅中创建新资源的人都可以设置 QnA Maker 服务。

创建资源之前，了解以下概念非常有用：

* [QnA Maker 资源](../Concepts/azure-resources.md)
* [创作和发布密钥](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>新建 QnA Maker 服务

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

此过程创建管理知识库内容所需的 Azure 资源。 完成这些步骤后，可以在 "**密钥**" 页上找到 Azure 门户资源的 _订阅_ 密钥。

1. 登录到 Azure 门户并 [创建 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 资源。

1. 阅读条款和条件后，选择 " **创建** "：

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 **QnA Maker** 中，选择适当的层和区域：

    ![新建 QnA Maker 服务 - 定价层和区域](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * 在 " **名称** " 字段中，输入唯一的名称来标识此 QnA Maker 服务。 此名称还标识您的知识库将与之关联的 QnA Maker 终结点。
    * 选择将在其下部署 QnA Maker 资源的 **订阅** 。
    * 为 QnA Maker 管理服务 (门户和管理 Api) 选择 **定价层** 。 查看 [有关 SKU 定价的更多详细信息](https://aka.ms/qnamaker-pricing)。
    *  (建议) 创建新的资源组，或使用现有 **资源组** 部署此 QnA Maker 资源。 QnA Maker 创建多个 Azure 资源。 创建用于保存这些资源的资源组时，可以通过资源组名称轻松查找、管理和删除这些资源。
    * 选择 **资源组位置**。
    * 选择 "搜索" Azure 认知搜索服务的 **定价层** 。 如果 "免费层" 选项不可用 (显示灰显) ，这意味着你已通过订阅部署了免费服务。 在这种情况下，你将需要从 "基本" 层开始。 请参阅 [Azure 认知搜索定价详细信息](https://azure.microsoft.com/pricing/details/search/)。
    * 选择要在其中部署 Azure 认知搜索索引的 **搜索位置** 。 对客户数据的存储位置的限制有助于确定为 Azure 认知搜索选择的位置。
    * 在 " **应用名称** " 字段中，输入 Azure App Service 实例的名称。
    * 默认情况下，应用服务默认为标准 (S1) 层。 可以在创建后更改该计划。 了解有关 [应用服务定价](https://azure.microsoft.com/pricing/details/app-service/)的详细信息。
    * 选择将在其中部署应用服务的 **网站位置** 。

        > [!NOTE]
        > **搜索位置** 可能不同于 **网站位置**。

    * 选择是否要启用 **Application Insights**。 如果启用了 **Application Insights**，则 QnA Maker 会收集有关流量、聊天日志和错误的遥测数据。
    * 选择将在其中部署 Application Insights 资源的 **App insights 位置** 。
    * 为了节省成本，可以[共享](#configure-qna-maker-to-use-different-cognitive-search-resource)为 QnA Maker 创建的某些 Azure 资源，但不是所有。

1. 验证所有字段后，选择 " **创建**"。 此过程可能需要几分钟才能完成。

1. 部署完成后，你将看到在订阅中创建的以下资源：

   ![新建 QnA Maker 服务资源](../media/qnamaker-how-to-setup-service/resources-created.png)

    具有 _认知服务_ 类型的资源具有您的 _订阅_ 密钥。

### <a name="upgrade-qna-maker-sku"></a>升级 QnA Maker SKU

如果你想要在你的知识库中获得更多问题和答案，请将 QnA Maker 服务定价层升级。

升级 QnA Maker 管理 SKU：

1. 转到 Azure 门户中的 QnA Maker 资源，然后选择“定价层”。

    ![QnA Maker 资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 选择合适的 SKU 并按“选择”。

    ![QnA Maker 定价](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>升级应用服务

 当您的知识库需要提供客户端应用程序的更多请求时，请升级您的应用服务定价层。

你可以 [纵向扩展](../../../app-service/manage-scale-up.md) 或横向扩展应用服务。

中转到 Azure 门户中的 "应用服务" 资源，并根据需要选择 " **纵向扩展** " 或 " **横向扩展** " 选项。

![QnA Maker 应用服务规模](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="get-the-latest-runtime-updates"></a>获取最新的运行时更新

QnAMaker 运行时是在 Azure 门户中 [创建 QnAMaker 服务](./set-up-qnamaker-service-azure.md) 时部署的 Azure App Service 实例的一部分。 对运行时的更新定期进行。 2019年4月版) 的站点扩展 (发布后，QnA Maker 应用服务实例处于自动更新模式。 此更新旨在在升级过程中处理零停机时间。

您可以在中检查当前版本 https://www.qnamaker.ai/UserSettings 。 如果版本低于版本1.x，则必须重启应用服务才能应用最新更新：

1. 请在 [Azure 门户](https://portal.azure.com)中 (资源组) 中转到你的 QnAMaker 服务。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择应用服务实例，并打开 " **概述** " 部分。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker 应用服务实例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 重新启动应用服务。 更新过程只需几秒钟即可完成。 在此重新启动期间，最终用户将无法使用此 QnAMaker 服务的所有从属应用程序或 bot。

    ![QnAMaker 应用服务实例的重新启动](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>配置应用服务空闲设置以避免超时

用于为已发布的知识库提供 QnA Maker 预测运行时的应用服务具有空闲超时配置，默认情况下，如果服务处于空闲状态，则会自动超时。 对于 QnA Maker，这意味着预测运行时 generateAnswer API 有时会在没有流量的时段超时。

若要使预测终结点应用始终加载（即使没有流量），请将 "空闲" 设置为 "始终打开"。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择 QnA Maker 资源的应用服务。 它将具有与 QnA Maker 资源相同的名称，但它将具有不同 **类型** 的应用服务。
1. 找到 " **设置** "，然后选择 " **配置**"。
1. 在 "配置" 窗格上，选择 " **常规设置**"，然后找到 " **始终打开**"，并选择 **"打开** " 作为值。

    > [!div class="mx-imgBorder"]
    > ![在 "配置" 窗格上，选择 "常规设置"，然后查找 * * Always on * *，然后选择 * * On * * 作为值。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. 选择“保存”以保存配置。
1. 系统会询问你是否要重新启动应用程序以使用新设置。 选择“继续”。 

详细了解如何配置应用服务 [常规设置](../../../app-service/configure-common.md#configure-general-settings)。

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>配置应用服务环境以承载 QnA Maker 应用服务
应用服务环境可用于承载 QnA Maker 应用服务。 如果应用服务环境是内部的，则需要执行以下步骤：
1. 创建应用服务和 Azure 搜索服务。
2. 公开应用服务并允许 QnA Maker 可用性，如下所示：
    * 公开可用-默认
    * DNS 服务标记： `CognitiveServicesManagement`
3. 使用 Azure 资源管理器 (Cognitiveservices account/accounts) 创建 QnA Maker 认知服务实例，其中 QnA Maker 终结点应设置为应用服务环境。

### <a name="network-isolation-for-app-service"></a>应用服务的网络隔离

QnA Maker 认知服务使用服务标记： `CognitiveServicesManagement` 。 请按照以下步骤将 IP 地址范围添加到允许列表：

* 下载 [所有服务标记的 IP 范围](https://www.microsoft.com/download/details.aspx?id=56519)。
* 选择 "CognitiveServicesManagement" 的 Ip。
* 导航到应用服务资源的 "网络" 部分，然后单击 "配置访问限制" 选项，将 Ip 添加到允许列表。

此外，我们还提供了一个自动化脚本，为你的应用服务执行相同的操作。 可在 GitHub 上找到 [用于配置允许列表的 PowerShell 脚本](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) 。 需要输入订阅 id、资源组和实际应用服务名称作为脚本参数。 运行脚本会自动将 Ip 添加到应用服务允许列表中。

### <a name="business-continuity-with-traffic-manager"></a>流量管理器的业务连续性

业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 计划](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上述高级想法如下：

1. 在 [Azure 配对区域](../../../best-practices-availability-paired-regions.md)中设置两个并行 [QnA Maker 服务](set-up-qnamaker-service-azure.md)。

1. [备份](../../../app-service/manage-backup.md) 主 QnA Maker 应用服务，并在辅助安装程序中将其 [还原](../../../app-service/web-sites-restore.md) 。 这将确保两个设置使用相同的主机名和密钥。

1. 使主要和辅助 Azure 搜索索引保持同步。在 [此处](https://github.com/pchoudhari/QnAMakerBackupRestore) 使用 GitHub 示例，了解如何备份-还原 Azure 索引。

1. 使用[连续导出](../../../azure-monitor/app/export-telemetry.md)备份 Application Insights。

1. 主要和辅助堆栈设置完成后，使用[流量管理器](../../../traffic-manager/traffic-manager-overview.md)配置两个终结点并设置路由方法。

1. 需要为流量管理器终结点创建 (TLS) 之前称为安全套接字层 (SSL) 证书的传输层安全性。 绑定应用服务中[的 TLS/SSL 证书](../../../app-service/configure-ssl-bindings.md)。

1. 最后，在机器人或应用中使用流量管理器终结点。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

此过程创建管理知识库内容所需的 Azure 资源。 完成这些步骤后，可以在 "**密钥**" 页上找到 Azure 门户资源的 *订阅* 密钥。

1. 登录到 Azure 门户并 [创建 QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 资源。

1. 阅读条款和条件后，选择 " **创建** "：

    ![新建 QnA Maker 服务](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. 在 **QnA Maker** 中，选中 "托管 (预览版") 复选框，然后选择相应的层和区域：

    ![创建新的 QnA Maker 托管服务-定价层和区域](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * 选择将在其下部署 QnA Maker 资源的 **订阅** 。
    *  (建议) 创建新的资源组，或使用现有的 **资源组** 来部署此 QnA Maker 托管 (预览) 资源。 QnA Maker 托管 (预览版) 会创建几个 Azure 资源。 创建用于保存这些资源的资源组时，可以通过资源组名称轻松查找、管理和删除这些资源。
    * 在 " **名称** " 字段中，输入唯一的名称来标识此 QnA Maker 管理的 (预览版) 服务。 
    * 选择要在其中部署 QnA Maker 管理 (预览版) 服务的 **位置** 。 管理 Api 和服务终结点将托管在此位置。 
    * 选择 QnA Maker 托管 (预览版) 服务 (免费) 预览版 "的 **定价层** 。 查看 [有关 SKU 定价的更多详细信息](https://aka.ms/qnamaker-pricing)。
    * 选择要在其中部署 Azure 认知搜索索引的 **搜索位置** 。 对客户数据的存储位置的限制有助于确定为 Azure 认知搜索选择的位置。
    * 选择 "搜索" Azure 认知搜索服务的 **定价层** 。 如果 "免费层" 选项不可用 (显示灰显) ，这意味着你已通过订阅部署了免费服务。 在这种情况下，你将需要从 "基本" 层开始。 请参阅 [Azure 认知搜索定价详细信息](https://azure.microsoft.com/pricing/details/search/)。

1. 验证所有字段后，选择 " **查看 + 创建**"。 此过程可能需要几分钟才能完成。

1. 部署完成后，你将看到在订阅中创建的以下资源：

    ![资源已创建新 QnA Maker 管理 (预览版) 服务](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    具有 _认知服务_ 类型的资源具有您的 _订阅_ 密钥。

---

## <a name="find-authoring-keys-in-the-azure-portal"></a>在 Azure 门户中查找创作密钥

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

你可以从创建 QnA Maker 资源的 Azure 门户查看和重置创作密钥。 这些密钥可能称为订阅密钥。

1. 中转到 Azure 门户中的 QnA Maker 资源，并选择具有 _认知服务_ 类型的资源：

    ![QnA Maker 资源列表](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 中转到 **密钥**：

    ![订阅密钥](../media/qnamaker-how-to-key-management/subscription-key.PNG)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>在 QnA Maker 门户中查找查询终结点密钥

终结点与资源位于同一区域，因为终结点键用于调用知识库。

可以从 [QnA Maker 门户](https://qnamaker.ai)管理终结点密钥。

1. 登录到 [QnA Maker 门户](https://qnamaker.ai)，中转到你的配置文件，然后选择 " **服务设置**"：

    ![终结点密钥](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 查看或重置密钥：

    > [!div class="mx-imgBorder"]
    > ![终结点密钥管理器](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果你认为密钥已泄露，请刷新你的密钥。 这可能需要相应地更改客户端应用程序或机器人代码。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

你可以从 Azure 门户中查看和重置创作密钥，你可以在其中创建 QnA Maker 托管 (预览版) 资源。 这些密钥可能称为订阅密钥。

1. 请在 Azure 门户中中转到 QnA Maker 托管 (预览版) 资源，并选择具有 *认知服务* 类型的资源：

    ![QnA Maker 托管 (预览版) 资源列表](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. 中转到 **密钥和终结点**：

    ![QnA Maker 托管 (预览版) 订阅密钥](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>更新资源

了解如何升级知识库使用的资源。 QnA Maker 托管 () 预览版，请在预览阶段 **免费** 。 

---

## <a name="upgrade-the-azure-cognitive-search-service"></a>升级 Azure 认知搜索服务

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

如果计划有很多知识库，请升级 Azure 认知搜索服务定价层。

目前不能执行 Azure 搜索 SKU 的就地升级。 但是，你可以使用所需的 SKU 创建新的 Azure 搜索资源、将数据还原到新资源，然后将其链接到 QnA Maker 堆栈。 为此，请执行以下步骤：

1. 在 Azure 门户中创建新的 Azure 搜索资源，并选择所需的 SKU。

    ![QnA Maker Azure 搜索资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 将索引从原始 Azure 搜索资源还原到新资源。 请参阅 [备份还原示例代码](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 还原数据后，请切换到新的 Azure 搜索资源，选择 " **密钥**"，并记下 **名称** 和 **管理密钥**：

    ![QnA Maker Azure 搜索密钥](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 若要将新的 Azure 搜索资源链接到 QnA Maker 堆栈，请中转到 QnA Maker 应用服务实例。

    ![QnA Maker 应用服务实例](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. 选择 " **应用程序设置** "，并修改步骤3中 " **AzureSearchName** " 和 " **AzureSearchAdminKey** " 字段中的设置。

    ![QnA Maker 应用服务设置](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 重新启动应用服务实例。

    ![QnA Maker 应用服务实例的重新启动](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="cognitive-search-consideration"></a>认知搜索注意事项

作为单独的资源认知搜索，你应该注意一些不同的配置。

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>将 QnA Maker 配置为使用不同的认知搜索资源

如果通过门户创建 QnA 服务及其依赖项 (如搜索) ，系统会为您创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

QnA Maker 的 **应用服务** 资源使用认知搜索资源。 若要更改 QnA Maker 使用的认知搜索资源，需要更改 Azure 门户中的设置。

1. 获取要 QnA Maker 使用的认知搜索资源的 **管理员密钥** 和 **名称** 。

1. 登录到 [Azure 门户](https://portal.azure.com) 并找到与 QnA Maker 资源关联的 **应用服务** 。 具有相同名称的。

1. 依次选择 " **设置**"、" **配置**"。 这将显示 QnA Maker 的应用服务的所有现有设置。

    > [!div class="mx-imgBorder"]
    > ![显示应用服务配置设置的 Azure 门户屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 更改以下项的值：

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 若要使用新设置，需要重新启动应用服务。 选择 " **概述**"，然后选择 " **重新启动**"。

    > [!div class="mx-imgBorder"]
    > ![更改配置设置之后 Azure 门户重启应用服务的屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果通过 Azure 资源管理器模板创建 QnA 服务，则可以创建所有资源，并控制应用服务创建，以使用现有搜索服务。

了解有关如何配置应用服务 [应用程序设置](../../../app-service/configure-common.md#configure-app-settings)的详细信息。

### <a name="configuring-cognitive-search-as-a-private-endpoint-inside-a-vnet"></a>将认知搜索配置为 VNET 中的专用终结点

如果在创建 QnA Maker 资源的过程中创建搜索实例，则可以强制认知搜索支持完全在客户 VNet 中创建的专用终结点配置。

必须在同一区域中创建所有资源，才能使用专用终结点。

* QnA Maker 资源
* 新建认知搜索资源
* 新建虚拟网络资源

在 [Azure 门户](https://portal.azure.com)中完成以下步骤：

1. 创建 [QnA Maker 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。
1. 使用终结点连接创建新的认知搜索资源 (数据) 设置为 " _专用_"。 在步骤1中创建的 QnA Maker 资源所在的同一区域中创建资源。 详细了解如何 [创建认知搜索资源](../../../search/search-create-service-portal.md)，然后使用此链接直接前往 [资源的创建页面](https://ms.portal.azure.com/#create/Microsoft.Search)。
1. 创建新的 [虚拟网络资源](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)。
1. 在此过程的步骤1中创建的应用服务资源上配置 VNET。
    1. 在 VNET 中为在步骤2中创建的新认知搜索资源创建新的 DNS 条目。 到认知搜索的 IP 地址。
1. [将应用服务关联到步骤2中创建的新认知搜索资源](#configure-qna-maker-to-use-different-cognitive-search-resource) 。 然后，你可以删除在步骤1中创建的原始认知搜索资源。

在 [QnA Maker 门户](https://www.qnamaker.ai/)中，创建第一个知识库。


### <a name="inactivity-policy-for-free-search-resources"></a>免费搜索资源的非活动策略

如果未使用 QnA maker 资源，则应删除所有资源。 如果不删除未使用的资源，则在创建免费搜索资源时，知识库将停止工作。

免费搜索资源在90天后将被删除，而无需接收 API 调用。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

如果计划有很多知识库，请升级 Azure 认知搜索服务定价层。

目前不能执行 Azure 搜索 SKU 的就地升级。 但是，你可以使用所需的 SKU 创建新的 Azure 搜索资源、将数据还原到新资源，然后将其链接到 QnA Maker 堆栈。 为此，请执行以下步骤：

1. 在 Azure 门户中创建新的 Azure 搜索资源，并选择所需的 SKU。

    ![QnA Maker Azure 搜索资源](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 将索引从原始 Azure 搜索资源还原到新资源。 请参阅 [备份还原示例代码](https://github.com/pchoudhari/QnAMakerBackupRestore)。

1. 若要将新的 Azure 搜索资源链接到 QnA Maker 管理的 (预览版) 服务，请参阅以下主题。

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>将 QnA Maker 托管 (预览版) 服务配置为使用不同的认知搜索资源

如果通过门户创建 QnA 服务托管 (预览版) 及其依赖项 (如搜索) ，则会为您创建一个搜索服务，并将其链接到 QnA Maker 托管 (Preview) 服务。 创建这些资源后，可以在 " **配置** " 选项卡中更新搜索服务。

1. 请在 Azure 门户中转到 QnA Maker 托管 (预览版) 服务。

1. 选择 " **配置** "，然后选择要与 QnA Maker 托管 (预览版) 服务链接的 Azure 认知搜索服务。

    ![QnA Maker managed (Preview) 配置页的屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. 单击“保存”  。

> [!NOTE]
> 如果更改与 QnA Maker 关联的 Azure 搜索服务，将无法访问其中已存在的所有知识库。 请确保在更改 Azure 搜索服务之前导出现有知识库。
### <a name="inactivity-policy-for-free-search-resources"></a>免费搜索资源的非活动策略

如果未使用 QnA maker 资源，则应删除所有资源。 如果不删除未使用的资源，则在创建免费搜索资源时，知识库将停止工作。

免费搜索资源在90天后将被删除，而无需接收 API 调用。

---

## <a name="delete-azure-resources"></a>删除 Azure 资源

如果你删除了用于 QnA Maker 知识库的任何 Azure 资源，则知识库将不再工作。 在删除任何资源之前，请务必从“设置”页导出知识库。

## <a name="next-steps"></a>后续步骤

了解有关 [应用服务](../../../app-service/index.yml) 和 [搜索服务](../../../search/index.yml)的详细信息。

> [!div class="nextstepaction"]
> [了解如何与他人创作](../index.yml)