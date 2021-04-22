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
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96356255"
---
本部分介绍如何对直接对等互连执行以下修改操作。

### <a name="add-direct-peering-connections"></a>添加直接对等互连连接
1. 选择“+ 添加连接”按钮，然后配置新的对等互连连接。
    > [!div class="mx-imgBorder"]
    > ![“AshburnPeering - 连接”页列出了连接。 “+ 添加连接”按钮已突出显示。](../media/setup-direct-modify-addconnection.png)

1. 填写“直接对等互连连接”表单，然后选择“保存” 。 有关配置对等互连连接的帮助，请查看“创建和预配直接对等互连”部分中的步骤。
    > [!div class="mx-imgBorder"]
    > ![“直接对等互连连接”表单](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>删除直接对等互连连接

Azure 门户目前不支持删除连接。 有关详细信息，请联系 [Microsoft 对等互连](mailto:peeringexperience@microsoft.com)。

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>在“活动连接”上升级或降级带宽
1. 选择要修改的对等互连连接，然后选择“...” > “编辑连接” 。
    > [!div class="mx-imgBorder"]
    > ![编辑连接](../media/setup-direct-modify-editconnection.png)

1. 移动滑块来修改带宽，然后选择“保存”。
    > [!div class="mx-imgBorder"]
    > ![修改带宽](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>在“活动连接”上添加 IPv4 或 IPv6 会话信息
1. 选择要修改的对等互连连接，然后选择“...” > “编辑连接”，如步骤 1 所示 。
1. 输入“会话 IPv4 前缀”或“会话 IPv6 前缀”信息，然后选择“保存”  。

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>在“活动连接”上删除 IPv4 或 IPv6 会话信息
门户目前不支持删除“会话 IPv4 前缀”或“会话 IPv6 前缀”信息 。 有关详细信息，请联系 [Microsoft 对等互连](mailto:peeringexperience@microsoft.com)。
