---
title: 教程：使用 Azure Active Directory 为 myPolicies 配置自动用户设置 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 myPolicies 和解除其预配。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 221f63ab9a7eb3f71a4c730a11565dda64c9edc9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353577"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>教程：为 myPolicies 配置自动用户设置

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 myPolicies 以及解除其预配，需在 myPolicies 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。
>
> 此连接器目前以公共预览版提供。 若要详细了解 Microsoft Azure 预览版功能的一般使用条款，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

本教程中概述的方案假定你已具有以下先决条件：

* Azure AD 租户。
* [myPolicies 租户](https://mypolicies.com/index.html#section10)。
* 在 myPolicies 中具有管理员权限的用户帐户。

## <a name="assigning-users-to-mypolicies"></a>将用户分配到 myPolicies

Azure Active Directory 使用称为分配的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户设置之前，应确定 Azure AD 中的哪些用户和/或组需要访问 myPolicies。 确定后，可以按照此处的说明将这些用户和/或组分配到 myPolicies：
* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>将用户分配到 myPolicies 的重要提示

* 建议将单个 Azure AD 用户分配到 myPolicies，以测试自动用户设置配置。 其他用户和/或组可以稍后分配。

* 将用户分配到 myPolicies 时，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="setup-mypolicies-for-provisioning"></a>设置 myPolicies 以进行预配

使用 Azure AD 为 myPolicies 配置自动用户设置之前，需要在 myPolicies 上启用 SCIM 预配。

1. 请通过 support@mypolicies.com 联系 myPolicies 代表，获取配置 SCIM 预配所需的机密令牌。

2.  保存 myPolicies 代表提供的令牌值。 在 Azure 门户的 myPolicies 应用程序的“预配”选项卡中，将此值输入“机密令牌”字段。

## <a name="add-mypolicies-from-the-gallery"></a>从库中添加 myPolicies

若要使用 Azure AD 为 myPolicies 配置自动用户设置，需要从 Azure AD 应用程序库将 myPolicies 添加到托管的 SaaS 应用程序列表。

若要从 Azure AD 应用程序库中添加 myPolicies，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory” 。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。 

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择窗格顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 myPolicies，在结果面板中选择“myPolicies”，然后单击“添加”按钮添加该应用程序  。

    ![结果列表中的 myPolicies](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>配置 myPolicies 的自动用户设置 

本部分介绍如何配置 Azure AD 预配服务，以便根据 Azure AD 中的用户和/或组分配在 myPolicies 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [myPolicies 单一登录教程](mypolicies-tutorial.md)中提供的说明为 myPolicies 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>在 Azure AD 中为 myPolicies 配置自动用户设置：

1. 登录 [Azure 门户](https://portal.azure.com)。 依次选择“企业应用程序”、“所有应用程序” 。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“myPolicies”。

    ![应用程序列表中的 myPolicies 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![“管理”选项的屏幕截图，其中突出显示了“预配”选项。](common/provisioning.png)

4. 将“预配模式”  设置为“自动”  。

    ![“预配模式”下拉列表的屏幕截图，其中突出显示了“自动”选项。](common/provisioning-automatic.png)

5. 在“管理员凭据”部分下的“租户 URL”中，输入 `https://<myPoliciesCustomDomain>.mypolicies.com/scim`，其中 `<myPoliciesCustomDomain>` 是你的 myPolicies 自定义域 。 你可以从 URL 检索你的 myPolicies 客户域。
例如：`<demo0-qa>`.mypolicies.com。

6. 在“机密令牌”中，输入之前检索到的令牌值。 单击“测试连接”，确保 Azure AD 可连接到 myPolicies。 如果连接失败，请确保 myPolicies 帐户具有管理员权限，然后重试。

    ![租户 URL + 令牌](common/provisioning-testconnection-tenanturltoken.png)

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![通知电子邮件](common/provisioning-notification-email.png)

8. 单击“ **保存**”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 myPolicies” 。

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="“映射”部分的屏幕截图。在“名称”下可看到“将 Azure Active Directory 用户同步到 customappsso”。" border="false":::

10. 在“特性映射”部分中，查看从 Azure AD 同步到 myPolicies 的用户特性。 选为“匹配”属性的特性用于匹配 myPolicies 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

   |Attribute|类型|
   |---|---|
   |userName|字符串|
   |活动|Boolean|
   |emails[type eq "work"].value|字符串|
   |name.givenName|字符串|
   |name.familyName|字符串|
   |name.formatted|字符串|
   |externalId|字符串|
   |addresses[type eq "work"].country|字符串|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|参考|


11. 若要配置范围筛选器，请参阅[范围筛选器教程](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

12. 若要为 myPolicies 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”  。

    ![预配状态已打开](common/provisioning-toggle-on.png)

13. 通过在“设置”部分的“范围”中选择所需的值，定义要预配到 myPolicies 的用户和/或组 。

    ![预配范围](common/provisioning-scope.png)

14. 已准备好预配时，单击“保存”  。

    ![保存预配配置](common/provisioning-configuration-save.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，该报告描述了 Azure AD 预配服务对 myPolicies 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* myPolicies 始终需要 userName、email 和 externalId  。
* myPolicies 不支持硬删除用户特性。

## <a name="change-log"></a>更改日志

* 2020/09/15 - 增加了对用户的“country”特性的支持。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
