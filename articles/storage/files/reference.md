---
title: Azure 文件存储参考
description: 查找 Azure 文件存储 API 参考、自述文件和客户端库包。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/14/2020
ms.service: storage
ms.topic: conceptual
ms.reviewer: ripohane
ms.openlocfilehash: 828bb909aeb5d34f087a3173f792b325dc7cfd55
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504808"
---
# <a name="azure-files-reference"></a>Azure 文件存储参考

查找 Azure 文件 API 参考、库包、自述文件和入门文章。

## <a name="net-client-libraries"></a>.NET 客户端库

下表列出了 Azure 文件 .NET API 的参考文档和示例文档。

|  版本  | 参考文档 | 程序包 | 快速入门 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [适用于 .NET 的 Azure 文件客户端库 v12](/dotnet/api/overview/azure/storage.files.shares-readme) | [包 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files/) | &nbsp; |
| 11.x | [Microsoft.Azure.Storage.File 命名空间](/dotnet/api/microsoft.azure.storage.file) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/) | [使用 .NET 针对 Azure 文件进行开发](./storage-dotnet-how-to-use-files.md) |

### <a name="storage-management"></a>存储管理

下表列出了 Azure 存储管理 .NET API 的参考文档。

|  版本  | 参考文档 | 程序包 |
| :-------: | ----------------------- | ------- |
| 16.x | [Microsoft.Azure.Management.Storage](/dotnet/api/microsoft.azure.management.storage) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) |

### <a name="data-movement"></a>数据移动

下表列出了 Azure 存储数据移动 .NET API 的参考文档。

|  版本  | 参考文档 | 程序包 |
| :-------: | ----------------------- | ------- |
| 1.x | [数据移动](/dotnet/api/microsoft.azure.storage.datamovement) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) |

## <a name="java-client-libraries"></a>Java 客户端库

下表列出了 Azure 文件 Java API 的参考文档和示例文档。

|  版本  | 参考文档 | 程序包 | 快速入门 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [适用于 Java 的 Azure 文件客户端库](/java/api/overview/azure/storage-file-share-readme) | [包 (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-file-share) | &nbsp; |
| 8.x | [com.microsoft.azure.storage.file](/java/api/com.microsoft.azure.storage.file) | [包 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) | [使用 Java 针对 Azure 文件进行开发](./storage-java-how-to-use-file-storage.md) |

### <a name="storage-management"></a>存储管理

下表列出了 Azure 存储管理 Java API 的参考文档。

|  版本  | 参考文档 | 程序包 |
| :-------: | ----------------------- | ------- |
| 0.9.x | [com.microsoft.azure.management.storage](/java/api/overview/azure/storage/management) | [包 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-svc-mgmt-storage) |

## <a name="python-client-libraries"></a>Python 客户端库

下表列出了 Azure 文件 Python API 的参考文档和示例文档。

|  版本  | 参考文档 | 程序包 | 快速入门 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [适用于 Python 的 Azure 存储客户端库 v12](/azure/developer/python/sdk/storage/overview) | [包 (PyPI)](https://pypi.org/project/azure-storage-file/12.0.0b4/) | [示例](/python/api/overview/azure/storage-file-share-readme#examples) |
| 2.x | [适用于 Python 的 Azure 存储客户端库 v2](/azure/developer/python/sdk/storage/overview?view=storage-py-v2&preserve-view=true) | [包 (PyPI)](https://pypi.org/project/azure-storage-file/2.1.0/) | [使用 Python 针对 Azure 文件进行开发](./storage-python-how-to-use-file-storage.md) |

## <a name="javascript-client-libraries"></a>JavaScript 客户端库

下表列出了 Azure 文件 JavaScript API 的参考文档和示例文档。

|  版本  | 参考文档 | 程序包 | 快速入门 |
| :-------: | ----------------------- | ------- | ---------- |
| 12.x | [适用于 JavaScript 的 Azure 文件客户端库](/javascript/api/overview/azure/storage-file-share-readme) | [包 (npm)](https://www.npmjs.com/package/@azure/storage-file-share) | [示例](/javascript/api/overview/azure/storage-file-share-readme#examples) |
| 10.x | [@azure/storage-file](/javascript/api/@azure/storage-file) | [包 (npm)](https://www.npmjs.com/package/@azure/storage-file) | &nbsp; |

## <a name="rest-apis"></a>REST API

下表列出了 Azure 文件 REST API 的参考文档和示例文档。

| 参考文档 | 概述 |
| ----------------------- | -------- |
| [文件服务 REST API](/rest/api/storageservices/file-service-rest-api) | [文件服务概念](/rest/api/storageservices/file-service-concepts) |

### <a name="other-rest-reference"></a>其他 REST 参考

- [Azure 存储导入和导出 REST API](/rest/api/storageimportexport/) 可帮助你管理导入/导出作业，这些作业将数据传输到 Blob 存储或从 Blob 存储传输数据。

## <a name="other-languages-and-platforms"></a>其他语言和平台

下面的列表包含一些链接，这些链接指向其他编程语言和平台的库。

- [C++](https://azure.github.io/azure-storage-cpp)
- [Ruby](https://azure.github.io/azure-storage-ruby)
- [PHP](https://azure.github.io/azure-storage-php/)
- [iOS](https://azure.github.io/azure-storage-ios/)
- [Android](https://azure.github.io/azure-storage-android)

## <a name="powershell"></a>PowerShell

下表包含指向参考内容的最新版本的链接。

| 版本 | 平台 |
| ------- | -------- |
|  4.x  | [PowerShell](/powershell/module/az.storage/?view=azps-4.8.0&preserve-view=true) |
|  3.x  | [PowerShell](/powershell/module/az.storage/?view=azps-3.8.0&preserve-view=true) |
|  2.x  | [PowerShell](/powershell/module/az.storage/?view=azps-2.8.0&preserve-view=true) |

## <a name="azure-cli"></a>Azure CLI

- [Azure CLI](/cli/azure/storage)