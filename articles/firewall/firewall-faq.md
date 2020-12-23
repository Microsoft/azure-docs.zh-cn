---
title: Azure 防火墙常见问题解答
description: Azure 防火墙常见问题解答。 是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: victorh
ms.openlocfilehash: 3e6ea6692a81a06bbf3180904dfb465a88b105d1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94653413"
---
# <a name="azure-firewall-faq"></a>Azure 防火墙常见问题解答

## <a name="what-is-azure-firewall"></a>什么是 Azure 防火墙？

Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 可以跨订阅和虚拟网络集中创建、实施和记录应用程序与网络连接策略。

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure 防火墙支持哪些功能？

若要了解 Azure 防火墙的功能，请参阅 [Azure 防火墙功能](features.md)。

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure 防火墙的典型部署模型是什么？

可在任何虚拟网络中部署 Azure 防火墙，但客户往往在中心虚拟网络中部署它，并在中心辐射模型中将其对等互连到其他虚拟网络。 然后，可将对等互连的虚拟网络中的默认路由设置为指向此中心防火墙虚拟网络。 支持全局 VNet 对等互连，但不建议使用，因为它可能会在不同的区域中造成性能影响和延迟问题。 为获得最佳性能，请为每个区域部署一个防火墙。

该模型的优点是能够跨不同订阅集中控制多个辐射 VNET。 由于不需要在每个 VNet 中单独部署防火墙，因此还能节约成本。 应根据客户流量模式衡量成本节约与相关对等互连成本。

## <a name="how-can-i-install-the-azure-firewall"></a>如何安装 Azure 防火墙？

可以使用 Azure 门户、PowerShell、REST API 或使用模板设置 Azure 防火墙。 请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。

## <a name="what-are-some-azure-firewall-concepts"></a>有哪些 Azure 防火墙概念？

Azure 防火墙支持规则和规则集合。 规则集合是一组共享相同顺序和优先级的规则。 规则集合按其优先顺序执行。 网络规则集合的优先级高于应用程序规则集合，所有规则都具有终止性。

有三种类型的规则集合：

* *应用程序规则*：配置可从子网访问的完全限定域名 (FQDN)。
* *网络规则*：配置包含源地址、协议、目标端口和目标地址的规则。
* *NAT 规则*：将 DNAT 规则配置为允许传入的 Internet 连接。

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure 防火墙是否支持入站流量筛选？

Azure 防火墙支持入站和出站筛选。 入站保护通常用于非 HTTP/S 协议。 例如 RDP、SSH 和 FTP 协议。 为了获得最佳的入站 HTTP/S 保护，请使用 Web 应用程序防火墙，如 [Azure Web 应用程序防火墙 (WAF)](../web-application-firewall/overview.md)。

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure 防火墙支持哪些日志记录和分析服务？

Azure 防火墙与 Azure Monitor 集成，可用于查看和分析防火墙日志。 日志可发送到 Log Analytics、Azure 存储或事件中心。 它们可在 Log Analytics 中进行分析，也可通过 Excel 和 Power BI 等不同工具进行分析。 有关详细信息，请参阅[教程：监视 Azure 防火墙日志](./firewall-diagnostics.md)。

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure 防火墙的工作原理与市场中现有的服务（例如 NVA）有何不同？

Azure 防火墙是一项基础防火墙服务，可解决特定客户方案的问题。 应将第三方 NVA 与 Azure 防火墙混合使用。 更好地合作是核心任务。

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>应用程序网关 WAF 与 Azure 防火墙之间有何区别？

Web 应用程序防火墙 (WAF) 是应用程序网关的一项功能，可在出现常见攻击和漏洞时为 Web 应用程序提供集中的入站保护。 Azure 防火墙防火墙为非 HTTP/S 协议（例如，RDP、SSH、FTP）提供入站保护、为所有端口和协议提供出站网络级别的保护，并为出站 HTTP/S 提供应用程序级别的保护。

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>网络安全组 (NSG) 和 Azure 防火墙之间有何区别？

Azure 防火墙服务为网络安全组功能提供了补充。 两者共同提供了更好的“深层防御”网络安全性。 网络安全组提供分布式网络层流量过滤，以限制每个订阅中虚拟网络内资源的流量。 Azure 防火墙是一个服务形式的完全有状态的集中式网络防火墙，可跨不同的订阅和虚拟网络提供网络和应用程序级别的保护。

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>AzureFirewallSubnet 是否支持网络安全组 (NSG)？

Azure 防火墙是具有多个保护层的托管服务，这些层包括使用 NIC 级 NSG（不可查看）进行的平台保护。  不需要在 AzureFirewallSubnet 中配置子网级 NSG，为确保服务不会中断，将禁用此类 NSG。

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>如何使用服务终结点设置 Azure 防火墙？

若要安全访问 PaaS 服务，我们建议使用服务终结点。 可以选择在 Azure 防火墙子网中启用服务终结点，并在连接的分支虚拟网络中禁用它们。 这样可以受益于以下两个功能：服务终结点安全性和针对所有流量的集中日志记录。

## <a name="what-is-the-pricing-for-azure-firewall"></a>Azure 防火墙采用何种定价方式？

请参阅 [Azure 防火墙定价](https://azure.microsoft.com/pricing/details/azure-firewall/)。

## <a name="how-can-i-stop-and-start-azure-firewall"></a>如何停止和启动 Azure 防火墙？

可以使用 Azure PowerShell 的 *deallocate* 和 *allocate* 方法。

例如：

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip1 = Get-AzPublicIpAddress -Name "Public IP1 Name" -ResourceGroupName "RG Name"
$publicip2 = Get-AzPublicIpAddress -Name "Public IP2 Name" -ResourceGroupName "RG Name"
$azfw.Allocate($vnet,@($publicip1,$publicip2))

Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> 必须将防火墙和公共 IP 重新分配到原始资源组和订阅。

## <a name="what-are-the-known-service-limits"></a>有哪些已知的服务限制？

有关 Azure 防火墙服务限制，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)。

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>中心虚拟网络中的 Azure 防火墙能否转发并筛选两个分支虚拟网络之间的网络流量？

能，可以在中心虚拟网络中使用 Azure 防火墙来路由和筛选两个分支虚拟网络之间的流量。 每个分支虚拟网络中的子网必须具有指向 Azure 防火墙的 UDR，作为此方案生效所需的默认网关。

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure 防火墙能否转发和筛选同一虚拟网络或对等互连虚拟网络中子网之间的网络流量？

是的。 但是，将 UDR 配置为在同一 VNET 中的子网之间重定向流量时需要额外注意。 虽然使用 VNET 地址范围作为 UDR 的目标前缀就足够了，但这也会通过 Azure 防火墙实例将所有流量从一台计算机路由到同一子网中的另一台计算机。 为避免这种情况，请在 UDR 中包含下一跃点类型为 **VNET** 的子网路由。 管理这些路由可能很麻烦并且容易出错。 建议的内部网络分段方法是使用不需要 UDR 的网络安全组。

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>是否在专用网络之间进行 Azure 防火墙出站 SNAT？

如果目标 IP 地址是符合 [IANA RFC 1918](https://tools.ietf.org/html/rfc1918) 的专用 IP 范围，Azure 防火墙不会执行 SNAT。 如果组织对专用网络使用公共 IP 地址范围，Azure 防火墙会通过 SNAT 将流量发送到 AzureFirewallSubnet 中的某个防火墙专用 IP 地址。 可以将 Azure 防火墙配置为 **不** SNAT 公共 IP 地址范围。 有关详细信息，请参阅 [Azure 防火墙 SNAT 专用 IP 地址范围](snat-private-range.md)。

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>是否支持强制隧道/链接到网络虚拟设备？

创建新的防火墙时，支持强制隧道。 不能为强制隧道配置现有的防火墙。 有关详细信息，请参阅 [Azure 防火墙强制隧道](forced-tunneling.md)。

Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接 。

如果你的配置需要通过强制隧道连接到本地网络，并且可以确定 Internet 目标的目标 IP 前缀，则可以通过 AzureFirewallSubnet 上用户定义的路由将本地网络的这些范围配置为下一跃点。 或者，可以使用 BGP 来定义这些路由。

## <a name="are-there-any-firewall-resource-group-restrictions"></a>是否有任何防火墙资源组限制？

是的。 防火墙、VNet 和公共 IP 地址都必须位于同一资源组中。

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>为入站 Internet 网络流量配置 DNAT 时，是否还需要配置相应的网络规则以允许该流量？

不是。 NAT 规则会隐式添加一个对应的网络规则来允许转换后的流量。 可以通过以下方法替代此行为：显式添加一个网络规则集合并在其中包含将匹配转换后流量的拒绝规则。 若要详细了解 Azure 防火墙规则处理逻辑，请参阅 [Azure 防火墙规则处理逻辑](rule-processing.md)。

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>应用程序规则目标 FQDN 中的通配符有什么作用？

目前只能在 FQDN 的左侧使用通配符。 例如，**_. contoso.com_* 和 **_contoso.com_*。

如果配置 **_. contoso.com_*，则允许 *anyvalue* contoso.com，但不允许 contoso.com (域顶点) 。 如果希望允许域顶点，必须显式将其配置为目标 FQDN。

## <a name="what-does-provisioning-state-failed-mean"></a>“预配状态:失败”意味着什么？

每当应用配置更改时，Azure 防火墙就会尝试更新其所有底层后端实例。 在极少见的情况下，其中的某个后端实例可能无法使用新配置进行更新，并且更新过程将会停止，并出现预配失败状态。 Azure 防火墙仍可正常运行，但应用的配置可能处于不一致状态，有些实例使用以前的配置，而有些实例则使用更新的规则集。 如果发生这种情况，请尝试再一次更新配置，直到操作成功，并且防火墙处于“成功”预配状态。

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Azure 防火墙如何处理计划内维护和计划外故障？
Azure 防火墙包含多个采用主动-主动配置的后端节点。  对于任何计划内维护，我们都可以通过连接清空逻辑来正常更新节点。  更新安排在每个 Azure 区域的非营业时间，这样可以进一步限制中断风险。  对于计划外问题，我们会实例化一个新节点来代替故障节点。  通常情况下，我们会在发生故障后 10 秒钟内重新建立到新节点的连接。

## <a name="how-does-connection-draining-work"></a>连接清空的工作原理

对于任何计划内维护，连接清空逻辑会正常更新后端节点。 Azure 防火墙会等待 90 秒，以便关闭现有连接。 如果需要，客户端可以自动重建到另一个后端节点的连接。

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>防火墙名称是否存在字符限制？

是的。 防火墙名称有 50 个字符的限制。

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>为何 Azure 防火墙需要 /26 子网大小？

Azure 防火墙在缩放时必须预配更多的虚拟机实例。 /26 地址空间确保防火墙有足够的 IP 地址来应对缩放操作。

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>在服务缩放时，防火墙子网大小是否需要更改？

不是。 Azure 防火墙不需要大于 /26 的子网。

## <a name="how-can-i-increase-my-firewall-throughput"></a>如何提高防火墙吞吐量？

Azure 防火墙的初始吞吐容量为 2.5 - 3 Gbps，可以横向扩展到 30 Gbps。 它会根据 CPU 使用率和吞吐量自动进行横向扩展。

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Azure 防火墙横向扩展需要多长时间？

当平均吞吐量或 CPU 消耗达到 60% 时，Azure 防火墙就会逐渐扩展。 默认部署最大吞吐量约为 2.5 - 3 Gbps，并在达到该数字的 60% 时开始横向扩展。 横向扩展需要 5 到 7 分钟。 

进行性能测试时，请确保至少测试 10 到 15 分钟，并启动新连接以利用新创建的防火墙节点。

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>默认情况下，Azure 防火墙是否允许访问 Active Directory？

不是。 Azure 防火墙默认阻止 Active Directory 访问。 若要允许访问，请配置 AzureActiveDirectory 服务标记。 有关详细信息，请参阅 [Azure 防火墙服务标记](service-tags.md)。

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>能否从基于 Azure 防火墙威胁情报的筛选中排除 FQDN 或 IP 地址？

能。可以使用 Azure PowerShell 执行该操作：

```azurepowershell
# Add a Threat Intelligence allow list to an Existing Azure Firewall

## Create the allow list with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses)
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>为什么 TCP ping 和类似工具可以成功连接到目标 FQDN，即使 Azure 防火墙上没有允许该流量的规则也是如此？

TCP ping 实际上并未连接到目标 FQDN。 这是因为 Azure 防火墙的透明代理侦听端口 80/443 上的出站流量。 TCP ping 将与防火墙建立连接，然后该防火墙会丢弃数据包。 此行为不会对安全性产生任何影响。 但是，为了避免混淆，我们正在调查这种行为的潜在变化。

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>IP 组支持的 IP 地址数量是否有限制？

是的。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>能否将 IP 组移到其他资源组？

否，目前不支持将 IP 组移动到其他资源组。

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Azure 防火墙的 TCP 空闲超时是多长时间？

网络防火墙的标准行为是确保 TCP 连接保持活动状态，并在没有活动时迅速将其关闭。 Azure 防火墙 TCP 空闲超时为 4 分钟。 此设置不可配置。 如果处于非活动状态的时间超过超时值，则不能保证维持 TCP 或 HTTP 会话。 常见的做法是使用 TCP 保持连接状态。 这种做法可以使连接状态保持更长时间。 有关详细信息，请参阅 [.NET 示例](/dotnet/api/system.net.servicepoint.settcpkeepalive?view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_)。

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>是否可以在不使用公共 IP 地址的情况下部署 Azure 防火墙？

否。目前，必须使用公共 IP 地址部署 Azure 防火墙。

## <a name="where-does-azure-firewall-store-customer-data"></a>Azure 防火墙将客户数据存储在何处？

Azure 防火墙不会将客户数据移动或存储到部署了该防火墙的区域之外。