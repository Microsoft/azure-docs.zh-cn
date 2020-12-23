---
title: 映射数据流中的多个分支
description: 复制包含多个分支的映射数据流中的数据流
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a1dd4baecd0e1f817c93652fbc0766069ccf5583
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040133"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>在映射数据流中创建新分支

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

添加新分支，对同一数据流执行多组操作和转换。 如果要为多个接收器使用相同的源，或将自联接数据一起使用，则添加新分支会很有用。

可以从转换列表添加新分支，类似于其他转换。 仅当正在尝试分支的转换之后存在转换后， **新分支** 才会作为操作可用。

![屏幕截图显示 "多输入/输出" 菜单中的 "新建分支" 选项。](media/data-flow/new-branch2.png "添加新分支")

在下面的示例中，数据流正在读取出租车行程数据。 由 day 和供应商聚合的输出是必需的。 可以添加一个新的分支，而不是创建两个从同一源中读取的单独数据流。 这样，就可以将这两种聚合作为同一数据流的一部分来执行。 

![屏幕截图显示来自源的两个分支的数据流。](media/data-flow/new-branch.png "添加新分支")
