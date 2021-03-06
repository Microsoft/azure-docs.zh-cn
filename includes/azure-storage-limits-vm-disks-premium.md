---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334971"
---
**高级非托管虚拟机磁盘：每个帐户的限制**

| 资源 | 限制 |
| --- | --- |
| 每个帐户的总磁盘容量 |35 TB |
| 每个帐户的总快照容量 |10 TB |
| 每个帐户的最大带宽（传入 + 传出）<sup>1</sup> |<=50 Gbps |

<sup>1</sup>“传入”指从请求发送到存储帐户的所有数据。 “传出”指从存储帐户接收的响应中的所有数据。

**高级非托管虚拟机磁盘：每磁盘限制**

| 高级存储磁盘类型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁盘大小 |128 GiB |512 GiB |1,024 GiB (1 TB) |2,048 GiB (2 TB)|4,095 GiB (4 TB)|
| 每个磁盘的最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 每个磁盘的最大吞吐量 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每个存储帐户的最大磁盘数 |280 |70 |35 | 17 | 8 |

**高级非托管虚拟机磁盘：每个 VM 的限制**

| 资源 | 限制 |
| --- | --- |
| 每个 VM 的最大 IOPS |GS5 VM 为 80,000 IOPS |
| 每个 VM 的最大吞吐量 |GS5 VM 为 2,000 MB/秒 |

