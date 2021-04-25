---
title: include 文件
titleSuffix: Azure
description: include 文件
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "81680927"
---
本部分介绍如何对直接对等互连执行以下修改操作。

### <a name="add-exchange-peering-connections"></a>添加 Exchange 对等互连连接

1. 选择“+ 添加连接”按钮，然后配置新的对等互连连接。
    > [!div class="mx-imgBorder"]
    > ![对等互连资源视图](../media/setup-exchange-modify-addconnection.png)
1. 填写“Exchange 对等互连连接”表单，然后选择“保存” 。 有关配置对等互连连接的帮助，请查看“创建和预配直接对等互连”部分中的步骤。
    > [!div class="mx-imgBorder"]
    > ![“Exchange 对等互连连接”表单](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>删除 Exchange 对等互连连接

1. 选择要删除的对等互连连接，然后选择“…” > “删除连接” 。
    > [!div class="mx-imgBorder"]
    > ![“删除连接”按钮](../media/setup-exchange-modify-deleteconnection.png)
1. 在“确认删除”框中输入资源 ID，然后选择“删除” 。
    > [!div class="mx-imgBorder"]
    > ![删除确认](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>在活动连接上添加 IPv4 或 IPv6 会话

1. 选择要修改的对等互连连接，然后选择“…” > “编辑连接” 。
    > [!div class="mx-imgBorder"]
    > ![“编辑连接”按钮](../media/setup-exchange-modify-editconnection.png)
1. 添加 IPv4 地址或 IPv6 地址信息，然后选择“保存”  。
    > [!div class="mx-imgBorder"]
    > ![对等互连连接修改](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>删除活动连接上的 IPv4 或 IPv6 会话

门户当前不支持从现有连接中删除 IPv4 或 IPv6 会话。 有关详细信息，请联系 [Microsoft 对等互连](mailto:peeringexperience@microsoft.com)团队。