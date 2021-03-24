---
title: 使用弹性作业的作业自动化概述
description: 使用弹性作业实现作业自动化，以跨一个或多个数据库运行 Transact-SQL (T-SQL) 脚本
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 1f4bd28d2b95aeebe07fcad84d757327622d51f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690424"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>使用弹性作业（预览版）自动完成管理任务

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

可以创建和计划可针对一个或多个 Azure SQL 数据库定期执行的弹性作业，以运行 Transact-SQL (T-SQL) 查询和执行维护任务。 

可以定义目标数据库或者要在其中执行作业的数据库组，同时定义作业的运行计划。
作业可以处理登录到目标数据库的任务。 此外，可以定义、维护以及保存要跨一组数据库执行的 Transact-SQL 脚本。

每个作业会记录执行状态，如果发生任何失败，则还会自动重试操作。

## <a name="when-to-use-elastic-jobs"></a>何时使用弹性作业

弹性作业自动化有多种使用场景：

- 自动完成管理任务，并将作业计划为在每个工作日、数小时之后或按其他频率运行。
  - 部署架构更改、凭据管理、性能数据收集或租户（客户）遥测数据收集。
  - 更新引用数据（所有数据库的公用信息）、从 Azure Blob 存储加载数据。
- 配置作业，以便定期（例如，在非高峰时段）对一系列数据库执行作业。
  - 持续将一组数据库中的查询结果收集到中央表中。 性能查询可以持续执行，并可配置为触发执行其他任务。
- 收集要报告的数据
  - 将数据库集合中的数据聚合到单个目标表中。
  - 对大量的数据库执行长时间运行的数据处理查询，例如，收集客户遥测数据。 结果将收集到单个目标表以供进一步分析。
- 数据移动 

### <a name="automation-on-other-platforms"></a>其他平台上的自动化

在不同平台上考虑以下作业安排技术：

- 弹性作业是针对一个或多个 Azure SQL 数据库中的数据库执行自定义作业的作业计划服务。
- SQL 代理作业由 SQL 代理服务执行，该服务继续用于 SQL Server 中的任务自动化，同时还包含在 Azure SQL 托管实例中。 SQL 代理作业在 Azure SQL 数据库中不可用。

弹性作业可以将 [Azure SQL数据库](sql-database-paas-overview.md)、[Azure SQL 数据库弹性池](elastic-pool-overview.md)和[分片映射](elastic-scale-shard-map-management.md)中的 Azure SQL 数据库作为目标。

对于 SQL Server 和 Azure SQL 托管实例中的 T-SQL 脚本作业自动化，请考虑 [SQL 代理](job-automation-managed-instances.md)。 

对于 Azure Synapse Analytics 中的 T-SQL 脚本作业自动化，请考虑[具有重复触发器的管道](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)，这些管道[基于 Azure 数据工厂](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)。

值得注意的是，SQL 代理（在 SQL Server 中提供以及作为 SQL 托管实例的一部分提供）与数据库弹性作业代理（可对 Azure SQL 数据库或 SQL Server 和 Azure SQL 托管实例、Azure Synapse Analytics 中的数据库执行 T-SQL）之间存在差异。

| |弹性作业 |SQL 代理 |
|---------|---------|---------|
|**范围** | 作业代理所在 Azure 云中任意数目的 Azure SQL 数据库中的数据库和/或数据仓库。 目标可以位于不同的服务器、订阅和/或区域中。 <br><br>目标组可以包含单个数据库或数据仓库，也可以包含某个服务器、池或分片映射中的所有数据库（在作业运行时动态枚举）。 | SQL 代理所在实例中的任何单个数据库。 SQL Server 代理的多服务器管理功能使主实例/目标实例能够协调作业执行，但此功能在 SQL 托管实例中不可用。 |
|**支持的 API 和工具** | 门户、PowerShell、T-SQL、Azure 资源管理器 | T-SQL、SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>弹性作业目标

使用弹性作业，可以按计划或按需跨大量数据库并行运行一个或多个 T-SQL 脚本。

可以针对数据库的任意组合运行计划作业：可以是一个或多个单独的数据库，可以是一个服务器上的所有数据库，可以是一个弹性池中的所有数据库，也可以是分片映射，而且还可以灵活地包括或排除任何特定的数据库。 作业可以跨多个服务器、多个池来运行，甚至可以针对不同订阅中的数据库来运行。 服务器和池会在运行时进行动态枚举，因此作业会针对执行时目标组中存在的所有数据库来运行。

下图显示了一个跨不同类型的目标组执行作业的作业代理：

![弹性作业代理概念模型](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>弹性作业组件

|组件 | 说明（表下提供其他详细信息） |
|---------|---------|
|[**弹性作业代理**](#elastic-job-agent) | 为了运行和管理作业而创建的 Azure 资源。 |
|[**作业数据库**](#elastic-job-database) | 作业代理用来存储作业相关数据、作业定义等内容的 Azure SQL 数据库中的数据库。 |
|[**目标组**](#target-group) | 一组服务器、池、数据库和分片映射，可对其运行作业。 |
|[**作业**](#elastic-jobs-and-job-steps) | 作业是由一个或多个作业步骤组成的工作单元。 作业步骤指定要运行的 T-SQL 脚本，以及执行脚本所需的其他详细信息。 |

#### <a name="elastic-job-agent"></a>弹性作业代理

弹性作业代理是用于创建、运行和管理作业的 Azure 资源。 弹性作业代理是在门户（也支持 [PowerShell](elastic-jobs-powershell-create.md) 和 REST）中创建的 Azure 资源。

创建“弹性作业代理”需要现有的 Azure SQL 数据库中的数据库。 代理将这个现有的 Azure SQL 数据库配置为[作业数据库](#elastic-job-database)。

弹性作业代理免费。 作业数据库的费率与任何 Azure SQL 数据库中的数据库一样。

#### <a name="elastic-job-database"></a>弹性作业数据库

作业数据库用于定义作业以及跟踪作业执行操作的状态和历史记录。 作业数据库也用于存储代理元数据、日志、结果、作业定义，并且还包含许多有用的存储过程，以及其他数据库对象，可以通过 T-SQL 创建、运行和管理作业。

就目前的预览版来说，需要使用现有的 Azure SQL 数据库中的数据库（S0 或更高级别）来创建弹性作业代理。

作业数据库应该干净且为空，其服务目标应该为 S0 或更高的 Azure SQL 数据库。 作业数据库的服务对象建议使用 S1 或更高，但最佳选择取决于作业的性能需求：作业步骤数，作业目标数，以及作业的运行频率。 

如果针对作业数据库的操作的速度比预期慢，则在出现速度缓慢的情况时使用 Azure 门户或 [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV [监视](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring)作业数据库中的数据库性能和资源利用率。 如果资源（如 CPU、数据 IO 或日志写入）的使用率达到 100%，且与出现缓慢情况的时间段相关，请考虑以增量方式将数据库扩展到更高的服务目标（采用 [DTU 模型](service-tiers-dtu.md)或 [vCore 模型](service-tiers-vcore.md)），直到工作数据库性能得到充分改进。

##### <a name="elastic-job-database-permissions"></a>弹性作业数据库权限

在创建作业代理期间，会在作业数据库中创建一个架构、多个表和一个名为 *jobs_reader* 的角色。 此角色使用以下权限创建，旨在为管理员提供进行作业监视所需的更细致访问控制：

|角色名称 |“作业”架构权限 |“jobs_internal”架构权限 |
|---------|---------|---------|
|**jobs_reader** | SELECT | 无 |

> [!IMPORTANT]
> 在以数据库管理员身份授予作业数据库的访问权限之前，请考虑清楚安全隐患。 有权创建或编辑作业的恶意用户可能会创建或编辑一个作业，以便使用存储的凭据连接到受其控制的数据库，从而确定凭据的密码。

#### <a name="target-group"></a>目标组

目标组定义可以在其上执行作业步骤的数据库集。 目标组可以包含任意数目和任意组合的以下项：

- **SQL Server** - 如果指定一个服务器，则在执行作业时存在于该服务器中的所有数据库都会成为组的一部分。 必须提供 master 数据库凭据，然后才能在执行作业之前枚举和更新组。 有关逻辑服务器的详细信息，请参阅 [Azure SQL 数据库和 Azure Synapse Analytics 中的服务器是什么？](logical-servers.md)。
- **弹性池** - 如果指定一个弹性池，则在执行作业时位于该弹性池中的所有数据库都会成为组的一部分。 就服务器来说，必须提供 master 数据库凭据，然后才能在执行作业之前更新组。
- **单个数据库** - 指定一个或多个将要成为组的一部分的单独数据库。
- **分片映射** - 分片映射的数据库。

> [!TIP]
> 在执行作业时，动态枚举会重新评估包含服务器或池的目标组中的数据库集。 动态枚举确保 **在执行作业时，作业可以跨服务器或池中存在的所有数据库运行**。 在池或服务器成员身份更改频繁的情况下，在运行时重新评估数据库列表特别有用。

可以将池和单个数据库指定为包括在组中或从组中排除。 这样就可以使用任意数据库组合来创建目标组。 例如，可以向目标组添加一个服务器，但将弹性池中的特定数据库排除出去（也可以排除整个池）。

目标组可以包括多个区域的多个订阅中的数据库。 请注意，跨区域执行的延迟高于同一区域内的执行。

以下示例演示了如何在执行作业时动态枚举不同的目标组定义，以便确定作业要运行哪些数据库：

![目标组示例](./media/job-automation-overview/targetgroup-examples1.png)

**示例 1** 演示的目标组包含一个由各个数据库组成的列表。 使用此目标组执行某个作业步骤时，作业步骤的操作会在这其中的每个数据库中执行。<br>
**示例 2** 演示的目标组包含一个充当目标的服务器。 使用此目标组执行某个作业步骤时，会动态枚举服务器，以便确定目前在服务器中的数据库的列表。 作业步骤的操作会在这其中的每个数据库中执行。<br>
**示例 3** 演示的目标组与 *示例 2* 的类似，但明确排除了单个数据库。 作业步骤的操作不会在排除的数据库中执行。<br>
**示例 4** 演示的目标组包含一个充当目标的弹性池。 与 *示例 2* 类似，此池会在作业运行时动态枚举，以便确定池中数据库的列表。
<br><br>

![更多目标组示例](./media/job-automation-overview/targetgroup-examples2.png)

**示例 5** 和 **示例 6** 演示高级方案，其中的服务器、弹性池和数据库可以使用包括和排除规则进行组合。<br>
**示例 7** 表明分片映射中的分片也可在作业运行时进行评估。

> [!NOTE]
> 作业数据库本身可以是作业的目标。 在这种情况下，会像处理任何其他目标数据库一样处理作业数据库。 必须在作业数据库中创建作业用户并为其授予足够权限，并且该作业用户的数据库范围的凭据也必须存在于作业数据库中，就像任何其他目标数据库的情况一样。

#### <a name="elastic-jobs-and-job-steps"></a>弹性作业和作业步骤

作业是按计划执行的或只执行一次的工作单元。 作业包含一个或多个作业步骤。

每个作业步骤都会指定一个要执行的 T-SQL 脚本、一个或多个要对其运行 T-SQL 脚本的目标组，以及作业代理连接到目标数据库所需的凭据。 每个作业步骤都有可自定义的超时和重试策略，并且可以选择性地指定输出参数。

#### <a name="job-output"></a>作业输出

针对每个目标数据库执行的作业步骤的结果会详细记录，而脚本输出则可捕获到指定的表中。 可以指定一个数据库，用于保存从作业返回的任何数据。

#### <a name="job-history"></a>作业历史记录

通过[查询表 jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)，在作业数据库中查看弹性作业执行历史记录。 系统清除作业会清除时间超过 45 天的执行历史记录。 若要删除时间不到 45 天的历史记录，请调用作业数据库中的 **sp_purge_history** 存储过程。

#### <a name="job-status"></a>作业状态

可以通过[查询表 jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status)，在作业数据库中监视弹性作业执行情况。 

### <a name="agent-performance-capacity-and-limitations"></a>代理性能、容量和限制

弹性作业在等待长时间运行的作业完成时使用极少的计算资源。

根据目标数据库组的大小和作业所需执行时间（并发辅助角色数）的不同，代理需要的计算量和作业数据库性能也会有所不同（目标数和作业数越多，所需计算量越大）。

当前，此限额为 100 个并发作业。

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>防止作业降低目标数据库性能

若要确保针对 SQL 弹性池中的数据库运行作业时资源不会超负荷，可以对作业进行配置，限制可以在同一时间对其运行作业的数据库数。

## <a name="next-steps"></a>后续步骤

- [如何创建和管理弹性作业](elastic-jobs-overview.md)
- [使用 PowerShell 创建和管理弹性作业](elastic-jobs-powershell-create.md)
- [使用 Transact-SQL (T-SQL) 创建和管理弹性作业](elastic-jobs-tsql-create-manage.md)