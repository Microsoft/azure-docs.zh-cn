---
title: 检测图像中的人脸
titleSuffix: Azure Cognitive Services
description: 本指南演示如何使用人脸检测功能从给定的图像中提取性别、年龄或姿势等特性。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: f7a740b1015bda80000f65180eda2c5e618670da
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911233"
---
# <a name="get-face-detection-data"></a>获取人脸检测数据

本指南演示如何使用人脸检测功能从给定的图像中提取性别、年龄或姿势等特性。 本指南中的代码片段使用 Azure 认知服务面部客户端库以 c # 编写。 同样的功能通过 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 提供。

本指南介绍以下操作：

- 获取图像中人脸的位置和维度。
- 获取图像中各个人脸特征点（例如瞳孔、鼻子、嘴巴）的位置。
- 猜测性别、年龄、情绪，以及检测到的人脸的其他特性。

## <a name="setup"></a>设置

本指南假设你已使用人脸订阅密钥和终结点 URL 构造了名为 `faceClient` 的 [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) 对象。 在此处，可以通过调用 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)（本指南中使用）或 [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) 来使用人脸检测功能。 有关如何设置此功能的说明，请单击其中一个快速入门。

本指南重点介绍有关检测调用的具体信息，例如，可以传递哪些参数，以及可对返回的数据执行哪些操作。 建议仅查询所需功能。 每项操作都需要额外的时间来完成。

## <a name="get-basic-face-data"></a>获取基本人脸数据

若要查找人脸并获取图像中的位置，请调用 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) 或 [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) 方法，并将 _returnFaceId_ 参数设置为 **true** 。 此设置为默认设置。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

可在返回的 [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) 对象中查询其唯一 ID，矩形提供了人脸的像素坐标。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

有关如何分析人脸位置和维度的信息，请参阅 [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)。 通常情况下，此矩形包含眼睛、眉毛、鼻子和嘴巴， 不一定包括头顶、耳朵和下巴。 若要使用人脸矩形来裁剪整个头部或获取中景肖像（也许是身份证类型的图像），可朝每个方向拉伸矩形。

## <a name="get-face-landmarks"></a>获取人脸特征点

[人脸特征点](../concepts/face-detection.md#face-landmarks)是人脸上的一组易于查找的点，例如瞳孔或鼻尖。 若要获取面部路标数据，请将 _detectionModel_ 参数设置为 **detectionModel. Detection01** ，并将 _returnFaceLandmarks_ 参数设置为 **true** 。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

以下代码演示如何检索鼻子和瞳孔的位置：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

人脸特征点数据还可用于准确计算人脸的方向。 例如，可以将人脸的旋转角定义为从嘴巴中心到眼睛中心的矢量。 以下代码计算此矢量：

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

知道人脸的方向后，可以旋转矩形人脸框，使人脸更适当地对齐。 若要裁剪图像中的人脸，可以编程方式旋转图像，使人脸始终朝上。

## <a name="get-face-attributes"></a>获取人脸特性

除了人脸矩形和特征点以外，人脸检测 API 还可以分析人脸的几个概念特性。 如需完整列表，请参阅[人脸属性](../concepts/face-detection.md#attributes)概念部分。

若要分析人脸属性，请将 _detectionModel_ 参数设置为 **detectionModel** ，并将 _ReturnFaceAttributes_ 参数设置为 [FaceAttributeType 枚举](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) 值的列表。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

然后，获取对返回的数据的引用，并根据需要执行进一步的操作。

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

若要详细了解每个属性，请参阅[人脸检测和属性](../concepts/face-detection.md)概念指南。

## <a name="next-steps"></a>后续步骤

本指南介绍了如何使用人脸检测的各项功能。 接下来，请按深度教程的说明操作，将这些功能集成到应用中。

- [教程：创建一个用于显示图像中人脸数据的 WPF 应用](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>相关主题

- [参考文档 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [.NET SDK ( 参考文档) ](/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)