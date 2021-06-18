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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ecc8396204138df05aa779e8bfa09bbfc6cd184d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720885"
---
本部分介绍如何对直接对等互连执行以下修改操作：

* 添加直接对等互连连接。
* 删除直接对等互连连接。
* 升级或降级活动连接上的带宽。
* 在活动连接上添加 IPv4 或 IPv6 会话。
* 删除活动连接上的 IPv4 或 IPv6 会话。

### <a name="add-direct-peering-connections"></a>添加直接对等互连连接

此示例说明如何将连接添加到现有直接对等互连。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>删除直接对等互连连接

PowerShell 当前不支持删除连接。 有关详细信息，请联系 [Microsoft 对等互连](mailto:peeringexperience@microsoft.com)团队。

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>升级或降级活动连接上的带宽

此示例说明如何将 10 Gbps 添加到现有的直接连接。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>在活动连接上添加 IPv4 或 IPv6 会话

此示例说明如何在只具有 IPv4 会话的现有直接连接上添加 IPv6 会话。 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>删除活动连接上的 IPv4 或 IPv6 会话

PowerShell 当前不支持从现有连接中删除 IPv4 或 IPv6 会话。 有关详细信息，请联系 [Microsoft 对等互连](mailto:peeringexperience@microsoft.com)。
