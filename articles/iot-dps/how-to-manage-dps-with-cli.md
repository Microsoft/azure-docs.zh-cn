---
title: 使用 Azure CLI 和 IoT 扩展管理 IoT 中心设备预配服务
description: 了解如何使用 Azure CLI 和 IoT 扩展来管理 IoT 中心设备预配服务 (DPS)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: devx-track-azurecli
services: iot-dps
ms.openlocfilehash: 020b0dbc937885923b26c115f91147437b7a0f9b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964722"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>如何使用 Azure CLI 和 IoT 扩展来管理 IoT 中心设备预配服务

[Azure CLI](/cli/azure?view=azure-cli-latest) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 Azure CLI 适用于 Windows、Linux 和 macOS。 使用 Azure CLI 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。

IoT 扩展丰富了 Azure CLI 的功能，例如设备管理和完整的 IoT Edge 功能。

在本教程中，我们先完成设置 Azure CLI 和 IoT 扩展的步骤。 然后了解如何运行 CLI 命令来执行基本的设备预配服务操作。 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>安装 

### <a name="install-python"></a>安装 Python

需要 [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)。

### <a name="install-the-azure-cli"></a>安装 Azure CLI

按照[安装说明](/cli/azure/install-azure-cli?view=azure-cli-latest)在环境中安装 Azure CLI。 Azure CLI 版本必须至少是 2.0.70 或更高版本。 请使用 `az –version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 在 Windows 上，一种简单的安装方法是下载并安装 [MSI](https://aka.ms/InstallAzureCliWindows)。

### <a name="install-iot-extension"></a>安装 IoT 扩展

[IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension)介绍了该扩展的多种安装方法。 最简单的方法是运行 `az extension add --name azure-iot`。 安装完成后，可以使用 `az extension list` 验证当前安装的扩展，或使用 `az extension show --name azure-iot` 查看有关 IoT 扩展的详细信息。 若要删除该扩展，可以使用 `az extension remove --name azure-iot`。


## <a name="basic-device-provisioning-service-operations"></a>基本的设备预配服务操作

该示例演示如何使用 CLI 命令登录到 Azure 帐户、创建 Azure 资源组（保存 Azure 解决方案相关资源的容器）、创建 IoT 中心、创建设备预配服务、列出现有的设备预配服务，以及如何创建链接的 IoT 中心。 

在开始之前，请完成前面所述的安装步骤。 如果没有 Azure 帐户，可以立即[创建一个免费帐户](https://azure.microsoft.com/free/?v=17.39a)。 


### <a name="1-log-in-to-the-azure-account"></a>1.登录到 Azure 帐户
  
```azurecli
az login
```

![屏幕截图显示了一个运行 az login 命令的命令提示符窗口。](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. 在 eastus 中创建资源组 IoTHubBlogDemo

```azurecli
az group create -l eastus -n IoTHubBlogDemo
```

![创建资源组](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3.创建两个设备预配服务

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps
```

![创建设备预配服务](./media/how-to-manage-dps-with-cli/create-dps.jpg)

```azurecli
az iot dps create --resource-group IoTHubBlogDemo --name demodps2
```

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4.列出此资源组下的所有现有设备预配服务

```azurecli
az iot dps list --resource-group IoTHubBlogDemo
```

![列出设备预配服务](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5.在新建的资源组下创建 IoT 中心 blogDemoHub

```azurecli
az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo
```

![创建 IoT 中心](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6.将一个现有的 IoT 中心链接到设备预配服务

```azurecli
az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus
```

![链接中心](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>后续步骤
在本教程中，你已学习了如何执行以下操作：

> [!div class="checklist"]
> * 注册设备
> * 启动设备
> * 验证设备已注册

前往下一教程，了解如何跨负载均衡的中心预配多台设备。 

> [!div class="nextstepaction"]
> [跨负载均衡的 IoT 中心预配设备](./tutorial-provision-multiple-hubs.md)