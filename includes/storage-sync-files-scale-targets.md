---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29518fb971649087d8a5afa39d69c7fc2c014f98
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331143"
---
| 资源 | 目标 | 硬限制 |
|----------|--------------|------------|
| 每个区域的存储同步服务数 | 100 个存储同步服务 | 是 |
| 每个存储同步服务的同步组数 | 200 个同步组 | 是 |
| 每个存储同步服务的已注册服务器 | 99 台服务器 | 是 |
| 每个同步组的云终结点数 | 1 个云终结点 | 是 |
| 每个同步组的服务器终结点数 | 100 个服务器终结点 | 是 |
| 每个服务器的服务器终结点数 | 30 个服务器终结点 | 是 |
| 每个同步组的文件系统对象数（目录和文件） | 1 亿个对象 | 否 |
| 目录中的最大文件系统对象（目录和文件）数 | 500 万个对象 | 是 |
| 最大对象（目录和文件）安全描述符大小 | 64 KiB | 是 |
| 文件大小 | 100 GiB | 否 |
| 要进行分层的文件的最小文件大小 | V9 及更高版本：基于文件系统群集大小（双文件系统群集大小）。 例如，如果文件系统群集大小为 4kb，则最小文件大小为 8kb。<br> V8 和更早版本：64 KiB  | 是 |

> [!Note]  
> Azure 文件同步终结点可以纵向扩展到 Azure 文件共享的大小。 如果达到 Azure 文件共享大小限制，同步将无法运行。
