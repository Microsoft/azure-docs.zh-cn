---
title: 对 Azure 存储表数据进行加密 | Microsoft Docs
description: 了解 Azure 存储中的表数据加密。 使用 .NET Azure 存储客户端库可以加密插入和替换操作的字符串实体。
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/11/2018
ms.subservice: tables
ms.openlocfilehash: 6b874bfb9f3c261f395f8358151ae555cdaed8e8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756880"
---
# <a name="encrypt-table-data"></a>对表数据进行加密
.NET Azure 存储客户端库支持对插入和替换操作的字符串实体属性进行加密。 加密的字符串作为二进制属性存储在服务中，并在解密之后转换回字符串。    

对于表，除了加密策略以外，用户还必须指定要加密的属性。 可以通过指定 [EncryptProperty] 特性（适用于从 TableEntity 派生的 POCO 实体）或在请求选项中指定加密解析程序来完成此操作。 加密解析程序是一个委托，它接受分区键、行键和属性名称并返回一个布尔值以指示是否应加密该属性。 在加密过程中，客户端库使用此信息来确定是否在写入到网络时对属性进行加密。 该委托还可以围绕如何加密属性实现逻辑的可能性。 （例如，如果 X，则加密属性 A；否则加密属性 A 和 B。）在读取或查询实体时，不必提供此信息。

## <a name="merge-support"></a>合并支持

当前不支持合并。 由于一部分属性可能以前已使用不同的密钥加密，因此只合并新属性和更新元数据会导致数据丢失。 合并需要进行额外的服务调用以从服务中读取预先存在的实体，或者需要为属性使用一个新密钥，由于性能方面的原因，这两种方案都不适用。     

若要了解如何加密表数据，请参阅 [Microsoft Azure 存储的客户端加密和 Azure 密钥保管库](../common/storage-client-side-encryption.md)。  

## <a name="next-steps"></a>后续步骤

- [表设计模式](table-storage-design-patterns.md)
- [为关系建模](table-storage-design-modeling.md)
- [针对数据修改的设计](table-storage-design-for-modification.md)
