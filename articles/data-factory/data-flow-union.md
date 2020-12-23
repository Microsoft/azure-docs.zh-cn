---
title: 映射数据流中的联合转换
description: Azure 数据工厂映射数据流新建分支转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/27/2020
ms.openlocfilehash: e10418c18e6f8e0089a893e9d33ee1081f3131f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823639"
---
# <a name="union-transformation-in-mapping-data-flow"></a>映射数据流中的联合转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Union 会将多个数据流组合成一个数据流，而这些流的 SQL Union 会作为 Union 转换的新输出。 每个输入流中的所有架构将合并到数据流中，而无需具有联接键。

您可以通过选择每个已配置的行旁边的 "+" 图标，同时包含源数据以及数据流中现有转换的流，来合并设置表中的 n 个流。

下面是 ADF 地图数据流中联合转换的简短视频演练：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

![联合转换](media/data-flow/union.png "Union")

在这种情况下，可以将来自多个源的不同元数据组合在一起 (在此示例中，三个不同的源文件) 并将它们合并为一个流：

![联合转换概述](media/data-flow/union111.png "联合1")

若要实现此目的，请通过包含要添加的所有源在联合设置中添加其他行。 无需使用常见的查找或联接键：

![联合转换设置](media/data-flow/unionsettings.png "联合设置")

如果在联合后设置了 Select 转换，则可以重命名不是从 headerless 源命名的重叠字段或字段。 单击 "检查" 以查看在此示例中的三个不同源合并元数据和132总计列：

![联合转换最终](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>名称和位置

选择 "按名称联合" 时，每个列值都将从每个源的相应列中拖放到新的连接的元数据架构。

如果选择 "按位置联合"，则每个列值都将从每个相应的源中拖放到原始位置，从而产生一个新的合并数据流，其中每个源的数据添加到同一个流中：

![联合输出](media/data-flow/unionoutput.png "联合输出")

## <a name="next-steps"></a>后续步骤

探索类似的转换，包括 [Join](data-flow-join.md) 和 [Exists](data-flow-exists.md)。
