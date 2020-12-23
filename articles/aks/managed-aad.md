---
title: 在 Azure Kubernetes 服务中使用 Azure AD
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure AD
services: container-service
ms.topic: article
ms.date: 08/26/2020
ms.author: thomasge
ms.openlocfilehash: f229075d0bad4f9522e02e30bdabc1d42bb086cf
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684179"
---
# <a name="aks-managed-azure-active-directory-integration"></a>AKS-托管 Azure Active Directory 集成

AKS 托管 Azure AD 集成旨在简化 Azure AD 集成体验，其中用户以前需要创建客户端应用、服务器应用，并需要 Azure AD 租户授予目录读取权限。 在新版本中，AKS 资源提供程序为你管理客户端应用和服务器应用。

## <a name="azure-ad-authentication-overview"></a>Azure AD 身份验证概述

群集管理员可以根据用户的标识或目录组成员身份，配置 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 。 使用 OpenID Connect 向 AKS 群集提供 Azure AD 身份验证。 OpenID Connect 是构建在 OAuth 2.0 协议顶层的标识层。 有关 OpenID Connect 的详细信息，请参阅 [Open ID Connect 文档][open-id-connect]。

有关详细信息，请参阅 [Azure Active Directory 集成概念文档](concepts-identity.md#azure-active-directory-integration)中的 Azure AD 集成流。

## <a name="limitations"></a>限制 

* 无法禁用 AKS 管理的 Azure AD 集成
* AKS 管理的 Azure AD 集成不支持非 Kubernetes RBAC 启用的群集
* 不支持更改与 AKS 托管 Azure AD 集成关联的 Azure AD 租户

## <a name="prerequisites"></a>先决条件

* Azure CLI 版本2.11.0 或更高版本
* Kubectl，最低版本为 [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) 或 [kubelogin](https://github.com/Azure/kubelogin)
* 如果使用的是 [helm](https://github.com/helm/helm)，则 helm 3.3 的最低版本。

> [!Important]
> 必须使用最低版本为1.18.1 或 kubelogin 的 Kubectl。 如果不使用正确的版本，你会注意到身份验证问题。

若要安装 kubectl 和 kubelogin，请使用以下命令：

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

对于其他操作系统，请遵循[这些说明](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。


## <a name="before-you-begin"></a>在开始之前

对于群集，需要 Azure AD 组。 此组需要作为群集的管理员组，以便授予群集管理员权限。 您可以使用现有的 Azure AD 组，或创建一个新组。 记录 Azure AD 组的对象 ID。

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

若要为群集管理员创建新的 Azure AD 组，请使用以下命令：

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>创建已启用 Azure AD 的 AKS 群集

使用以下 CLI 命令创建 AKS 群集。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

创建 AKS 群集，并为 Azure AD 组启用管理访问权限

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功创建 AKS 托管 Azure AD 群集的响应正文中包含以下部分
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

创建群集后，即可开始访问该群集。

## <a name="access-an-azure-ad-enabled-cluster"></a>访问已启用 Azure AD 的群集

需要 [Azure Kubernetes Service 群集用户](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) 内置角色才能执行以下步骤。

获取用户凭据以访问群集：
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
按照说明进行登录。

使用 kubectl get 节点命令查看群集中的节点：

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
配置 azure [RBAC)  (azure 基于角色的访问控制 ](./azure-ad-rbac.md) ，为群集配置其他安全组。

## <a name="troubleshooting-access-issues-with-azure-ad"></a>排查 Azure AD 的访问问题

> [!Important]
> 下面所述的步骤将绕过普通 Azure AD 组身份验证。 仅在紧急情况下使用它们。

如果你被永久阻止，不能访问具有群集访问权限的有效 Azure AD 组，你仍可以获取管理员凭据以直接访问群集。

若要执行这些步骤，你需要具有 [Azure Kubernetes Service 群集管理员](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) 内置角色的访问权限。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>在现有群集上启用 AKS 管理的 Azure AD 集成

可以在现有的启用了 Kubernetes RBAC 的群集上启用 AKS 管理的 Azure AD 集成。 确保将管理组设置为在群集上保留访问权限。

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

成功激活 AKS 托管的 Azure AD 群集后，响应正文中将包含以下部分

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

请按照 [此处][access-cluster]的步骤，重新下载用户凭据以访问群集。

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>升级到 AKS-托管 Azure AD 集成

如果群集使用旧的 Azure AD 集成，则可以升级到 AKS 托管的 Azure AD 集成。

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

成功迁移 AKS 托管 Azure AD 群集的响应正文中包含以下部分

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

如果要访问群集，请按照 [此处][access-cluster]的步骤操作。

## <a name="non-interactive-sign-in-with-kubelogin"></a>Kubelogin 中的非交互式登录

有一些非交互式方案，如持续集成管道，当前不适用于 kubectl。 你可以使用 [`kubelogin`](https://github.com/Azure/kubelogin) 来访问包含非交互式服务主体登录的群集。

## <a name="next-steps"></a>后续步骤

* 了解 [适用于 Kubernetes 授权的 AZURE RBAC 集成][azure-rbac-integration]
* 了解 [Azure AD 与 KUBERNETES RBAC 集成][azure-ad-rbac]。
* 使用 [kubelogin](https://github.com/Azure/kubelogin) 访问 kubectl 中不可用的 Azure 身份验证功能。
* 了解有关 [AKS 和 Kubernetes 标识概念][aks-concepts-identity]的详细信息。
* 使用 [Azure 资源管理器 (ARM) 模板 ][aks-arm-template] 来创建启用 AKS Azure AD 管理的群集。

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
