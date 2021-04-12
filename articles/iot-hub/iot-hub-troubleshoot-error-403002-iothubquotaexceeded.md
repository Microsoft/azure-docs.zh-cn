---
title: 排查 Azure IoT 中心错误 403002 IoTHubQuotaExceeded
description: 了解如何修复错误 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3521933baa8dc328910fbacd8b1b6768832fa5f1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061309"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

本文介绍 **403002 IoTHubQuotaExceeded** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

对 IoT 中心的所有请求都失败，并出现错误 **403002 IoTHubQuotaExceeded**。 在 Azure 门户中，无法加载 IoT 中心设备列表。

## <a name="cause"></a>原因

已超过 IoT 中心的每日消息配额。 

## <a name="solution"></a>解决方案

[升级或增加 IoT 中心的单位数](iot-hub-upgrade.md)，或等待下一 UTC 日期，以刷新每日配额。

## <a name="next-steps"></a>后续步骤

* 若要了解如何将操作计入配额（如孪生查询和直接方法），请参阅[了解 IoT 中心定价](iot-hub-devguide-pricing.md#charges-per-operation)
* 若要针对每日配额使用情况来设置监视，请设置一个警报，其指标为“使用的消息总数”。 有关分步说明，请参阅[使用 IoT 中心设置指标和警报](tutorial-use-metrics-and-diags.md#set-up-metrics)