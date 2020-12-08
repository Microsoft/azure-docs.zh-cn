---
title: Azure Service Fabric 宿主激活和停用生命周期
description: 介绍了节点上的应用程序和 ServicePackage 的生命周期
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779594"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric 宿主生命周期

本文概述了在节点上激活应用程序时 Azure Service Fabric 中发生的事件，以及用于控制该行为的各种群集配置。

在继续阅读之前，请确保了解[在 Service Fabric 中为应用程序建模][a1]中所述的各种概念及其相互关系。 

> [!NOTE]
> 在本文中，除非明确说明，否则：
>
> - “副本”指的是有状态服务的副本和无状态服务的实例。
> - CodePackage 被视为与注册 ServiceType 并承载该 ServiceType 的服务副本的 ServiceHost 进程相同。
>

## <a name="activation-of-applicationservicepackage"></a>激活 Application/ServicePackage

用于激活的管道如下所示：

1. 下载 ApplicationPackage。 例如：ApplicationManifest.xml 等。
2. 为 Application 设置环境（例如：创建用户等）。
3. 开始跟踪 Application 的停用情况。
4. 下载 ServicePackage。 例如：ServiceManifest.xml、代码、配置、数据包等。
5. 设置用于 ServicePackage 的环境，例如：设置防火墙、为终结点分配端口，等等。
6. 开始跟踪 ServicePackage 的停用情况。
7. 启动 Codepackage 的 SetupEntryPoint 并等待其完成。
8. 启动 Codepackage 的 MainEntryPoint。

### <a name="servicetype-blocklisting"></a>将 ServiceType 加入阻止列表
**ServiceTypeDisableFailureThreshold** 决定了在失败（激活、下载、CodePackage 失败）多少次后会安排将 ServiceType 加入阻止列表。 第一次激活失败、下载失败或 CodePackage 崩溃将计划 ServiceType 列入阻止列表。 **ServiceTypeDisableGraceInterval** config 确定了在该节点上将 ServiceType 标记为列入阻止列表之后的宽限时间间隔。 尽管这一切发生，但会并行重试激活、下载和 CodePackage 重启。 重试是指例如，在崩溃或 Service Fabric 将再次尝试下载包后，CodePackage 将重新启动。

当 ServiceType 为列入阻止列表时，你将看到一个运行状况错误 "托管" 报告属性 "ServiceTypeRegistration： ServiceType" 的错误。 ServiceType 已在节点上禁用。”

如果发生以下任何情况，则会在节点上再次启用 ServiceType：
- 激活成功或在失败时达到 **activationmaxfailurecount 次** 的重试次数。
- 下载成功或在失败时达到 **DeploymentMaxFailureCount** 的重试次数。
- 已崩溃的 CodePackage 启动并成功注册 ServiceType。

**Activationmaxfailurecount 次** 和 **DeploymentMaxFailureCount** 是 Service Fabric 将用于激活/下载节点上的应用程序的最大尝试次数，之后 Service Fabric 会使 ServiceType 再次激活。 这是为了为服务提供另一个激活机会，这可能会成功，导致问题自动修复。 通过放置和激活副本触发的新激活/下载操作可以再次触发 ServiceType 列入阻止列表，也可能会成功。

> [!NOTE]
> 如果未注册 ServiceType 的 CodePackage 崩溃，则不会影响 ServiceType。 只有承载副本的 CodePackage 崩溃，才会影响 ServiceType。
>

### <a name="codepackage-crash"></a>CodePackage 崩溃
当 CodePackage 崩溃时，Service Fabric 使用回退来重新启动它。 回退与代码包是否已向 Service Fabric 运行时注册了类型无关。

回退值为 (RetryTime， **ActivationMaxRetryInterval**) 的最小值，并且根据 **ActivationRetryBackoffExponentiationBase** 配置设置，此回退值在常量、线性或指数量中递增。

- 常数：如果 **ActivationRetryBackoffExponentiationBase** == 0，则 RetryTime = **ActivationRetryBackoffInterval**；
- 线性值：如果 **ActivationRetryBackoffExponentiationBase** == 0，则 RetryTime = ContinuousFailureCount* **ActivationRetryBackoffInterval**，其中的 ContinousFailureCount 是 CodePackage 崩溃或激活失败的次数。
- 指数：RetryTime = (**ActivationRetryBackoffInterval**，以秒为单位) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount)；
    
可以通过更改值来控制行为。 例如，如果需要多次快速重新启动尝试，可以通过将 **ActivationRetryBackoffExponentiationBase** 设置为0并将 **ActivationRetryBackoffInterval** 设置为10来使用线性。 使用这些设置时，如果 CodePackage 崩溃，开始间隔将在10秒后。 如果包持续出现故障，则回退会更改为、20、30、40秒，依此类推，直到 CodePackage 激活成功或代码包停用。 
    
**ActivationMaxRetryInterval** 控制故障后 (等待) Service Fabric 等待的最长时间。
    
如果 CodePackage 崩溃并重新启动，则需要保持 **CodePackageContinuousExitFailureResetInterval** 的 Service Fabric 将其视为正常，此时，会将之前的错误运行状况报告覆盖为正常，并重置 ContinousFailureCount。

### <a name="codepackage-not-registering-servicetype"></a>CodePackage 未注册 ServiceType
如果 CodePackage 保持活动状态，并且预计会向我们注册一个 ServiceType，但并不注册，Service Fabric 将在预期的时间内未注册 **servicetype 后，** 将生成一个警告 HealthReport。

### <a name="activation-failure"></a>激活失败
如果在激活过程中发现错误，Service Fabric 始终会使用线性退避（与 CodePackage 崩溃相同）。 这意味着激活操作将在 (0 + 10 + 20 + 30 + 40) = 100 秒后放弃， (第一次重试是立即) 。 此后不重试激活。
    
最大激活退避可以是 **ActivationMaxRetryInterval** 秒，可以重试 **ActivationMaxFailureCount** 次。

### <a name="download-failure"></a>下载失败
如果在下载过程中遇到错误，Service Fabric 始终会使用线性退避。 这意味着在 (0+ 10 + 20 + 30 + 40) = 100 秒后会放弃激活操作（首次重试会立即进行）。 此后，不会重试下载。 用于下载的线性回退等同于 ContinuousFailureCount **_DeploymentRetryBackoffInterval_* ，可以最大为 **DeploymentMaxRetryInterval**。 与激活一样，下载操作可以重试 **ActivationMaxFailureCount** 次。

> [!NOTE]
> 更改这些设置之前，请注意下面的几个示例。

* 如果 CodePackage 保持崩溃并退出，则将禁用 ServiceType。 但如果激活配置使它能够快速重新启动，则 CodePackage 可能会在 ServiceType 实际列入阻止列表之前出现几次。 例如：假定 CodePackage 启动后将 ServiceType 注册到 Service Fabric，然后崩溃。 在这种情况下，一旦宿主收到类型注册，就会取消 **ServiceTypeDisableGraceInterval** 期限。 这可能会重复，直到 CodePackage 的值达到大于  **ServiceTypeDisableGraceInterval** 的值，然后才会在节点上列入阻止列表 ServiceType。 它 maytake 的时间比你希望看到 ServiceType 列入阻止列表更长。

* 在激活的情况下，当 Service Fabric 系统需要在节点上放置一个副本时，RA (ReconfigurationAgent) 会要求承载子系统激活应用程序，并每15秒重试一次激活请求， (由 **RAPMessageRetryInterval** 配置设置) 控制。 为了 Service Fabric 了解 ServiceType 是否已列入阻止列表，托管的激活操作需要的时间比重试间隔和 **ServiceTypeDisableGraceInterval** 长。 例如：假设群集的 **activationmaxfailurecount 次** 设置为5， **ActivationRetryBackoffInterval** 设置为1秒。 这意味着激活操作将在 (0 + 1 + 2 + 3 + 4) = 10 秒后放弃， (记得第一次重试是立即) ，并且在该宿主放弃重试之后。 在这种情况下，激活操作就完成了，不会在 15 秒后重试。 发生这种情况是因为 Service Fabric 在15秒内用尽了所有允许的重试次数。 因此，ReconfigurationAgent 的每次重试都将在托管子系统中创建新的激活操作，该模式将保持重复。 因此，ServiceType 永远不会列入阻止列表在节点上。 由于 ServiceType 不会在节点上获得列入阻止列表，因此不会在不同的节点上移动和尝试副本。
> 

## <a name="deactivation"></a>停用

在节点上激活 ServicePackage 时，会跟踪其停用情况。 

停用有两种方式：

- 定期：在每个 **DeactivationScanInterval** 中，它将检查从未托管过副本的 ServicePackages，并将其标记为候选项以供停用。
- ReplicaClose：如果副本已关闭，Deactivator 会获取 DecrementUsageCount。 如果计数为0，则表示 ServicePackage 不托管任何副本，因此是取消激活的候选项。

 基于激活模式 " [独占/共享][a2]"，将在 **DeactivationGraceInterval** For SharedMode 之后和 **ExclusiveModeDeactivationGraceInterval** for ExclusiveMode 之后计划停用的候选项。 如果在这段时间内新的副本放置在中，则取消停用。

### <a name="periodically"></a>定期：
我们来讨论定期停用的一些示例：

示例1：假设 Deactivator 在执行 **扫描)  (** 。 其下次扫描的时间将是 2T。 假设 ServicePackage 激活发生在 T+1 秒的时间。 此 ServicePackage 不承载副本，因此需要将其停用。 ServicePackage 需要处于“无副本”状态至少 T 秒的时间才能成为停用的候选项。 也就是说，它会在 2T+1 秒的时候符合停用条件。 因此，2T 上的扫描将找不到此 ServicePackage 作为停用的候选项。 下一个停用周期 3T 会安排停用此 ServicePackage，因为此时它已处于“无副本”状态 T 秒长的时间。  

示例2：假设 ServicePackage 在时间 T-1 激活，Deactivator 将在 T 处扫描。ServicePackage 不承载副本。 那么，在 2T 秒的时候进行下一次扫描时，此 ServicePackage 会显示为停用候选项，因此系统会安排停用它。  

示例3：假设 ServicePackage 在 T –1处激活，Deactivator 在 T 处进行扫描。ServicePackage 尚未托管副本。 现在会在 T+1 秒的时候放置副本，也就是说， 宿主会获得一个 IncrementUsageCount，这意味着副本已创建。 现在，在 2T 秒的时候，系统不会安排停用此 ServicePackage。 由于它包含副本，因此停用会转到下面所述的 ReplicaClose 逻辑。

示例4：假设你的 ServicePackage 很大，如 10 GB，可能需要一些时间才能在该节点上下载。 激活应用程序后，Deactivator 会跟踪其生命周期。 现在，如果将 **DeactivationScanInterval** 配置设置为一个较小的值，则可能会遇到 ServicePackage 无法在节点上激活的时间，因为所有时间都在下载中。 若要解决此问题，可以 [在节点上预下载 ServicePackage][p1] 或增加 **DeactivationScanInterval**。 

> [!NOTE]
> ServicePackage 可以在 (**DeactivationScanInterval** 到 2 **_DeactivationScanInterval_* ) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval * * 之间的任何位置停用。 
>

### <a name="replica-close"></a>副本关闭：
停用会保留 ServicePackage 的副本计数。 如果 ServicePackage 保存了一个副本，而该副本处于关闭/故障状态，则宿主会获得一个 DecrementUsageCount。 打开副本时，宿主会获得一个 IncrementUsageCount。 Decrement（递减）意味着 ServicePackage 现在承载的副本数减一。当计数降到 0 时，系统就会安排停用 ServicePackage，在 **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** 秒过后停用它。 

对于 ex：假设发生减量，并计划在 2T + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**) 上停用。 如果在这段时间内宿主获得了一个 IncrementUsage，则表明已创建一个副本，这种情况下会取消停用。

> [!NOTE]
> 这些配置设置的含义是什么？
**DeactivationGraceInterval** /**ExclusiveModeDeactivationGraceInterval**：指定给 ServicePackage 的时间，在托管任何副本后再次托管其他副本。 
**DeactivationScanInterval**：为 ServicePackage 留出的最小时间，在此时间内，允许其在未承载任何副本（即从未使用过）的情况下 承载一个副本。
>

### <a name="ctrlc"></a>Ctrl+C
如果 ServicePackage 在经过 **DeactivationGraceInterval**/**ExclusiveModeDeactivationGraceInterval** 秒后仍未承载副本，则停用操作会变得不可取消。 系统会向 CodePackage 发出 Ctrl+C 处理程序，这意味着现在必须通过停用管道来停止进程。 在该时间内，如果尝试放置同一 ServicePackage 的新副本，则操作会失败，因为我们无法从“停用”转变为“激活”。

## <a name="cluster-configs"></a>群集配置

会影响激活/停用的配置及其默认值。

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**：默认值为 1。 失败计数的阈值，超过此值就会通知 FM(FailoverManager) 禁用该节点上的此服务类型并尝试在另一个节点上放置对象。
**ServiceTypeDisableGraceInterval**：默认值为 30 秒。超过此时间间隔后就会禁用此服务类型。
**ServiceTypeRegistrationTimeout**：默认值为 300 秒。为 ServiceType 注册到 Service Fabric 设置的超时。

### <a name="activation"></a>激活
**ActivationRetryBackoffInterval**：默认值为 10 秒。每次激活失败时的退避间隔。
**ActivationMaxFailureCount**：默认值为 20。 系统在放弃之前重试失败激活的最大次数。 
**ActivationRetryBackoffExponentiationBase**：默认值为 1.5。
**ActivationMaxRetryInterval**：默认值为 3600 秒。失败时用于激活的最大退避值。
**CodePackageContinuousExitFailureResetInterval**：默认值为 300 秒。为 CodePackage 重置连续退出失败计数的超时时间。

### <a name="download"></a>下载
**DeploymentRetryBackoffInterval**：默认值为 10。 部署失败的回退时间间隔。
**DeploymentMaxRetryInterval**：默认值为 3600 秒。失败时用于部署的最大退避值。
**DeploymentMaxFailureCount**：默认值为 20。 重试 DeploymentMaxFailureCount 次应用程序部署后，节点上该应用程序的部署才会失败。

### <a name="deactivation"></a>停用
**DeactivationScanInterval**：默认值为 600 秒。为 ServicePackage 留出的最小时间，在此时间内，允许其在从未承载任何副本（即从未使用过）的情况下 承载一个副本。
**DeactivationGraceInterval**：默认值为 60 秒。在使用 **共享** 进程模型时，为 ServicePackage 留出的时间，在此时间内，允许其在承载任意副本后再重新承载另一个副本。
**ExclusiveModeDeactivationGraceInterval**：默认值为 1 秒。在使用 **独占** 进程模型时，为 ServicePackage 留出的时间，在此时间内，允许其在承载任意副本后再重新承载另一个副本。

## <a name="next-steps"></a>后续步骤
[打包应用程序][a3]并准备好进行部署。

[部署和删除应用程序][a4]。 此文介绍如何使用 PowerShell 来管理应用程序实例。

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
