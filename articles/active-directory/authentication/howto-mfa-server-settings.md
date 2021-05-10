---
title: 配置 MFA 服务器 - Azure Active Directory
description: 了解如何在 Azure 门户中配置 Azure MFA 服务器设置
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb28f415b7d08b4c4430ed90a7ccbfe3a333416d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689039"
---
# <a name="configure-mfa-server-settings"></a>配置 MFA 服务器设置

本文可帮助你在 Azure 门户中管理 Azure MFA 服务器设置。

> [!IMPORTANT]
> 从 2019 年 7 月 1 日开始，Microsoft 将不再为新部署提供 MFA 服务器。 希望用户执行多重身份验证的新客户应使用基于云的 Azure AD 多重身份验证。 在 7 月 1 日之前激活了 MFA 服务器的现有客户可以像平时一样下载最新版本、将来的更新以及生成激活凭据。

可使用以下 MFA 服务器设置：

| 功能 | 说明 |
| ------- | ----------- |
| 服务器设置 | 下载 MFA 服务器并生成激活凭据，以便初始化环境 |
| [免验证一次](#one-time-bypass) | 在有限的时间内，允许用户在不执行多重身份验证的情况下进行身份验证。 |
| [缓存规则](#caching-rules) |  缓存主要用于本地系统（例如 VPN）在第一个请求进行中发送多个验证请求的情况。 使用此功能，当用户成功进行第一次验证后，后续请求会自动成功。 |
| 服务器状态 | 查看本地 MFA 服务器的状态，包括版本、状态、IP 以及上次通信时间和日期。 |

## <a name="one-time-bypass"></a>免验证一次

“免验证一次”功能允许用户在不执行多重身份验证的情况下通过一次身份验证。 免验证是暂时性的，会在指定的秒数后过期。 在移动应用或电话无法接收通知或来电的情况下，可以启用“免验证一次”，使用户能够访问所需的资源。

若要创建“免验证一次”，请完成以下步骤：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后浏览到“安全性” > “MFA” > “免验证一次”。
1. 选择 **添加** 。
1. 如有必要，可选择要免验证的复制组。
1. 以 `username@domain.com` 格式输入用户名。 输入免验证应持续的秒数以及免验证的原因。
1. 选择 **添加** 。 时间限制将立即生效。 用户需在免验证一次过期前登录。

还可以在这个相同的窗口中查看“免验证一次”报告。

## <a name="caching-rules"></a>缓存规则

可设置用户使用“缓存”功能进行身份验证后允许身份验证尝试的时间段。 在指定时间段内，用户的后续身份验证尝试将自动成功。

缓存主要用于本地系统（例如 VPN）在第一个请求进行中发送多个验证请求的情况。 使用此功能，当用户成功进行第一次验证后，后续请求会自动成功。

>[!NOTE]
> 缓存功能不应用于登录到 Azure Active Directory (Azure AD)。

若要设置缓存功能，请完成以下步骤：

1. 浏览到“Azure Active Directory” > “安全性” > “MFA” > “缓存规则”。   
1. 选择 **添加** 。
1. 从下拉列表中选择缓存类型。 输入最大缓存秒数。
1. 如有必要，可选择身份验证类型并指定应用程序。
1. 选择 **添加** 。

## <a name="next-steps"></a>后续步骤

其他 MFA 服务器配置选项可从 MFA 服务器本身的 Web 控制台获得。 另请参阅[配置 Azure MFA 服务器以实现高可用性](howto-mfaserver-deploy-ha.md)。
