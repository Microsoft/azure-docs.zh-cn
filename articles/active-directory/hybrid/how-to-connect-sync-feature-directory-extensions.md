---
title: Azure AD Connect 同步：目录扩展 | Microsoft Docs
description: 本主题介绍 Azure AD Connect 中的目录扩展功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74bc659c11c4f43ab3cf85cdc53f704cd07a1cde
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172361"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同步：目录扩展
通过目录扩展，可以使用本地 Active Directory 中的属性扩展 Azure AD 中的架构。 此功能允许使用继续在本地管理的属性来构建 LOB 应用。 可通过[扩展](/graph/extensibility-overview
)使用这些属性。 可以使用 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)查看可用属性。 你还可以使用此功能在 Azure AD 中创建动态组。

目前，没有任何 Microsoft 365 工作负荷使用这些属性。

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>自定义要与 Azure AD 同步的属性

在安装向导的自定义设置路径中配置要同步的其他属性。

> [!NOTE]
> 在 1.2.65.0 之前的 Azure AD Connect 版本中，“可用属性”搜索框区分大小写。

![架构扩展向导](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

安装显示以下属性，它们是有效的候选项：

* “用户”和“组”对象类型
* 单值属性：String、Boolean、Integer、Binary
* 多值属性：String、Binary


>[!NOTE]
> 虽然 Azure AD Connect 支持将多值 Active Directory 属性作为多值目录扩展同步到 Azure AD，但目前无法检索/使用在多值目录扩展属性中上传的数据。

属性列表是从安装 Azure AD Connect 期间创建的架构缓存中读取的。 如果已使用附加属性扩展了 Active Directory 架构，则必须[刷新架构](how-to-connect-installation-wizard.md#refresh-directory-schema)，然后这些新属性才可见。

Azure AD 中的对象最多可以有 100 个目录扩展属性。 最大长度为 250 个字符。 如果属性值更长，则同步引擎会将其截断。

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>向导在 Azure AD 中进行的配置更改

在安装 Azure AD Connect 期间，会注册可以使用这些属性的应用程序。 可以在 Azure 门户中看到此应用程序。 其名称始终为“租户架构扩展应用”  。

![架构扩展应用](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

请确保选择“所有应用程序”  以查看此应用。

这些属性的前面带有扩展名 \_{ApplicationId}\_ 前缀。 对于 Azure AD 租户中的所有属性，ApplicationId 具有相同的值。 本主题中的所有其他方案都需要此值。

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>使用 Microsoft Graph API 查看属性

通过使用 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer#)，这些属性现在可以通过 Microsoft Graph API 获得。

>[!NOTE]
> 在 Microsoft Graph API 中，你需要请求要返回的属性。 按以下所示显式选择属性：`https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`。
>
> 有关详细信息，请参阅 [Microsoft Graph: Use query parameters](/graph/query-parameters#select-parameter)（Microsoft Graph：使用查询参数）。

>[!NOTE]
> 不支持将属性值从 AADConnect 同步到不是由 AADConnect 创建的扩展属性。 这样做可能会产生性能问题和意外的结果。 仅支持在以上所示的中创建的扩展属性以进行同步。

## <a name="use-the-attributes-in-dynamic-groups"></a>使用动态组中的属性

一个更有用的方案是在动态安全或 Microsoft 365 组中使用这些属性。

1. 在 Azure AD 中创建一个新组。 为其提供一个良好的名称，并确保 **成员身份类型** 为 " **动态用户**"。

   ![使用新组的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. 选择以 **添加动态查询**。 如果查看属性，则不会看到这些扩展属性。 需要首先添加它们。 单击 " **获取自定义扩展属性**"，输入应用程序 ID，然后单击 " **刷新属性**"。

   ![添加了目录扩展的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. 打开属性下拉，请注意，添加的属性现在可见。

   ![新属性显示在 UI 中的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   完成表达式以满足你的要求。 在我们的示例中，将规则设置为 **(user.extension_9d98ed114c4840d298fad781915f27e4_division "销售和营销" )**。

4. 创建组后，为 Azure AD 提供一段时间来填充成员，然后查看成员。

   ![包含动态组中成员的屏幕截图](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>后续步骤
了解有关 [Azure AD Connect 同步](how-to-connect-sync-whatis.md)配置的详细信息。

了解有关[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
