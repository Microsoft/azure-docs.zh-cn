---
title: 从多个容器复制文件
description: 了解如何使用解决方案模板通过 Azure 数据工厂复制多个容器中的文件。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: ec7af1e81e0b295491420597636c8443f4d36512
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376082"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>通过 Azure 数据工厂复制多个文件夹

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍了一个解决方案模板。你可以通过该模板使用多个复制活动在基于文件的存储（每个复制活动都会在其中复制单个容器或文件夹）之间复制容器或文件夹。 

> [!NOTE]
> 若要复制单个容器中的文件，使用[复制数据工具](copy-data-tool.md)通过单个复制活动创建管道的做法会更有效。 本文中的模板超出了你对该简单方案的需求。

## <a name="about-this-solution-template"></a>关于此解决方案模板

此模板会枚举源存储库中给定父文件夹中的文件夹。 然后，它会将每个文件夹复制到目标存储。

该模板包含三个活动：
- GetMetadata 会扫描源存储库，并从给定的父文件夹中获取子文件夹列表。
- ForEach 会获取 GetMetadata 活动提供的子文件夹列表，然后循环访问该列表并将每个文件夹传递到 Copy 活动。
- Copy 会将源存储中的每个文件夹复制到目标存储。

模板定义以下参数：
- SourceFileFolder 是以下数据源存储的父文件夹路径的一部分：SourceFileFolder/SourceFileDirectory，你可以在其中获取子文件夹的列表。 
- SourceFileDirectory 是以下数据源存储的父文件夹路径的一部分：SourceFileFolder/SourceFileDirectory，你可以在其中获取子文件夹的列表。 
- DestinationFileFolder 是以下父文件夹路径的一部分：DestinationFileFolder/DestinationFileDirectory，可以在其中将文件复制到目标存储。 
- DestinationFileDirectory 是父文件夹路径的一部分：DestinationFileFolder/DestinationFileDirectory，可以在其中将文件复制到目标存储。 

如果要在存储之间复制根文件夹下的多个容器，则可以以“/”形式输入所有四个参数。 这样，你将在存储之间复制所有内容。

## <a name="how-to-use-this-solution-template"></a>如何使用此解决方案模板

1. 转到“在文件存储之间复制多个文件容器”模板。 创建与源存储的 **新** 连接。 源存储是你要从多个容器复制文件的位置。

    ![与源建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png)

2. 创建与目标存储的 **新** 连接。

    ![与目标建立新的连接](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png)

3. 选择“使用此模板”  。

    ![使用此模板](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png)
    
4. 你将看到管道，如以下示例所示：

    ![显示管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png)

5. 选择“调试”，输入“参数”，然后选择“完成”。

    ![运行管道](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png)

6. 查看结果。

    ![查看结果](media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png)

## <a name="next-steps"></a>后续步骤

- [在 Azure 数据工厂中使用控制表从数据库执行批量复制](solution-template-bulk-copy-with-control-table.md)

- [使用 Azure 数据工厂复制多个容器中的文件](solution-template-copy-files-multiple-containers.md)
