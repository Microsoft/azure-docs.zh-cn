---
title: 使用服务主体的 Azure 容器注册中心身份验证
description: 了解如何使用 Azure Active Directory 服务主体访问专用容器注册中心中的镜像。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 30f0eb04b4b7d07785854e3079bc6656889edec6
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854469"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>使用服务主体的 Azure 容器注册中心身份验证

可以使用 Azure Active Directory (Azure AD) 服务主体提供对容器注册中心的容器镜像 `docker push` 和 `pull` 访问权限。 通过使用服务主体，可以提供对“无外设”服务和应用程序的访问权限。

## <a name="what-is-a-service-principal"></a>什么是服务主体？

Azure AD“服务主体”提供对订阅中的 Azure 资源的访问权限。 可以将服务主体视为某个服务的用户标识，其中，“服务”是需要访问资源的任何应用程序、服务或平台。 可以为服务主体配置作用域仅限于你指定的那些资源的访问权限。 然后，可以将应用程序或服务配置为使用服务主体的凭据来访问那些资源。

在 Azure 容器注册中心的上下文中，你可以创建对 Azure 中的 Docker 注册中心具有拉取、推送和拉取或所有者权限的 Azure AD 服务主体。

## <a name="why-use-a-service-principal"></a>为何使用服务主体？

通过使用 Azure AD 服务主体，可以针对专用容器注册中心提供具有作用域的访问权限。 可以为每个应用程序或服务创建不同的服务主体，每个服务主体对注册中心具有定制的访问权限。 而且，因为你可以避免在各个服务和应用程序之间共享凭据，因此可以仅针对你选择的服务主体（和涉及的应用程序）滚动更新凭据或撤销访问权限。

例如，Web 应用程序可以使用仅为其提供了镜像 `pull` 访问权限的服务主体，而生成系统可以使用为其提供了 `push` 和 `pull` 访问权限的服务主体。 如果应用程序开发变更了人手，则你可以滚动更新其服务主体凭据且不会影响生成系统。

## <a name="when-to-use-a-service-principal"></a>何时使用服务主体

在**无外设方案**中，应当使用服务主体来提供注册中心访问。 即，任何必须以自动或其他无人参与方式来推送或拉取容器镜像的应用程序、服务或脚本。

若要对注册中心进行个人访问，例如手动将容器镜像拉取到开发工作站时，则应当改用你自己的 [Azure AD 标识](container-registry-authentication.md#individual-login-with-azure-ad)进行注册中心访问（例如使用 [az acr login][az-acr-login]）。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>示例脚本

可以在 GitHub 上找到前面的 Azure CLI 示例脚本以及 Azure PowerShell 所对应的版本：

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>后续步骤

在创建服务主体并向其授予对容器注册中心的访问权限后，可以在应用程序和服务中使用其凭据进行注册中心交互。

虽然对个体应用程序进行配置以使用服务主体凭据不在本文的讨论范围内，但是可以在此处找到针对一些特定服务和平台的说明：

* [使用 Azure 容器注册中心从 Azure Kubernetes 服务 (AKS) 进行身份验证](container-registry-auth-aks.md)
* [使用 Azure 容器注册中心从 Azure 容器实例 (ACI) 进行身份验证](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
