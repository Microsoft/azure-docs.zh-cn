---
title: 使用 Azure CLI 管理 Azure DNS 中的 DNS 记录
description: 在 Azure DNS 上托管域时管理 Azure DNS 上的 DNS 记录集和记录。
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: rohink
ms.openlocfilehash: 52e82e52ba0476aba02b4d083537e085181bbde9
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108228063"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure DNS 中的 DNS 记录和记录集

> [!div class="op_single_selector"]
> * [Azure 门户](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

本文介绍如何使用跨平台 Azure CLI 管理 DNS 区域的 DNS 记录。 Azure CLI 适用于 Windows、Mac 和 Linux。 也可以使用 [Azure PowerShell](dns-operations-recordsets.md) 或 [Azure 门户](dns-operations-recordsets-portal.md)管理 DNS 记录。

本文中的示例假设你[已安装 Azure CLI、已登录，并且已创建一个 DNS 区域](dns-operations-dnszones-cli.md)。

## <a name="introduction"></a>简介

在 Azure DNS 中创建 DNS 记录之前，首先需了解 Azure DNS 如何将 DNS 记录组织到 DNS 记录集中。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

有关 Azure DNS 中的 DNS 记录的详细信息，请参阅 [DNS 区域和记录](dns-zones-records.md)。

## <a name="create-a-dns-record"></a>创建 DNS 记录

请使用 `az network dns record-set <record-type> add-record` 命令创建 DNS 记录（其中 `<record-type>` 为一种记录类型，即 a、srv、txt 等）有关帮助，请参阅 `az network dns record-set --help`。

创建记录时，需要指定以下信息： 

* 资源组名称
* 区域名称
* 记录集名称
* 记录类型

给定的记录集名称必须是 *相对* 名称，这意味着它必须排除区域名称。 如果记录集不存在，此命令将为你创建该记录集。 但如果记录集已存在，则此命令将添加指定的记录。

如果创建了新记录集，将使用默认生存时间 (TTL) 3600。 有关如何使用不同 TTL 的说明，请参阅[创建 DNS 记录集](#create-a-dns-record-set)。

以下示例在区域 *contoso.com* 中的资源组 *MyResourceGroup* 内创建名为 *www* 的 A 记录。 该 A 记录的 IP 地址为 *1.2.3.4*。

```azurecli-interactive
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

若要在区域（在本例中为“contoso.com”）顶点中创建记录集，请使用记录名称“\@”（包括引号）：

```azurecli-interactive
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>创建 DNS 记录集

在上面的示例中，已将 DNS 记录添加到现有记录集，或者 *隐式* 创建了记录集。 也可以 *显式* 创建记录集，并在其中添加记录。 Azure DNS 支持“空”记录集，此类记录集可充当占位符，用于在创建 DNS 记录之前保留某个 DNS 名称。 空记录集在 Azure DNS 控制平面可见，但不会显示在 Azure DNS 名称服务器上。

记录集是使用 `az network dns record-set <record-type> create` 命令创建的。 有关帮助，请参阅 `az network dns record-set <record-type> create --help`。

显式创建记录集可以指定[生存时间 (TTL)](dns-zones-records.md#time-to-live) 等记录集属性以及元数据。 可以使用[记录集元数据](dns-zones-records.md#tags-and-metadata)，以键-值对的形式将特定于应用程序的数据与每个记录集相关联。

以下示例使用 `--ttl` 参数（简短格式为 `-l`）创建 TTL 为 60 秒的类型为“A”的空记录集：

```azurecli-interactive
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

以下示例使用 `--metadata` 参数创建包含两个元数据项（“dept=finance”和“environment=production”）的记录集：

```azurecli-interactive
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

创建空记录集后，可根据[创建 DNS 记录](#create-a-dns-record)中所述，使用 `azure network dns record-set <record-type> add-record` 添加记录。

## <a name="create-records-of-other-types"></a>创建其他类型的记录

详细了解如何创建“A”记录以后，还可通过以下示例了解如何创建 Azure DNS 支持的其他记录类型的记录。

用于指定记录数据的参数根据记录类型的不同而异。 例如，对于“A”类型的记录，应使用参数 `--ipv4-address <IPv4 address>` 指定 IPv4 地址。 可以使用 `az network dns record-set <record-type> add-record --help` 列出每个记录类型的参数。

在每种情况下，我们都演示了如何创建单个记录。 记录将添加到现有记录集或隐式创建的记录集。 有关创建记录集和显式定义记录集参数的详细信息，请参阅[创建 DNS 记录集](#create-a-dns-record-set)。

没有创建 SOA 记录集的示例，因为 SOA 是随每个 DNS 区域一起创建和删除的。 无法单独创建或删除 SOA 记录。 但是，可以[修改](#to-modify-an-soa-record) SOA，如后面的示例所示。

### <a name="create-an-aaaa-record"></a>创建 AAAA 记录

```azurecli-interactive
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-caa-record"></a>创建 CAA 记录

```azurecli-interactive
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>创建 CNAME 记录

> [!NOTE]
> DNS 标准不允许在区域的顶点创建 CNAME 记录 (`--Name "@"`)，也不允许记录集包含多个记录。
> 
> 有关详细信息，请参阅 [CNAME 记录](dns-zones-records.md#cname-records)。

```azurecli-interactive
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>创建 MX 记录

在此示例中，使用记录集名称“\@”在区域顶端（在本例中为“contoso.com”）创建 MX 记录。

```azurecli-interactive
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>创建 NS 记录

```azurecli-interactive
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>创建 PTR 记录

在此示例中，“my-arpa-zone.com”表示代表用户 IP 范围的 ARPA 区域。 此区域中的每个 PTR 记录集对应于此 IP 范围内的一个 IP 地址。  记录名称“10”是此 IP 范围内由此记录表示的 IP 地址的最后一个八位字节。

```azurecli-interactive
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>创建 SRV 记录

创建 [SRV 记录集](dns-zones-records.md#srv-records)时，请在记录集名称中指定 *\_service* 和 *\_protocol*。 在区域顶点创建 SRV 记录集时，无需在记录集名称中包括“\@”。

```azurecli-interactive
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>创建 TXT 记录

以下示例说明如何创建 TXT 记录。 如需详细了解 TXT 记录中支持的最大字符串长度，请参阅 [TXT 记录](dns-zones-records.md#txt-records)。

```azurecli-interactive
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>获取记录集

若要检索现有的记录集，请使用 `az network dns record-set <record-type> show`。 有关帮助，请参阅 `az network dns record-set <record-type> show --help`。

创建记录或记录集时，给定的记录集名称必须是相对名称。 此名称不包括区域名称。 此外，需要指定记录类型、包含该记录集的区域，以及包含该区域的资源组。

以下示例从资源组 *MyResourceGroup* 中的区域 *contoso.com* 检索 A 类型的 *www* 记录。

```azurecli-interactive
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>列出记录集

可以通过使用 `az network dns record-set list` 命令列出 DNS 区域中的所有记录。 有关帮助，请参阅 `az network dns record-set list --help`。

此示例将返回资源组 MyResourceGroup 中区域 contoso.com 内的所有记录集 ：

```azurecli-interactive
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

此示例返回与给定的记录类型（在此例中为“A”记录）匹配的所有记录集：

```azurecli-interactive
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>将记录添加到现有记录集

可以使用 `az network dns record-set <record-type> add-record` 在新记录集中创建记录，或者将记录添加到现有记录集。

有关详细信息，请参阅[创建 DNS 记录](#create-a-dns-record)和前面的[创建其他类型的记录](#create-records-of-other-types)。

## <a name="remove-a-record-from-an-existing-record-set"></a>从现有记录集中删除记录。

若从现有记录集中删除 DNS 记录，请使用 `az network dns record-set <record-type> remove-record`。 有关帮助，请参阅 `az network dns record-set <record-type> remove-record -h`。

此命令从记录集中删除 DNS 记录。 如果删除了记录集中的最后一条记录，也会同时删除记录集本身。 若要保留空记录集，请使用 `--keep-empty-record-set` 选项。

使用 `az network dns record-set <record-type> add-record` 命令时，需要指定要删除的记录以及要从中删除的区域。 [创建 DNS 记录](#create-a-dns-record)和前面的[创建其他类型的记录](#create-records-of-other-types)中介绍了这些参数。

以下示例从资源组 *MyResourceGroup* 中的区域 *contoso.com* 内的名为 *www* 的记录集中，删除值为“1.2.3.4”的 A 记录。

```azurecli-interactive
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>修改现有记录集

每个记录集包含[生存时间 (TTL)](dns-zones-records.md#time-to-live)、[元数据](dns-zones-records.md#tags-and-metadata)和 DNS 记录。 以下部分介绍如何修改其中的每个属性。

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>修改 A、AAAA、CAA、MX、NS、PTR、SRV 或 TXT 记录

若要修改现有的 A、AAAA、CAA、MX、NS、PTR、SRV 或 TXT 类型的记录，应先添加一条新记录，再删除现有记录。 有关如何删除和添加记录的详细说明，请参阅本文中前面的部分。

以下示例演示如何将一条“A”记录中的 IP 地址 1.2.3.4 修改为 IP 地址 5.6.7.8：

```azurecli-interactive
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

不能在区域顶点（`--Name "@"`，包括引号）从自动创建的 SOA 记录集添加、删除或修改记录。 对于此记录集，允许的更改仅限修改记录集 TTL 和元数据。

### <a name="to-modify-a-cname-record"></a>修改 CNAME 记录

与大多数其他记录类型不同，一个 CNAME 记录集只能包含一条记录。  这就是为什么无法像其他记录类型一样，通过添加新记录并删除现有记录来替换当前值的原因。

相反，若要修改 CNAME 记录，请使用 `az network dns record-set cname set-record`。 有关帮助，请参阅 `az network dns record-set cname set-record --help`

该示例将修改资源组 *MyResourceGroup* 中的区域 *contoso.com* 内的 CNAME 记录集 *www*，使其指向“www.fabrikam.net”而不是现有值：

```azurecli-interactive
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>修改 SOA 记录

与大多数其他记录类型不同，一个 SOA 记录集只能包含一条记录。  这就是为什么无法像其他记录类型一样，通过添加新记录并删除现有记录来替换当前值的原因。

相反，若要修改 SOA 记录，请使用 `az network dns record-set soa update`。 有关帮助，请参阅 `az network dns record-set soa update --help`。

以下示例演示如何设置区域 contoso.com 的 SOA 记录的“email”属性：

```azurecli-interactive
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>修改区域顶点处的 NS 记录

在每个 DNS 区域自动创建区域顶点处的 NS 记录集。 其中包含分配给该区域的 Azure DNS 名称服务器名称。

可向此 NS 记录集添加更多名称服务器，以支持具有多个 DNS 提供商的共同托管域。 还可修改此记录集的 TTL 和元数据。 但是，无法删除或修改预填充的 Azure DNS 名称服务器。

此限制仅适用于区域顶点处的 NS 记录集。 区域中的其他 NS 记录集（用于委派子区域）不受约束，可进行修改。

以下示例展示如何向区域顶点处的 NS 记录集添加其他名称服务器：

```azurecli-interactive
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>修改现有记录集的 TTL

若要修改现有记录集的 TTL，请使用 `azure network dns record-set <record-type> update`。 有关帮助，请参阅 `azure network dns record-set <record-type> update --help`。

以下示例演示如何修改记录集的 TTL（在本例中，将修改为 60 秒）：

```azurecli-interactive
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>修改现有记录集的元数据

可以使用[记录集元数据](dns-zones-records.md#tags-and-metadata)，以键-值对的形式将特定于应用程序的数据与每个记录集相关联。 若要修改现有记录集的元数据，请使用 `az network dns record-set <record-type> update`。 有关帮助，请参阅 `az network dns record-set <record-type> update --help`。

以下示例说明如何使用“dept=finance”和“environment=production”这两个元数据条目修改记录集。 任何现有元数据都会被给定的值替换。

```azurecli-interactive
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>删除记录集

可以使用 `az network dns record-set <record-type> delete` 命令删除记录集。 有关帮助，请参阅 `azure network dns record-set <record-type> delete --help`。 删除记录集也会删除记录集内的所有记录。

> [!NOTE]
> 无法删除区域顶点的 SOA 和 NS 记录集 (`--name "@"`)。  这些记录集是在创建区域时自动创建的，会在删除区域时自动删除。

以下示例从资源组 *MyResourceGroup* 中的区域 *contoso.com* 内删除 A 类型的、名为 *www* 的记录集：

```azurecli-interactive
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

系统会提示用户确认删除操作。 若要禁止显示此提示，请使用 `--yes` 开关。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure DNS 中的区域和记录](dns-zones-records.md)。
<br>
了解如何在使用 Azure DNS 时[保护区域和记录](dns-protect-zones-recordsets.md)。
