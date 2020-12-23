---
title: 安全模块和设备孪生
description: 了解安全模块孪生的概念，以及如何在用于 IoT 的 Defender 中使用它们。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: b33c456d47426a3721e8582f24ffd603db0429c9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340027"
---
# <a name="security-module"></a>安全模块

本文介绍 IoT 如何使用设备孪生和模块。

## <a name="device-twins"></a>设备孪生

对于在 Azure 中生成的 IoT 解决方案，设备孪生在设备管理和流程自动化方面发挥着关键作用。

适用于 IoT 的 Defender 可与现有的 IoT 设备管理平台完全集成，使你能够管理设备的安全状态，以及利用现有的设备控制功能。 集成是通过使用 IoT 中心克隆机制实现的。

详细了解 Azure IoT 中心 [设备孪生](../iot-hub/iot-hub-devguide-device-twins.md) 的概念。

## <a name="security-module-twins"></a>安全模块孪生

Defender for IoT 为服务中的每个设备保留安全模块。
安全模块克隆包含与解决方案中每个特定设备的设备安全性相关的所有信息。
设备安全属性保留在专用的安全模块中，以实现更安全的通信，并允许更新和维护所需的资源更少。

若要了解如何创建、自定义和配置克隆，请参阅 [创建安全模块](quickstart-create-security-twin.md) 克隆和 [配置安全代理](how-to-agent-configuration.md) 。 请参阅 [了解 module 孪生](../iot-hub/iot-hub-devguide-module-twins.md) ，了解有关 IoT 中心的模块孪生概念的详细信息。

## <a name="see-also"></a>请参阅

- [用于 IoT 的 Defender 概述](overview.md)
- [部署安全代理](how-to-deploy-agent.md)
- [安全代理身份验证方法](concept-security-agent-authentication-methods.md)