---
title: 配置 Avere vFXT 存储 - Azure
description: 了解如何为 Azure 的 Avere vFXT 中的群集添加后端存储系统。 如果创建了包含群集的 Azure Blob 容器，则可以使用。
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: c17d3c7cd2cf6fe5bca725cf94344b2d2cb50bf2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001518"
---
# <a name="configure-storage"></a>配置存储

本步骤为 vFXT 群集设置后端存储系统。

> [!TIP]
> 如果已创建新的 Azure Blob 容器和 Avere vFXT 群集，则该容器已配置并且可供使用。

如果没有随群集创建新的 Blob 容器，或者想要添加其他硬件或基于云的存储系统，请按照这些说明进行操作。

有两个主要任务：

1. [创建核心文件服务器](#create-a-core-filer)，它将 vFXT 群集连接到现有存储系统或 Azure 存储帐户容器。

1. [创建命名空间交接点](#create-a-junction)，用于定义客户端将安装的路径。

这些步骤会用到 Avere 控制面板。 请阅读[访问 vFXT 群集](avere-vfxt-cluster-gui.md)，了解如何操作。

## <a name="create-a-core-filer"></a>创建核心文件管理器

"核心文件服务器" 是后端存储系统的 vFXT 术语。 存储器可以是 NetApp 或 Isilon 等硬件 NAS 设备，也可以是云对象存储。 有关核心文件的详细信息，请参阅 [Avere 群集设置指南](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)。

要添加核心文件管理器，请选择以下两种主要类型的核心文件管理器之一：

* [NAS 核心文件管理器](#nas-core-filer) - 介绍如何添加 NAS 核心文件管理器
* [Azure 存储云核心文件服务器](#azure-blob-storage-cloud-core-filer) -介绍如何添加 azure Blob 存储容器作为云核心文件服务器

### <a name="nas-core-filer"></a>NAS 核心文件管理器

NAS 核心文件服务器可以是本地 NetApp 或 Isilon 设备，也可以是云中的 NAS 终结点。 存储系统必须与 Avere vFXT 群集具有可靠的高速连接 - 例如，1GBps ExpressRoute 连接（非 VPN） - 并且它必须为群集根提供对正在使用的 NAS 导出的访问权限。

请按照以下步骤添加 NAS 核心文件服务器：

1. 在 Avere 控制面板中，单击顶部的“设置”选项卡。

1. 单击左侧的 "**核心文件服务器**  >  **管理核心文件** 服务器"。

1. 单击“创建”。

   ![“添加新核心文件”页的屏幕截图，其中光标位于“创建”按钮上](media/avere-vfxt-add-core-filer-start.png)

1. 在向导中填写所需信息：

   * 为核心文件服务器命名。
   * 如果有，提供完全限定的域名 (FQDN)。 否则，请提供解析为核心文件管理器的 IP 地址或主机名。
   * 从列表中选择文件管理器类。 如果不确定，请选择“其他”。

     ![添加新核心文件管理器页面的屏幕截图，其中包含核心文件管理器名称及其完全限定的域名](media/avere-vfxt-add-core-filer.png)
  
   * 单击“下一步”，然后选择一个高速缓存策略。
   * 单击“添加文件管理器”。
   * 有关更多详细信息，请参阅 Avere 群集设置指南中的 [Adding a new NAS core filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html)（添加新的 NAS 核心文件管理器）。

接下来，继续执行[创建交接点](#create-a-junction)。  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob 存储云核心文件服务器

若要使用 Azure Blob 存储作为 vFXT 群集的后端存储，需要将一个空容器添加为核心文件服务器。

将 Blob 存储添加到群集需要执行以下任务：

* 创建存储帐户（下面的步骤 1）
* 创建一个空的 Blob 容器（步骤 2-3）
* 将存储访问密钥添加为 vFXT 群集的云凭据（步骤 4-6）
* 将 Blob 容器添加为 vFXT 群集的核心文件服务器（步骤 7-9）
* 创建客户端用于挂载核心文件管理器的命名空间交接点（[创建交接点](#create-a-junction)，硬件和云存储的交接点都一样）

> [!TIP]
> 如果在创建 Azure 群集的 Avere vFXT 时创建新的 Blob 容器，则部署模板会自动将该容器配置为核心文件服务器。  (如果你使用在请求时可用的创建脚本，也会出现此情况。 ) 你以后无需配置核心文件管理器。
>
> 群集创建工具为你执行以下配置任务：
>
> * 在提供的存储帐户中创建新的 Blob 容器
> * 将容器定义为核心文件服务器
> * 创建命名空间与容器的联接
> * 在群集的虚拟网络中创建存储服务终结点

要在创建群集后添加 Blob 存储，请按照下列步骤操作。

1. 使用以下设置创建常规用途 V2 存储帐户：

   * **订阅** - 与 vFXT 群集相同
   * **资源组** - 与 vFXT 群集组相同（可选）
   * **位置** - 与 vFXT 群集相同
   * **性能** - 标准（不支持高级存储）
   * **帐户类型** - 常规用途 V2 (StorageV2)
   * **复制** - 本地冗余存储 (LRS)
   * **访问层** - 热访问层
   * **需要安全传输** - 禁用此选项（非默认值）
   * **虚拟网络** - 非必需

   可以使用 Azure 门户或单击下面的“部署到 Azure”按钮。

   [![用于创建存储帐户的按钮](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. 创建帐户后，浏览到存储帐户页。

   ![Azure 门户中的新存储帐户](media/avere-vfxt-new-storage-acct.png)

1. 创建新的 Blob 容器：单击 "概述" 页上的 " **容器** "，然后单击 " **+ 容器**"。 使用任何容器名称，并确保访问权限设置为“专用”。

   ![在弹出页中使用 "+ 容器" 按钮进行了圆圈并创建新容器的 "存储 blob" 页](media/avere-vfxt-new-blob.png)

1. 单击 "**设置**" 下的 "**访问密钥**" 获取 Azure 存储帐户密钥。 复制所提供的一个密钥。

   ![用于复制密钥的 Azure 门户 GUI](media/avere-vfxt-copy-storage-key.png)

1. 打开群集的 Avere 控制面板。 单击“设置”，然后在左侧导航窗格中打开“群集” > “云凭据”。 在“云凭据”页上，单击“添加凭据”。

   ![单击“云凭据”配置页上的“添加凭据”按钮](media/avere-vfxt-new-credential-button.png)

1. 填写以下信息以创建云核心文件管理器的凭据：

   | 字段 | 值 |
   | --- | --- |
   | 凭据名称 | 任何描述性名称 |
   | 服务类型 |  (选择 Azure 存储访问密钥)  |
   | 租户 | 存储帐户名称 |
   | 订阅 | 订阅 ID |
   | 存储访问密钥 | Azure 存储帐户密钥（在上一步中复制） |

   单击“提交”  。

   ![Avere 控制面板中已完成的云凭据表单](media/avere-vfxt-new-credential-submit.png)

1. 接下来，创建核心文件管理器。 在 Avere 控制面板的左侧，单击 "**核心文件** 管理器" "  >   **管理核心文件** 服务器"。

1. 单击“管理核心文件管理器”设置页上的“创建”按钮。

1. 填写该向导：

   * 选择文件管理器类型“云”。
   * 命名新的核心文件管理器，然后单击“下一步”。
   * 接受默认缓存策略并进入第三页。
   * 在“服务类型”中，选择“Azure 存储”。
   * 选择之前创建的凭据。
   * 将“Bucket 内容”设置为“空”
   * 将“证书验证”更改为“禁用”
   * 将“压缩模式”更改为“无”
   * 单击“配置目录分区”  。
   * 在第四页上，在“Bucket 名称”中输入容器的名称 storage_account_name/container_name。
   * （可选）将“加密类型”设置为“无”。  Azure 存储默认已加密。
   * 单击“添加文件管理器”。

   有关更多详细信息，请阅读 Avere 群集配置指南中的 [Adding a new cloud core filer](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>)（添加新的云核心文件管理器）。

页面将刷新，或者你可以刷新页面以显示新的核心文件管理器。

接下来，需要[创建交接点](#create-a-junction)。

## <a name="create-a-junction"></a>创建交接点

交接点是为客户端创建的路径。 客户端装载路径并转到选择的目标路径。

例如，可以创建 `/vfxt/files` 以映射到 NetApp 核心文件管理器 `/vol0/data` 导出和 `/project/resources` 子目录。

有关交接点的详细信息，请参阅 [Avere 群集配置指南的命名空间部分](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)。

在 Avere 控制面板界面中执行以下步骤：

* 单击 **VServer**  >  左上角的 "VServer **命名空间**"。
* 提供以“/”（正斜杠）开头的命名空间路径，如 ``/vfxt/data``。
* 选择核心文件管理器。
* 选择核心文件管理器导出。
* 单击“配置目录分区”  。

  ![“添加新交接点”页面的屏幕截图，其中包含为交接点、核心文件管理器和导出完成的字段](media/avere-vfxt-add-junction.png)

几秒钟后将显示交接点。 根据需要创建其他交接点。

创建联接后，客户端将使用命名空间路径从存储系统访问这些文件。

## <a name="next-steps"></a>后续步骤

* [装载 Avere vFXT 群集](avere-vfxt-mount-clients.md)
* 了解[将数据移动到新的 Blob 容器的](avere-vfxt-data-ingest.md)有效方法
