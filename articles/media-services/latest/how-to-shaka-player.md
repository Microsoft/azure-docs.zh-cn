---
title: 如何将 Shaka Player 与 Azure 媒体服务一起使用
description: 本文介绍了如何将 Shaka Player 与 Azure 媒体服务一起使用
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: bd180586b863e14953160689ddcc7946c2247e15
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067837"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>如何将 Shaka Player 与 Azure 媒体服务一起使用

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概述

Shaka Player 是适用于自适应媒体的开源 JavaScript 库。 它在浏览器中播放自适应媒体格式（例如 DASH 和 HLS），而无需使用插件或 Flash。 相反，Shaka Player 使用开放 Web 标准媒体源扩展和加密媒体扩展。

建议使用 [Mux.js](https://github.com/videojs/mux.js/)，若非如此，Shaka Player 将只支持 HLS CMAF 格式，不支持 HLS TS。

可在 [Shaka Player 文档](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)中找到其官方文档。

## <a name="sample-code"></a>代码示例

本文的示例代码可在 [Azure-Samples/media-services-3rdparty-player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples) 中获取。

## <a name="implementing-the-player"></a>实现播放机

如需实现你自己的播放机实例，请按下述说明操作：

1. 创建托管播放机需用到的 `index.html` 文件。 添加以下代码行（可根据适用情况采用较新版本）：

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. 添加包含以下代码的 JavaScript 文件：

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. 将 `manifestUrl` 替换为资产流式处理定位符中的 HLS 或 DASH URL，该 URL 可在 Azure 门户的“流式处理定位符”页面中找到。
    ![流式处理定位符 URL](media/how-to-shaka-player/streaming-urls.png)

1. 运行服务器（如 `npm http-server`），这时你的播放机应该可以正常运行了……

## <a name="set-up-captions"></a>设置标题

### <a name="set-up-vod-captions"></a>设置 VOD 标题

运行以下代码行，并将 `captionUrl` 替换为 .vtt 目录（vtt 文件必须位于同一主机中，以免发生 CORS 错误），将 `lang` 替换为两个字母的语言代码，并将 `type` 替换为 `caption` 或 `subtitle`：

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>设置实时传送流标题

添加以下代码行，配置实时传送流中的启用字幕：

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>设置令牌身份验证

运行以下代码行，并将 `token` 替换为令牌字符串：

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>设置 AES-128 加密

Shaka Player 目前不支持 AES-128 加密。

你可以通过此 GitHub [问题](https://github.com/google/shaka-player/issues/850)链接跟踪该功能的状态。

## <a name="set-up-drm-protection"></a>设置 DRM 保护

Shaka Player 使用加密媒体扩展 (EME)，需要安全 URL 才能使用。 因此，在测试任何受 DRM 保护的内容时，必须使用 https。 如果网站使用 https，则清单和每个段也需要使用 https。 这是因为混合内容有此要求。

其许可证服务器 URL 的 Shaka 管理优先顺序如下：

1. 用于调试的 ClearKey config 应覆盖其他内容 （应用程序仍可以指定 ClearKey 许可证服务器。）
2. 如果存在任何由应用程序配置的服务器，则应覆盖清单中的全部内容。
3. 只有未指定其他内容时，才使用清单提供的许可证服务器。

若要为 Widevine 或 PlayReady 指定许可证服务器 URL，可使用以下代码：

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

所有 FairPlay 内容都需要设置服务器证书。 可在播放机配置中设置：

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

有关详细信息，请参阅 [Shaka Player DRM 保护文档](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [快速入门：加密内容](drm-encrypt-content-how-to.md)
