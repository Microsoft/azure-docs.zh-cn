---
title: 部署 Azure IoT Edge 工作负载
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 部署 Azure IoT Edge 工作负载
keywords: Kubernetes, Arc, Azure, K8s, 容器
ms.openlocfilehash: e77446170e5a6adac995394d66640fd183f453b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121722"
---
# <a name="deploy-azure-iot-edge-workloads"></a>部署 Azure IoT Edge 工作负载

## <a name="overview"></a>概述

Azure Arc 和 Azure IoT Edge 可以轻松互补彼此的功能。 

Azure Arc 为群集操作员提供配置群集的基础组件以及应用和强制实施群集策略的机制。 

Azure IoT Edge 允许应用程序运营商通过方便的云引入和双向通信基元大规模地远程部署和管理工作负载。 

下图对 Azure Arc 和 Azure IoT Edge 的关系进行了说明：

![IoT Arc 配置](./media/edge-arc.png)

## <a name="pre-requisites"></a>先决条件

* [注册 IoT Edge 设备](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device)并[部署模拟温度传感器模块](../../iot-edge/quickstart-linux.md#deploy-a-module)。 记下设备的连接字符串，该连接字符串将用于下文中提到的 values.yaml。

* 使用 [Kubernetes 的 IoT Edge 支持 ](https://aka.ms/edgek8sdoc)通过 Azure Arc 的 Flux 运算符来部署它。

* 下载 IoT Edge Helm 图表的 [values.yaml](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) 文件，并将该文件末尾的 `deviceConnectionString` 占位符替换为在之前记下的连接字符串。 可以根据需要设置任何其他受支持的图表安装选项。 为 IoT Edge 工作负载创建一个命名空间，并在其中创建一个机密：

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  还可以使用[群集配置示例](./tutorial-use-gitops-connected-cluster.md)进行远程设置。

## <a name="connect-a-cluster"></a>连接群集

使用 `az` Azure CLI `connectedk8s` 扩展将 Kubernetes 群集连接到 Azure Arc：

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>创建 IoT Edge 的配置

[示例 Git 存储库](https://github.com/veyalla/edgearc)指向 IoT Edge Helm 图表，并引用“先决条件”部分中创建的机密。

使用 `az` Azure CLI `k8s-configuration` 扩展创建配置，将连接的群集链接到 Git 存储库：

  ```
  az k8s-configuration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

在几分钟内，应该会看到 IoT Edge 工作负载模块部署到群集中的 `iotedge` 命名空间。 

在该命名空间中查看 `SimulatedTemperatureSensor` Pod 日志，以了解所生成的示例值。 也可使用 [Visual Studio Code 的 Azure IoT Toolkit 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看到达 IoT 中心的消息。

## <a name="cleanup"></a>清理

使用以下命令删除配置：

```
az k8s-configuration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure Policy 来控制群集配置](./use-azure-policy.md)。
