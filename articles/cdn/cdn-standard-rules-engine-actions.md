---
title: Azure CDN 标准规则引擎的操作 | Microsoft Docs
description: 有关 Azure 内容分发网络 (Azure CDN) 标准规则引擎的操作的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760118"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN 标准规则引擎的操作

在 Azure 内容分发网络 (Azure CDN) [标准规则引擎](cdn-standard-rules-engine.md)中，规则由一个或多个匹配条件和一个操作组成。 本文将详细介绍可在 Azure CDN 标准规则引擎中使用的操作。

规则的第二部分是一个操作。 操作定义了要应用于某个匹配条件或匹配条件集所识别的请求类型的行为。

## <a name="actions"></a>Actions

可在 Azure CDN 标准规则引擎中使用以下操作。 

### <a name="cache-expiration"></a>缓存到期

根据规则匹配条件指定的请求，使用此操作来覆盖终结点的生存时间 (TTL) 值。

#### <a name="required-fields"></a>Required fields

缓存行为 |  说明              
---------------|----------------
绕过缓存 | 如果选择此选项并且规则匹配，则不对内容进行缓存。
替代 | 如果选择此选项并且规则匹配，则从原点返回的 TTL 值会被该操作中指定的值覆盖。 仅当响应可缓存时才会应用此行为。 cache-control 响应头的值为“no-cache”、“private”、“no-store”时，操作将不适用。
缺少时设置 | 如果选择此选项并且规则匹配，而没有 TTL 值从原点返回，那么规则会将 TTL 设置为被该操作中指定的值。 仅当响应可缓存时才会应用此行为。 cache-control 响应头的值为“no-cache”、“private”、“no-store”时，操作将不适用。

#### <a name="additional-fields"></a>其他字段

天 | 小时 | 分钟数 | 秒
-----|-------|---------|--------
int | int | int | int 

### <a name="cache-key-query-string"></a>缓存键查询字符串

使用此操作来根据查询字符串修改缓存键。

#### <a name="required-fields"></a>Required fields

行为 | 说明
---------|------------
包括 | 如果选择此选项并且规则匹配，则在生成缓存键时将包含参数中指定的查询字符串。 
缓存每个唯一的 URL | 如果选择此选项并且规则匹配，则每个唯一 URL 都有其自己的缓存键。 
Exclude | 如果选择此选项并且规则匹配，则在生成缓存键时将不包含参数中指定的查询字符串。
忽略查询字符串 | 如果选择此选项并且规则匹配，则在生成缓存键时不考虑查询字符串。 

### <a name="modify-request-header"></a>修改请求标头

使用此操作可以修改发送到源的请求中提供的标头。

#### <a name="required-fields"></a>Required fields

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 如果选择此选项并且规则匹配，则会将“标头名称”中指定的标头添加到请求并使用指定的值。 如果该标头已存在，则会将该值追加到现有值后面。 | 字符串
Overwrite | 如果选择此选项并且规则匹配，则会将“标头名称”中指定的标头添加到请求并使用指定的值。 如果该标头已存在，则指定的值将替代现有值。 | 字符串
删除 | 如果选择此选项，规则匹配，并且在规则中指定的标头存在，则会从请求中删除该标头。 | 字符串

### <a name="modify-response-header"></a>修改响应标头

使用此操作可以修改返回给客户端的响应中提供的标头。

#### <a name="required-fields"></a>Required fields

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 如果选择此选项并且规则匹配，则会将“标头名称”中指定的标头添加到响应并使用指定的“值” 。 如果该标头已存在，则会将该“值”追加到现有值后面。 | 字符串
Overwrite | 如果选择此选项并且规则匹配，则会将“标头名称”中指定的标头添加到响应并使用指定的“值” 。 如果该标头已存在，则该“值”将替代现有值。 | 字符串
删除 | 如果选择此选项，规则匹配，并且在规则中指定的标头已存在，则会从响应中删除该标头。 | 字符串

### <a name="url-redirect"></a>URL 重定向

使用此操作可将客户端重定向到一个新 URL。 

#### <a name="required-fields"></a>Required fields

字段 | 说明 
------|------------
类型 | 选择要返回给请求方的响应类型：“已找到”(302)、“已移动”(301)、“临时重定向”(307) 和“永久重定向”(308)。
协议 | 匹配请求、HTTP、HTTPS。
主机名 | 选择要将请求重定向到的主机名。 留空会保留传入主机。
路径 | 定义要在重定向中使用的路径。 留空会保留传入路径。  
查询字符串 | 定义重定向中使用的查询字符串。 留空会保留传入的查询字符串。 
Fragment | 定义要在重定向中使用的片段。 留空会保留传入片段。 

强烈建议使用绝对 URL。 使用相对 URL 可能会将 Azure CDN URL 重定向到无效的路径。 

### <a name="url-rewrite"></a>URL 重写

使用此操作可以重写路由到源的请求的路径。

#### <a name="required-fields"></a>Required fields

字段 | 说明 
------|------------
源模式 | 定义要替换的 URL 路径中的源模式。 当前，源模式使用基于前缀的匹配。 要匹配所有 URL 路径，可使用正斜杠（“/”）作为源模式值。
目标 | 定义要在重写中使用的目标路径。 目标路径会覆盖源模式。
暂留不匹配的路径 | 如果设置为“是”，则会将源模式后面的剩余路径追加到新的目标路径。 

## <a name="next-steps"></a>后续步骤

- [Azure CDN 概述](cdn-overview.md)
- [标准规则引擎参考](cdn-standard-rules-engine-reference.md)
- [标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
