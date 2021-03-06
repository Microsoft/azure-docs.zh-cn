---
title: '管理 Microsoft Azure Map Creator (Preview) '
description: 在本文中，你将了解如何管理 Microsoft Azure Map Creator)  (预览版。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/16/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d26df4287032bc59cc58dd1d832d9d5a9c40afcd
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559213"
---
# <a name="manage-azure-maps-creator-preview"></a>管理 Azure Maps Creator (预览版)  

> [!IMPORTANT]
> Azure Maps Creator 服务目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Maps Creator 允许你创建专用室内定位数据。 使用 Azure Maps API 和室内定位模块，可开发动态交互式室内定位 Web 应用程序。 目前，Creator 只在使用 S1 定价层的美国提供。

本文将指导你完成在 Azure Maps 帐户中创建和删除 Creator 资源的步骤。

## <a name="create-creator-preview-resource"></a>创建 Creator (预览) 资源

1. 登录到 [Azure 门户](https://portal.azure.com)

2. 选择你的 Azure Maps 帐户。 如果在“最近使用的资源”下看不到 Azure Maps 帐户，请导航到“Azure 门户”菜单。 选择“所有资源”， 找到并选择你的 Azure Maps 帐户。

    ![Azure Maps 门户主页](./media/how-to-manage-creator/select-maps-account.png)

3. 在“Azure Maps 帐户”页上，导航到“Creator”下的“概述”选项。 选择 "  **创建**  "，创建 Azure Maps 创建者资源。

    ![创建 Azure Maps Creator 页](./media/how-to-manage-creator/creator-blade-settings.png)

4. 输入 Creator 资源的“名称”和“位置”。 目前，仅在美国支持 Creator。 选择“查看 + 创建”。

   ![输入 Creator 帐户信息页](./media/how-to-manage-creator/creator-creation-dialog.png)

5. 查看设置，然后选择 " **创建**"。

    ![确认 Creator 帐户设置页](./media/how-to-manage-creator/creator-create-dialog.png)

6. 部署完成后，会看到一个页面，其中包含成功或失败消息。

   ![资源部署状态页](./media/how-to-manage-creator/creator-resource-created.png)

7. 选择“转到资源”。 Creator 资源视图页将显示 Creator 资源和所选人口统计区域的状态。

    ![Creator 状态页](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >在 "创建者资源" 页上，可以通过选择 Azure Maps 帐户，导航回其所属的 Azure Maps 帐户。

## <a name="delete-creator-preview-resource"></a>删除 Creator (预览) 资源

若要删除 Creator 资源，请导航到 Azure Maps 帐户。 选择“Creator”下的“概述”。 选择“删除”按钮。

>[!WARNING]
>删除 Azure Maps 帐户的 Creator 资源后，还会删除使用 Creator 服务创建的数据集、图块集和功能状态集。

![带“删除”按钮的 Creator 页](./media/how-to-manage-creator/creator-delete.png)

选择 " **删除** " 按钮，然后键入 Creator 名称以确认删除。 删除资源后，会看到一个确认页，如下图所示：

![带删除确认的 Creator 页](./media/how-to-manage-creator/creator-confirm-delete.png)

## <a name="authentication"></a>身份验证

Creator (预览) 继承 Azure Maps 访问控制 (IAM) 设置。 必须使用身份验证和授权规则发送数据访问的所有 API 调用。

Creator 使用情况数据合并到 Azure Maps 使用情况图表和活动日志中。  有关详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

## <a name="access-to-creator-services"></a>访问 Creator 服务

创建者服务 (预览) 和使用在创建者 (中托管的数据的服务（例如，Render service) ）可在地理位置 URL 访问。 地理 URL 由创建时选择的位置确定。 例如，如果创建者是在美国地理位置创建的，则必须将对转换服务的所有调用提交给 `us.atlas.microsoft.com/conversion/convert` 。

此外，导入到 Creator 的所有数据都应上传到与 Creator 资源相同的地理位置。 例如，如果在美国预配了 Creator，则所有原始数据都应通过 `us.atlas.microsoft.com/mapData/upload` 上传。

## <a name="next-steps"></a>后续步骤

Creator 服务简介 (预览) 用于室内地图：

> [!div class="nextstepaction"]
> [数据上传](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [数据转换](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [数据集](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [图块集](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [特征状态集](creator-indoor-maps.md#feature-statesets)

了解如何使用创建者服务 (预览) 在应用程序中呈现室内地图：

> [!div class="nextstepaction"]
> [Azure Maps Creator 教程](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室内定位的动态样式](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [使用室内定位模块](how-to-use-indoor-module.md)