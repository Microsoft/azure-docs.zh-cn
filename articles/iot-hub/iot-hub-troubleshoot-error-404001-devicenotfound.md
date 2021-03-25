---
title: 排查 Azure IoT 中心错误 404001 DeviceNotFound
description: 了解如何修复错误 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960823"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

本文介绍 **404001 DeviceNotFound** 错误的原因和解决方案。

## <a name="symptoms"></a>症状

在云到设备 (C2D) 通信期间（例如，C2D 消息、孪生更新或直接方法），操作失败且错误为 **404001 DeviceNotFound**。

## <a name="cause"></a>原因

操作失败，因为 IoT 中心找不到设备。 设备未注册或已禁用。

## <a name="solution"></a>解决方案

注册所用的设备 ID，然后重试。