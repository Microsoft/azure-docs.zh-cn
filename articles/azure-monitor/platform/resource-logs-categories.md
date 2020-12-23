---
title: Azure Monitor 资源日志支持的服务和类别
description: Azure Monitor 参考：了解 Azure 资源日志支持的服务和事件架构。
ms.subservice: logs
ms.topic: reference
ms.date: 12/09/2020
ms.openlocfilehash: c7b2d48b40843930bba78f54d2294769d952daf6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931222"
---
# <a name="supported-categories-for-azure-resource-logs"></a>Azure 资源日志支持的类别

> [!NOTE]
> 资源日志以前称为诊断日志。 此名称在 2019 年 10 月发生了更改，因为 Azure Monitor 收集的日志类型已转变，不仅仅包括 Azure 资源。

[Azure Monitor 资源日志](./platform-logs-overview.md)是 Azure 服务发出的日志，用于描述这些服务或资源的操作。 通过 Azure Monitor 提供的所有资源日志共享公共顶级架构，且每个服务都能灵活地为其事件发出唯一属性。

资源类型（为 `resourceId` 属性时可用）和 `category` 的组合唯一标识架构。 所有资源日志都有一个通用架构，其中包含特定于服务的字段，这些字段随后会针对不同的日志类别进行添加。 有关详细信息，请参阅 [Azure 资源日志的通用架构和特定于服务的架构]()


## <a name="costs"></a>成本

将任何数据发送到 Log Analytics、Azure 存储和/或事件中心时，会产生相关的费用。 你可能需要支付费用，以便将数据导入到这些位置，并将其保留在那里。  资源日志是可以发送到这些位置的一种数据类型。 将 [某些类别的资源日志导出](https://azure.microsoft.com/pricing/details/monitor/) 到这些位置需要额外的费用，而其他人则不收取出口费用。 下表列出了导出成本细节。

## <a name="supported-log-categories-per-resource-type"></a>每种资源类型支持的日志类别

下面列出了可用于每种资源类型的日志类型。 

某些类别可能只适用于特定类型的资源。 如果你觉得缺少资源，请参阅特定于资源的文档。 例如，Microsoft.Sql/servers/databases 类别并非适用于所有类型的数据库。 有关详细信息，请参阅[有关 SQL 数据库诊断日志记录的信息](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)。 

如果仍觉得缺少某些内容，可以在本文底部打开 GitHub 评论。
## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

成本：免费 

|Category |类别显示名称|
|---|---|
|引擎|引擎|
|服务|服务|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

成本：免费 

|Category |类别显示名称|
|---|---|
|GatewayLogs|ApiManagement 网关的相关日志|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

成本：免费 

|Category |类别显示名称|
|---|---|
|ApplicationConsole|应用程序控制台|
|SystemLogs|系统日志|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

成本：免费 

|Category |类别显示名称|
|---|---|
|DscNodeStatus|Dsc 节点状态|
|JobLogs|作业日志|
|JobStreams|作业流|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

成本：免费 

|Category |类别显示名称|
|---|---|
|ServiceLog|服务日志|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

成本：免费 

|Category |类别显示名称|
|---|---|
|BaiClusterEvent|BaiClusterEvent|
|BaiClusterNodeEvent|BaiClusterNodeEvent|
|BaiJobEvent|BaiJobEvent|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

成本：免费 

|Category |类别显示名称|
|---|---|
|BlockchainApplication|区块链应用程序|
|FabricOrderer|Fabric Orderer|
|FabricPeer|构造对等|
|代理|代理|


## <a name="microsoftblockchaincordamembers"></a>Microsoft.Blockchain/cordaMembers

成本：免费 

|Category |类别显示名称|
|---|---|
|BlockchainApplication|区块链应用程序|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

成本：免费 

|Category |类别显示名称|
|---|---|
|WebApplicationFirewallLogs|Web 应用程序防火墙日志|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

成本：免费 

|Category |类别显示名称|
|---|---|
|AzureCdnAccessLog|Azure CDN 访问日志|


## <a name="microsoftcdnprofilesendpoints"></a>Microsoft.Cdn/profiles/endpoints

成本：免费 

|Category |类别显示名称|
|---|---|
|CoreAnalytics|获取终结点的指标，例如带宽、流出量等。|


## <a name="microsoftclassicnetworknetworksecuritygroups"></a>Microsoft.ClassicNetwork/networksecuritygroups

成本：免费 

|Category |类别显示名称|
|---|---|
|网络安全组规则流事件|网络安全组规则流事件|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

成本：免费 

|Category |类别显示名称|
|---|---|
|审核|审核日志|
|RequestResponse|请求和响应日志|
|跟踪|跟踪日志|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

成本：免费 

|Category |类别显示名称|
|---|---|
|ContainerRegistryLoginEvents|登录事件|
|ContainerRegistryRepositoryEvents|RepositoryEvent 日志|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

成本：免费 

|Category |类别显示名称|
|---|---|
|cluster-autoscaler|Kubernetes 群集自动缩放程序|
|kube-apiserver|Kubernetes API 服务器|
|kube-audit|Kubernetes 审核|
|kube-controller-manager|Kubernetes 控制器管理器|
|kube-scheduler|Kubernetes 计划程序|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

成本：免费 

|Category |类别显示名称|
|---|---|
|AuditLogs|MiniRP 调用的审核日志|


## <a name="microsoftdatabricksworkspaces"></a>Microsoft.Databricks/workspaces

成本：免费 

|Category |类别显示名称|
|---|---|
|accounts|Databricks 帐户|
|clusters|Databricks 群集|
|dbfs|Databricks 文件系统|
|instancePools|实例池|
|jobs|Databricks 作业|
|笔记本|Databricks Notebook|
|secrets|Databricks 机密|
|sqlPermissions|Databricks SQLPermissions|
|ssh|Databricks SSH|
|工作区|Databricks 工作区|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

成本：免费 

|Category |类别显示名称|
|---|---|
|ActivityRuns|管道活动运行日志|
|PipelineRuns|管道运行日志|
|TriggerRuns|触发器运行日志|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

成本：免费 

|Category |类别显示名称|
|---|---|
|审核|审核日志|
|请求|请求日志|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

成本：免费 

|Category |类别显示名称|
|---|---|
|ReceivedShareSnapshots|已收到共享快照|
|SentShareSnapshots|已发送共享快照|
|共享|共享|
|ShareSubscriptions|共享订阅|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

成本：免费 

|Category |类别显示名称|
|---|---|
|MySqlAuditLogs|MariaDB 审核日志|
|MySqlSlowLogs|MariaDB 服务器日志|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

成本：免费 

|Category |类别显示名称|
|---|---|
|MySqlAuditLogs|MySQL 审核日志|
|MySqlSlowLogs|MySQL 慢速日志|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

成本：免费 

|Category |类别显示名称|
|---|---|
|MySqlAuditLogs|MySQL 审核日志|
|MySqlSlowLogs|MySQL 服务器日志|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

成本：免费 

|Category |类别显示名称|
|---|---|
|PostgreSQLLogs|PostgreSQL 服务器日志|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

成本：免费 

|Category |类别显示名称|
|---|---|
|PostgreSQLLogs|PostgreSQL 服务器日志|
|QueryStoreRuntimeStatistics|PostgreSQL 查询存储运行时统计信息|
|QueryStoreWaitStatistics|PostgreSQL 查询存储等待统计信息|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

成本：免费 

|Category |类别显示名称|
|---|---|
|PostgreSQLLogs|PostgreSQL 服务器日志|


## <a name="microsoftdesktopvirtualizationapplicationgroups"></a>Microsoft.DesktopVirtualization/applicationgroups

成本：免费 

|Category |类别显示名称|
|---|---|
|检查点|检查点|
|错误|错误|
|管理|管理|


## <a name="microsoftdesktopvirtualizationhostpools"></a>Microsoft.DesktopVirtualization/hostpools

成本：免费 

|Category |类别显示名称|
|---|---|
|检查点|检查点|
|连接|连接|
|错误|错误|
|HostRegistration|HostRegistration|
|管理|管理|


## <a name="microsoftdesktopvirtualizationworkspaces"></a>Microsoft.DesktopVirtualization/workspaces

成本：免费 

|Category |类别显示名称|
|---|---|
|检查点|检查点|
|错误|错误|
|源|源|
|管理|管理|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

成本：免费 

|Category |类别显示名称|
|---|---|
|C2DCommands|C2D 命令|
|C2DTwinOperations|C2D 孪生操作|
|配置|配置|
|连接|连接|
|D2C 孪生操作|D2C 孪生操作|
|DeviceIdentityOperations|设备标识操作|
|DeviceStreams|设备流（预览版）|
|DeviceTelemetry|设备遥测|
|DirectMethods|直接方法|
|DistributedTracing|分布式跟踪（预览版）|
|FileUploadOperations|文件上传操作|
|JobsOperations|作业操作|
|路由|路由|
|TwinQueries|孪生查询|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

成本：免费 

|Category |类别显示名称|
|---|---|
|DeviceOperations|设备操作|
|ServiceOperations|服务操作|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

成本：免费 

|Category |类别显示名称|
|---|---|
|CassandraRequests|CassandraRequests|
|ControlPlaneRequests|ControlPlaneRequests|
|DataPlaneRequests|DataPlaneRequests|
|GremlinRequests|GremlinRequests|
|MongoRequests|MongoRequests|
|PartitionKeyRUConsumption|PartitionKeyRUConsumption|
|PartitionKeyStatistics|PartitionKeyStatistics|
|QueryRuntimeStatistics|QueryRuntimeStatistics|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

成本：免费 

|Category |类别显示名称|
|---|---|
|DeliveryFailures|传递失败日志|
|PublishFailures|发布失败日志|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

成本：免费 

|Category |类别显示名称|
|---|---|
|DeliveryFailures|传递失败日志|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

成本：免费 

|Category |类别显示名称|
|---|---|
|DeliveryFailures|传递失败日志|
|PublishFailures|发布失败日志|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

成本：免费 

|Category |类别显示名称|
|---|---|
|ArchiveLogs|存档日志|
|AutoScaleLogs|自动缩放日志|
|CustomerManagedKeyUserLogs|客户管理的密钥日志|
|EventHubVNetConnectionEvent|VNet/IP 筛选连接日志|
|KafkaCoordinatorLogs|Kafka 协调器日志|
|KafkaUserErrorLogs|Kafka 用户错误日志|
|OperationalLogs|操作日志|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

成本：免费 

|Category |类别显示名称|
|---|---|
|AuditLogs|审核日志|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

成本：免费 

|Category |类别显示名称|
|---|---|
|AutoscaleEvaluations|自动缩放评估|
|AutoscaleScaleActions|自动缩放缩放操作|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

成本：免费 

|Category |类别显示名称|
|---|---|
|AppAvailabilityResults|可用性结果|
|AppBrowserTimings|浏览器计时|
|AppDependencies|依赖项|
|AppEvents|事件|
|AppExceptions|异常|
|AppMetrics|指标|
|AppPageViews|页面视图|
|AppPerformanceCounters|性能计数器|
|AppRequests|请求|
|AppSystemEvents|系统事件|
|AppTraces|跟踪|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

成本：免费 

|Category |类别显示名称|
|---|---|
|AuditEvent|审核日志|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

成本：免费 

|Category |类别显示名称|
|---|---|
|命令|命令|
|FailedIngestion|引入操作失败|
|IngestionBatching|引入批处理|
|查询|查询|
|SucceededIngestion|成功引入操作|
|TableDetails|表详细信息|
|TableUsageStatistics|表使用情况统计信息|


## <a name="microsoftlogicintegrationaccounts"></a>Microsoft.Logic/integrationAccounts

成本：免费 

|Category |类别显示名称|
|---|---|
|IntegrationAccountTrackingEvents|集成帐户跟踪事件|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

成本：免费 

|Category |类别显示名称|
|---|---|
|WorkflowRuntime|工作流运行时诊断事件|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

成本：免费 

|Category |类别显示名称|
|---|---|
|AmlComputeClusterEvent|AmlComputeClusterEvent|
|AmlComputeClusterNodeEvent|AmlComputeClusterNodeEvent|
|AmlComputeCpuGpuUtilization|AmlComputeCpuGpuUtilization|
|AmlComputeJobEvent|AmlComputeJobEvent|
|AmlRunStatusChangedEvent|AmlRunStatusChangedEvent|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

成本：免费 

|Category |类别显示名称|
|---|---|
|KeyDeliveryRequests|密钥传递请求|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

成本：免费 

|Category |类别显示名称|
|---|---|
|ApplicationGatewayAccessLog|应用程序网关访问日志|
|ApplicationGatewayFirewallLog|应用程序网关防火墙日志|
|ApplicationGatewayPerformanceLog|应用程序网关性能日志|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

成本：免费 

|Category |类别显示名称|
|---|---|
|AzureFirewallApplicationRule|Azure 防火墙应用程序规则|
|AzureFirewallNetworkRule|Azure 防火墙网络规则|


## <a name="microsoftnetworkbastionhosts"></a>Microsoft.Network/bastionHosts

成本：免费 

|Category |类别显示名称|
|---|---|
|BastionAuditLogs|堡垒审核日志|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

成本：免费 

|Category |类别显示名称|
|---|---|
|PeeringRouteLog|对等互连路由表日志|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

成本：免费 

|Category |类别显示名称|
|---|---|
|FrontdoorAccessLog|Frontdoor 访问日志|
|FrontdoorWebApplicationFirewallLog|Frontdoor Web 应用程序防火墙日志|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

成本：免费 

|Category |类别显示名称|
|---|---|
|LoadBalancerAlertEvent|负载均衡器警报事件|
|LoadBalancerProbeHealthStatus|负载均衡器探测运行状况|


## <a name="microsoftnetworknetworksecuritygroups"></a>Microsoft.Network/networksecuritygroups

成本：免费 

|Category |类别显示名称|
|---|---|
|NetworkSecurityGroupEvent|网络安全组事件|
|NetworkSecurityGroupFlowEvent|网络安全组规则流事件|
|NetworkSecurityGroupRuleCounter|网络安全组规则计数器|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

成本：免费 

|Category |类别显示名称|
|---|---|
|DDoSMitigationFlowLogs|DDoS 缓解决策的流日志|
|DDoSMitigationReports|DDoS 缓解措施报告|
|DDoSProtectionNotifications|DDoS 保护通知|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

成本：免费 

|Category |类别显示名称|
|---|---|
|ProbeHealthStatusEvents|流量管理器探测运行状况结果事件|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

成本：免费 

|Category |类别显示名称|
|---|---|
|GatewayDiagnosticLog|网关诊断日志|
|IKEDiagnosticLog|IKE 诊断日志|
|P2SDiagnosticLog|P2S 诊断日志|
|RouteDiagnosticLog|路由诊断日志|
|TunnelDiagnosticLog|隧道诊断日志|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

成本：免费 

|Category |类别显示名称|
|---|---|
|VMProtectionAlerts|VM 保护警报|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

成本：免费 

|Category |类别显示名称|
|---|---|
|引擎|引擎|


## <a name="microsoftrecoveryservicesvaults"></a>Microsoft.RecoveryServices/Vaults

成本：免费 

|Category |类别显示名称|
|---|---|
|AddonAzureBackupAlerts|附加 Azure 备份警报数据|
|AddonAzureBackupJobs|附加 Azure 备份作业数据|
|AddonAzureBackupPolicy|附加 Azure 备份策略数据|
|AddonAzureBackupProtectedInstance|附加 Azure 备份受保护实例数据|
|AddonAzureBackupStorage|附加 Azure 备份存储数据|
|AzureBackupReport|Azure 备份报告数据|
|AzureSiteRecoveryEvents|Azure Site Recovery 事件|
|AzureSiteRecoveryJobs|Azure Site Recovery 作业|
|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 受保护的磁盘数据改动|
|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 恢复点|
|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 复制项|
|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 复制数据上传速度|
|AzureSiteRecoveryReplicationStats|Azure Site Recovery 复制统计信息|
|CoreAzureBackup|核心 Azure 备份数据|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

成本：免费 

|Category |类别显示名称|
|---|---|
|HybridConnectionsEvent|HybridConnections 事件|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

成本：免费 

|Category |类别显示名称|
|---|---|
|OperationLogs|操作日志|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

成本：免费 

|Category |类别显示名称|
|---|---|
|OperationalLogs|操作日志|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

成本：免费 

|Category |类别显示名称|
|---|---|
|AllLogs|Azure SignalR 服务日志。|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

成本：免费 

|Category |类别显示名称|
|---|---|
|DevOpsOperationsAudit|Devops 操作审核日志|
|ResourceUsageStats|资源使用情况统计信息|
|SQLSecurityAuditEvents|SQL 安全审核事件|


## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.Sql/managedInstances/databases

成本：免费 

|Category |类别显示名称|
|---|---|
|错误|错误|
|QueryStoreRuntimeStatistics|查询存储运行时统计信息|
|QueryStoreWaitStatistics|查询存储等待统计信息|
|SQLInsights|SQL Insights|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

成本：免费 

|Category |类别显示名称|
|---|---|
|AutomaticTuning|自动优化|
|块|块|
|DatabaseWaitStatistics|数据库等待统计信息|
|死锁数|死锁数|
|DevOpsOperationsAudit|Devops 操作审核日志|
|DmsWorkers|Dms 辅助角色|
|错误|错误|
|ExecRequests|Exec 请求|
|QueryStoreRuntimeStatistics|查询存储运行时统计信息|
|QueryStoreWaitStatistics|查询存储等待统计信息|
|RequestSteps|请求步骤|
|SQLInsights|SQL Insights|
|SqlRequests|Sql 请求|
|SQLSecurityAuditEvents|SQL 安全审核事件|
|超时|超时|
|等待|等待|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

成本： [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)的 "平台日志" 部分中所述付费。 

|Category |类别显示名称|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

成本： [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)的 "平台日志" 部分中所述付费。 

|Category |类别显示名称|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

成本： [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)的 "平台日志" 部分中所述付费。 
 
|Category |类别显示名称|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

成本： [Azure Monitor 定价页](https://azure.microsoft.com/pricing/details/monitor/)的 "平台日志" 部分中所述付费。 
 
|Category |类别显示名称|
|---|---|
|StorageDelete|StorageDelete|
|StorageRead|StorageRead|
|StorageWrite|StorageWrite|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

成本：免费 

|Category |类别显示名称|
|---|---|
|创作|创作|
|执行|执行|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

成本：免费 

|Category |类别显示名称|
|---|---|
|BuiltinSqlReqsEnded|内置 Sql 池请求已结束|
|GatewayApiRequests|Synapse 网关 Api 请求|
|SQLSecurityAuditEvents|SQL 安全审核事件|
|SynapseRbacOperations|Synapse RBAC 操作|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

成本：免费 

|Category |类别显示名称|
|---|---|
|BigDataPoolAppsEnded|大数据池应用程序已结束|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

成本：免费 

|Category |类别显示名称|
|---|---|
|DmsWorkers|Dms 辅助角色|
|ExecRequests|Exec 请求|
|RequestSteps|请求步骤|
|SqlRequests|Sql 请求|
|SQLSecurityAuditEvents|Sql 安全审核事件|
|等待|等待|


## <a name="microsoftwebhostingenvironments"></a>microsoft.web/hostingenvironments

成本：免费 

|Category |类别显示名称|
|---|---|
|AppServiceEnvironmentPlatformLogs|应用服务环境平台日志|


## <a name="microsoftwebsites"></a>microsoft.web/sites

成本：免费 


|Category |类别显示名称|
|---|---|
|AppServiceAppLogs|应用服务应用程序日志|
|AppServiceAuditLogs|访问审核日志|
|AppServiceConsoleLogs|应用服务控制台日志|
|AppServiceFileAuditLogs|站点内容更改审核日志|
|AppServiceHTTPLogs|HTTP 日志|
|FunctionAppLogs|函数应用程序日志|


## <a name="microsoftwebsitesslots"></a>microsoft.web/sites/slots

成本：免费 


|Category |类别显示名称|
|---|---|
|AppServiceAppLogs|应用服务应用程序日志|
|AppServiceAuditLogs|访问审核日志|
|AppServiceConsoleLogs|应用服务控制台日志|
|AppServiceFileAuditLogs|站点内容更改审核日志|
|AppServiceHTTPLogs|HTTP 日志|
|FunctionAppLogs|函数应用程序日志|


## <a name="next-steps"></a>后续步骤

* [详细了解资源日志](./platform-logs-overview.md)
* [将资源日志流式传输到 **事件中心**](./resource-logs.md#send-to-azure-event-hubs)
* [使用 Azure Monitor REST API 更改资源日志诊断设置](/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析 Azure 存储中的日志](./resource-logs.md#send-to-log-analytics-workspace)

