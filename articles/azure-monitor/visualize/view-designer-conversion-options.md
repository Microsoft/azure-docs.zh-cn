---
title: Azure Monitor 视图设计器到工作簿转换选项
description: Azure Monitor 中用于从视图转换到工作簿的转换选项。
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b8b6b8b41c729c3cbb6cf4589d679e93149e5314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043382"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor 视图设计器到工作簿转换选项
[视图设计器](view-designer.md)是 Azure Monitor 的一项功能，它让你能够创建自定义视图，帮助你通过图表、列表和时间线将 Log Analytics 工作区中的数据可视化。 这些元素已逐步淘汰，取而代之的是除这些功能外还可提供其他功能的工作簿。 本文比较了这两者之间的基本概念，以及用于将视图转换为工作簿的选项。

## <a name="basic-workbook-designs"></a>基本工作簿设计

视图设计器具有固定的静态表示形式，而工作簿可以自由地包含和修改数据的表示方式。 下图描绘了两个示例，说明了在转换视图时如何排列工作簿。

[垂直工作簿](view-designer-conversion-examples.md#vertical)
![垂直](media/view-designer-conversion-options/view-designer-vertical.png)

[选项卡式工作簿](view-designer-conversion-examples.md#tabbed)
![数据类型分布选项卡](media/view-designer-conversion-options/distribution-tab.png)
![随时间推移的数据类型选项卡](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>磁贴转换
视图设计器使用概览磁贴功能来表示和汇总总体状态。 它们以七个磁贴表示，既有数字也有图表。 在工作簿中，用户可以创建类似的可视化效果，并将其固定，类似于概览磁贴的原始样式。 

![库](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>查看仪表板转换
视图设计器磁贴通常由两部分组成：一个可视化对象和一个与可视化对象中的数据相匹配的列表，例如 **环形和列表** 磁贴。

![环形](media/view-designer-conversion-options/donut-example.png)

使用工作簿，用户可以选择对视图的一个或两个部分进行查询。 在工作簿中构建查询是一个简单的两步过程。 首先，通过查询生成数据；其次，数据将呈现为可视化效果。  下面是一个示例，演示如何在工作簿中重新创建此视图：

![转换](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>后续步骤
- [访问工作簿和权限](view-designer-conversion-access.md)