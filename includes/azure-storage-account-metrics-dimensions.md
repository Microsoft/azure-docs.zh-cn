---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: c939bc87ef1f0ae4f74a7e7c21380704bafb241d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509238"
---
| 维度名称 | 说明 |
| ------------------- | ----------------- |
| **GeoType** | 来自主要或辅助群集的事务。 可用值包括 **Primary** 和 **Secondary**。 从辅助租户读取对象时，该维度会应用到读取访问异地冗余存储 (RA-GRS)。 |
| **ResponseType** | 事务响应类型。 可用的值包括： <br/><br/> <li>**ServerOtherError**：除描述的错误以外的其他所有服务器端错误 </li> <li>**ServerBusyError**：已经过身份验证的请求返回了 HTTP 503 状态代码。 </li> <li>**ServerTimeoutError**：已经过身份验证的超时请求返回了 HTTP 500 状态代码。 由于服务器错误而发生超时。 </li> <li>**AuthorizationError**：由于未经授权访问数据或者授权失败，经过身份验证的请求失败。 </li> <li>**NetworkError**：由于网络错误，经过身份验证的请求失败。 往往发生于客户端在超时失效之前提前关闭了连接时。 </li><li>**ClientAccountBandwidthThrottlingError**：因为超出了 [存储帐户可伸缩性限制](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，在带宽方面对请求进行了限制。</li><li>**ClientAccountRequestThrottlingError**：因为超出了 [存储帐户可伸缩性限制](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)，在请求速率方面对请求进行了限制。<li>**ClientShareError**：其他客户端限制错误。 不包括 ClientAccountBandwidthThrottlingError 和 ClientAccountRequestThrottlingError。</li><li>**ClientShareEgressThrottlingError**：仅适用于高级文件共享。 其他客户端限制错误。 请求失败，因为出口带宽限制超出了共享限制。 排除 ClientAccountBandwidthThrottlingError。</li><li>**ClientShareIngressThrottlingError**：仅适用于高级文件共享。 其他客户端限制错误。 请求失败，因为入口带宽限制超出了共享限制。 排除 ClientAccountBandwidthThrottlingError。</li><li>**ClientShareIopsThrottlingError**：仅适用于高级文件共享。 其他客户端限制错误。 由于 IOPS 限制，请求失败。 排除 ClientAccountRequestThrottlingError。</li><li>**ClientTimeoutError**：已经过身份验证的超时请求返回了 HTTP 500 状态代码。 如果将客户端的网络超时或请求超时设置为比存储服务预期值更小的值，则预期会发生此超时。 否则，会报告为 ServerTimeoutError。 </li> <li>**ClientOtherError**：除描述的错误以外的其他所有客户端错误。 </li> <li>**成功**：请求成功</li> <li> **SuccessWithThrottling**：请求成功，具体表现在：头一次或头几次尝试时，SMB 客户端会被限制，但重试后会成功。</li><li> **SuccessWithShareEgressThrottling**：仅适用于高级文件共享。 如果在第一次尝试)  (时，SMB 客户端由于出口带宽限制而受到限制，则该请求将会成功，但在重试后会成功。</li><li> **SuccessWithShareIngressThrottling**：仅适用于高级文件共享。 当 SMB 客户端由于第一次尝试 (时进入带宽限制而受到限制时，成功的请求) 但在重试后成功完成。</li><li> **SuccessWithShareIopsThrottling**：仅适用于高级文件共享。 在第一次尝试 (时，SMB 客户端因 IOPS 限制而受到限制时的成功请求) 但在重试后成功完成。</li> |
| **ApiName** | 操作的名称。 
| **身份验证** | 事务中所用的身份验证类型。 可用的值包括： <br/> <li>**AccountKey**：事务通过存储帐户密钥进行身份验证。</li> <li>**SAS**：事务通过共享访问签名进行身份验证。</li> <li>**OAuth**：事务通过 OAuth 访问令牌进行身份验证。</li> <li>**Anonymous**：事务以匿名方式请求。 不包括预检请求。</li> <li>**AnonymousPreflight**：事务为预检请求。</li> |