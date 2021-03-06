---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 FortiWeb Web 应用防火墙的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 FortiWeb Web 应用防火墙之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731930"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>教程：Azure Active Directory 单一登录 (SSO) 与 FortiWeb Web 应用防火墙的集成

本教程介绍如何将 FortiWeb Web 应用防火墙与 Azure Active Directory (Azure AD) 集成。 将 FortiWeb Web 应用防火墙与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 FortiWeb Web 应用防火墙。
* 让用户使用其 Azure AD 帐户自动登录到 FortiWeb Web 应用防火墙。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 FortiWeb Web 应用防火墙单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* FortiWeb Web 应用防火墙支持 SP 发起的 SSO。

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>从库中添加 FortiWeb Web 应用防火墙

若要配置 FortiWeb Web 应用防火墙与 Azure AD 的集成，需从库中将 FortiWeb Web 应用防火墙添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“FortiWeb Web 应用防火墙” 。
1. 从结果面板中选择“FortiWeb Web 应用防火墙”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>配置并测试 FortiWeb Web 应用防火墙的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 FortiWeb Web 应用防火墙的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 FortiWeb Web 应用防火墙相关用户之间建立链接关系。

若要配置并测试 FortiWeb Web 应用防火墙的 Azure AD SSO，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 FortiWeb Web 应用防火墙 SSO](#configure-fortiweb-web-application-firewall-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 FortiWeb Web 应用防火墙测试用户](#create-fortiweb-web-application-firewall-test-user)** - 在 FortiWeb Web 应用防火墙中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 Azure 门户的“FortiWeb Web 应用防火墙”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

   a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://www.<CUSTOMER_DOMAIN>.com`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://www.<CUSTOMER_DOMAIN>.com` 

    d. 在“注销 URL”文本框中，使用以下模式键入 URL：`https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` 是稍后为 FortiWeb 提供配置时将使用的名称标识符。
    > 请联系 [FortiWeb Web 应用防火墙支持团队](mailto:support@fortinet.com)以获取实际的 URL 值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 FortiWeb Web 应用防火墙的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“FortiWeb Web 应用防火墙”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。
1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果你希望将某角色分配给用户，可以从“选择角色”下拉列表中选择该角色。 如果尚未为此应用设置任何角色，你将看到选择了“默认访问权限”角色。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-fortiweb-web-application-firewall-sso"></a>配置 FortiWeb Web 应用防火墙 SSO

1.  导航到 `https://<address>:8443`，其中 `<address>` 是分配给 FortiWeb VM 的 FQDN 或公共 IP 地址。

2.  使用在 FortiWeb VM 部署期间提供的管理员凭据登录。

1. 在下面的页中执行以下步骤。

    ![SAML 服务器页面](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  在左侧菜单中，单击“用户”。

    b.  在“用户”下单击“远程服务器”。

    c.  单击“SAML 服务器”。

    d.  单击 **“新建”**。

    e.  在“名称”字段中，提供在“配置 Azure AD”部分中使用的 `<fwName>` 的值。

    f.  在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    g. 单击“元数据”旁边的“选择文件”，并选择从 Azure 门户下载的“联合元数据 XML”文件  。

    h.  单击“确定”。

### <a name="create-a-site-publishing-rule"></a>创建站点发布规则

1.  导航到 `https://<address>:8443`，其中 `<address>` 是分配给 FortiWeb VM 的 FQDN 或公共 IP 地址。

1.  使用在 FortiWeb VM 部署期间提供的管理员凭据登录。
1. 在下面的页中执行以下步骤。

    ![站点发布规则](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  在左侧菜单中，单击“应用程序交付”。
    
    b.  在“应用程序交付”下，单击“站点发布” 。
    
    c.  在“站点发布”下，单击“站点发布” 。
    
    d.  单击“站点发布规则”。
    
    e.  单击 **“新建”**。
    
    f.  提供站点发布规则的名称。
    
    g.  单击“已发布的站点类型”旁边的“正则表达式” 。
    
    i.  在“已发布的站点”旁边，提供一个字符串，该字符串将与要发布的网站的主机头匹配。
    
    j.  在“路径”旁边，提供一个 /。
    
    k.  选择“客户端身份验证方法”旁边的“SAML 身份验证” 。
    
    l.  在“SAML 服务器”下拉列表中，选择之前创建的 SAML 服务器。
    
    m.  单击“确定”。


### <a name="create-a-site-publishing-policy"></a>创建站点发布策略

1.  导航到 `https://<address>:8443`，其中 `<address>` 是分配给 FortiWeb VM 的 FQDN 或公共 IP 地址。

2.  使用在 FortiWeb VM 部署期间提供的管理员凭据登录。

1. 在下面的页中执行以下步骤。

    ![站点发布策略](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  在左侧菜单中，单击“应用程序交付”。

    b.  在“应用程序交付”下，单击“站点发布” 。

    c.  在“站点发布”下，单击“站点发布” 。

    d.  单击“站点发布策略”。

    e.  单击 **“新建”**。

    f.  提供站点发布策略的名称。

    g.  单击“确定”。

    h.  单击 **“新建”**。

    i.  在“规则”下拉列表中，选择之前创建的站点发布规则。

    j.  单击“确定”。

### <a name="create-and-assign-a-web-protection-profile"></a>创建和分配 Web 保护配置文件

1.  导航到 `https://<address>:8443`，其中 `<address>` 是分配给 FortiWeb VM 的 FQDN 或公共 IP 地址。

2.  使用在 FortiWeb VM 部署期间提供的管理员凭据登录。
3.  在左侧菜单中，单击“策略”。
4.  在“策略”下，单击“Web 保护配置文件” 。
5.  单击“内联标准保护”，然后单击“克隆” 。
6.  为新的 Web 保护配置文件提供一个名称，然后单击“确定”。
7.  选择新的 Web 保护配置文件，然后单击“编辑”。
8.  在“站点发布”旁边，选择之前创建的站点发布策略。
9.  单击“确定”。
 
    ![站点发布](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. 在左侧菜单中，单击“策略”。
11. 在“策略”下，单击“服务器策略” 。
12. 选择用于发布网站（希望使用 Azure Active Directory 对其进行身份验证）的服务器策略。
13. 单击 **“编辑”** 。
14. 在“Web 保护配置文件”下拉列表中，选择刚创建的 Web 保护配置文件。
15. 单击“确定”。
16. 尝试访问 FortiWeb 发布网站的外部 URL。 应重定向到 Azure Active Directory 以进行身份验证。


### <a name="create-fortiweb-web-application-firewall-test-user"></a>创建 FortiWeb Web 应用防火墙测试用户

在本部分中，你将在 FortiWeb Web 应用防火墙中创建名为 Britta Simon 的用户。 与 [FortiWeb Web 应用防火墙支持团队](mailto:support@fortinet.com)合作，在 FortiWeb Web 应用防火墙平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用以下选项测试 Azure AD 单一登录配置。 

* 在 Azure 门户中单击“测试此应用程序”。 这会重定向到 FortiWeb Web 应用登录 URL，可以从那里启动登录流。 

* 直接转到 FortiWeb Web 应用登录 URL，并从那里启动登录流。

* 你可使用 Microsoft 的“我的应用”。 在“我的应用”中单击 FortiWeb Web 应用磁贴时，会重定向到 FortiWeb Web 应用登录 URL。 有关“我的应用”的详细信息，请参阅[“我的应用”简介](../user-help/my-apps-portal-end-user-access.md)。


## <a name="next-steps"></a>后续步骤

配置 FortiWeb Web 应用防火墙后，即可强制实施会话控制，从而实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。