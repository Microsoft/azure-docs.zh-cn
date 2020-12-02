---
title: 使用 AzCopy v10 向/从 Azure Blob 存储传输数据 | Microsoft Docs
description: 本文包含一系列 AzCopy 示例命令，以帮助你创建容器，并在本地文件系统与容器之间复制文件和同步目录。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 1c9c271fed094bf4777af73d588551f66f4db6f5
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512114"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>使用 AzCopy 和 Blob 存储传输数据

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制数据，或者在存储帐户之间复制数据。 本文包含适用于 Blob 存储的示例命令。

> [!TIP]
> 本文中的示例将路径参数括在单引号 ('') 中。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

## <a name="get-started"></a>入门

请参阅 [AzCopy 入门](storage-use-azcopy-v10.md)一文下载 AzCopy，并了解如何提供存储服务的授权凭据。

> [!NOTE]
> 本文中的示例假设你已使用 `AzCopy login` 命令验证自己的身份。 然后，AzCopy 会使用你的 Azure AD 帐户来授权访问 Blob 存储中的数据。
>
> 如果你希望使用 SAS 令牌来授权访问 Blob 数据，可将该令牌追加到每个 AzCopy 命令中的资源 URL。
>
> 例如：`'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`。

## <a name="create-a-container"></a>创建容器

可以使用 [azcopy make](storage-ref-azcopy-make.md) 命令创建容器。 本部分中的示例创建名为 `mycontainer` 的容器。

|    |     |
|--------|-----------|
| **语法** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **示例** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **示例**（分层命名空间） | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

有关详细参考文档，请参阅 [azcopy make](storage-ref-azcopy-make.md)。

## <a name="upload-files"></a>上传文件

可以使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令从本地计算机上传文件和目录。

本部分包含以下示例：

> [!div class="checklist"]
> * 上传文件
> * 上传目录
> * 上传目录的内容 
> * 上传特定的文件
> * 上传带有索引标记的文件

> [!TIP]
> 可以通过使用可选标志来调整上传操作。 下面是几个示例。
>
> |方案|标志|
> |---|---|
> |将文件作为追加 Blob 或页 Blob 上传。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |上传到特定访问层（如存档层）。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
> 
> 有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

### <a name="upload-a-file"></a>上传文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **示例** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

还可以在文件路径或文件名中的任意位置使用通配符 (*) 来上传文件。 例如：`'C:\myDirectory\*.txt'` 或 `C:\my*\*.txt`。

### <a name="upload-a-directory"></a>上传目录

此示例将某个目录（以及该目录中的所有文件）复制到 Blob 容器。 结果是该文件共享中出现一个同名的容器。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

若要复制到容器中的某个目录，只需在命令字符串中指定该目录的名称。

|    |     |
|--------|-----------|
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

如果指定的目录名称在容器中不存在，AzCopy 将以该名称创建一个新目录。

### <a name="upload-the-contents-of-a-directory"></a>上传目录的内容

可以使用通配符 (*) 上传目录的内容，而无需复制包含的目录本身。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **示例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> 追加 `--recursive` 标志可以上传所有子目录中的文件。

### <a name="upload-specific-files"></a>上传特定的文件

可以使用完整的文件名、包含通配符 (*) 的部分名称或者日期和时间来上传特定文件。

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整文件名

结合 `--include-path` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 使用分号 (`;`) 分隔各个文件名。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

在此示例中，AzCopy 将传输 `C:\myDirectory\photos` 目录和 `C:\myDirectory\documents\myFile.txt` 文件。 需要包含 `--recursive` 选项才能传输 `C:\myDirectory\photos` 目录中的所有文件。

还可以使用 `--exclude-path` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

结合 `--include-pattern` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 指定包含通配符的部分名称。 使用分号 (`;`) 分隔名称。 

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **示例** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

还可以使用 `--exclude-pattern` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  若要复制目录树中存在的所有文本文件，请使用 `–recursive` 选项获取整个目录树，然后使用 `–include-pattern` 并指定 `*.txt` 来获取所有文本文件。

#### <a name="upload-files-that-were-modified-after-a-date-and-time"></a>上传在某个日期和时间之后修改的文件 

结合 `--include-after` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 以 ISO-8601 格式指定日期和时间（例如 `2020-08-19T15:04:00Z`）。 

|    |     |
|--------|-----------|
| **语法** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **示例** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **示例**（分层命名空间） | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

如需详细的参考，请查看 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

### <a name="upload-a-file-with-index-tags"></a>上传带有索引标记的文件

你可以上传文件，并将 [blob 索引标记 (预览) ](../blobs/storage-manage-find-blobs.md) 添加到目标 blob。  

如果使用 Azure AD 授权，则必须向安全主体分配 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 角色，或者必须 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 通过自定义 azure 角色向其授予对 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 的权限。 如果你使用共享访问签名 (SAS) 令牌，则该令牌必须通过 SAS 权限提供对 blob 的标记的访问权限 `t` 。

若要添加标记，请使用 `--blob-tags` 选项以及 URL 编码的键值对。 例如，若要添加一个键 `my tag` 和一个值 `my tag value` ，请将添加 `--blob-tags='my%20tag=my%20tag%20value'` 到目标参数。 

使用与号)  (分隔多个索引标记 `&` 。  例如，如果要添加键 `my second tag` 和值 `my second tag value` ，则完整选项字符串将为 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` 。

下面的示例演示如何使用 `--blob-tags` 选项。

|    |     |
|--------|-----------|
| **上传文件** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **上传目录** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **上传目录内容** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> 如果为源指定一个目录，则复制到该目标的所有 blob 都将具有在命令中指定的相同标记。

## <a name="download-files"></a>下载文件

可以使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令将 Blob、目录和容器下载到本地计算机。

本部分包含以下示例：

> [!div class="checklist"]
> * 下载文件
> * 下载目录
> * 下载目录的内容
> * 下载特定的文件

> [!TIP]
> 可以通过使用可选标志来调整下载操作。 下面是几个示例。
>
> |方案|标志|
> |---|---|
> |自动解压缩文件。|**--decompress**|
> |指定你希望与复制相关的日志条目达到何种详细程度。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
> |指定是否以及如何覆盖目标位置的冲突文件和 Blob。|**--overwrite**=\[true\|false\|ifSourceNewer\|prompt\]|
> 
> 有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

> [!NOTE]
> 如果 Blob 的 `Content-md5` 属性值包含哈希，AzCopy 将计算已下载的数据的 MD5 哈希，并验证存储在该 Blob 的 `Content-md5` 属性中的 MD5 哈希是否与计算出的哈希相匹配。 如果这些值不匹配，除非通过将 `--check-md5=NoCheck` 或 `--check-md5=LogOnly` 追加到 copy 命令来重写此行为，否则下载将会失败。

### <a name="download-a-file"></a>下载文件

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>下载目录

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

此示例将生成名为 `C:\myDirectory\myBlobDirectory` 的目录，其中包含所有已下载的文件。

### <a name="download-the-contents-of-a-directory"></a>下载目录的内容

可以使用通配符 (*) 下载目录的内容，而无需复制包含的目录本身。

> [!NOTE]
> 目前，只有不使用分层命名空间的帐户才支持此方案。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> 追加 `--recursive` 标志可以下载所有子目录中的文件。

### <a name="download-specific-files"></a>下载特定的文件

可以使用完整的文件名、包含通配符 (*) 的部分名称或者日期和时间来下载特定文件。 

#### <a name="specify-multiple-complete-file-names"></a>指定多个完整文件名

结合 `--include-path` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 使用分号 (`;`) 分隔各个文件名。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

在此示例中，AzCopy 将传输 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目录和 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` 文件。 需要包含 `--recursive` 选项才能传输 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 目录中的所有文件。

还可以使用 `--exclude-path` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="use-wildcard-characters"></a>使用通配符

结合 `--include-pattern` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 指定包含通配符的部分名称。 使用分号 (`;`) 分隔名称。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

还可以使用 `--exclude-pattern` 选项来排除文件。 有关详细信息，请参阅 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

`--include-pattern` 和 `--exclude-pattern` 选项仅适用于文件名，而不适用于路径。  若要复制目录树中存在的所有文本文件，请使用 `–recursive` 选项获取整个目录树，然后使用 `–include-pattern` 并指定 `*.txt` 来获取所有文本文件。

#### <a name="download-files-that-were-modified-after-a-date-and-time"></a>下载在某个日期和时间之后修改的文件 

结合 `--include-after` 选项使用 [azcopy copy](storage-ref-azcopy-copy.md) 命令。 以 ISO-8601 格式指定日期和时间（例如 `2020-08-19T15:04:00Z`）。 

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

如需详细的参考，请查看 [azcopy copy](storage-ref-azcopy-copy.md) 参考文档。

#### <a name="download-previous-versions-of-a-blob"></a>下载以前版本的 blob

如果已启用 [blob 版本控制](../blobs/versioning-enable.md)，则可下载一个或多个以前版本的 blob。 

首先，创建一个包含 [版本 id](../blobs/versioning-overview.md)列表的文本文件。 每个版本 ID 必须出现在单独的行中。 例如： 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

然后，将 [azcopy copy](storage-ref-azcopy-copy.md) 命令与选项一起使用 `--list-of-versions` 。 指定包含版本列表的文本文件的位置 (例如： `D:\\list-of-versions.txt`) 。  

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-directory-path>' --list-of-versions '<list-of-versions-file>'`|
| **示例** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt' --list-of-versions 'D:\\list-of-versions.txt'` |

每个下载文件的名称以版本 ID 开头，后面是 blob 的名称。 

## <a name="copy-blobs-between-storage-accounts"></a>在存储帐户之间复制 Blob

可以使用 AzCopy 将 Blob 复制到其他存储帐户。 复制操作是同步的，因此，当命令返回时，表示已复制所有文件。 

AzCopy 使用[服务器到服务器](/rest/api/storageservices/put-block-from-url) [API](/rest/api/storageservices/put-page-from-url)，因此，数据会直接在存储服务器之间复制。 这些复制操作不会占用计算机的网络带宽。 可以通过设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量的值来提高这些操作的吞吐量。 有关详细信息，请参阅[优化吞吐量](storage-use-azcopy-configure.md#optimize-throughput)。

> [!NOTE]
> 此方案在当前版本中存在以下限制。
>
> - 必须向每个源 URL 追加一个 SAS 令牌。 如果使用 Azure Active Directory (AD) 提供授权凭据，则只能从目标 URL 中省略 SAS 令牌。 请确保已在目标帐户中设置了适当的角色。 请参阅[选项 1：使用 Azure Active Directory](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)。
>-  高级块 Blob 存储帐户不支持访问层。 请通过将 `s2s-preserve-access-tier` 设置为 `false`（例如：`--s2s-preserve-access-tier=false`），在复制操作中省略 Blob 的访问层。

本部分包含以下示例：

> [!div class="checklist"]
> * 将 Blob 复制到另一个存储帐户
> * 将目录复制到另一个存储帐户
> * 将容器复制到另一个存储帐户
> * 将所有容器、目录和文件复制到另一个存储帐户
> * 将 blob 复制到具有索引标记的其他存储帐户

这些示例也适用于具有分层命名空间的帐户。 [Data Lake Storage 上的多协议访问](../blobs/data-lake-storage-multi-protocol-access.md)使你可以在这些帐户上使用相同的 URL 语法 (`blob.core.windows.net`)。

> [!TIP]
> 可以通过使用可选标志来调整复制操作。 下面是几个示例。
>
> |方案|标志|
> |---|---|
> |将 Blob 复制为块 Blob、页 Blob 或追加 Blob。|**--blob-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |复制到特定访问层（如存档层）。|**--block-blob-tier**=\[None\|Hot\|Cool\|Archive\]|
> |自动解压缩文件。|**--decompress**=\[gzip\|deflate\]|
> 
> 有关完整列表，请参阅[选项](storage-ref-azcopy-copy.md#options)。

### <a name="copy-a-blob-to-another-storage-account"></a>将 Blob 复制到另一个存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **示例**（分层命名空间） | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

> [!NOTE]
> 如果源 blob 具有索引标记，并且你想要保留这些标记，则必须将它们重新应用于目标 blob。 有关如何设置索引标记的信息，请参阅本文的将 [Blob 复制到另一个具有索引标记的存储帐户](#copy-between-accounts-and-add-index-tags) 部分。  

### <a name="copy-a-directory-to-another-storage-account"></a>将目录复制到另一个存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（分层命名空间）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>将容器复制到另一个存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **示例**（分层命名空间）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

> [!NOTE]
> 如果源 blob 具有索引标记，并且你想要保留这些标记，则必须将它们重新应用于目标 blob。 有关如何设置索引标记的信息，请参阅本文的将 [Blob 复制到另一个具有索引标记的存储帐户](#copy-between-accounts-and-add-index-tags) 部分。 

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>将所有容器、目录和 Blob 复制到另一个存储帐户

对具有分层命名空间的帐户使用相同的 URL 语法 (`blob.core.windows.net`)。

|    |     |
|--------|-----------|
| **语法** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **示例** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **示例**（分层命名空间）| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

> [!NOTE]
> 如果源 blob 具有索引标记，并且你想要保留这些标记，则必须将它们重新应用于目标 blob。 有关如何设置索引标记的信息，请参阅下面的将 **Blob 复制到另一个具有索引标记的存储帐户** 部分。 

<a id="copy-between-accounts-and-add-index-tags"></a>

### <a name="copy-blobs-to-another-storage-account-with-index-tags"></a>将 blob 复制到具有索引标记的其他存储帐户

可以将 blob 复制到其他存储帐户，并将 [blob 索引标记 (预览) ](../blobs/storage-manage-find-blobs.md) 添加到目标 blob。

如果使用 Azure AD 授权，则必须向安全主体分配 [存储 Blob 数据所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 角色，或者必须 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 通过自定义 azure 角色向其授予对 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 的权限。 如果你使用共享访问签名 (SAS) 令牌，则该令牌必须通过 SAS 权限提供对 blob 的标记的访问权限 `t` 。

若要添加标记，请使用 `--blob-tags` 选项以及 URL 编码的键值对。 

例如，若要添加一个键 `my tag` 和一个值 `my tag value` ，请将添加 `--blob-tags='my%20tag=my%20tag%20value'` 到目标参数。 

使用与号)  (分隔多个索引标记 `&` 。  例如，如果要添加键 `my second tag` 和值 `my second tag value` ，则完整选项字符串将为 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` 。

下面的示例演示如何使用 `--blob-tags` 选项。

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **Directory** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **容器** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **帐户** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> 如果为源指定目录、容器或帐户，则复制到目标的所有 blob 都将具有在命令中指定的相同标记。 


## <a name="synchronize-files"></a>同步文件

可将本地文件系统的内容与 Blob 容器同步。 还可以将容器和虚拟目录相互同步。 同步是单向的。 换言之，需要选择这两个终结点中哪一个是源，哪一个是目标。 同步也使用服务器到服务器 API。 本部分中提供的示例也适用于具有分层命名空间的帐户。 

> [!NOTE]
> 当前版本的 AzCopy 不会在其他源和目标之间同步（例如：文件存储或 Amazon Web Services (AWS) S3 桶）。

[sync](storage-ref-azcopy-sync.md) 命令比较文件名和上次修改时间戳。 如果将 `--delete-destination` 可选标志设置为 `true` 或 `prompt` 值，当目标目录中的文件不在源目录中存在时，会删除这些文件。

如果将 `--delete-destination` 标志设置为 `true`，AzCopy 将删除文件且不提供提示。 若要在 AzCopy 删除文件之前显示提示，请将 `--delete-destination` 标志设置为 `prompt`。

> [!NOTE]
> 为了防止意外删除，请务必在使用 `--delete-destination=prompt|true` 标志之前启用[软删除](../blobs/soft-delete-blob-overview.md)功能。

> [!TIP]
> 可以通过使用可选标志来调整同步操作。 下面是几个示例。
>
> |方案|标志|
> |---|---|
> |指定下载时应验证 MD5 哈希的严格程度。|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
> |基于模式排除文件。|**--exclude-path**|
> |指定你希望与同步相关的日志条目达到何种详细程度。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|
> 
> 有关完整列表，请参阅[选项](storage-ref-azcopy-sync.md#options)。

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>使用对本地文件系统所做的更改来更新容器

在这种情况下，容器是目标，本地文件系统是源。 

|    |     |
|--------|-----------|
| **语法** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>使用对容器所做的更改来更新本地文件系统

在这种情况下，本地文件系统是目标，容器是源。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **示例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>使用一个容器的更改来更新另一个容器

此命令中显示的第一个容器是源。 第二个是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>使用对另一个文件共享中的目录所做的更改来更新某个目录

此命令中显示的第一个目录是源。 第二个是目标。

|    |     |
|--------|-----------|
| **语法** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **示例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>后续步骤

在以下文章中查找更多示例：

- [AzCopy 入门](storage-use-azcopy-v10.md)

- [教程：使用 AzCopy 将本地数据迁移到云存储](storage-use-azcopy-migrate-on-premises-data.md)

- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)

- [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)