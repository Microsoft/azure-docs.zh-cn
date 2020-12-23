---
title: 从 Azure 媒体服务 v2 迁移到 v3
description: 本文介绍了 Azure 媒体服务 v3 中引入的更改，并说明了两个版本之间的差异。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure 媒体服务, 流, 广播, 实时, 脱机
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/01/2020
ms.author: inhenkel
ms.openlocfilehash: 14544f58bcda56a55cef33de8fe0a70d5859b589
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510941"
---
# <a name="media-services-v2-vs-v3"></a>媒体服务 v2 与 v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍了 Azure 媒体服务 v3 中引入的更改，并说明了两个版本之间的差异。

## <a name="general-changes-from-v2"></a>v2 中的常规更改

* 有关资产相关更改，请参阅下面的 " [资产特定更改](#asset-specific-changes) " 部分。
* v3 SDK 现在已与存储 SDK 分离，可让你更精细地控制所要使用的存储 SDK 版本，并避免版本控制问题。 
* 在 v3 API 中，所有编码比特率以“比特/秒”为单位。 这与 v2 Media Encoder Standard 预设不同。 例如，v2 中的比特率指定为 128 (kbps)，而在 v3 中，则指定 128000（比特/秒）。 
* v3 中不存在实体 AssetFile、AccessPolicy 和 Ingestmanifest。
* 现在，Contentkey 不再是实体，而是流式处理定位符的一个属性。
* 事件网格支持替换 NotificationEndpoint。
* 以下实体已重命名：

   * v3 JobOutput 替换 v2 任务，现已成为作业的一部分。 输入和输出现在处于作业级别。 有关详细信息，请参阅 [从本地文件创建作业输入](job-input-from-local-file-how-to.md)。 

       若要获取作业进度的历史记录，请侦听 EventGrid 事件。 有关详细信息，请参阅[处理事件网格事件](reacting-to-media-services-events.md)。
    * 流式处理定位符取代了定位符。
    * 直播活动取代了频道。<br/>直播活动计费基于实时频道计量器。 有关详细信息，请参阅[计费](live-event-states-billing.md)和[定价](https://azure.microsoft.com/pricing/details/media-services/)。
    * 实时输出取代了节目。
* 实时输出在创建时启动，在删除后停止。 v2 API 中的节目以不同的方式工作，它们必须在创建后启动。
* 若要获取有关作业的信息，需要知道创建作业时使用的转换名称。 
* 在 v2 中，XML [输入](../previous/media-services-input-metadata-schema.md)和[输出](../previous/media-services-output-metadata-schema.md)元数据文件将作为编码作业的结果生成。 在 v3 中，元数据格式已从 XML 更改为 JSON。 
* 在媒体服务 v2 中，可以指定初始化向量 (IV)。 在媒体服务 v3 中，无法指定 FairPlay IV。 尽管这不会影响使用媒体服务进行打包和许可证传递的客户，但在使用第三方 DRM 系统提供 FairPlay 许可证（混合模式）时可能会遇到问题。 在这种情况下，请务必知道，FairPlay IV 派生自 cbcs 密钥 ID，可以使用以下公式检索：

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    替换为

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    有关详细信息，请参阅[在混合模式下适用于直播和 VOD 操作的媒体服务 v3 的 Azure Functions C# 代码](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)。
 
> [!NOTE]
> 查看适用于[媒体服务 v3 资源](media-services-apis-overview.md#naming-conventions)的命名约定。 还要查看[命名 Blob](assets-concept.md#naming)。

## <a name="feature-gaps-with-respect-to-v2-apis"></a>与 v2 API 之间的功能差距

与 v2 API 相比，v3 API 存在以下功能差距。 我们正在弥补这些差距。

* [Premium Encoder](../previous/media-services-encode-asset.md) 和旧版[媒体分析处理器](../previous/legacy-components.md)（Azure 媒体服务索引器 2 预览版、Face Redactor 等）不可通过 v3 访问。<br/>想要从媒体索引器 1 或 2 预览版迁移的客户可以立即使用 v3 API 中的 AudioAnalyzer 预设。  此新预设包含的功能比旧版媒体索引器 1 或 2 更多。 
* [v2 API 中的许多 Media Encoder Standard 高级功能](../previous/media-services-advanced-encoding-with-mes.md)目前在 v3 中不可用，例如：
  
    * 资产拼接
    * 叠加
    * 裁剪
    * 在输入不包含音频时插入静音曲目
    * 在输入不包含视频时插入视频轨道
* 包含转码的直播活动目前不支持静态图像插入中间流，以及通过 API 调用执行的广告标记插入。 
* 有关使用 .NETCore SDK 上的 V2 REST API 的最佳做法和模式，请参阅 `https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git`。

## <a name="asset-specific-changes"></a>特定于资产的更改

* 对于通过 v3 创建的资产，媒体服务仅支持 [Azure 存储服务器端存储加密](../../storage/common/storage-service-encryption.md)。
    * 对于通过 v2 API 创建的，并采用媒体服务提供的[存储加密](../previous/media-services-rest-storage-encryption.md) (AES 256) 的资产，可以使用 v3 API。
    * 无法使用 v3 API 创建采用旧版 AES 256 [存储加密](../previous/media-services-rest-storage-encryption.md)的新资产。
* v3 中[资产](assets-concept.md)的属性与 v2 不同，请参阅[属性如何映射](#map-v3-asset-properties-to-v2)。
* v3 中不存在 IAsset.ParentAssets 属性。

### <a name="map-v3-asset-properties-to-v2"></a>将 v3 资产属性映射到 v2

下表显示了 v3 中[资产](/rest/api/media/assets/createorupdate#asset)的属性如何映射到 v2 中资产的属性。

|v3 属性|v2 属性|
|---|---|
|`id` -（唯一）完整的 Azure 资源管理器路径，请参见[资产](/rest/api/media/assets/createorupdate)中的示例||
|`name` -（唯一）请参阅[命名约定](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` -（唯一）值以 `nb:cid:UUID:` 前缀开头。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options`（创建选项）|
|`type`||

### <a name="storage-side-encryption"></a>存储端加密

若要保护静态资产，应通过存储端加密对资产进行加密。 下表显示了存储端加密在媒体服务中的工作方式：

|加密选项|说明|媒体服务 v2|媒体服务 v3|
|---|---|---|---|
|媒体服务存储加密|AES-256 加密，媒体服务管理的密钥。|支持<sup>(1)</sup>|不支持<sup>(2)</sup>|
|[静态数据的存储服务加密](../../storage/common/storage-service-encryption.md)|由 Azure 存储提供的服务器端加密，由 Azure 或客户托管的密钥。|支持|支持|
|[存储客户端加密](../../storage/common/storage-client-side-encryption.md)|由 Azure 存储提供的客户端加密，由 Key Vault 中的客户托管密钥。|不支持|不支持|

<sup>1</sup> 虽然媒体服务确实支持处理明文形式（未经过任何形式的加密）的内容，但不建议这样做。

<sup>2</sup> 在媒体服务 v3 中，仅当资产是使用媒体服务 v2 创建的时才支持存储加密（AES-256 加密）以实现向后兼容性。 这意味着 v3 会处理现有的存储加密资产，但不会允许创建新资产。

## <a name="code-differences"></a>代码差异

下表显示了常见方案中 v2 和 v3 的代码差异。

|方案|v2 API|v3 API|
|---|---|---|
|创建资产并上传文件 |[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|提交作业|[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>演示如何先创建转换，再提交作业。|
|发布使用 AES 加密的资产 |1.创建 ContentKeyAuthorizationPolicyOption<br/>2.创建 ContentKeyAuthorizationPolicy<br/>3.创建 AssetDeliveryPolicy<br/>4.创建资产并上传内容或提交作业并使用输出资产<br/>5.将 AssetDeliveryPolicy 与 Asset 关联<br/>6.创建 ContentKey<br/>7.将 ContentKey 附加到 Asset<br/>8.创建 AccessPolicy<br/>9.创建 Locator<br/><br/>[v2 .NET 示例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1.创建内容密钥策略<br/>2.创建 Asset<br/>3.上传内容或将 Asset 用作 JobOutput<br/>4.创建流式处理定位符<br/><br/>[v3 .NET 示例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|获取作业详细信息和管理作业 |[使用 v2 管理作业](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[使用 v3 管理作业](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> 请将本文加入书签，并不时地查看最新信息。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[有关从媒体服务 v2 迁移到 v3 的指导](migrate-from-v2-to-v3.md)