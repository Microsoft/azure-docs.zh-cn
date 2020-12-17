---
title: 使用 Azure AD B2C 的复原最终用户体验 |Microsoft Docs
description: 使用 Azure AD B2C 在最终用户体验中构建复原能力的方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c695466fbd50435a85c63842ceb50ce80765760
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630286"
---
# <a name="resilient-end-user-experience"></a>复原最终用户体验

注册和登录最终用户体验由以下几个元素组成：

- 用户与之交互的接口（如 CSS、HTML 和 JavaScript）

- 用户流和你创建的自定义策略-例如注册、登录和配置文件编辑

- 标识提供程序 (应用程序的 Idp) -例如本地帐户用户名/密码、Outlook、Facebook 和 Google

![图像显示最终用户体验组件](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>在用户流和自定义策略之间进行选择  

为帮助您设置最常见的标识任务，Azure AD B2C 提供内置的可配置 [用户流](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-overview)。 你还可以构建你自己的 [自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)，这为你提供最大的灵活性。 但建议仅使用自定义策略来处理复杂的情况。

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>如何确定用户流和自定义策略

如果用户可以满足您的业务需求，请选择内置用户流。 由于 Microsoft 的广泛测试，你可以最大程度地减少对这些标识用户流的策略级别功能、性能或规模进行验证所需的测试。 你仍需要测试应用程序的功能、性能和缩放性。

由于你的业务要求，你应 [选择自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) ，请确保对功能、性能或规模以及应用程序级别的测试执行策略级测试。

请参阅 [比较用户流和自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview#comparing-user-flows-and-custom-policies) 以帮助你做出决定的文章。

## <a name="choose-multiple-idps"></a>选择多个 Idp

当使用 [外部标识提供者](https://docs.microsoft.com/azure/active-directory-b2c/technical-overview#external-identity-providers) （如 Facebook）时，请确保在外部提供商不可用的情况下获得备用计划。

### <a name="how-to-set-up-multiple-idps"></a>如何设置多个 Idp

作为外部标识提供者注册过程的一部分，请包含经过验证的标识声明，如用户的移动电话号码或电子邮件地址。 提交验证的声明到基础 Azure AD B2C 目录实例。 如果外部提供程序不可用，则还原为验证的标识声明，并回退到电话号码作为身份验证方法。 另一种方法是向用户发送一次性密码，以允许用户登录。

 请按照以下步骤来 [构建备用身份验证路径](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)：

 1. 配置你的注册策略以允许本地帐户和外部 Idp 注册。

 2. 配置配置文件策略以允许用户在登录后将 [其他标识链接到其帐户](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) 。

 3. 通知并允许用户在中断期间 [切换到备用 IDP](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-with-html#configure-dynamic-custom-page-content-uri) 。

## <a name="availability-of-multi-factor-authentication"></a>多重身份验证的可用性

使用 [电话服务进行多重身份验证时 (MFA) ](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)，请务必考虑备选服务提供商。 本地电信或电话服务提供商可能会在其服务中遇到中断。

### <a name="how-to-choose-an-alternate-mfa"></a>如何选择备用 MFA  

Azure AD B2C 服务使用内置的基于电话的 MFA 提供程序，以提供基于时间的一次性密码 (OTPs) 。 它以语音呼叫和短信形式发送给用户的预注册电话号码。 以下替代方法适用于各种方案：

使用 **用户流** 时，有两种方法可生成复原能力：

- **更改用户流配置**：检测到基于手机的 OTP 传递中的中断后，请将 OTP 传递方法从电话更改为基于电子邮件的，并重新部署用户流，使应用程序保持不变。

![屏幕截图显示登录注册](media/resilient-end-user-experiences/create-sign-in.png)

- **更改应用程序**：对于每个标识任务（例如，注册和登录），定义两组用户流。 将第一个设置为使用基于电话的 OTP，将第二个设置为使用基于电子邮件的 OTP。 检测到基于手机的 OTP 传递中断后，更改和重新部署应用程序，使其从第一组用户流切换到第二组，使用户保持不变。  

使用 **自定义策略** 时，有四种方法可生成复原能力。 下面列出了复杂性，需要重新部署更新的策略。

- **允许用户选择基于电话的 otp 或基于电子邮件的 otp**：向用户公开两个选项，并使用户能够自行选择其中一个选项。 不需要对策略或应用程序进行更改。

- 在 **基于电话的 otp 和基于电子邮件的 otp 之间动态切换**：注册时收集电话和电子邮件信息。 提前定义自定义策略，以有条件地在电话中断期间切换，从电话到基于电子邮件的 OTP 交付。 不需要对策略或应用程序进行更改。

- **使用验证器应用**：更新自定义策略以使用 [验证器应用](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp)。 如果你的普通 MFA 是基于电话或基于电子邮件的 OTP，则重新部署自定义策略以切换为使用验证器应用。

>[!Note]
>用户需要在注册过程中配置验证器应用集成。

- **使用安全问题**：如果上述任何方法均不适用，请将安全问题作为备份进行实施。 在载入或配置文件编辑过程中为用户设置安全问题，并将答案存储在目录以外的其他数据库中。 此方法并不满足 "你有什么" 的 MFA 要求，例如 "电话"，但提供辅助 "了解你知道的内容"。

## <a name="use-a-content-delivery-network"></a>使用内容交付网络

与用于存储自定义用户流 UI 的 blob 存储相比， (Cdn) 的内容交付网络具有更好的性能和更低的成本。 从高可用性服务器的地理上分散的网络中，可以更快地交付网页内容。  

定期通过端到端方案和负载测试来测试你的 CDN 的可用性和内容分发性能。 如果要规划即将发生的冲击，因为促销或假期流量，请修改负载测试的估计值。
  
## <a name="next-steps"></a>后续步骤

- [面向 Azure AD B2C 开发人员的复原资源](resilience-b2c.md)
  
  - [具有外部进程的弹性接口](resilient-external-processes.md)
  - [通过开发人员的最佳做法实现复原](resilience-b2c-developer-best-practices.md)
  - [通过监视和分析进行恢复](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在应用程序中提高身份验证和授权的复原能力](resilience-app-development-overview.md)
