---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 AskYourTeam 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 AskYourTeam 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: 5d17d4769825bdf377c2dbe9892ceba429beabb7
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2020
ms.locfileid: "92457611"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>教程：Azure Active Directory 单一登录 (SSO) 与 AskYourTeam 的集成

本教程介绍如何将 AskYourTeam 与 Azure Active Directory (Azure AD) 集成。 将 AskYourTeam 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AskYourTeam。
* 让用户使用其 Azure AD 帐户自动登录到 AskYourTeam。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 AskYourTeam 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* AskYourTeam 支持 **SP 和 IDP** 发起的 SSO
* 配置 AskYourTeam 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-askyourteam-from-the-gallery"></a>从库中添加 AskYourTeam

若要配置 AskYourTeam 与 Azure AD 的集成，需要从库中将 AskYourTeam 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **AskYourTeam** 。 
1. 在结果面板中选择“AskYourTeam”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>配置并测试 AskYourTeam 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 AskYourTeam 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 AskYourTeam 中的相关用户之间建立链接关系。

若要配置并测试 AskYourTeam 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 AskYourTeam SSO](#configure-askyourteam-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 AskYourTeam 测试用户](#create-askyourteam-test-user)** - 在 AskYourTeam 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“AskYourTeam”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback` 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > 这些不是实际值。 使用本教程稍后将会解释的实际“回复 URL”和“登录 URL”值来更新这些值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 AskYourTeam”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 AskYourTeam 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“AskYourTeam”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-askyourteam-sso"></a>配置 AskYourTeam SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 AskYourTeam 网站。

1. 单击“我的组织”。 

    ![显示“我的组织”链接的屏幕截图。](./media/askyourteam-tutorial/user1.png)

1. 单击“集成”。 

    ![显示“集成”链接的屏幕截图。](./media/askyourteam-tutorial/configure1.png)

1. 单击“编辑设置”。 

    ![显示带有“编辑设置”按钮的“单一登录”消息的屏幕截图。](./media/askyourteam-tutorial/configure2.png)

1. 在“编辑单一登录集成”页上执行以下步骤：  

    ![显示“编辑单一登录集成”部分的屏幕截图，可以在其中输入此步骤的值。](./media/askyourteam-tutorial/configure3.png)

    a. 在“SAML 单一登录服务 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  

    b. 在“SAML 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。  

    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。  

    d. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“SAML 签名证书 - Base64”文本框中。  

    > [!NOTE]
    > 或者，也可以单击“选择文件”选项上传“联合元数据 XML”文件。  

    e. 复制“回复 URL (断言使用者服务 URL)”值，并将此值粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。   

    f. 复制“登录 URL”值，并将此值粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。   

    g. 单击“ **保存** ”。

### <a name="create-askyourteam-test-user"></a>创建 AskYourTeam 测试用户

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 AskYourTeam 网站。

1. 单击“我的组织”。 

    ![显示“我的组织”链接的屏幕截图，可在其中开始该任务。](./media/askyourteam-tutorial/user1.png)

1. 单击“用户”并选择“新建用户”。  

    ![显示具有“新建用户”选项的“用户”链接的屏幕截图。](./media/askyourteam-tutorial/user2.png)

1. 在“新建用户”部分执行以下步骤： 

    ![显示“新建用户”部分的屏幕截图，可在其中输入用户信息。](./media/askyourteam-tutorial/user3.png)

    1. 在“名字”文本框中输入用户的名字。 

    1. 在“姓氏”文本框中输入用户的姓氏。 

    1. 在“电子邮件”文本框中输入用户的电子邮件地址，例如 B.Simon@contoso.com。 

    1. 根据组织要求选择用户的“角色”。 

    1. 单击“ **保存** ”。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“AskYourTeam”磁贴时，应会自动登录到设置了 SSO 的 AskYourTeam。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](./tutorial-list.md)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)

- [在 Azure AD 中试用 AskYourTeam](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](/cloud-app-security/proxy-intro-aad)