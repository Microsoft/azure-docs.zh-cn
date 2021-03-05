---
title: 使用 Velero 创建 Azure Red Hat OpenShift 4 群集应用程序备份
description: 了解如何使用 Velero 创建 Azure Red Hat OpenShift 群集应用程序的备份
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc
ms.openlocfilehash: 42ad24e4421b75a3942879bcac8f99b3c8b8138a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180949"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>创建 Azure Red Hat OpenShift 4 群集应用程序备份

本文介绍如何准备环境，以创建 Azure Red Hat OpenShift 4 群集应用程序备份。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 安装必备组件并安装所需的工具
> * 创建 Azure Red Hat OpenShift 4 应用程序备份

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="before-you-begin"></a>准备阶段

### <a name="install-velero"></a>安装 Velero

若要在系统上 [安装](https://velero.io/docs/main/basic-install/) Velero，请遵循适用于你的操作系统的建议过程。

### <a name="set-up-azure-storage-account-and-blob-container"></a>设置 Azure 存储帐户和 Blob 容器

此步骤将在 ARO 群集的资源组之外创建一个资源组。  此资源组将允许备份持久保存，并可以将应用程序还原到新群集。

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>设置 Velero 的权限

### <a name="create-service-principal"></a>创建服务主体

Velero 需要权限才能执行备份和还原。 在创建服务主体时，你将为 Velero 授予访问在上一步中定义的资源组的权限。 此步骤将获取群集的资源组：

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上安装 Velero

此步骤会将 Velero 安装到其自己的项目中，以及执行备份和还原 Velero 所需的 [自定义资源定义](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) 。 请确保已成功登录到 Azure Red Hat OpenShift v4 群集。


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>使用 Velero 创建备份

若要使用 Velero 创建应用程序备份，需要包含此应用程序所在的命名空间。  如果你有一个 `nginx-example` 命名空间，并且想要在备份中包含该命名空间中的所有资源，请在终端中运行以下命令：

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
可以通过运行以下操作来检查备份的状态：

```bash
oc get backups -n velero <name of backup> -o yaml
```

将输出成功的备份 `phase:Completed` ，并将对象存放在存储帐户的容器中。

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>使用 Velero 创建备份以包括快照

若要创建具有 Velero 的应用程序备份以包含应用程序的持久卷，则需要包括应用程序所在的命名空间，并在 `snapshot-volumes=true` 创建备份时包括该标志

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

可以通过运行以下操作来检查备份的状态：

```bash
oc get backups -n velero <name of backup> -o yaml
```

带有输出的成功备份 `phase:Completed` 和对象将位于存储帐户中的容器内。

有关如何使用 Velero 创建备份和还原的详细信息，请参阅以 [本机方式备份 OpenShift 资源](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Red Hat OpenShift 4 群集应用程序的备份。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Velero 创建 OpenShift v4 群集应用程序备份
> * 使用 Velero 创建包含快照的 OpenShift v4 群集应用程序备份


转到下一篇文章，了解如何创建 Azure Red Hat OpenShift 4 群集应用程序还原。

* [创建 Azure Red Hat OpenShift 4 群集应用程序还原](howto-create-a-restore.md)
* [创建 Azure Red Hat OpenShift 4 群集应用程序还原，包括快照](howto-create-a-restore.md)
