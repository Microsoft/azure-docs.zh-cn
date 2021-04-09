---
title: 适用于专用 SQL 池的持续集成和部署
description: 适用于 Azure Synapse Analytics 中专用 SQL 池的企业级数据库 DevOps 体验，原生支持使用 Azure Pipelines 进行持续集成和部署。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d43039c98e991cd23a8e5c4fb866a8e3dcab6fc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585632"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>适用于 Azure Synapse Analytics 中专用 SQL 池的持续集成和部署

本简易教程概述如何将 SQL Server Data Tools (SSDT) 数据库项目集成到 Azure DevOps，并利用 Azure Pipelines 来设置持续集成和部署。 本教程是为数据仓库构建持续集成和部署管道的第二步。

## <a name="before-you-begin"></a>准备阶段

- 阅读[源代码管理集成教程](sql-data-warehouse-source-control-integration.md)

- 设置并连接到 Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>使用 Visual Studio 生成实现持续集成

1. 导航到 Azure Pipelines 并创建新的生成管道。

      ![新建管道](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "新建管道")

2. 选择源代码存储库 (Azure Repos Git)，然后选择 .NET Desktop 应用模板。

      ![管道设置](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "管道设置")

3. 编辑 YAML 文件，以使用适当的代理池。 YAML 文件应如下所示：

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

现已创建一个简单的环境，在其中，只要签入到源代码管理存储库主分支，就会自动触发 Visual Studio 成功生成数据库项目。 通过在本地数据库项目中做出更改并将该更改签入到主分支，来验证自动化是否能够自始至终正常运行。

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>使用 Azure Synapse Analytics（或数据库）部署任务实现持续部署

1. 使用 [Azure SQL 数据库部署任务](/azure/devops/pipelines/targets/azure-sqldb)添加一个新任务，并填写必填字段以连接到目标数据仓库。 当此任务运行时，上一生成过程生成的 DACPAC 将部署到目标数据仓库。 还可以使用 [Azure Synapse Analytics 部署任务](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)。

      ![部署任务](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "部署任务")

2. 如果使用自托管代理，请确保将环境变量设置为对 Azure Synapse Analytics 使用正确的 SqlPackage.exe。 路径应如下所示：

      ![环境变量](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "环境变量")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   运行并验证管道。 可以在本地进行更改，然后将更改签入到应生成自动生成和部署项目的源代码管理。

## <a name="next-steps"></a>后续步骤

- 探索[专用 SQL 池（以前称为 SQL DW）体系结构](massively-parallel-processing-mpp-architecture.md)
- 快速[创建专用 SQL 池（以前称为 SQL DW）](create-data-warehouse-portal.md)
- [加载示例数据](./load-data-from-azure-blob-storage-using-copy.md)
- 浏览[视频](sql-data-warehouse-videos.md)