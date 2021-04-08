---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "74806570"
---
## <a name="install-client-library-packages"></a>安装客户端库包

> [!NOTE]
> 本文中的示例使用 Azure 存储客户端库版本 12。 版本 12 的客户端库是 Azure SDK 的一部分。 有关 Azure SDK 的更多信息，请参阅 [GitHub](https://github.com/Azure/azure-sdk) 上的 Azure SDK 存储库。

若要安装 Blob 存储包，请在 NuGet 包管理器控制台中运行以下命令：

```powershell
Install-Package Azure.Storage.Blobs
```

本文中的示例还使用[用于 .NET 的 Azure 标识客户端库](https://www.nuget.org/packages/Azure.Identity/)的最新版通过 Azure AD 凭据进行身份验证。 若要安装包，请在 NuGet 包管理器控制台中运行以下命令：

```powershell
Install-Package Azure.Identity
```
