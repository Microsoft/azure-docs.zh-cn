---
title: 在栅格地图上呈现自定义数据 | Microsoft Azure Maps
description: 了解如何将图钉、标签和几何形状添加到栅格地图。 为此，请参阅“如何在 Azure Maps 中使用静态图像服务”。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 80919473d5d3f4b34ce8d621d82bf4bc458b8b58
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108326842"
---
# <a name="render-custom-data-on-a-raster-map"></a>在栅格地图上呈现自定义数据

本文介绍了如何使用具有图像合成功能的[静态图像服务](/rest/api/maps/render/getmapimage)在栅格地图上实现覆盖。 图像合成包括使用自定义图钉、标签和几何覆盖图层等附加数据来恢复栅格图块的功能。

若要呈现自定义图钉、标签和几何覆盖图层，你可以使用 Postman 应用程序。 你可以使用 Azure Maps [数据服务 API](/rest/api/maps/data) 来存储和呈现覆盖图层。

> [!Tip]
> 使用 Azure Maps Web SDK 在网页上显示简单的图像通常比使用静态图像服务经济高效得多。 Web SDK 使用地图图块。除非用户平移和缩放地图，否则每次加载地图时，它们通常只生成事务的一小部分。 请注意，Azure Maps Web SDK 提供禁用平移和缩放的选项。 此外，Azure Maps Web SDK 还提供一组数据可视化选项，比静态地图 Web 服务提供的更丰富。  

## <a name="prerequisites"></a>先决条件

### <a name="create-an-azure-maps-account"></a>创建 Azure Maps 帐户

若要完成本文中的过程，你首先需要创建一个 Azure Maps 帐户并获取你的地图帐户密钥。 按照[创建帐户](quick-demo-map-app.md#create-an-azure-maps-account)中的说明创建 Azure Maps 帐户订阅，并按照[获取主密钥](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步骤获取帐户的主密钥。 有关 Azure Maps 中身份验证的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>呈现具有标签和自定义图像的图钉

> [!Note]
> 本部分的过程需要 Gen 1 或 Gen 2 定价层中的 Azure Maps 帐户。

Azure Maps 帐户 S0 层仅支持 `pins` 参数的单个实例。 它允许你呈现在 URL 请求中指定的具有自定义图像的图钉（最多五个）。

若要呈现具有标签和自定义图像的图钉，请完成以下步骤：

1. 创建用于存储请求的集合。 在 Postman 应用中，选择“新建”。 在“新建”窗口中，选择“集合”。 命名集合，然后选择“创建”按钮。 

2. 若要创建请求，请再次选择“新建”。 在“新建”窗口中，选择“请求”。 为图钉输入一个“请求名称”。 选择你在上一步创建的集合作为保存请求的位置。 然后选择“保存”。
    
    ![在 Postman 中创建请求](./media/how-to-render-custom-data/postman-new.png)

3. 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 来创建 GET 请求。

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    生成的图像如下所示：

    ![带标签的自定义图钉](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>从 Azure Maps 数据存储获取数据

> [!Note]
> 本部分的过程需要 Gen 1 (S1) 或 Gen 2 定价层中的 Azure Maps 帐户。

你还可以使用[数据上传 API](/rest/api/maps/data/uploadpreview) 获取路径和图钉位置信息。 按照以下步骤上传路径和图钉数据。

1. 在 Postman 应用中打开一个新的选项卡，该选项卡位于你在上一部分创建的集合中。 在生成器选项卡上选择 POST HTTP 方法，并输入以下 URL 来发出 POST 请求：

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 在“参数”选项卡上，输入用于 POST 请求 URL 的以下键/值对。 将 `subscription-key` 值替换为你的 Azure Maps 订阅密钥。
    
    ![Postman 中的键/值参数](./media/how-to-render-custom-data/postman-key-vals.png)

3. 在“正文”选项卡上，选择原始输入格式，然后从下拉列表中选择“JSON”作为输入格式。 提供以下 JSON 作为要上传的数据：
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. 单击“发送”并查看响应头。 成功请求后，Location 标头将包含状态 URI，可在其中检查上传请求的当前状态。 状态 URI 将采用以下格式。  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 复制你的状态 URI 并在其后追加 subscription-key 参数，该参数的值为你的 Azure Maps 帐户订阅密钥。 使用用于上传数据的帐户订阅密钥。 状态 URI 格式应如下所示：

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. 若要获取 udId，请在 Postman 应用中打开一个新选项卡。 在生成器选项卡上选择 GET HTTP 方法。对状态 URI 发出 GET 请求。 如果数据上传成功，将在响应正文中收到一个 udId。 复制 udId。

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. 使用从数据上传 API 收到的 `udId` 值来呈现地图上的功能。 为此，请打开一个新选项卡，该选项卡位于你在上一部分创建的集合中。 在生成器选项卡上选择 GET HTTP 方法，将 {subscription-key} 和 {udId} 替换为你的值，然后输入以下 URL 来发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    下面是响应图像：

    ![从 Azure Maps 数据存储获取数据](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>使用颜色和不透明度呈现多边形

> [!Note]
> 本部分的过程需要 Gen 1 (S1) 或 Gen 2 定价层中的 Azure Maps 帐户。


可以使用带有[路径参数](/rest/api/maps/render/getmapimage#uri-parameters)的样式修改器来修改多边形的外观。

1. 在 Postman 应用中打开一个新选项卡，该选项卡位于你之前创建的集合中。 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 来配置 GET 请求，以使用颜色和不透明度呈现多边形：
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    下面是响应图像：

    ![呈现不透明多边形](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>呈现具有自定义标签的圆和图钉

> [!Note]
> 本部分的过程需要 Gen 1 (S1) 或 Gen 2 定价层中的 Azure Maps 帐户。


可以通过添加样式修饰符来修改图钉的外观。 例如，若要使图钉及其标签变得更大或更小，请使用 `sc`“比例样式”修饰符。 此修饰符采用大于零的值。 值为 1 即为标准比例。 大于 1 的值将使图钉变大，而小于 1 的值将使图钉变小。 有关样式修饰符的详细信息，请参阅[静态图像服务路径参数](/rest/api/maps/render/getmapimage#uri-parameters)。


按照以下步骤呈现具有自定义标签的圆和图钉：

1. 在 Postman 应用中打开一个新选项卡，该选项卡位于你之前创建的集合中。 在生成器选项卡上选择 GET HTTP 方法，并输入以下 URL 来发出 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    下面是响应图像：

    ![呈现具有自定义图钉的圆](./media/how-to-render-custom-data/circle-custom-pins.png)

2. 若要更改上一步骤中图钉的颜色，请更改“co”样式修饰符。 查看 `pins=default|la15+50|al0.66|lc003C62|co002D62|`，当前颜色将在 CSS 中指定为 #002D62。 假设你想要将其更改为 #41d42a。 在“co”说明符后编写新的颜色值，如下所示：`pins=default|la15+50|al0.66|lc003C62|co41D42A|`。 发出新的 GET 请求：

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    下面是更改图钉颜色后的响应图像：

    ![呈现具有更新的图钉的圆](./media/how-to-render-custom-data/circle-updated-pins.png)

同样，你可以更改、添加和删除其他样式修饰符。

## <a name="next-steps"></a>后续步骤


* 浏览 [Azure Maps 获取地图图像 API](/rest/api/maps/render/getmapimage) 文档。
* 若要详细了解 Azure Maps 数据服务（预览版），请参阅[服务文档](/rest/api/maps/data)。