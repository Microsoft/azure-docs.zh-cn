---
title: Azure 安全中心的安全评分
description: 介绍 Azure 安全中心的安全评分及其安全控制
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2020
ms.author: memildin
ms.openlocfilehash: 3cd536d051f3e227ba86429ae3f1633bf6c2e82f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490527"
---
# <a name="secure-score-in-azure-security-center"></a>Azure 安全中心的安全评分

## <a name="introduction-to-secure-score"></a>安全评分简介

Azure 安全中心有两个主要目标： 

- 帮助你了解当前的安全状况
- 帮助你有效提高安全性

使你能够实现这些目标的安全中心的核心功能是安全功能分数。

安全中心会持续评估资源、订阅和组织的安全问题。 然后，它将所有调查结果汇总成一个分数，让你可以一目了然地了解当前的安全状况：分数越高，识别出的风险级别就越低。

Azure 门户页面显示的安全功能分数为百分比值，但原值也一目了然：

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="门户中显示的总体安全功能分数":::

若要提高安全性，请查看安全中心的建议页面，了解提高分数需要采取的有效措施。 每项建议都包含有助于你修正特定问题的说明。

建议会被分组到各项安全控件中。 每个控件都是相关安全建议的逻辑组，反映易受攻击的攻击面。 只有修正控制中针对单个资源的所有建议后，分数才会提高。 若要查看你的组织对每个单独攻击面的保护力度，请查看每个安全控件的分数。

有关详细信息，请参阅下面的[如何计算安全功能分数](secure-score-security-controls.md#how-your-secure-score-is-calculated)。 


## <a name="access-your-secure-score"></a>访问安全功能分数

可以通过 Azure 门户或以编程方式查找总体安全功能分数以及每个订阅的分数，如以下各部分所述：

- [从门户获取安全功能分数](#get-your-secure-score-from-the-portal)
- [从 REST API 获取安全功能分数](#get-your-secure-score-from-the-rest-api)
- [从 Azure Resource Graph (ARG) 获取安全功能分数](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>从门户获取安全功能分数

安全中心会在门户中突出显示你的分数：这是“安全中心”概述页面中显示的第一个主磁贴。 选择此磁贴，会转到专用安全功能分数页，其中显示按订阅细分的分数。 选择单个订阅可查看重要建议的详细列表，以及实现这些建议将对订阅分数产生的潜在影响。

概括而言，你的安全功能分数将显示在安全中心门户页面的以下位置。

- 在安全中心的“概述”（主仪表板）上的磁贴中：

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="安全中心仪表板上的安全功能分数":::

- 在专用“安全功能分数”页面中：

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="安全中心“安全功能分数”页面上的安全功能分数":::

- 在“建议”页面的顶部：

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="安全中心建议页面上的安全功能分数":::

### <a name="get-your-secure-score-from-the-rest-api"></a>从 REST API 获取安全功能分数

可以通过安全功能分数 API（当前为预览版）访问分数。 通过 API 方法，可灵活地查询数据，久而久之构建自己的安全功能分数报告机制。 例如，你可以使用[安全功能分数 API](/rest/api/securitycenter/securescores) 来获取特定订阅的分数。 此外，你可以使用 [ API](/rest/api/securitycenter/securescorecontrols) 列出订阅的安全控件和当前分数。

![正在通过 API 检索单个安全功能分数](media/secure-score-security-controls/single-secure-score-via-api.png)

有关构建在安全功能分数 API 之上的工具示例，请参阅 [GitHub 社区的安全功能分数区域](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)。 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>从 Azure Resource Graph (ARG) 获取安全功能分数

使用 Azure Resource Graph (ARG)，可以通过可靠的筛选、分组和排序功能，快速访问你的云环境中的资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。 [详细了解 Azure Resource Graph](../governance/resource-graph/index.yml)。

使用 ARG 访问多个订阅的安全功能分数：

1. 在 Azure 门户中，打开 Azure Resource Graph Explorer。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="启动 Azure Resource Graph Explorer 建议页面" :::

1. 输入你的 Kusto 查询（使用下面的示例作为指导）。

    - 此查询返回订阅 ID、当前分数（以分数和百分比表示）以及订阅的最大分数。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - 该查询返回所有安全控件的状态。 对于每个控件，你将获得运行不正常资源的数量、当前分数和最高分数。 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. 选择“运行查询”。




## <a name="tracking-your-secure-score-over-time"></a>跟踪一段时间内的安全评分

如果你是具有 Pro 帐户的 Power BI 用户，则可以使用一段 **时间的安全评分** Power BI 仪表板跟踪一段时间内的安全评分，并调查所有更改。

> [!TIP]
> 你可以在 GitHub 上的 Azure 安全中心社区的专用区域中找到此仪表板，以及可通过安全分数以编程方式使用的其他工具： https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

该仪表板包含以下两个报表，可帮助你分析安全状态：

- **资源摘要** -提供有关资源运行状况的汇总数据。
- **安全分数摘要** -提供有关你的评分进度的汇总数据。 使用 "每个订阅的时间的安全评分" 图表来查看分数中的更改。 如果你注意到分数发生了重大变化，请检查 "检测到的更改可能会影响安全分数" 表，了解可能导致更改的可能更改。 此表显示了已删除的资源、新部署的资源或其安全状态在其中一项建议中发生了更改的资源。

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="可选的基于时间的安全分数 PowerBI 仪表板，用于跟踪安全分数随时间推移并调查更改":::





## <a name="how-your-secure-score-is-calculated"></a>如何计算安全功能分数 

建议页面上清楚地显示了每个安全控制对总体安全评分的贡献。

[![安全评分增强版引入了安全控制](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

若要获得某个安全控制所有可能的分数，你的所有资源都必须符合该安全控制中的所有安全建议。 例如，安全中心针对如何保护管理端口提供了多条建议。 你需要对它们进行修正，以区分安全分数。

例如，名为“应用系统更新”的安全控制的最高分为 6 分，你可以在该控制可能增加的分数值上的工具提示中看到它：

[![安全控制“应用系统更新”](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

此控制（应用系统更新）的最高分始终为 6 分。 此示例中一共有 50 个资源。 因此，我们将最高分除以 50，结果是每个资源贡献 0.12 分。 

* **可能增加的分数**（0.12 x 8 个运行不正常的资源 = 0.96）- 该控制中剩余可增加的分数。 如果修正此控制中的所有建议，分数将增加 2%（本例中为 0.96 分，四舍五入为 1 分）。 
* **当前分数**（0.12 x 42 个正常运行的资源 = 5.04）- 此控制的当前分数。 每个控制都为总分贡献分数。 在此示例中，该控制为当前安全总分贡献了 5.04 分。
* **最高分** - 完成某个控制中的所有建议后可获得的最高分数。 控制的最高分表明该控制的相对重要性。 可使用最高分值来会审要优先处理的问题。 


### <a name="calculations---understanding-your-score"></a>计算 - 了解你的分数

|指标|公式和示例|
|-|-|
|**安全控制的当前分数**|<br>![用于计算安全控件分数的公式](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>每一个安全控制都计入安全评分。 受控制中的建议影响的每个资源都计入控制的当前分数。 各个控制的当前分数是对该控制中资源状态的度量。<br>![工具提示显示了计算安全控制的当前分数时使用的值](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>在此示例中，最高分 6 将除以 78，因为这是正常运行的资源和运行不正常的资源的总和。<br>6/78 = 0.0769<br>将其乘以正常运行的资源数量 (4) 可得出当前分数：<br>0.0769 * 4 = 0.31<br><br>|
|**安全评分**<br>一个订阅|<br>![用于计算订阅安全分数的公式](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![启用了所有控制的单个订阅的安全评分](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>在此示例中，单个订阅启用了所有安全控制（可能的最高分为 60 分）。 该分数显示了可能的最高分 60 分中的 28 分，其余 32 分反映在安全控制的“可能增加的分数”数字中。<br>![控制和可能增加的分数的列表](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**安全评分**<br>多个订阅|<br>![计算多个订阅的安全分数的公式](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>计算多个订阅的组合分数时，安全中心包含每个订阅的 *权重* 。 订阅的相对权重由安全中心根据资源数量等因素来确定。<br>计算每个订阅的当前分数的方式与单个订阅的计算方式相同，但会按照公式中所示应用权重。<br>查看多个订阅时，安全评分会计算所有已启用策略中的所有资源，并将其对每个安全控制的最高分的综合影响进行分组。<br>![启用了所有控制的多个订阅的安全评分](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>综合得分不是平均值，而是指所有订阅中所有资源状态的计算状况。<br>同样，在这里，如果转到建议页面并将可能得到的分数相加，你会发现结果是当前分数 (24) 与最高得分 (60) 之差。|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>安全功能分数计算中包括哪些建议？

只有内置建议才会影响安全评分。

计算安全分数时不包括标记为“预览”的建议。 仍应尽可能按这些建议修正，以便在预览期结束时，它们会有助于提升评分。

预览建议示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议":::

## <a name="improve-your-secure-score"></a>提高安全分数

若要提高安全评分，请修正建议列表中的安全建议。 既可以为每个资源手动修正每个建议，也可以使用“快速修复!” 选项（如果有）对一组资源快速应用建议修正。 有关详细信息，请参阅[修正建议](security-center-remediate-recommendations.md)。

改善分数并确保用户不会创建对分数产生负面影响的资源的另一种方法是在相关建议上配置 "强制" 和 "拒绝" 选项。 有关详细信息，请参阅[使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)。

## <a name="security-controls-and-their-recommendations"></a>安全控制及其建议

下表列出了 Azure 安全中心的安全控制。 对于每个控制，可以看到为所有资源修正该控制中列出的所有建议后，安全评分可以增加的最高分数。 

安全中心提供的安全建议是针对每个组织环境中的可用资源量身定制的。 可以通过 [禁用策略](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) 并 [从建议中豁免特定资源](exempt-resource.md)来进一步自定义建议。 
 
建议每个组织仔细检查其分配的 Azure Policy 计划。 

> [!TIP]
> 有关查看和编辑你的计划的详细信息，请参阅[使用安全策略](tutorial-security-policy.md)。 

即使安全中心的默认安全计划是基于行业最佳做法和标准的，但在某些情况下，下面列出的内置建议可能并不完全适合你的组织。 因此，有时有必要调整默认计划，而又不影响安全性，以确保其与组织自己的策略保持一致。 你必须符合的行业标准、法规标准和基准。<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>安全控制、分数和说明</b><br></th>
    <th class="tg-cly1"><b>建议</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用 MFA（最高 10 分）</p></strong>如果只使用密码对用户进行身份验证，则会开放攻击途径。 如果密码较弱或者已在其他位置公开，那么如何确定是该用户在使用用户名和密码登录？<br>启用 <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> 后，帐户将更安全，并且用户仍然可以通过单一登录 (SSO) 向几乎所有应用程序验证身份。</td>
    <td class="tg-lboi"; width=55%>- 应在对订阅拥有所有者权限的帐户上启用 MFA<br>- 应在对订阅拥有写入权限的帐户上启用 MFA</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">保护管理端口（最高 8 分）</p></strong>暴力攻击以管理端口为目标来获取对 VM 的访问权限。 由于没必要总是打开端口，因此，使用实时网络访问控制、网络安全组和虚拟机端口管理来减少对端口的暴露不失为一种缓解策略。<br>由于许多 IT 组织不会阻止从其网络出站的 SSH 通信，因此攻击者可以创建加密隧道，允许受感染系统上的 RDP 端口与攻击者命令通信，以便控制服务器。 攻击者可以使用 Windows 远程管理子系统在你的环境中横向移动，并使用盗用的凭据访问网络上的其他资源。</td>
    <td class="tg-lboi"; width=55%>- 应通过实时网络访问控制来保护虚拟机的管理端口<br>- 虚拟机应与网络安全组关联<br>- 应关闭虚拟机上的管理端口</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用系统更新（最高 6 分）</p></strong>系统更新使组织能够保持运营效率、减少安全漏洞，并为最终用户提供更稳定的环境。 不应用更新会留下未修补的漏洞，使环境容易受到攻击。 这些漏洞可能会被人利用，导致数据丢失、数据泄露、勒索软件和资源滥用。 若要部署系统更新，可以使用<a href="/azure/automation/update-management/overview">更新管理解决方案来管理虚拟机的修补程序和更新</a>。 更新管理是指控制软件版本的部署和维护的过程。</td>
    <td class="tg-lboi"; width=55%>- 应在计算机上解决监视代理运行状况问题<br>- 应在虚拟机规模集上安装监视代理<br>- 应在计算机上安装监视代理<br>- 应为云服务角色更新 OS 版本<br>- 应在虚拟机规模集上安装系统更新<br>- 应在计算机上安装系统更新<br>- 应重启计算机来应用系统更新<br>- Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本<br>- 应在虚拟机上安装监视代理<br>- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上(预览)<br>- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上(预览)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">修正漏洞（最高 6 分）</p></strong>漏洞是攻击者可用来破坏资源机密性、可用性或完整性的薄弱环节。 <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">管理漏洞</a>可以减少组织暴露、强化终结点外围应用、提高组织复原能力以及减少资源的受攻击面。 威胁和漏洞管理可显示错误的软件和安全配置，并提供缓解建议。</td>
    <td class="tg-lboi"; width=55%>- 应对 SQL 数据库启用高级数据安全<br>- 应修正 Azure 容器注册表映像中的漏洞<br>- 应修正 SQL 数据库中的漏洞<br>- 应通过漏洞评估解决方案修正漏洞<br>- 应对 SQL 托管实例启用漏洞评估<br>- 应对 SQL Server 启用漏洞评估<br>- 应在虚拟机上安装漏洞评估解决方案<br>- 容器映像应仅从受信任的注册表中部署（预览）<br>- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项（预览）</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用静态加密（最高 4 分）</p></strong><a href="/azure/security/fundamentals/encryption-atrest">静态加密</a>为已存储的数据提供数据保护。 对静态数据进行的攻击包括试图获得对存储数据的硬件的物理访问权限。 Azure 使用对称加密来加密和解密大量静态数据。 将使用对称加密密钥在将数据写入到存储时对数据进行加密。 该加密密钥还用于解密准备在内存中使用的数据。 必须将密钥存储在实施了基于标识的访问控制和审核策略的安全位置。 Azure 密钥保管库就是这样的安全位置。 如果攻击者获取了加密数据但未获取加密密钥，则攻击者必须破解加密才能访问数据。</td>
    <td class="tg-lboi"; width=55%>- 应对虚拟机应用磁盘加密<br>- 应对 SQL 数据库启用透明数据加密<br>- 应加密自动化帐户变量<br>- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign<br>- 应使用自己的密钥加密 SQL Server 的 TDE 保护器</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">加密传输中的数据（最高 4 分）</p></strong>在各组件、位置或程序间传输数据时，数据处于“传输中”状态。 无法保护传输中的数据的组织更容易遭受中间人攻击、窃听和会话劫持。 应使用 SSL/TLS 协议交换数据，并建议使用 VPN。 通过 Internet 在 Azure 虚拟机和本地位置之间发送加密数据时，可以使用虚拟网络网关（例如 <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN 网关</a>）发送加密流量。</td>
    <td class="tg-lboi"; width=55%>- 只能通过 HTTPS 访问 API 应用<br>- 只能通过 HTTPS 访问函数应用<br>- 应仅启用与 Redis 缓存的安全连接<br>- 应启用到存储帐户的安全传输<br>- 只能通过 HTTPS 访问 Web 应用程序<br>- 应为 PostgreSQL 服务器启用专用终结点<br>- 应为 PostgreSQL 数据库服务器启用强制 SSL 连接<br>- 应为 MySQL 数据库服务器启用强制 SSL 连接<br>- TLS 应更新为 API 应用的最新版本<br>- TLS 应该更新到函数应用的最新版本<br>- TLS 应该更新到 web 应用的最新版本<br>- API 应用中应该需要 FTPS<br>- 函数应用中应该需要 FTPS<br>- Web 应用中应该需要 FTPS</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">管理访问和权限（最高 4 分）</p></strong>安全程序的核心是确保用户具有完成其工作（但仅限于此）所需的访问权限：<a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">最小特权访问模型</a>。<br>通过使用 <a href="/azure/role-based-access-control/overview">AZURE RBAC)  (azure 基于角色的访问控制 </a>创建角色分配来控制对资源的访问权限。 角色分配由三个元素组成：<br>- <strong>安全主体</strong>：用户请求访问的对象<br>- <strong>角色定义</strong>：他们的权限<br>- <strong>作用域</strong>：权限适用于的资源集</td>
    <td class="tg-lboi"; width=55%>- 应从订阅中删除弃用帐户(预览版)<br>- 应从订阅中删除拥有所有者权限的弃用帐户(预览版)<br>- 应从订阅中删除拥有所有者权限的外部帐户(预览版)<br>- 应从订阅中删除拥有写入权限的外部帐户(预览版)<br>- 应向订阅分配多个所有者<br>- 应在 Kubernetes Services (预览版上使用 azure RBAC)  (azure 基于角色的访问控制) <br>- Service Fabric 群集应仅使用 Azure Active Directory 进行客户端身份验证<br>- 应使用服务主体（而不是管理证书）来保护你的订阅<br>- 应该对容器强制执行最低特权的 Linux 功能（预览）<br>- 应该对容器强制执行不可变（只读）根文件系统（预览）<br>- 应避免特权升级的容器（预览）<br>- 应避免以根用户身份运行容器（预览）<br>- 应避免共享敏感主机命名空间的容器（预览）<br>- Pod HostPath 卷挂载的使用应限制在已知列表中（预览）<br>- 应避免使用特权容器（预览）<br>- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项（预览）<br>- Web apps 应为所有传入请求请求一个 SSL 证书<br>- 应在 API 应用中使用托管标识<br>- 托管标识应在函数应用中使用<br>- 应在 web 应用中使用托管标识</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">修正安全配置（最高 4 分）</p></strong>配置错误的 IT 资产受到攻击的风险更高。 当部署资产并且必须在截止日期之前完成时，通常会忘记基本的强化措施。 错误的安全配置可能出现在基础结构中的任何级别：从操作系统和网络设备到云资源。<br>Azure 安全中心会不断将资源的配置与行业标准、法规和基准中的要求进行比较。 配置了对组织而言很重要的相关“合规性包”（标准和基线）后，任何差距都会产生安全建议，其中包括 CCEID 以及对潜在安全影响的说明。<br>常用包为 <a href="/azure/security/benchmarks/introduction">Azure 安全基准</a>和 <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure 基础基准版本 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- 应修正容器安全配置中的漏洞<br>- 应修正计算机上安全配置中的漏洞<br>- 应修正虚拟机规模集上安全配置中的漏洞<br>- 应在虚拟机上安装监视代理<br>- 应在计算机上安装监视代理<br>- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上(预览)<br>- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上(预览)<br>- 应在虚拟机规模集上安装监视代理<br>- 应在计算机上解决监视代理运行状况问题<br>- 应限制替代或禁用容器 AppArmor 配置文件（预览）<br>- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项（预览）</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">限制未经授权的网络访问（最高 4 分）</p></strong>组织内的终结点提供从虚拟网络到受支持的 Azure 服务的直接连接。 子网中的虚拟机可以与所有资源通信。 若要限制与子网内资源的通信，请创建一个网络安全组并将其关联到子网。 组织可以通过创建入站和出站规则来限制和防范未经授权的流量。</td>
    <td class="tg-lboi"; width=55%>- 应禁用虚拟机上的 IP 转发<br>- 应在 Kubernetes 服务上定义已授权 IP 范围(预览版)<br>- (已弃用)应限制对应用服务的访问(预览版)<br>- (已弃用)应加强 IaaS NSG 上 Web 应用程序的规则<br>- 虚拟机应与网络安全组关联<br>- CORS 不应允许所有资源都能访问 API 应用<br>- CORS 不应允许所有资源都能访问函数应用<br>- CORS 不应允许所有资源都能访问 Web 应用程序<br>- 应为 API 应用禁用远程调试<br>- 应为函数应用禁用远程调试<br>- 应为 Web 应用程序禁用远程调试<br>- 应限制许可网络安全组(包含面向 Internet 的 VM)的访问<br>- 应强化面向 Internet 的虚拟机的网络安全组规则<br>- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项（预览）<br>- 容器应只侦听允许的端口（预览）<br>- 服务应只侦听允许的端口（预览）<br>- 应限制对主机网络和端口的使用（预览）<br>- 虚拟网络应受 Azure 防火墙保护（预览）<br>- 应为 MariaDB 服务器启用专用终结点<br>- 应为 MySQL 服务器启用专用终结点<br>- 应为 PostgreSQL 服务器启用专用终结点</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用自适应应用程序控制（最高 3 分）</p></strong>自适应应用程序控制 (AAC) 是一种智能的、自动化的端到端解决方案，可用于控制哪些应用程序可以在 Azure 计算机和非 Azure 计算机上运行。 它还有助于强化计算机免受恶意软件的侵害。<br>安全中心使用机器学习为一组计算机创建一个已知安全应用程序列表。<br>这种将已批准的应用程序列入列表的创新方法在不增加管理复杂性的情况下提供了安全优势。<br>AAC 尤其适用于需要运行一组特定应用程序的专用服务器。</td>
    <td class="tg-lboi"; width=55%>- 应对虚拟机启用自适应应用程序控制<br>- 应在虚拟机上安装监视代理<br>- 应在计算机上安装监视代理<br>- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上(预览)<br>- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上(预览)<br>- 应在计算机上解决监视代理运行状况问题</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">应用数据分类（最高 2 分）</p></strong>通过按敏感度和业务影响对组织数据进行分类，可以为数据确定并分配值，为治理提供策略和基础。<br><a href="/azure/information-protection/what-is-information-protection">Azure 信息保护</a>可帮助进行数据分类。 它使用加密、标识和授权策略来保护数据并限制数据访问。 Microsoft 使用的一些分类包括非业务、公共、常规、机密和高度机密。</td>
    <td class="tg-lboi"; width=55%>- 应对 SQL 数据库中的敏感数据进行分类(预览版)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">保护应用程序免受 DDoS 攻击（最高 2 分）</p></strong>分布式拒绝服务 (DDoS) 攻击会使资源瘫痪，导致应用程序无法使用。 可使用 <a href="/azure/virtual-network/ddos-protection-overview">Azure DDoS 防护标准</a>保护组织免受三种主要的 DDoS 攻击：<br>- <strong>容量耗尽攻击</strong>利用合法流量淹没网络。 DDoS 防护标准通过自动吸收或清理来缓解这些攻击。<br>- <strong>协议攻击</strong>通过利用第 3 层和第 4 层协议堆栈中的漏洞，使目标无法访问。 DDoS 防护标准通过阻止恶意流量来缓解这些攻击。<br>- <strong>资源（应用程序）层攻击</strong>以 Web 应用程序数据包为目标。 可使用 Web 应用程序防火墙和 DDoS 防护标准来防御此类攻击。</td>
    <td class="tg-lboi"; width=55%>- 应启用 DDoS 防护标准<br>- 应强制执行容器 CPU 和内存限制（预览）<br>- 应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项（预览）</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用 Endpoint Protection（最高 2 分）</p></strong>为确保终结点免受恶意软件的侵害，行为传感器会从终结点的操作系统收集数据并加以处理，然后将此数据发送到私有云进行分析。 安全分析利用大数据、机器学习和其他来源针对威胁提出响应建议。 例如，<a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> 使用威胁情报来识别攻击方法并生成安全警报。<br>安全中心支持以下终结点保护解决方案：Windows Defender、System Center Endpoint Protection、Trend Micro、Symantec v12.1.1.1100、适用于 Windows 的 McAfee v10、适用于 Linux 的 McAfee v10 和适用于 Linux 的 Sophos v9。 如果安全中心检测到以上任一解决方案，则不再显示安装 Endpoint Protection 的建议。</td>
    <td class="tg-lboi"; width=55%>- 应在虚拟机规模集上修正 Endpoint Protection 运行状况故障<br>- 应在计算机上解决 Endpoint Protection 运行状况问题<br>- 应在虚拟机规模集上安装 Endpoint Protection 解决方案<br>- 在虚拟机上安装 Endpoint Protection 解决方案<br>- 应在计算机上解决监视代理运行状况问题<br>- 应在虚拟机规模集上安装监视代理<br>- 应在计算机上安装监视代理<br>- 应在虚拟机上安装监视代理<br>- Log Analytics 代理应安装在基于 Windows 的 Azure Arc 计算机上(预览)<br>- Log Analytics 代理应安装在基于 Linux 的 Azure Arc 计算机上(预览)<br>- 在计算机上安装 Endpoint Protection 解决方案</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">启用审核与日志记录（最高 1 分）</p></strong>日志记录数据可让你深入了解过去的问题，防止潜在的问题，可以提高应用程序的性能，并允许自动执行原本手动执行的操作。<br>- <strong>控制和管理日志</strong>提供有关 <a href="/azure/azure-resource-manager/management/overview">Azure 资源管理器</a>操作的信息。<br>- <strong>数据平面日志</strong>提供作为 Azure 资源使用情况的一部分引发的事件的相关信息。<br>- <strong>已处理的事件</strong>提供已处理的分析事件/警报的相关信息。</td>
    <td class="tg-lboi"; width=55%>- 应对 SQL Server 启用审核<br>- 应启用应用服务的诊断日志<br>- 应启用 Azure Data Lake Store 的诊断日志<br>- 应启用 Azure 流分析的诊断日志<br>- 应启用 Batch 帐户的诊断日志<br>- 应启用 Data Lake Analytics 的诊断日志<br>- 应启用事件中心的诊断日志<br>- 应启用 IoT 中心的诊断日志<br>- 应启用密钥保管库的诊断日志<br>- 应启用逻辑应用的诊断日志<br>- 应启用搜索服务的诊断日志<br>- 应启用服务总线的诊断日志<br>- 应启用虚拟机规模集的诊断日志<br>- 应对 Batch 帐户配置指标警报规则<br>- SQL 审核设置中应包含配置为捕获关键活动的操作组<br>- 应将 SQL Server 的审核保留期配置为大于 90 天。</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px"> 启用高级威胁防护（最大分数 0）</p></strong>Azure 安全中心的可选 Azure Defender 威胁防护计划为你的环境提供了全面的防御。 当安全中心检测到环境中的任何区域遭到威胁时，会生成警报。 这些警报会描述受影响资源的详细信息、建议的修正步骤，在某些情况下还会提供触发逻辑应用作为响应的选项。<br>每个 Azure Defender 计划都是单独的可选产品/服务，可以使用此安全控件中的相关建议启用该产品/服务。<br><a href="/azure/security-center/threat-protection">详细了解安全中心的威胁防护</a>。</td>
    <td class="tg-lboi"; width=55%>- 应在 Azure SQL Database 服务器上启用高级数据安全<br>- 应对计算机上的 SQL Server 启用高级数据安全<br>- 应对虚拟机启用高级威胁防护<br>- 应在 Azure 应用服务计划上启用高级威胁防护<br>- 应对 Azure 存储帐户启用高级威胁防护<br>- 应对 Azure Kubernetes 服务的群集启用高级威胁防护<br>- 应对 Azure 容器注册表的注册表启用高级威胁防护<br>- 应对 Azure Key Vault 的保管库启用高级威胁防护</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">实施安全最佳做法（最高 0 分）</p></strong>新式安全做法“假定突破”网络边界。 因此，此控制中的许多最佳做法都集中在标识管理上。<br>丢失密钥和凭据是一个常见问题。 <a href="/azure/key-vault/key-vault-overview">Azure 密钥保管库</a>通过加密密钥、.pfx 文件和密码来保护密钥和机密。<br>虚拟专用网 (VPN) 是访问虚拟机的一种安全方法。 如果 Vpn 不可用，则使用复杂的密码和双因素身份验证，例如 <a href="/azure/active-directory/authentication/concept-mfa-howitworks">Azure AD 多重身份验证</a>。 双重身份验证避开了固有的仅依赖用户名和密码的弱点。<br>使用强身份验证和授权平台是另一种最佳做法。 组织可以使用联合标识来委派授权标识的管理。 当员工离职，需要撤销其访问权限时，这一点也很重要。</td>
    <td class="tg-lboi"; width=55%>- 最多只能为订阅指定 3 个所有者<br>- 应从订阅中删除拥有读取权限的外部帐户<br>- 应在对订阅拥有读取权限的帐户上启用 MFA<br>- 应限制对具有防火墙和虚拟网络配置的存储帐户的访问<br>- 应从事件中心命名空间中删除 RootManageSharedAccessKey 以外的所有授权规则<br>- 应为 SQL Server 预配 Azure Active Directory 管理员<br>- 应对托管实例启用高级数据安全<br>- 应定义事件中心实例上的授权规则<br>- 应将存储帐户迁移到新的 Azure 资源管理器资源<br>- 应将虚拟机迁移到新的 Azure 资源管理器资源<br>- 子网应与网络安全组关联<br>- [预览版]应启用 Windows 攻击防护 <br>- [预览版]应安装来宾配置代理<br>- 应使用网络安全组来保护非面向 Internet 的虚拟机<br>- 应为虚拟机启用 Azure 备份<br>- 应为 Azure Database for MariaDB 启用异地冗余备份<br>- 应为 Azure Database for MySQL 启用异地冗余备份<br>- 应为 Azure Database for PostgreSQL 启用异地冗余备份<br>- PHP 应更新为 API 应用的最新版本<br>- PHP 应更新到 web 应用的最新版本<br>- Java 应更新为 API 应用的最新版本<br>- Java 应更新为 function app 的最新版本<br>- Java 应更新到 web 应用的最新版本<br>- Python 应更新为 API 应用的最新版本<br>- Python 应更新为函数应用的最新版本<br>- Python 应更新到 web 应用的最新版本<br>- 应将 SQL server 的审核保留至少设置为90天</td>
  </tr>
</tbody>
</table>


</div>




## <a name="secure-score-faq"></a>安全评分 FAQ

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>如果仅处理某个安全控制四分之三的建议，安全评分是否会变化？
不是。 为单个资源修正所有建议后，安全评分才会变化。 若要获得某个控制的最高分，必须为所有资源修正所有建议。

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>如果某个建议对我不适用，我在策略中禁用它，我能否达到安全控制的要求，我的安全评分是否会更新？
是的。 如果建议不适用于你的环境，建议禁用它们。 有关如何禁用特定建议的说明，请参阅[禁用安全策略](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)。

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>如果某个安全控制为安全评分贡献的分数为零，我应该忽略它吗？
在某些情况下，你会看到某个控制的最高分大于零，但影响为零。 如果通过修复资源增加的分数可忽略不计，则会将其舍入为零。 请勿忽略这些建议，因为它们仍然可以改善安全性。 唯一的例外是“其他最佳做法”控制。 修正这些建议不会提高分数，但会提高整体安全性。

## <a name="next-steps"></a>后续步骤

本文介绍了安全评分及其引入的安全控制。 如需相关材料，请参阅以下文章：

- [了解建议的不同元素](security-center-recommendations.md)
- [了解如何修正建议](security-center-remediate-recommendations.md)
- [查看基于 GitHub 的工具以便以编程方式使用安全分数](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)