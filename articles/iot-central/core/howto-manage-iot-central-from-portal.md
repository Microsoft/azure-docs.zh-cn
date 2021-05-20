---
title: 通过 Azure 门户管理 IoT Central | Microsoft Docs
description: 本文介绍如何通过 Azure 门户创建和管理 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 2af97206db00d683ab409710bc71a3b5048bf6ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658459"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

如果不在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序，可以使用 [Azure 门户](https://portal.azure.com)来管理应用程序。

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]


若要创建应用程序，请导航到 [Azure 门户](https://ms.portal.azure.com)并选择“创建资源”。

在“搜索市场”栏中，键入“IoT Central”：

![管理门户：搜索](media/howto-manage-iot-central-from-portal/image0a1.png)

选择搜索结果中的“IoT Central 应用程序”磁贴：

![管理门户：搜索结果](media/howto-manage-iot-central-from-portal/image0b1.png)

现在选择“创建”：

![管理门户：IoT Central 资源](media/howto-manage-iot-central-from-portal/image0c1.png)

填写窗体中的所有字段。 此窗体与在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站创建应用程序时需要填写的窗体类似。 有关详细信息，请参阅[创建 IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。

![创建 IoT Central 窗体](media/howto-manage-iot-central-from-portal/image6a.png)

“位置”是你想要创建应用程序的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)  。 通常，应选择物理上离设备最近的位置以获得最佳性能。 Azure IoT Central 目前可在澳大利亚、亚太、欧洲、美国、英国和日本地理区域使用     。 选择一个位置后，之后便不能将应用程序移到其他位置。

填写所有字段后，选择“创建”。

## <a name="manage-existing-iot-central-applications"></a>管理现有 IoT Central 应用程序

如果已有 Azure IoT Central 应用程序，可将其删除或移动到 Azure 门户中的其他订阅或资源组。

> [!NOTE]
> 使用免费计划创建的应用程序不要具备 Azure 订阅，因此你不会在 Azure 门户上你的 Azure 订阅中找到它们。 你只能通过 IoT Central 门户查看和管理免费应用。

在门户中选择“所有资源”以开始。 选择“显示隐藏的类型”，然后开始在“按名称筛选”中键入应用程序的名称以进行查找。 然后选择想要管理的 IoT Central 应用程序。

要想导航到应用程序，请选择“IoT Central 应用程序 URL”：

![屏幕截图显示了“概述”页，其中突出显示了“IoT Central 应用程序 URL”。](media/howto-manage-iot-central-from-portal/image3.png)

若要将应用程序移动到其他资源组，请选择资源组旁边的“更改”。 在“移动资源”页上，选择要将此应用程序移动到其中的资源组：

![屏幕截图显示了“概述”页，其中突出显示了“资源组（更改）”。](media/howto-manage-iot-central-from-portal/image4a.png)

若要将应用程序移动到不同的订阅，请选择订阅旁的“更改”。 在“移动资源”页上，选择要将此应用程序移动到其中的订阅：

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)