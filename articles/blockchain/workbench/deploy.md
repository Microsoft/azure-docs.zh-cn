---
title: 部署 Azure 区块链工作台预览版
description: 如何部署 Azure 区块链工作台预览版
ms.date: 07/16/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: b46a35b45a51d0cc76942c4ca142c4c7792a28b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077018"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>部署 Azure 区块链工作台预览版

Azure 区块链工作台预览版是使用 Azure Marketplace 中的解决方案模板部署的。 该模板可以简化创建区块链应用程序所需的组件的部署。 部署后，Blockchain Workbench 提供对客户端应用的访问权限，以创建和管理用户与区块链应用程序。

有关 Blockchain Workbench 组件的详细信息，请参阅 [Azure Blockchain Workbench 体系结构](architecture.md)。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>准备部署

使用 Blockchain Workbench，可部署区块链账本以及最常用于构建基于区块链的应用程序的一组相关 Azure 服务。 部署 Blockchain Workbench 会导致在 Azure 订阅的资源组内预配以下 Azure 服务。

* 应用服务计划 (标准) 
* Application Insights
* 事件网格
* Azure Key Vault
* 服务总线
* SQL Database (标准 S0) 
* Azure 存储帐户 (标准 LRS) 
* 容量为1的虚拟机规模集
* 虚拟网络资源组 (负载平衡器、网络安全组、公共 IP 地址、虚拟网络) 
* Azure 区块链服务。 如果你使用的是以前的区块链工作台部署，请考虑将 Azure 区块链工作台重新部署为使用 Azure 区块链服务。

以下是在 **myblockchain** 资源组中创建的示例部署。

![示例部署](media/deploy/example-deployment.png)

Blockchain Workbench 的成本是基础 Azure 服务成本的总和。 Azure 服务的定价信息可以使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)进行计算。

## <a name="prerequisites"></a>必备条件

Azure Blockchain Workbench 需要 Azure AD 配置和应用程序注册。 可以选择在部署之前[手动配置](#azure-ad-configuration) Azure AD，或者在部署后运行一个脚本。 若要重新部署 Blockchain Workbench，请参阅 [Azure AD 配置](#azure-ad-configuration)以验证 Azure AD 配置。

> [!IMPORTANT]
> Workbench 不必部署在你用来注册 Azure AD 应用程序的同一租户中。 Workbench 必须部署在你有足够的权限来部署资源的租户中。 有关 Azure AD 租户的详细信息，请参阅[如何获取 Active Directory 租户](../../active-directory/develop/quickstart-create-new-tenant.md)和[将应用程序与 Azure Active Directory 集成](../../active-directory/develop/quickstart-register-app.md)。

## <a name="deploy-blockchain-workbench"></a>部署 Blockchain Workbench

完成先决条件步骤后，便可以部署 Blockchain Workbench。 以下部分概述了如何部署框架。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在右上角选择自己的帐户，然后切换到要在其中部署 Azure Blockchain Workbench 的所需 Azure AD 租户。
1. 在 Azure 门户的左上角选择“创建资源”。
1. 选择 "**区块链**  >  **Azure 区块链工作台 (预览") **。

    ![创建 Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | 设置 | 说明  |
    |---------|--------------|
    | 资源前缀 | 部署的短唯一标识符。 此值用作资源命名的基础。 |
    | VM 用户名 | 该用户名用作所有虚拟机 (VM) 的管理员。 |
    | 身份验证类型 | 选择是要使用密码还是密钥连接到 VM。 |
    | Password | 使用密码连接到 VM。 |
    | SSH | 使用单行格式（以 **ssh-rsa** 开头）的 RSA 公钥，或使用多行 PEM 格式。 可以在 Linux 和 OS X 上使用 `ssh-keygen` 生成 SSH 密钥，或在 Windows 上使用 PuTTYGen 生成这些密钥。 有关 SSH 密钥的详细信息，请参阅[如何在 Azure 上的 Windows 中使用 SSH 密钥](../../virtual-machines/linux/ssh-from-windows.md)。 |
    | Database 和区块链密码 | 指定密码，用于访问在部署过程中创建的数据库。 密码必须满足以下四个条件中的三个要求：长度需要介于 12 & 72 个字符、1个小写字符、1个大写字符、1个数字和1个不是数字符号 ( # ) ，% (% ) ，逗号 (，) ，星号 ( * ) ，后跟引号 () ，双引号 ( ) ， ( \` |
    | 部署区域 | 指定部署 Blockchain Workbench 资源的位置。 为了获得最佳可用性，这应与 **区域** 位置设置匹配。 预览期间并非所有区域都可用。 功能在某些地区可能不可用。 Azure 区块链数据管理器在以下 Azure 区域提供：“美国东部”和“西欧”。|
    | 订阅 | 指定要用于部署的 Azure 订阅。 |
    | 资源组 | 选择“新建”创建新资源组，并指定唯一的资源组名称。**** |
    | 位置 | 指定要将框架部署到的区域。 |

1. 选择“确定”完成基本设置配置部分。****

1. 在“高级设置”中，选择是要创建新的区块链网络，还是使用现有的权威证明区块链网络。****

    对于**新建**：

    " *新建" 选项使用* 默认的基本 Sku 部署 Azure 区块链服务仲裁分类帐。

    ![新区块链网络的高级设置](media/deploy/advanced-blockchain-settings-new.png)

    | 设置 | 说明  |
    |---------|--------------|
    | Azure 区块链服务定价层 | 选择用于区块链工作台的 **基本** 或 **标准** Azure 区块链服务层 |
    | Azure Active Directory 设置 | 选择“稍后添加”。****</br>注意：如果选择[预配置 Azure AD](#azure-ad-configuration) 或要重新部署，请选择“立即添加”。** |
    | VM 选择 | 选择区块链网络的首选存储性能和 VM 大小。 如果使用具有较低服务限制的订阅（如 Azure 免费层），请选择较小的 VM（如标准 DS1 v2**）。 |

    对于**使用现有**：

    “使用现有”** 选项允许你指定 Ethereum 权威证明 (PoA) 区块链网络。 终结点具有以下要求。

   * 终结点必须是 Ethereum 权威证明 (PoA) 区块链网络。
   * 终结点必须可通过网络公开访问。
   * PoA 区块链网络应配置为将天然气价格设置为零。

     > [!NOTE]
     > Blockchain Workbench 帐户不会获得资助。 如果需要资金，交易将会失败。

     ![现有区块链网络的高级设置](media/deploy/advanced-blockchain-settings-existing.png)

     | 设置 | 说明  |
     |---------|--------------|
     | Ethereum RPC 终结点 | 提供现有 PoA 区块链网络的 RPC 终结点。 终结点以 https:// 或 http:// 开头，以端口号结尾。 例如 `http<s>://<network-url>:<port>` |
     | Azure Active Directory 设置 | 选择“稍后添加”。****</br>注意：如果选择[预配置 Azure AD](#azure-ad-configuration) 或要重新部署，请选择“立即添加”。** |
     | VM 选择 | 选择区块链网络的首选存储性能和 VM 大小。 如果使用具有较低服务限制的订阅（如 Azure 免费层），请选择较小的 VM（如标准 DS1 v2**）。 |

1. 选择 " **查看 + 创建** " 完成高级设置。

1. 查看摘要，验证参数是否准确。

    ![总结](media/deploy/blockchain-workbench-summary.png)

1. 选择“创建”并同意条款，以部署 Azure Blockchain Workbench。****

部署最长可能需要花费 90 分钟。 可以使用 Azure 门户监视进度。 在新建的资源组中，选择“部署”>“概述”查看已部署项目的状态。****

> [!IMPORTANT]
> 部署后，需要完成 Active Directory 设置。 如果选择了“稍后添加”，则需要运行 [Azure AD 配置脚本](#azure-ad-configuration-script)。****  如果选择了“立即添加”，则需要[配置回复 URL](#configuring-the-reply-url)。****

## <a name="blockchain-workbench-web-url"></a>区块链工作台 web URL

完成 Blockchain Workbench 的部署后，某个新资源组会包含你的 Blockchain Workbench 资源。 通过 Web URL 访问 Blockchain Workbench 服务。 以下步骤说明如何检索已部署框架的 Web URL。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择 " **资源组**"。
1. 选择部署 Blockchain Workbench 时指定的资源组名称。
1. 选择“类型”列标题，按类型的字母顺序将列表排序。****
1. 有两个类型为“应用服务”的资源。**** 选择类型为“应用服务”且不带“-api”后缀的资源。**** **

    ![应用服务列表](media/deploy/resource-group-list.png)

1. 在 "应用服务 **概述**" 中，复制 " **URL** " 值，该值表示已部署的区块链工作台的 web URL。

    ![应用服务概要](media/deploy/app-service.png)

若要将自定义域名与 Blockchain Workbench 相关联，请参阅[使用流量管理器为 Azure 应用服务中的 Web 应用配置自定义域名](../../app-service/configure-domain-traffic-manager.md)。

## <a name="azure-ad-configuration-script"></a>Azure AD 配置脚本

必须配置 Azure AD 才能完成 Blockchain Workbench 部署。 我们将使用 PowerShell 脚本来完成配置。

1. 在浏览器中，导航到 [Blockchain Workbench Web URL](#blockchain-workbench-web-url)。
1. 此时会显示有关使用 Cloud Shell 设置 Azure AD 的说明。 复制该命令并启动 Cloud Shell。

    ![启动 AAD 脚本](media/deploy/launch-aad-script.png)

1. 选择 Blockchain Workbench 部署到的 Azure AD 租户。
1. 在 Cloud Shell PowerShell 环境中，粘贴并运行命令。
1. 出现提示时，请输入要用于 Blockchain Workbench 的 Azure AD 租户。 这是包含 Blockchain Workbench 用户的租户。

    > [!IMPORTANT]
    > 经身份验证的用户需要拥有创建 Azure AD 应用程序注册，以及在租户中授予委托应用程序权限的权限。 可能需要请求租户管理员来运行 Azure AD 配置脚本或创建新租户。

    ![输入 Azure AD 租户](media/deploy/choose-tenant.png)

1. 系统会提示你使用浏览器在 Azure AD 租户中进行身份验证。 在浏览器中打开 Web URL，输入代码，然后进行身份验证。

    ![使用代码进行身份验证](media/deploy/authenticate.png)

1. 脚本将输出多个状态消息。 如果已成功预配租户，将显示 **SUCCESS** 状态消息。
1. 导航到 Blockchain Workbench URL。 系统会要求你许可授予对目录的读取权限。 这样，Blockchain Workbench Web 应用便可以访问租户中的用户。 租户管理员可以选择向整个组织提供许可。 此选项接受租户中所有用户的许可。 否则，在首次使用 Blockchain Workbench Web 应用程序时，系统会提示每个用户提供许可。
1. 选择“接受”以提供许可。****

     ![许可读取用户个人资料](media/deploy/graph-permission-consent.png)

1. 许可后，便可以使用 Blockchain Workbench Web 应用。

已完成 Azure 区块链工作台部署。 请参阅 [后续步骤](#next-steps) ，了解如何开始使用你的部署。

## <a name="azure-ad-configuration"></a>Azure AD 配置

如果选择在部署之前手动配置或验证 Azure AD 设置，请完成本部分中的所有步骤。 若要自动配置 Azure AD 设置，请在部署 Blockchain Workbench 后使用 [Azure AD 配置脚本](#azure-ad-configuration-script)。

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API 应用注册

Blockchain Workbench 部署要求注册 Azure AD 应用程序。 需要使用 Azure Active Directory (Azure AD) 来注册应用。 可以使用现有租户，也可以创建新租户。 如果使用现有的 Azure AD 租户，则需要拥有足够的权限才能在 Azure AD 租户中注册应用程序、授予图形 API 权限和允许来宾访问。 如果在现有的 Azure AD 租户中没有足够的权限，则创建一个新租户。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在右上角选择自己的帐户，然后切换到所需的 Azure AD 租户。 租户应是订阅管理员的订阅管理员租户，其中部署了 Azure 区块链工作台，你有足够的权限来注册应用程序。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务****。 选择**应用注册**"  >  **新注册**"。

    ![应用注册](media/deploy/app-registration.png)

1. 提供显示 **名称** 并仅选择 **此组织目录中的帐户**。

    ![创建应用注册](media/deploy/app-registration-create.png)

1. 选择 " **注册** " 以注册 Azure AD 应用程序。

### <a name="modify-manifest"></a>修改清单

接下来，需将清单修改为使用 Azure AD 中的应用程序角色，以指定 Blockchain Workbench 管理员。  有关应用程序清单的详细信息，请参阅 [Azure Active Directory 应用程序清单](../../active-directory/develop/reference-app-manifest.md)。

1. 清单需要 GUID。 可以使用 PowerShell 命令 `[guid]::NewGuid()` 或 cmdlet 生成 GUID `New-GUID` 。 还可以使用 GUID 生成器网站。
1. 对于注册的应用程序，请选择 "**管理**" 部分中的 "**清单**"。
1. 接下来，更新清单的 **appRoles** 部分。 替换 `"appRoles": []` 为提供的 JSON。 请确保将该字段的值替换为 `id` 生成的 GUID。
    ![编辑清单](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > 需要使用“管理员”值来标识 Blockchain Workbench 管理员。****

1. 在清单中，还需要将“Oauth2AllowImplicitFlow”值更改为“true”********。

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. 选择“保存”以保存清单更改。****

### <a name="add-graph-api-required-permissions"></a>添加图形 API 所需的权限

API 应用程序需要从用户请求目录访问权限。 为 API 应用程序设置以下所需权限：

1. 在 *区块链 API* 应用注册中，选择 " **API 权限**"。 默认情况下，将添加图形 API **用户读取** 权限。
1. 工作台应用程序需要用户的基本个人资料信息的读取访问权限。 在 " *配置的权限*" 中，选择 " **添加权限**"。 在 " **Microsoft api**" 中，选择 **Microsoft Graph**。
1. 由于工作台应用程序使用经过身份验证的用户凭据，请选择 " **委托的权限**"。
1. 在 " *用户* " 类别中，选择 " **user.readbasic.all** " 权限。

    ![Azure AD 应用注册配置，其中显示添加 Microsoft Graph User.readbasic.all。所有委托权限](media/deploy/add-graph-user-permission.png)

    选择“添加权限”。

1. 在 " *配置的权限*" 中，选择 "授予域的 **管理员许可** "，然后在验证提示中选择 **"是"** 。

   ![授予权限](media/deploy/client-app-grant-permissions.png)

   授予权限可让 Blockchain Workbench 访问目录中的用户。 在 Blockchain Workbench 中搜索和添加成员需有读取权限。

### <a name="get-application-id"></a>获取应用程序 ID

部署时需要应用程序 ID 和租户信息。 请收集并存储这些信息，以便在部署期间使用。

1. 对于注册的应用程序，选择 " **概述**"。
1. 复制并存储 **应用程序 ID** 值以供以后在部署过程中使用。

    ![API 应用属性](media/deploy/app-properties.png)

    | 要存储的设置  | 在部署中使用 |
    |------------------|-------------------|
    | 应用程序（客户端）ID | “Azure Active Directory 设置”>“应用程序 ID” |

### <a name="get-tenant-domain-name"></a>获取租户域名

收集并存储要在其中注册应用程序的 Active Directory 租户域名。

在左侧导航窗格中，选择“Azure Active Directory”服务****。 选择“自定义域名”。 复制并存储域名。

![域名](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>来宾用户设置

如果 Azure AD 租户中包含来宾用户，请执行附加的步骤，以确保正常分配和管理 Blockchain Workbench 用户。

1. 切换 Azure AD 租户，并选择“Azure Active Directory”>“用户设置”>“管理外部协作设置”。****
1. 将“来宾用户权限受限”设置为“否”。********
    ![外部协作设置](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>配置回复 URL

部署 Azure Blockchain Workbench 之后，必须配置已部署的 Blockchain Workbench Web URL 的 Azure Active Directory (Azure AD) 客户端应用程序“回复 URL”。****

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 验证是否位于 Azure AD 客户端应用程序所注册到的租户中。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务****。 选择 **“应用注册”**。
1. 选择在先决条件部分中注册的 Azure AD 客户端应用程序。
1. 选择“身份验证”。
1. 指定在 [区块链工作台 WEB url](#blockchain-workbench-web-url) 部分中检索到的 Azure 区块链工作台部署的主 web URL。 回复 URL 带有 `https://` 前缀。 例如 `https://myblockchain2-7v75.azurewebsites.net`

    ![身份验证回复 Url](media/deploy/configure-reply-url.png)

1. 在 " **高级设置** " 部分中，检查 " **访问令牌** " 和 " **ID 令牌**"。

    ![身份验证高级设置](media/deploy/authentication-advanced-settings.png)

1. 选择“保存”更新客户端注册。****

## <a name="remove-a-deployment"></a>删除部署

不再需要部署时，可以通过删除 Blockchain Workbench 资源组来删除部署。

1. 在 Azure 门户中，导航至左侧导航窗格中的“资源组”，然后选择要删除的资源组。
1. 选择“删除资源组”。 输入资源组名称确认删除并选择“删除”。

    ![删除资源组](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了如何部署 Azure Blockchain Workbench。 若要了解如何创建区块链应用程序，请继续学习下一篇操作指南文章。

> [!div class="nextstepaction"]
> [在 Azure Blockchain Workbench 中创建区块链应用程序](create-app.md)
