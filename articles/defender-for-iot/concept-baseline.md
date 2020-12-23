---
title: 基线和自定义检查
description: 了解 Azure Defender for IoT 基线的概念。
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
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: 75c6841b7fed354843b955a971f02946be349a02
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548879"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>用于 IoT 的 Azure Defender 基线和自定义检查

本文介绍 IoT 基线的 Defender，并汇总基线自定义检查的所有相关属性。

## <a name="baseline"></a>基线

基线建立每个设备的标准行为，并使与预期的标准建立异常行为或偏差更为容易。

## <a name="baseline-custom-checks"></a>基线自定义检查

基线自定义检查使用设备的 **模块标识** 克隆为每个设备基线建立检查的自定义列表。

## <a name="setting-baseline-properties"></a>设置基线属性

1. 在 IoT 中心中，找到并选择要更改的设备。
1. 单击该设备，然后单击 " **azureiotsecurity** " 模块。
1. 单击 " **模块标识符**"。
1. 将 **基线自定义检查** 文件上传到设备。
1. 将基线属性添加到 "安全" 模块，并单击 " **保存**"。

### <a name="baseline-custom-check-file-example"></a>基线自定义检查文件示例

配置基线自定义检查：

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>基线自定义检查属性

| 名称| 状态 | 有效值| 默认值| 描述 |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|必需： true |有效值：**布尔** 值 |默认值： **false** |发送高优先级消息之前的最大时间间隔。|
|baselineCustomChecksFilePath |必需： true|有效值： **String**、 **null** |默认值： **null** |基线 xml 配置的完整路径|
|baselineCustomChecksFileHash |必需： true|有效值： **String**、 **null** |默认值： **null** |`sha256sum` xml 配置文件的。 使用 [sha256sum 引用](https://linux.die.net/man/1/sha256sum) 获取其他信息。 |

若要查看其他基线示例，请参阅 [自定义基线示例-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) 和 [自定义基线示例-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)。

## <a name="next-steps"></a>后续步骤

- 访问 [原始安全数据](how-to-security-data-access.md)
- [调查设备](how-to-investigate-device.md)
- 了解并探索 [安全性建议](concept-recommendations.md)
- 了解并探索 [安全警报](concept-security-alerts.md)
