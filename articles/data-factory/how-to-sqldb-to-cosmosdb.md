---
title: 通过 Azure 数据工厂将 Azure SQL 数据库表迁移到 Azure CosmosDB
description: 使用 azure SQL 数据库中的现有规范化数据库架构，并使用 Azure 数据工厂迁移到 Azure CosmosDB 不规范容器。
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: makromer
ms.openlocfilehash: 5d93cb49c77a4c8164a8b4e9bca349a805f39678
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041628"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>将规范化数据库架构从 Azure SQL 数据库迁移到 Azure CosmosDB 非规范化容器

本指南将介绍如何在 Azure SQL 数据库中使用现有的规范化数据库架构，并将其转换为 Azure CosmosDB 不规范架构，以便加载到 Azure CosmosDB。

通常使用第三种普通窗体对 SQL 架构建模，从而生成标准化的架构，这些架构提供高级别的数据完整性和更少的重复数据值。 查询可以在表中将实体联接在一起以进行读取。 CosmosDB 经过优化，可用于在集合或容器内通过非规范化的架构在文档中自包含的非规范化架构进行优化。

使用 Azure 数据工厂，我们将构建一个管道，该管道使用单个映射数据流从包含主键和外键的两个 Azure SQL 数据库中读取作为实体关系。 ADF 将使用数据流 Spark 引擎将这些表加入单个流，将联接行收集到数组中，并生成单独的清理文档用于插入新的 Azure CosmosDB 容器。

本指南将在名为 "orders" 的动态上构建一个新容器，该容器将使用 ```SalesOrderHeader``` ```SalesOrderDetail``` 标准 SQL Server AdventureWorks 示例数据库中的和表。 这些表表示联接的销售事务 ```SalesOrderID``` 。 每个唯一的详细信息记录都有其自己的主键 ```SalesOrderDetailID``` 。 标头与详细信息之间的关系为 ```1:M``` 。 我们将 ```SalesOrderID``` 在 ADF 中加入，然后将每个相关的详细记录滚动到名为 "detail" 的数组中。

本指南的典型 SQL 查询是：

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

生成的 CosmosDB 容器将内部查询嵌入到单个文档中，如下所示：

![集合](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>创建管道

1. 选择 " **+ 新建管道** " 以创建新管道。

2. 添加数据流活动

3. 在 "数据流" 活动中，选择 " **新建映射** 数据流"。

4. 我们将在下面构造此数据流图形

![数据流图表](media/data-flow/cosmosb1.png)

5. 定义 "SourceOrderDetails" 的源。 对于数据集，请创建一个指向该表的新的 Azure SQL 数据库数据集 ```SalesOrderDetail``` 。

6. 定义 "SourceOrderHeader" 的源。 对于数据集，请创建一个指向该表的新的 Azure SQL 数据库数据集 ```SalesOrderHeader``` 。

7. 在顶部的源中，在 "SourceOrderDetails" 之后添加派生列转换。 调用新的转换 "转换"。 需要对列进行舍入 ```UnitPrice``` ，并将其转换为 double 数据类型 CosmosDB。 将公式设置为： ```toDouble(round(UnitPrice,2))``` 。

8. 添加另一个派生列并将其称为 "MakeStruct"。 我们将在此处创建一个用于保存详细信息表中的值的层次结构。 请记住，详细信息是 ```M:1``` 标头的关系。 为新结构命名 ```orderdetailsstruct``` 并以这种方式创建层次结构，并将每个 subcolumn 设置为传入列名：

![创建结构](media/data-flow/cosmosb9.png)

9. 现在，我们来看一下销售标题源。 添加联接转换。 对于右侧，选择 "MakeStruct"。 将其设置为 "内联接"，并选择 ```SalesOrderID``` 联接条件的两侧。

10. 在添加的新联接中单击 "数据预览" 选项卡，以便您可以看到结果。 应该会看到所有与详细信息行联接的标头行。 这是从形成联接的结果 ```SalesOrderID``` 。 接下来，我们将详细信息从公共行合并到详细信息结构中并聚合公共行。

![联接](media/data-flow/cosmosb4.png)

11. 首先，我们需要删除不需要的列并确保数据值与 CosmosDB 数据类型匹配，然后才能创建用于非规范化这些行的数组。

12. 接下来添加一个选择转换，并将字段映射设置为如下所示：

![列清理器](media/data-flow/cosmosb5.png)

13. 现在再次转换货币列 ```TotalDue``` 。 与上面的步骤7类似，将公式设置为： ```toDouble(round(TotalDue,2))``` 。

14. 在这里，我们将通过通用键进行分组来非规范化行 ```SalesOrderID``` 。 添加聚合转换，并将 "分组依据" 设置为 ```SalesOrderID``` 。

15. 在聚合公式中，添加一个名为 "details" 的新列，并使用此公式收集之前创建的结构中的值 ```orderdetailsstruct``` ： ```collect(orderdetailsstruct)``` 。

16. 聚合转换将只输出作为聚合或 group by 公式的一部分的列。 那么，我们还需要包含 sales 表头中的列。 为此，请在同一聚合转换中添加列模式。 此模式将在输出中包含所有其他列：

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. 在其他属性中使用 "this" 语法，以便我们维护相同的列名称并将函数用作 ```first()``` 聚合：

![聚合](media/data-flow/cosmosb6.png)

18. 我们已准备好通过添加接收器转换来完成迁移流。 单击数据集旁边的 "新建"，并添加指向 CosmosDB 数据库的 CosmosDB 数据集。 对于集合，我们将其称为 "orders"，它将不包含任何架构和文档，因为它将在动态上创建。

19. 在接收器设置中，将键分区为 ```\SalesOrderID``` 并将操作集合为 "重新创建"。 请确保 "映射" 选项卡如下所示：

![屏幕截图显示 "映射" 选项卡。](media/data-flow/cosmosb7.png)

20. 单击 "数据预览" 以确保你看到将以下32行设置为在新容器中插入新文档：

![屏幕截图显示 "数据预览" 选项卡。](media/data-flow/cosmosb8.png)

如果一切正常，现在可以创建新的管道，将此数据流活动添加到该管道并执行它。 可以从调试或触发的运行执行。 几分钟后，你的 CosmosDB 数据库中应该有一个名为 "orders" 的新的非规范化容器。

## <a name="next-steps"></a>后续步骤

* 使用映射数据流 [转换](concepts-data-flow-overview.md)生成数据流逻辑的其余部分。
* 下载本教程的[已完成管道模板](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip)，然后将模板导入工厂。
