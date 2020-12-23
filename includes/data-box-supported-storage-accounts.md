---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 25c2ea04cd062554a975c63aae9b97846e646d68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026956"
---
下面是 Data Box 设备支持的存储帐户和存储类型的列表。 有关所有不同类型的存储帐户及其完整功能的完整列表，请参阅[存储帐户类型](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts)。

对于导入顺序，下表显示了支持的存储帐户。

| **存储帐户/支持的存储类型** | **块 blob** |**页 blob** _ |_ *Azure 文件** |**说明**|
| --- | --- | -- | -- | -- |
| 经典标准 | Y | Y | Y |
| 常规用途 v1 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v1 高级  |  | Y| | |
| 常规用途 v2 标准  | Y | Y | Y | 支持热和冷。|
| 常规用途 v2 高级  |  |Y | | |
| Blob 存储标准 |Y | | |支持热和冷。 |

\**-上传到页 blob 的数据必须为512字节，如 vhd。*

对于出口订单，下表显示了支持的存储帐户。

| **存储帐户/支持的存储类型** | **块 blob** |**页 blob** _ |_ *Azure 文件** |**支持的访问层**|
| --- | --- | -- | -- | -- |
| 经典标准 | Y | Y | Y | |
| 常规用途 v1 标准  | Y | Y | Y | 热、冷|
| 常规用途 v1 高级  |  | Y| | |
| 常规用途 v2 标准  | Y | Y | Y | 热、冷|
| 常规用途 v2 高级  |  |Y | | |
| Blob 存储标准 |Y | | |热、冷 |
| 块 Blob 存储高级版 |Y | | |热、冷 |
| 页 Blob 存储高级版 | |Y | | |

> [!IMPORTANT]
> - 对于常规用途的帐户，Data Box 不支持导入订单的队列、表和磁盘存储类型。 对于出口订单，Data Box 不支持用于常规用途帐户的队列、表、磁盘和 Azure Data Lake 第2代存储类型。
> - Data Box 不支持 Blob 存储和块 Blob 存储帐户的追加 blob。
> - Data Box 不支持高级文件存储帐户。
> - 上传到页 blob 的数据必须为512字节，如 Vhd。
> - 最多可导出 80 TB。
> - 不会导出文件历史记录和 blob 快照。