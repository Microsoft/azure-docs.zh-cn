---
title: 排查 Azure IoT 中心错误 412002 DeviceMessageLockLost
description: 了解如何修复错误 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 53364009f9b9c041c39728e438c3e24eacfd1665
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435471"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

本文介绍 **412002 DeviceMessageLockLost** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

尝试发送云到设备的消息时，请求失败，并出现错误 **412002 DeviceMessageLockLost**。

## <a name="cause"></a>原因

当设备通过某种方式（例如，使用 [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) ）从队列接收云到设备的消息时，IoT 中心会将该消息锁定，锁定超时持续时间为一分钟。 如果在锁定超时过期后设备尝试完成消息，则 IoT 中心会引发此异常。

## <a name="solution"></a>解决方案

如果 IoT 中心在一分钟的锁定超时期限内没有收到通知，则会将该消息设置回“已排队”状态。 设备可以再次尝试接收消息。 若要防止将来发生错误，请在接收消息的一分钟内实现设备端逻辑来完成该消息。 无法更改这个一分钟的超时时间。