---
title: include 文件
description: include 文件
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562115"
---
1. 在虚拟 WAN 的页面上，选择“用户 VPN 配置”。
1. 在页面顶部，选择“下载用户 VPN 配置”。下载 WAN 级配置时，你将获得内置的基于流量管理器的用户 VPN 配置文件。 有关全局配置文件或基于中心的配置文件的详细信息，请参阅[中心配置文件](../articles/virtual-wan/global-hub-profile.md)。 使用全局配置文件可简化故障转移方案。

   如果中心由于某种原因而不可用，则该服务提供的内置流量管理可确保（通过不同的中心）连接到点到站点用户的 Azure 资源。 始终可以通过导航到中心来下载特定于中心的 VPN 配置。 在“用户 VPN (点到站点)”下，下载虚拟中心用户 VPN 配置文件 。
1. 完成创建文件后，选择相应的链接下载该文件。
1. 使用此配置文件配置 VPN 客户端。