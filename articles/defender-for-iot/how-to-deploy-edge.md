---
title: 部署 IoT Edge 安全模块
description: 了解如何在 IoT Edge 上部署适用于 IoT security agent 的 Defender。
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
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 75a9d1267a8824801e3c24fc25c687f29a808187
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339925"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>在 IoT Edge 设备上部署安全模块

**Defender For IoT** 模块为 IoT Edge 设备提供了一个全面的安全解决方案。
安全模块将收集、聚合和分析来自你的操作系统和容器系统的原始安全数据到可操作的安全建议和警报。
若要了解详细信息，请参阅 [IoT Edge 的安全模块](security-edge-architecture.md)。

本文介绍如何在 IoT Edge 设备上部署安全模块。

## <a name="deploy-security-module"></a>部署安全模块

使用以下步骤为 IoT Edge 部署用于 IoT 安全模块的 Defender。

### <a name="prerequisites"></a>先决条件

1. 在 IoT 中心中，确保你的设备已 [注册为 IoT Edge 设备](../iot-edge/how-to-manual-provision-symmetric-key.md#register-a-new-device)。

1. Defender for IoT Edge module 要求在 IoT Edge 设备上安装 [审核 framework](https://linux.die.net/man/8/auditd) 。

    - 在 IoT Edge 设备上运行以下命令，以安装框架：

    `sudo apt-get install auditd audispd-plugins`

    - 通过运行以下命令验证审核是否处于活动状态：

    `sudo systemctl status auditd`<br>
    - 预期响应为： `active (running)`

### <a name="deployment-using-azure-portal"></a>使用 Azure 门户部署

1. 在 Azure 门户中，打开 **Marketplace**。

1. 选择 " **物联网**"，然后搜索 " **IoT** " 并选择它。

   ![选择用于 IoT 的 Defender](media/howto/edge-onboarding-8.png)

1. 单击 " **创建** " 以配置部署。

1. 选择 IoT 中心的 Azure **订阅** ，并选择 **iot 中心**。<br>选择 " **部署到设备** " 以面向单个设备，或选择 " **大规模部署** " 以面向多个设备，并单击 " **创建**"。 有关大规模部署的详细信息，请参阅 [如何部署](../iot-edge/how-to-deploy-at-scale.md)。

    >[!Note]
    >如果选择了 " **大规模部署**"，请添加设备名称和详细信息，然后继续执行以下说明中的 " **添加模块** " 选项卡。

完成每个步骤，完成用于 IoT 的 IoT Edge 部署。

#### <a name="step-1-modules"></a>步骤1：模块

1. 选择 **AzureSecurityCenterforIoT** 模块。
1. 在 " **模块设置** " 选项卡上，将 " **名称** " 更改为 **azureiotsecurity**。
1. 在 " **环境变量** " 选项卡上，根据需要添加变量 (例如，调试级别) 。
1. 在 " **容器创建选项** " 选项卡上，添加以下配置：

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }
    ```

1. 在 " **模块克隆设置** " 选项卡上，添加以下配置：

   模块克隆属性：
   
   ``` json
     "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration"
   ```

   模块克隆属性内容： 

   ```json
     {

     }
   ```
    
   有关配置代理的详细信息，请参阅 [配置安全代理](./how-to-agent-configuration.md)。

1. 选择“更新”。

#### <a name="step-2-runtime-settings"></a>步骤2：运行时设置

1. 选择“运行时设置”。
1. 在 **Edge 中心** 下，将 **图像** 更改为 **mcr.microsoft.com/azureiotedge-hub:1.0.8.3**。
1. 验证 " **创建选项** " 设置为以下配置：

    ``` json
    {
       "HostConfig":{
          "PortBindings":{
             "8883/tcp":[
                {
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[
                {
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[
                {
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```

1. 选择“保存”。

1. 选择“**下一页**”。

#### <a name="step-3-specify-routes"></a>步骤3：指定路由

1. 在 " **指定路由** " 选项卡上，请确保具有 (显式或隐式) 的路由，该路由会将消息从 **azureiotsecurity** 模块转发到 **$upstream** 下面的示例。 仅当已准备好路由时，选择 " **下一步**"。

   示例路由：

    ```Default implicit route
    "route": "FROM /messages/* INTO $upstream"
    ```

    ```Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ```

1. 选择“**下一页**”。

#### <a name="step-4-review-deployment"></a>步骤4：查看部署

- 在 " **查看部署** " 选项卡上，查看部署信息，然后选择 " **创建** " 以完成部署。

## <a name="diagnostic-steps"></a>诊断步骤

如果遇到问题，则可以使用容器日志来了解 IoT Edge 安全模块设备的状态。 可以使用本部分中的命令和工具来收集信息。

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>验证是否已安装所需的容器并按预期方式工作

1. 在 IoT Edge 设备上运行以下命令：

    `sudo docker ps`

1. 验证以下容器是否正在运行：

   | 名称 | IMAGE |
   | --- | --- |
   | azureiotsecurity | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | edgeHub | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | edgeAgent | mcr.microsoft.com/azureiotedge-agent:1.0.1 |

   如果所需的容器不存在，请检查你的 IoT Edge 部署清单是否与推荐的设置一致。 有关详细信息，请参阅 [部署 IoT Edge 模块](#deployment-using-azure-portal)。

### <a name="inspect-the-module-logs-for-errors"></a>检查模块日志中是否存在错误

1. 在 IoT Edge 设备上运行以下命令：

   `sudo docker logs azureiotsecurity`

1. 对于更详细的日志，请将以下环境变量添加到 **azureiotsecurity** 模块部署： `logLevel=Debug` 。

## <a name="next-steps"></a>后续步骤

若要了解有关配置选项的详细信息，请转到模块配置的操作方法指南。
> [!div class="nextstepaction"]
> [模块配置操作方法指南](./how-to-agent-configuration.md)