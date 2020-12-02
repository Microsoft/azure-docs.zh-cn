---
title: 映射数据流转换概述
description: 映射数据流中可用的不同转换的概述
author: dcstwh
ms.author: weetok
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9d44890e84e97a413543a4291d1331fee0f04841
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490867"
---
# <a name="mapping-data-flow-transformation-overview"></a>映射数据流转换概述

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

下面是映射数据流当前支持的转换的列表。 单击每个转换可了解其配置详细信息。

| 名称 | 类别 | 说明 |
| ---- | -------- | ----------- |
| [聚合](data-flow-aggregate.md) | 架构修饰符 | 定义不同类型的聚合，如 SUM、MIN、MAX 和 COUNT，并按现有或计算列分组。 | 
| [更改行](data-flow-alter-row.md) | 行修饰符 | 对行设置 insert、delete、update 和 upsert 策略。 |
| [有条件拆分](data-flow-conditional-split.md) | 多个输入/输出 | 根据匹配条件将数据行路由到不同的流。 |
| [派生列](data-flow-derived-column.md) | 架构修饰符 | 使用数据流表达式语言生成新列或修改现有字段。 | 
| [Exists](data-flow-exists.md) | 多个输入/输出 | 检查数据是否存在于另一个源或流中。 | 
| [Filter](data-flow-filter.md) | 行修饰符 | 基于条件筛选行。 |
| [平展](data-flow-flatten.md) | 架构修饰符 |  在层次结构（如 JSON）中采用数组值，并将其展开到各个行。 |
| [Join](data-flow-join.md) | 多个输入/输出 |  合并两个源或流中的数据。 |
| [查找](data-flow-lookup.md) | 多个输入/输出 | 引用来自其他源的数据。 |
| [新建分支](data-flow-new-branch.md) | 多个输入/输出 | 对同一数据流应用多组操作和转换。 |
| [透视表](data-flow-pivot.md) | 架构修饰符 | 一种聚合，其中一个或多个分组列的不同行值转换为单独的列。 |
| [级别](data-flow-rank.md) | 架构修饰符 | 根据排序条件生成排序排名 |
| [Select](data-flow-select.md) | 架构修饰符 | 别名列和流名称，以及删除列或对列重新排序 |
| [接收器](data-flow-sink.md) | - | 数据的最终目标 |
| [Sort](data-flow-sort.md) | 行修饰符 | 对当前数据流中的传入行进行排序 |
| [源](data-flow-source.md) | - | 数据流的数据源 |
| [代理键](data-flow-surrogate-key.md) | 架构修饰符 | 添加增量非业务任意密钥值 |
| [联合](data-flow-union.md) | 多个输入/输出 | 垂直合并多个数据流 |
| [逆透视](data-flow-unpivot.md) | 架构修饰符 | 将列透视为行值 |
| [窗口](data-flow-window.md) | 架构修饰符 |  定义数据流中的列的基于窗口的聚合。 |
