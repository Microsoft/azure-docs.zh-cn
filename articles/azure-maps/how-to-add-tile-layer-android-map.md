---
title: 使用 Azure Maps 向地图添加图块层 Android SDK
description: 了解如何向地图添加图块层。 请参阅使用 Microsoft Azure Map Android SDK 将天气雷达图添加到地图的示例。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 22618a28f1a87e68c19467aedf639e96ec2fb91e
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532670"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>使用 Azure Maps 向地图添加图块层 Android SDK

本文介绍如何使用 Azure Maps Android SDK 在地图上呈现图块层。 通过图块层可以在 Azure Maps 基本地图图块顶部附加图像。 可在[缩放级别和图块网格](zoom-levels-and-tile-grid.md)文档中找到有关 Azure Maps 图块系统的详细信息。

图块层从服务器的磁贴中加载。 可以使用图块层所理解的命名约定，预先呈现这些图像并将其存储为服务器上的任何其他图像。 或者，可以使用动态服务呈现这些图像，这种动态服务会近乎实时生成图像。 Azure Maps TileLayer 类支持三个不同的平铺服务命名约定：

* X、Y、缩放表示法 - 基于缩放级别，x 是列，y 是图块网格中图块的行位置。
* Quadkey 表示法 - 将 x、y、缩放信息合并到单个字符串值（即图块的唯一标识符）中。
* 边界框 - 边界框坐标可用于指定格式为 `{west},{south},{east},{north}` 的图像，这通常由 [Web 地图定位服务 (WMS)](https://www.opengeospatial.org/standards/wms) 使用。

> [!TIP]
> TileLayer 是直观显示地图上的大型数据集的好办法。 不仅可以从图像中生成图块层，而且还可以将矢量数据呈现为图块层。 通过将矢量数据呈现为图块层，地图控件只需加载文件大小远小于它们所代表的矢量数据的图块。 此方法可供需要呈现地图上的数百万行数据的用户使用。

传递到图块层中的图块 URL 必须是 TileJSON 资源的 http/https URL 或使用以下参数的图块 URL 模板： 

* `{x}` - 图块的 X 位置。 还需要 `{y}` 和 `{z}`。
* `{y}` - 图块的 Y 位置。 还需要 `{x}` 和 `{z}`。
* `{z}` - 图块的缩放级别。 还需要 `{x}` 和 `{y}`。
* `{quadkey}` - 基于必应地图图块系统命名约定的图块 quadkey 标识符。
* `{bbox-epsg-3857}` - EPSG 3857 空间引用系统中格式为 `{west},{south},{east},{north}` 的边界框字符串。
* `{subdomain}` -子域值的占位符（如果指定了子域值）。

## <a name="prerequisites"></a>先决条件

若要完成本文中的过程，需要安装 [Azure Maps Android SDK](./how-to-use-android-map-control-library.md) 来加载地图。


## <a name="add-a-tile-layer-to-the-map"></a>向地图添加图块层

 此示例演示如何创建指向一组图块的图块层。 这些磁贴使用 "x，y，zoom" 平铺系统。 此图块层源自[爱荷华州立大学的 Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/) 的气象雷达图覆盖。 

可以按照以下步骤向地图中添加图块层。

1. 编辑 **res > 布局 > activity_main.xml** 如下所示：

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >
    
        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. 将以下代码片段复制到类的 **onCreate ( # B1** 方法 `MainActivity.java` 。

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    上面的代码段首先使用 **onReady ( # B1** 回调方法获取 Azure Maps 映射控件实例。 然后，它创建一个 `TileLayer` 对象，并将带格式的 **xyz** 磁贴 URL 传递到 `tileUrl` 选项中。 层的不透明度设置为 `0.8` ，因为正在使用的磁贴服务中的磁贴为256像素磁贴，此信息将传递到 `tileSize` 选项中。 然后，将图块层传递到地图层管理器中。

    添加上述代码片段后，应如下 `MainActivity.java` 所示：
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }    
    }
    ```

如果现在运行应用程序，应在图上看到一条线，如下所示：

<center>

![Android 地图线条](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解有关如何设置地图样式的详细信息

> [!div class="nextstepaction"]
> [更改 Android 地图中的地图样式](./set-android-map-styles.md)