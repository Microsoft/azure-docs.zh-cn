---
title: 适用于 IoT Edge 的 IoT 安全模块 Defender
description: 了解适用于 IoT Edge 的适用于 IoT 的 Azure Defender 安全模块的体系结构和功能。
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
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 4f6d9f670a1b85e55ccc8f6cb18645b92927221a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351633"
---
# <a name="azure-defender-for-iot-edge-security-module"></a>适用于 IoT Edge security module 的 Azure Defender

[Azure IoT Edge](../iot-edge/index.yml) 提供了强大的功能来管理和执行边缘的业务工作流。
IoT Edge 在 IoT 环境中发挥作用的关键部分使其对于恶意执行组件尤其有吸引力。

Defender for IoT security module 为 IoT Edge 设备提供了一个全面的安全解决方案。
用于 IoT 模块的 Defender 收集、聚合和分析来自你的操作系统和容器系统的原始安全数据到可操作的安全建议和警报。

与用于 IoT 设备的 IoT 安全代理类似，通过其模块克隆可高度自定义 IoT Edge 模块的 defender。
有关详细信息，请参阅 [配置代理](how-to-agent-configuration.md) 。

适用于 IoT Edge 的 IoT 安全模块提供以下功能：

- 从基础操作系统 (Linux) 和 IoT Edge 容器系统收集原始安全事件。

  有关可用的安全数据收集器的详细信息，请参阅 [用于 IoT 代理配置的 Defender](how-to-agent-configuration.md) 。

- 分析 IoT Edge 部署清单。

- 将原始安全事件聚合到通过 [IoT Edge 集线器](../iot-edge/iot-edge-runtime.md#iot-edge-hub)发送的消息中。

- 通过使用安全模块克隆来删除配置。

  若要了解详细信息，请参阅 [配置用于 IoT 代理的 Defender](how-to-agent-configuration.md) 。

IoT Edge 的用于 IoT 安全模块的 Defender 在 IoT Edge 下的特权模式下运行。
要使模块能够监视操作系统和其他 IoT Edge 模块，特权模式是必需的。

## <a name="module-supported-platforms"></a>模块支持的平台

适用于 IoT Edge 的 IoT 安全模块目前仅适用于 Linux。

## <a name="next-steps"></a>后续步骤

本文介绍了有关 IoT Edge 的 IoT 安全模块的 Defender 的体系结构和功能。

若要继续使用适用于 IoT 的 Defender 部署，请使用以下文章：

- 部署 [IoT Edge 的安全模块](how-to-deploy-edge.md)
- 了解如何 [配置安全模块](how-to-agent-configuration.md)
- 查看用于 IoT[服务先决条件](service-prerequisites.md)的 Defender
- 了解如何 [在 Iot 中心为 iot 服务启用 Defender](quickstart-onboard-iot-hub.md)
- 从[用于 IoT 的 DEFENDER 常见问题](resources-frequently-asked-questions.md)中了解有关该服务的详细信息