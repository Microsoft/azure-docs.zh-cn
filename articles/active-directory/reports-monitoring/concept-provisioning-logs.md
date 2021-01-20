---
title: 在 Azure Active Directory 门户中预配日志 (预览版) |Microsoft Docs
description: 在 Azure Active Directory 门户中预配日志报表简介
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/19/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05a514debcf8036a296bbe66b2dd75c7dacacdc2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600739"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>在 Azure Active Directory 门户中预配报表 (预览版) 

Azure Active Directory (Azure AD) 中的报告体系结构由以下部分组成：

- **活动** 
    - **登录** –有关托管应用程序和用户登录活动的使用情况的信息。
    - **审核日志**  - [审核日志](concept-audit-logs.md)提供有关用户和组管理、托管应用程序和目录活动的系统活动信息。
    - **设置日志** -提供有关由 Azure AD 预配服务设置的用户、组和角色的系统活动。 

- **安全性** 
    - 有 **风险的登录**-有 [风险登录](../identity-protection/overview-identity-protection.md)是指可能由不是用户帐户合法所有者执行的登录尝试的指示符。
    - **标记为存在风险的用户** -有 [风险的用户](../identity-protection/overview-identity-protection.md) 是可能已泄露的用户帐户的指示器。

本主题简要介绍预配报表。

## <a name="prerequisites"></a>先决条件

### <a name="who-can-access-the-data"></a>谁可以访问该数据？
* 应用程序所有者可以查看其拥有的应用程序的日志
* 安全管理员、安全读者、报表读者、应用程序管理员和云应用程序管理员角色中的用户
* 具有[provisioningLogs 权限](https://docs.microsoft.com/azure/active-directory/roles/custom-enterprise-app-permissions#full-list-of-permissions)的自定义角色中的用户
* 全局管理员


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>访问预配活动需要哪些 Azure AD 许可证？

租户必须具有与之关联的 Azure AD Premium 许可证，才能查看 "所有预配活动" 报表。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 

## <a name="provisioning-logs"></a>“预配”日志

预配日志提供以下问题的答案：

* 已成功在 ServiceNow 中创建哪些组？
* 哪些用户已成功从 Adobe 删除？
* 在 DropBox 中未成功创建哪些用户？

可以通过在 [Azure 门户](https://portal.azure.com)中 **Azure Active Directory** 边栏选项卡的 "**监视**" 部分选择 "**设置日志**" 来访问设置日志。 某些预配记录可能需要长达两个小时才能在门户中显示。

![“预配”日志](./media/concept-provisioning-logs/access-provisioning-logs.png "“预配”日志")


设置日志有一个默认列表视图，其中显示：

- 标识
- 操作
- 源系统
- 目标系统
- 状态
- 日期


![默认列](./media/concept-provisioning-logs/default-columns.png "默认列")

单击工具栏中的“列”即可自定义列表视图。 

![列选择器](./media/concept-provisioning-logs/column-chooser.png "列选择器")

用于显示其他字段，或者删除已显示的字段。

![可用列](./media/concept-provisioning-logs/available-columns.png "可用列")

选择列表视图中的某个项可获得更详细的信息。

![详细信息](./media/concept-provisioning-logs/steps.png "筛选器")


## <a name="filter-provisioning-activities"></a>筛选预配活动

你可以筛选预配数据。 某些筛选器值基于租户动态填充。 例如，如果租户中没有任何 create 事件，则不会有用于创建的筛选选项。
在默认视图中，您可以选择以下筛选器：

- 标识
- Date
- 状态
- 操作


![添加筛选器](./media/concept-provisioning-logs/default-filter.png "筛选器")

**标识** 筛选器使你能够指定所关注的名称或标识。 此标识可以是用户、组、角色或其他对象。 可以按对象的名称或 ID 进行搜索。 该 ID 因情况而异。 例如，在将 Azure AD 的对象预配到 SalesForce 时，源 ID 是 Azure AD 中用户的对象 ID，而 TargetID 是 Salesforce 中用户的 ID。 从 Workday 预配到 Active Directory 时，源 ID 是 Workday 工作人员员工 ID。 请注意，用户的名称可能并不总是出现在标识列中。 始终会有一个 ID。 


“日期”筛选器用于定义已返回数据的时间范围。  
可能的值有：

- 1 个月
- 7 天
- 30 天
- 24 小时
- 自定义时间范围

选择自定义时间范围时，可以配置开始日期和结束日期。


使用“状态”筛选器，可以选择：

- 全部
- Success
- 失败
- 已跳过



**操作** 筛选器可用于筛选：

- 创建 
- 更新
- 删除
- 禁用
- 其他

此外，对于默认视图的筛选器，还可以设置以下筛选器：

- 作业 ID
- 周期 ID
- 更改 ID
- 源 ID
- 目标 ID
- 应用程序


![选择字段](./media/concept-provisioning-logs/add-filter.png "选择字段")


- **作业 id** -唯一作业 id 与启用了设置的每个应用程序相关联。   

- **周期 ID** -唯一标识设置周期。 你可以共享此 ID 以支持查找发生此事件的周期。

- **更改 ID** -设置事件的唯一标识符。 你可以共享此 ID 以支持查找预配事件。   


- **源系统** -使你能够指定从何处设置标识。 例如，将对象从 Azure AD 设置为 ServiceNow 时，将 Azure AD 源系统。 

- **目标系统** -使你能够指定将标识预配到的位置。 例如，在将对象从 Azure AD 设置为 ServiceNow 时，目标系统为 ServiceNow。 

- **应用程序** -使你可以仅显示显示名称包含特定字符串的应用程序的记录。

 

## <a name="provisioning-details"></a>预配详细信息 

选择 "设置" 列表视图中的项时，可以获得有关此项的更多详细信息。
详细信息按以下类别分组：

- 步骤

- 故障排除和建议

- 修改的属性

- 总结


![预配详细信息](./media/concept-provisioning-logs/provisioning-tabs.png "制表符")



### <a name="steps"></a>步骤

" **步骤** " 选项卡概述了设置对象所需的步骤。 设置对象可能包含以下四个步骤： 

- 导入对象
- 确定对象是否在范围内
- 源和目标之间的匹配对象
- 预配对象 (执行操作-这可能是创建、更新、删除或禁用) 



![屏幕截图显示 "步骤" 选项卡，其中显示了预配步骤。](./media/concept-provisioning-logs/steps.png "筛选器")


### <a name="troubleshoot-and-recommendations"></a>故障排除和建议


" **疑难解答" 和 "建议** " 选项卡提供错误代码和原因。 仅当出现故障时，才可以使用错误信息。 


### <a name="modified-properties"></a>修改的属性

**修改后的属性** 显示旧值和新值。 在没有旧值的情况下，"旧值" 列为空白。 


### <a name="summary"></a>总结

" **摘要** " 选项卡概述源系统和目标系统中的对象发生了什么情况和标识符。 

## <a name="what-you-should-know"></a>要点

- 如果你有一个免费版，Azure 门户会将报告的预配数据存储30天，如果你有一个免费版，则为7天。预配日志可发布到 [log analytics](../app-provisioning/application-provisioning-log-analytics.md) ，以便保留超过30天的保留期。 

- 您可以使用 "更改 ID" 属性作为唯一标识符。 例如，当与产品支持交互时，这很有用。

- 对于不在作用域内的用户，可能会看到跳过的事件。 这是预期情况，特别是在同步作用域设置为 "所有用户和组" 时。 我们的服务将评估租户中的所有对象，即使是超出范围的对象。 

- 预配日志当前在政府云中不可用。 如果你无法访问预配日志，请使用审核日志作为临时解决方法。 

- 预配日志不显示 (适用于 AWS、SalesForce 和 ZenDesk) 的角色导入。 可在审核日志中找到角色导入日志。 

## <a name="error-codes"></a>错误代码

使用下表来更好地了解如何解决在设置日志中可能会发现的错误。 对于缺少的任何错误代码，请使用此页底部的链接提供反馈。 

|错误代码|说明|
|---|---|
|冲突，EntryConflict|更正 "Azure AD" 或 "应用程序" 中冲突的属性值，或者查看匹配的属性配置（如果应该匹配和接管冲突的用户帐户）。 有关配置匹配属性的详细信息，请参阅以下 [文档](../app-provisioning/customize-application-attributes.md) 。|
|TooManyRequests|目标应用拒绝了更新用户的尝试，因为该用户重载并收到太多请求。 无需执行任何操作。 此尝试将自动停用。 Microsoft 还收到此问题的通知。|
|InternalServerError |目标应用返回了意外错误。 目标应用程序可能存在导致此无法正常工作的服务问题。 在40分钟后，此尝试将自动停用。|
|InsufficientRights，MethodNotAllowed，NotPermitted，未授权| Azure AD 能够向目标应用程序进行身份验证，但无权执行更新。 请查看目标应用程序提供的任何说明，并查看相应的应用程序 [教程](../saas-apps/tutorial-list.md)。|
|UnprocessableEntity|目标应用程序返回了意外响应。 目标应用程序的配置可能不正确，或者目标应用程序可能存在导致此操作无法正常工作的服务问题。|
|WebExceptionProtocolError |连接到目标应用程序时出现 HTTP 协议错误。 无需执行任何操作。 在40分钟后，此尝试将自动停用。|
|InvalidAnchor|预配服务以前创建或匹配的用户已不存在。 检查以确保该用户存在。 若要强制重新匹配所有用户，请使用 MS 图形 API [重新启动作业](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)。 请注意，重新启动设置将触发初始周期，这可能需要一些时间才能完成。 它还会删除预配服务用于操作的缓存，这意味着租户中的所有用户和组都必须重新评估，并且某些预配事件可能会被删除。|
|NotImplemented | 目标应用返回了意外响应。 应用的配置可能不正确，或者目标应用可能存在服务问题，导致无法正常工作。 请查看目标应用程序提供的任何说明，并查看相应的应用程序 [教程](../saas-apps/tutorial-list.md)。 |
|MandatoryFieldsMissing, MissingValues |无法创建用户，因为缺少所需的值。 更正源记录中缺少的属性值，或查看匹配的属性配置以确保不省略必填字段。 [详细了解](../app-provisioning/customize-application-attributes.md) 如何配置匹配属性。|
|SchemaAttributeNotFound |无法执行该操作，因为指定的属性在目标应用程序中不存在。 请参阅有关属性自定义的 [文档](../app-provisioning/customize-application-attributes.md) ，并确保配置正确。|
|InternalError |Azure AD 预配服务中发生内部服务错误。 无需执行任何操作。 此尝试将在40分钟内自动重试。|
|InvalidDomain |由于包含无效域名的属性值，无法执行该操作。 更新用户的域名或将其添加到目标应用程序中的允许列表。 |
|超时 |操作无法完成，因为目标应用程序的响应时间太长。 无需执行任何操作。 此尝试将在40分钟内自动重试。|
|LicenseLimitExceeded|无法在目标应用程序中创建用户，因为没有此用户的可用许可证。 为目标应用程序购买其他许可证，或查看用户分配和属性映射配置，以确保为正确的属性分配正确的用户。|
|DuplicateTargetEntries  |操作无法完成，因为在目标应用程序中找到了多个具有配置的匹配属性的用户。 删除目标应用程序中的重复用户，或重新配置属性映射，如 [此处](../app-provisioning/customize-application-attributes.md)所述。|
|DuplicateSourceEntries | 操作无法完成，因为找到多个具有配置的匹配属性的用户。 请删除重复的用户，或重新配置属性映射，如 [此处](../app-provisioning/customize-application-attributes.md)所述。|
|ImportSkipped | 评估每个用户时，我们会尝试从源系统导入用户。 如果导入的用户缺少属性映射中定义的匹配属性，则通常会出现此错误。 如果在匹配属性的用户对象上不存在值，则无法计算范围、匹配或导出更改。 请注意，存在此错误并不表示用户处于范围内，因为我们尚未评估用户的范围。|
|EntrySynchronizationSkipped | 预配服务已成功查询源系统并确定了用户。 用户未采取进一步的操作，已跳过这些操作。 此跳过可能是由于用户超出了作用域，或者用户在目标系统中已存在，无需进行进一步的更改。|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| 执行 GET 请求以检索用户或组时，会在响应中收到多个用户或组。 只应在响应中接收一个用户或组。 [例如](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)，如果我们执行 GET 请求来检索组，并提供筛选器以排除成员，并且你的 SCIM 终结点返回成员，则会引发此错误。|

## <a name="next-steps"></a>后续步骤

* [检查用户设置的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [为 Azure AD 库应用程序配置用户预配时遇到的问题](../app-provisioning/application-provisioning-config-problem.md)
* [预配日志图形 API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)
