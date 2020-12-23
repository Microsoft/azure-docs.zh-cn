---
title: 配置虚拟网络 - 高级 Azure Cache for Redis
description: 了解如何为高级层 Azure Redis 缓存实例创建和管理虚拟网络支持
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 5fd82105c94bb9be2d07c8843834465821acd8bc
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95803777"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>如何为高级 Azure Redis 缓存配置虚拟网络支持
Azure Redis 缓存具有不同的缓存产品/服务，从而在缓存大小和功能（包括群集、暂留和虚拟网络支持等高级层功能）的选择上具有灵活性。 VNet 是云中的专用网络。 为 Azure Redis 缓存实例配置了 VNet 后，该实例不可公开寻址，而只能从 VNet 中的虚拟机和应用程序进行访问。 本文说明如何为高级 Azure Redis 缓存实例配置虚拟网络支持。

> [!NOTE]
> Azure Redis 缓存同时支持经典 VNet 和资源管理器 VNet。
> 
> 

## <a name="why-vnet"></a>为何使用 VNet？
[Azure 虚拟网络 (VNet)](https://azure.microsoft.com/services/virtual-network/) 部署为 Azure Redis 缓存提供增强的安全性和隔离性，并提供子网、访问控制策略以及其他进一步限制访问的功能。

## <a name="virtual-network-support"></a>虚拟网络支持
在创建缓存期间，可在“新建 Azure Redis 缓存”  边栏选项卡中配置虚拟网络 (VNet) 支持。 

1. 若要创建高级缓存，请登录到 [Azure 门户](https://portal.azure.com)并选择“创建资源”。 请注意：除了在 Azure 门户中创建缓存以外，也可使用资源管理器模板、PowerShell 或 Azure CLI 创建。 有关创建 Azure Redis 缓存的详细信息，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="创建资源。":::
   
2. 在“新建”页上选择“数据库”，然后选择“Azure Cache for Redis”。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="选择 Azure Cache for Redis。":::

3. 在“新建 Redis 缓存”页上配置新高级缓存的设置。
   
   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名称** | 输入任何全局唯一的名称。 | 缓存名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母或连字符。 该名称必须以数字或字母开头和结尾，且不能包含连续的连字符。 缓存实例的主机名是 *\<DNS name> .redis.cache.windows.net*。 | 
   | **订阅** | 单击下拉箭头并选择你的订阅。 | 要在其下创建此新的 Azure Cache for Redis 实例的订阅。 | 
   | **资源组** | 单击下拉箭头并选择一个资源组，或者选择“新建”并输入新的资源组名称。 | 要在其中创建缓存和其他资源的资源组的名称。 将所有应用资源放入一个资源组可以轻松地统一管理或删除这些资源。 | 
   | **位置** | 单击下拉箭头并选择一个位置。 | 选择与要使用该缓存的其他服务靠近的[区域](https://azure.microsoft.com/regions/)。 |
   | **缓存类型** | 单击下拉箭头并选择高级缓存来配置高级功能。 有关详细信息，请参阅 [Azure Cache for Redis 定价](https://azure.microsoft.com/pricing/details/cache/)。 |  定价层决定可用于缓存的大小、性能和功能。 有关详细信息，请参阅[用于 Redis 的 Azure 缓存概述](cache-overview.md)。 |

4. 选择“网络”选项卡，或单击页面底部的“网络”按钮 。

5. 在“网络”选项卡中，选择“虚拟网络”作为连接方法 。 若要使用新的虚拟网络，请先创建虚拟网络，方法是执行[使用 Azure 门户创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)或[使用 Azure 门户创建虚拟网络（经典）](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)中的步骤，然后返回“新建 Azure Cache for Redis”边栏选项卡来创建并配置高级缓存。

   > [!IMPORTANT]
   > 将 Azure Redis 缓存部署到资源管理器 VNet 时，缓存必须位于专用子网中，该子网中只能包含 Azure Redis 缓存实例，而不能包含其他资源。 如果尝试将 Azure Redis 缓存部署到包含其他资源的资源管理器 VNet 子网，部署会失败。
   > 
   > 

   | 设置      | 建议的值  | 说明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **虚拟网络** | 单击下拉箭头并选择你的虚拟网络。 | 选择与缓存位于同一订阅和位置中的虚拟网络。 | 
   | **子网** | 单击下拉箭头并选择你的子网。 | 应以 CIDR 表示法表示子网的地址范围（例如 192.168.1.0/24）。 它必须包含在虚拟网络的地址空间中。 | 
   | **静态 IP 地址** | （可选）输入静态 IP 地址。 | 如果未指定静态 IP，则会自动选择一个 IP 地址。 | 

   > [!IMPORTANT]
   > Azure 会保留每个子网中的某些 IP 地址，不可以使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > 除了 Azure VNET 基础结构使用的 IP 地址以外，子网中的每个 Redis 实例为每个分片使用两个 IP 地址，为负载均衡器使用一个额外的 IP 地址。 非群集缓存被视为包含一个分片。
   > 
   > 

6. 选择页面底部的“下一步:高级”选项卡，或者单击页面底部的“下一步:高级”按钮。

7. 在高级缓存实例的“高级”选项卡中，配置非 TLS 端口、群集和数据暂留的设置。 

8. 选择页面底部的“下一步:标记”选项卡，或者单击“下一步:标记”按钮。

9. 或者，在“标记”选项卡中，如果希望对资源分类，请输入名称或值。 

10. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”选项卡，Azure 将在此处验证配置。

11. 显示绿色的“已通过验证”消息后，选择“创建”。

创建缓存需要花费片刻时间。 可以在 Azure Cache for Redis 的“概述”页上监视进度。  如果“状态”显示为“正在运行”，则表示该缓存可供使用。  创建缓存之后，可以在“资源菜单”  中单击“虚拟网络”  ，查看 VNet 的配置。

![虚拟网络][redis-cache-vnet-info]

若要在使用 VNet 时连接到 Azure Redis 缓存实例，请在连接字符串中指定缓存的主机名，如以下示例所示：

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-vnet-faq"></a>Azure Redis 缓存 VNet 常见问题解答
以下列表包含有关 Azure Redis 缓存缩放的常见问题的解答。

* Azure Redis 缓存和 VNet 有哪些常见的错误配置问题？
* [如何验证缓存是否在 VNET 中正常工作？](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* 尝试连接到 VNET 中的 Azure Redis 缓存时，为何会收到一项指出远程证书无效的错误？
* [是否可以对标准或基本缓存使用 VNet？](#can-i-use-vnets-with-a-standard-or-basic-cache)
* 为什么在某些子网中创建 Azure Redis 缓存会失败，而在其他子网中不会失败？
* [子网地址空间的要求是什么？](#what-are-the-subnet-address-space-requirements)
* [在 VNET 中托管缓存时，是否可以使用所有缓存功能？](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Azure Redis 缓存和 VNet 有哪些常见的错误配置问题？
在 VNet 中托管 Azure Redis 缓存时，将使用下表中的端口。 

>[!IMPORTANT]
>如果下表中的端口受阻，缓存可能无法正常工作。 在 VNet 中使用 Azure Redis 缓存时，阻止这些端口中的一个或多个是最常见的错误配置问题。
> 
> 

- [出站端口要求](#outbound-port-requirements)
- [入站端口要求](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>出站端口要求

出站端口有九个要求。 这些范围内的出站请求要么出站到缓存运行所需的其他服务，要么在 Redis 子网内部进行节点间通信。 对于异地复制，主缓存和副本缓存的子网之间的通信存在其他出站要求。

| 端口 | 方向 | 传输协议 | 目的 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |出站 |TCP |Azure 存储/PKI (Internet) 上的 Redis 依赖关系 | （Redis 子网） |* <sup>4</sup> |
| 443 | 出站 | TCP | Azure Key Vault 和 Azure Monitor 上的 Redis 依赖关系 | （Redis 子网） | AzureKeyVault、AzureMonitor <sup>1</sup> |
| 53 |出站 |TCP/UDP |DNS (Internet/VNet) 上的 Redis 依赖关系 | （Redis 子网） | 168.63.129.16 和 169.254.169.254 <sup>2</sup> 以及子网的任何自定义 DNS 服务器 <sup>3</sup> |
| 8443 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 10221-10231 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 20226 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 13000-13999 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 15000-15999 |出站 |TCP |Redis 的内部通信和异地复制 | （Redis 子网） |（Redis 子网）（地域副本对等子网） |
| 6379-6380 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>1</sup> 可以将服务标记“AzureKeyVault”和“AzureMonitor”用于资源管理器网络安全组。

<sup>2</sup> Microsoft 拥有的这些 IP 地址用于对为 Azure DNS 提供服务的主机 VM 进行寻址。

<sup>3</sup> 没有自定义 DNS 服务器的子网或忽略自定义 DNS 的更新 redis 缓存不需要。

<sup>4</sup> 有关详细信息，请参阅 [其他 VNET 网络连接要求](#additional-vnet-network-connectivity-requirements)。

#### <a name="geo-replication-peer-port-requirements"></a>异地复制对等端口要求

如果在 Azure 虚拟网络中的缓存之间使用异地复制，请注意，建议的配置是在两个缓存的入站和出站方向上取消阻止整个子网的端口 15000-15999，这样即使将来发生异地故障转移，子网中的所有副本组件也可以直接相互通信。

#### <a name="inbound-port-requirements"></a>入站端口要求

入站端口范围有八个要求。 这些范围中的入站请求从同一 VNET 中托管的其他服务入站，或者是 Redis 子网通信的内部请求。

| 端口 | 方向 | 传输协议 | 目的 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 6379、6380 |入站 |TCP |与 Redis 的客户端通信、Azure 负载均衡 | （Redis 子网） |  (Redis 子网) ， (客户端子网) ，AzureLoadBalancer <sup>1</sup> |
| 8443 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 8500 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） | AzureLoadBalancer |
| 10221-10231 |入站 |TCP |与 Redis 群集的客户端通信、Redis 的内部通信 | （Redis 子网） | (Redis 子网) ，AzureLoadBalancer， (客户端子网)  |
| 13000-13999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载均衡 | （Redis 子网） |  (Redis 子网) ， (客户端子网) ，AzureLoadBalancer |
| 15000-15999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载均衡和异地复制 | （Redis 子网） |  (Redis 子网) ， (客户端子网) ，AzureLoadBalancer， (异地副本对等子网)  |
| 16001 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） | AzureLoadBalancer |
| 20226 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>1</sup> 可以使用服务标记“AzureLoadBalancer”（资源管理器）或“AZURE_LOADBALANCER”（经典）来创作 NSG 规则。

#### <a name="additional-vnet-network-connectivity-requirements"></a>其他 VNET 网络连接要求

在虚拟网络中，可能一开始不符合 Azure Redis 缓存的网络连接要求。 在虚拟网络中使用时，Azure Redis 缓存需要以下所有项才能正常运行。

* 与全球 Azure 存储终结点建立的出站网络连接。 这包括位于 Azure Redis 缓存实例区域的终结点，以及位于 **其他** Azure 区域的存储终结点。 Azure 存储终结点在以下 DNS 域下解析： *table.core.windows.net*、 *blob.core.windows.net*、 *queue.core.windows.net* 和 *file.core.windows.net*。 
* 与 ocsp.digicert.com、crl4.digicert.com、ocsp.msocsp.com、mscrl.microsoft.com、crl3.digicert.com、cacerts.digicert.com、oneocsp.microsoft.com 和 crl.microsoft.com 之间的出站网络连接       。 需要此连接才能支持 TLS/SSL 功能。
* 虚拟网络的 DNS 设置必须能够解析前面几点所提到的所有终结点和域。 确保已针对虚拟网络配置并维护有效的 DNS 基础结构即可符合这些 DNS 要求。
* 与以下Azure Monitor 终结点（在下列 DNS 域下进行解析）的出站网络连接：shoebox2-black.shoebox2.metrics.nsatc.net、north-prod2.prod2.metrics.nsatc.net、azglobal-black.azglobal.metrics.nsatc.net、shoebox2-red.shoebox2.metrics.nsatc.net、east-prod2.prod2.metrics.nsatc.net、azglobal-red.azglobal.metrics.nsatc.net     。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>如何验证缓存是否在 VNET 中正常工作？

>[!IMPORTANT]
>连接到 VNET 中托管的 Azure Redis 缓存实例时，缓存客户端必须位于同一 VNET 中，或位于同一 Azure 区域中已启用 VNET 对等互连的 VNET 中。 当前不支持全局 VNET 对等互连。 这包括任何测试应用程序或诊断 ping 工具。 无论客户端应用程序在哪里托管，都必须配置网络安全组或其他网络层，以便允许客户端的网络流量到达 Redis 实例。
>
>

根据上一部分中所述的要求配置端口后，可通过执行以下步骤来验证缓存是否正常工作。

- [重新启动](cache-administration.md#reboot)所有缓存节点。 如果无法访问全部所需的缓存依赖项（如[入站端口要求](cache-how-to-premium-vnet.md#inbound-port-requirements)和[出站端口要求](cache-how-to-premium-vnet.md#outbound-port-requirements)中所述），则缓存无法成功重启。
- 重启缓存节点后（由 Azure 门户中的缓存状态报告），可执行以下测试：
  - 使用 [tcping](https://www.elifulkerson.com/projects/tcping.php)，从缓存所在的同一 VNET 中的某台计算机 ping 缓存终结点（使用端口 6380）。 例如：
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    如果 `tcping` 工具报告端口已打开，则可以从该 VNET 中的客户端连接缓存。

  - 另一种测试方法是创建一个与缓存连接的测试缓存客户端（可以是使用 StackExchange.Redis 的简单控制台应用程序），然后在缓存中添加和检索一些项。 将示例客户端应用程序安装到缓存所在的同一个 VNET 中的某个 VM，然后运行该应用程序来验证与缓存之间的连接。


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>尝试连接到 VNET 中的 Azure Redis 缓存时，为何会收到一项指出远程证书无效的错误？

尝试连接到 VNET 中的 Azure Redis 缓存时，会看到类似于以下内容的证书验证错误：

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

这可能是因为你在通过 IP 地址来连接主机。 建议使用主机名。 换而言之，请使用以下方法：     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

避免使用类似于以下连接字符串的 IP 地址：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

如果无法解析 DNS 名称，某些客户端库包括了 `sslHost`（这是由 StackExchange.Redis 客户端提供的）之类的配置选项。 这允许你替代用于证书验证的主机名。 例如：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>是否可以对标准或基本缓存使用 VNet？
只能对高级缓存使用 VNet。

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>为什么在某些子网中创建 Azure Redis 缓存会失败，而在其他子网中不会失败？
如果要将适用于 Redis 的 Azure 缓存部署到 VNet，则该缓存必须在不包含任何其他资源类型的专用子网中。 如果尝试将适用于 Redis 的 Azure 缓存部署到包含其他资源的资源管理器 VNet 子网 (例如应用程序网关、出站 NAT 等) ，则部署通常会失败。 必须先删除其他类型的现有资源，然后才能为 Redis 创建新的 Azure 缓存。

还必须在子网中提供足够的 IP 地址。

### <a name="what-are-the-subnet-address-space-requirements"></a>子网地址空间的要求是什么？
Azure 会保留每个子网中的某些 IP 地址，不可以使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基础结构使用的 IP 地址外，子网中的每个 Redis 实例都使用每个群集的两个 IP 地址分片 (加上其他副本的其他 IP 地址（如果有任何) 和负载均衡器的其他 IP 地址）。 非群集缓存被视为包含一个分片。

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>在 VNET 中托管缓存时，是否可以使用所有缓存功能？
如果缓存是 VNET 的一部分，则只有 VNET 中的客户端可以访问缓存。 因此，以下缓存管理功能目前不起作用。

* Redis 控制台-由于 Redis 控制台在本地浏览器中运行，该浏览器通常位于未连接到 VNET 的开发人员计算机上，因此它无法连接到缓存。


## <a name="use-expressroute-with-azure-cache-for-redis"></a>将 ExpressRoute 与 Azure Redis 缓存配合使用

客户可以将 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路连接到虚拟网络基础结构，从而将其本地网络扩展到 Azure。 

默认情况下，新创建的 ExpressRoute 线路不会在 VNET 上执行强制隧道（默认路由播发，0.0.0.0/0）。 因此，出站 Internet 连接可以直接来自 VNET，而客户端应用程序能够连接到其他 Azure 终结点（包括 Azure Redis 缓存）。

但是，常见的客户配置是使用强制隧道 (播发默认路由) ，这会强制出站 Internet 流量改为在本地流动。 如果接下来出站流量在本地遭到阻止，此流量将断开与 Azure Redis 缓存的连接，这样 Azure Redis 缓存实例将无法与其依赖项通信。

解决方法是在包含 Azure Redis 缓存的子网上定义一个（或多个）用户定义的路由 (UDR)。 UDR 定义了要遵循的子网特定路由，而不是默认路由。

如果可能，建议使用以下配置：

* ExpressRoute 配置播发 0.0.0.0/0 并默认使用强制隧道将所有输出流量发送到本地。
* 已应用到包含 Azure Redis 缓存的子网的 UDR 使用公共 Internet 的 TCP/IP 流量工作路由来定义 0.0.0.0/0；例如，可以将下一跃点类型设置为“Internet”。

这些步骤的组合效应是子网级 UDR 优先于 ExpressRoute 强制隧道，因此可确保来自 Azure Redis 缓存的出站 Internet 访问。

由于性能原因，从本地应用程序使用 ExpressRoute 连接到 Azure Redis 缓存实例不是典型使用方案（为了获得最佳性能，Azure Redis 缓存客户端应与 Azure Redis 缓存位于同一区域中）。

>[!IMPORTANT] 
>UDR 中定义的路由 **必须** 足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 以下示例使用广泛 0.0.0.0/0 地址范围，因此使用更明确的地址范围，有可能意外地被路由播发重写。

>[!WARNING]  
>**从公共对等路径到专用对等路径未正确交叉播发路由** 的 ExpressRoute 配置不支持 Azure Redis 缓存。 已配置公共对等互连的 ExpressRoute 配置会收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。 如果这些地址范围在专用对等路径上未正确交叉播发，则结果是来自 Azure Redis 缓存实例子网的所有出站网络数据包都不会正确地使用强制隧道发送到客户的本地网络基础结构。 此网络流会破坏 Azure Redis 缓存。 此问题的解决方法是停止从公共对等路径到专用对等路径的交叉播发路由。


有关用户定义路由的背景信息，请参阅此[概述](../virtual-network/virtual-networks-udr-overview.md)。

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="next-steps"></a>后续步骤
了解有关 Azure Cache for Redis 功能的详细信息。

* [Azure Cache for Redis 高级服务层](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
