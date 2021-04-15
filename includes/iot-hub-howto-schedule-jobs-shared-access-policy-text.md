---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "70049063"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

在本文中，你将创建一项后端服务，该服务计划用于在设备上调用直接方法的作业及计划用于更新设备孪生的作业，并监视每个作业的进度。 若要执行这些操作，服务需要“注册表读取”和“注册表写入”权限。 默认情况下，每个 IoT 中心都使用名为 registryReadWrite的共享访问策略创建，该策略会授予这些权限。
