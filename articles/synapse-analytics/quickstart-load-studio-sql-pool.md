---
title: 快速入门：使用专用 SQL 池对数据进行大容量加载
description: 使用 Synapse Studio 将数据大容量加载到 Azure Synapse Analytics 中的专用 SQL 池中。
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 11/16/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 312c57c103bf733bc72c5de1d22ab3239d5b5e96
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484636"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>快速入门：使用 Synapse SQL 进行大容量加载

使用 Synapse Studio 中的“大容量加载”向导可轻松加载数据。 “大容量加载”向导将引导你使用 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)创建 T-SQL 脚本以大容量加载数据。 

## <a name="entry-points-to-the-bulk-load-wizard"></a>“大容量加载”向导的入口点

只需在 Synapse Studio 中的以下区域单击鼠标右键，即可轻松使用专用 SQL 池大容量加载数据：

- 已连接到工作区的 Azure 存储帐户中的文件或文件夹 ![右键单击存储帐户中的文件或文件夹](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>先决条件

- 此向导生成一个使用 Azure AD 直通进行身份验证的 COPY 语句。 [Azure AD 用户必须](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication)至少具有 ADLS Gen2 帐户的存储 Blob 数据参与者 Azure 角色才能访问工作区。 

- 若要创建一个新表，以便将数据加载到其中，必须具有[使用 COPY 语句所需的权限](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions)和“创建表”权限。

- 与 ADLS Gen2 帐户关联的链接服务必须能够访问要加载的文件/文件夹。 例如，如果链接服务身份验证机制为“托管标识”，则工作区托管标识必须至少对存储帐户拥有存储 Blob 读取者权限。

- 如果在工作区上启用了 VNet，请确保与源数据和错误文件位置的 ADLS Gen2 帐户链接服务关联的集成运行时已启用交互式创作。 在向导中自动检测架构、预览源文件内容和浏览 ADLS Gen2 存储帐户需要交互式创作。

### <a name="steps"></a>步骤

1. 在“源存储位置”面板上选择要从其加载数据的存储帐户以及文件或文件夹。 向导将自动尝试检测 Parquet 文件。 如果无法确认 Parquet 文件类型，则默认情况下将使用带分隔符的文本 (CSV)。

   ![选择源位置](./sql/media/bulk-load/bulk-load-source-location.png)

2. 选择文件格式设置，包括要在其中写入被拒绝行（错误文件）的存储帐户。 目前仅支持 CSV 和 Parquet 文件。

    ![选择文件格式设置](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. 可以选择“预览数据”来了解 COPY 语句如何分析文件，以便帮助你配置文件格式设置。 每次更改文件格式设置时选择“预览数据”，以了解 COPY 语句将如何使用更新的设置来分析文件：![预览数据](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - 大容量加载向导中不支持预览带有多字符字段终止符的字段。 指定多字符字段终止符时，大容量加载向导将预览单个列中的数据。 
> - COPY 语句支持指定多字符行终止符；但大容量加载向导中不支持此操作，这会引发错误。

4. 选择要用于加载的专用 SQL 池，包括选择该加载是针对现有表还是针对新表：![选择目标位置](./sql/media/bulk-load/bulk-load-target-location.png)

5. 选择“配置列映射”，以确保具有适当的列映射。 请注意，如果启用了“推断列名称”，将自动检测列名称。 就新表来说，配置列映射对于更新目标列数据类型至关重要：![配置列映射](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. 选择“打开脚本”，此时会生成一个 T-SQL 脚本，其中包含用于从数据湖加载数据的 COPY 语句：![打开 SQL 脚本](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>后续步骤

- 有关复制功能的详细信息，请查看 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)一文
- 查看[数据加载概述](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)一文
