---
title: Azure Stack Edge Pro GPU 计算管理 |Microsoft Docs
description: 介绍如何通过 Azure Stack Edge Pro GPU 上的 Azure 门户管理边缘计算设置，如触发器、模块、视图计算配置、删除配置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: 4c4fbef807d31e03a79f80db7fd29580074fb8bd
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955418"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>管理 Azure Stack Edge Pro GPU 上的计算

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文介绍如何通过 Azure Stack Edge Pro GPU 设备上的 IoT Edge 服务管理计算。 可以通过 Azure 门户或本地 Web UI 管理计算。 使用 Azure 门户管理模块、触发器和 IoT Edge 配置，并使用本地 web UI 管理计算网络设置。

在本文中，学习如何：

> [!div class="checklist"]
> * 管理触发器
> * 管理 IoT Edge 配置


## <a name="manage-triggers"></a>管理触发器

事件是在云环境中或者在设备上发生的、可能需要采取措施的情况。 例如，在共享中创建文件就是一个事件。 触发器引发这些事件。 对于 Azure Stack Edge Pro，触发器可以响应文件事件或计划。

- **文件**：这些触发器用于响应文件事件，如创建文件、修改文件。
- **计划**：这些触发器是对你可以用开始日期、开始时间和重复间隔定义的计划的响应。


### <a name="add-a-trigger"></a>添加触发器

在 Azure 门户中执行以下步骤可以创建触发器。

1. 在 Azure 门户中，请切换到 "Azure Stack" 边缘资源，然后 " **IoT Edge**"。 中转到 **触发器** ，然后在命令栏上选择 " **+ 添加触发器** "。

    ![选择“添加触发器”](media/azure-stack-edge-j-series-manage-compute/add-trigger-1m.png)

2. 在“添加触发器”边栏选项卡中，提供触发器的唯一名称。
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. 选择触发器的 **类型**。 如果该触发器用于响应文件事件，请选择“文件”。 如果希望该触发器在定义的时间启动并按指定的重复间隔运行，请选择“计划”。 根据所做的选择，屏幕上会显示一组不同的选项。

    - **文件触发器** - 从下拉列表中选择一个装载的共享。 当此共享中激发文件事件时，该触发器会调用某个 Azure 函数。

        ![添加 SMB 共享](media/azure-stack-edge-j-series-manage-compute/add-file-trigger.png)

    - **计划的触发器** - 指定开始日期/时间，并以小时、分钟或秒为单位指定重复间隔。 此外，请输入主题的名称。 使用主题可以灵活地将触发器路由到设备上部署的模块。

        示例路由字符串：`"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`。

        ![添加 NFS 共享](media/azure-stack-edge-j-series-manage-compute/add-scheduled-trigger.png)

4. 选择“添加”以创建该触发器。 此时会显示一条通知，指出正在创建触发器。 创建触发器后，边栏选项卡会更新以反映新的触发器。
 
    ![更新的触发器列表](media/azure-stack-edge-j-series-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>删除触发器

在 Azure 门户中执行以下步骤可以删除触发器。

1. 在触发器列表中，选择要删除的触发器。

    ![选择触发器](media/azure-stack-edge-j-series-manage-compute/delete-trigger-1.png)

2. 右键单击，然后选择 " **删除**"。

    ![选择“删除”](media/azure-stack-edge-j-series-manage-compute/delete-trigger-2.png)

3. 当系统提示你进行确认时，单击 **“是”**。

    ![确认删除](media/azure-stack-edge-j-series-manage-compute/delete-trigger-3.png)

触发器列表将会更新以反映删除后的结果。

## <a name="manage-iot-edge-configuration"></a>管理 IoT Edge 配置

使用 Azure 门户查看计算配置、删除现有的计算配置，或刷新计算配置以同步 IoT 设备的访问密钥，并为 Azure Stack Edge Pro IoT Edge 设备。

### <a name="view-iot-edge-configuration"></a>查看 IoT Edge 配置

在 Azure 门户中执行以下步骤，查看设备的 IoT Edge 配置。

1. 在 Azure 门户中，请切换到 "Azure Stack" 边缘资源，然后 " **IoT Edge**"。 在设备上启用 IoT Edge 服务后，"概述" 页将指示 IoT Edge 服务运行正常。

    ![选择“查看计算”](media/azure-stack-edge-j-series-manage-compute/view-compute-1.png)

2. 请参阅 " **属性** " 以查看设备上的 IoT Edge 配置。 配置计算时，已创建一个 IoT 中心资源。 在该 IoT 中心资源下，已配置 IoT 设备和 IoT Edge 设备。 仅支持在 IoT Edge 设备上运行 Linux 模块。

    ![查看配置](media/azure-stack-edge-j-series-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>删除 IoT Edge 服务

在 Azure 门户中执行以下步骤以删除设备的现有 IoT Edge 配置。

1. 在 Azure 门户中，请切换到 "Azure Stack" 边缘资源，然后 " **IoT Edge**"。 请参阅 " **概述** "，并在命令栏上选择 " **删除** "。

    ![选择“删除计算”](media/azure-stack-edge-j-series-manage-compute/remove-compute-1.png)

2. 如果删除 IoT Edge 服务，则操作是不可逆的，无法撤消。 你创建的模块和触发器也会被删除。 需要重新配置设备，以防需要再次使用 IoT Edge。 出现确认提示时，选择 **"确定"**。

    ![选择删除计算2](media/azure-stack-edge-j-series-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>同步 IoT 设备和 IoT Edge 设备的访问密钥

在 Azure Stack Edge Pro 上配置计算时，会创建 IoT 设备和 IoT Edge 设备。 系统会自动为这些设备分配对称访问密钥。 最佳安全做法是通过 IoT 中心服务定期轮换这些密钥。

若要轮换这些密钥，可以转到创建的 IoT 中心服务，并选择该 IoT 设备或 IoT Edge 设备。 每个设备都有一个主要访问密钥和辅助访问密钥。 将主要访问密钥分配到辅助访问密钥，然后重新生成主要访问密钥。

如果 IoT 设备和 IoT Edge 设备密钥已经旋转，则需要刷新 Azure Stack Edge Pro 上的配置以获取最新的访问密钥。 同步可帮助设备获取 IoT 设备和 IoT Edge 设备的最新密钥。 Azure Stack Edge Pro 只使用主访问密钥。

在 Azure 门户中执行以下步骤可以同步设备的访问密钥。

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后前往 **IoT Edge 计算**。 请参阅 **概述** ，并在命令栏上选择 " **刷新配置** "。

    ![选择“刷新配置”](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-1.png)

2. 出现确认提示时，选择“是”。

    ![出现提示时选择“是”](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-2.png)

3. 同步完成后，请退出对话框。

## <a name="next-steps"></a>后续步骤

- 了解如何对 [Azure Stack Edge Pro 进行故障排除](azure-stack-edge-gpu-troubleshoot.md)。
