---
title: '有关 Azure Kubernetes Service 上的机密节点支持的常见问题 (AKS) '
description: 查找有关 Azure Kubernetes Service (AKS) & Azure 机密计算 (ACC) 节点支持的常见问题的解答。
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 550995f0be3d634e7e9f24a8bf6826916003308e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653391"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes Service 上的机密计算节点的常见问题 (AKS) 

本文解决了有关 Azure Kubernetes Service (AKS) 上基于 Intel SGX 的机密计算节点的常见问题。 如果你有任何疑问，请发送电子邮件 **acconaks@microsoft.com** 。

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>AKS 中是否有机密计算节点？ ###
是

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>什么是证明，我们如何才能对 enclaves 中运行的应用进行认证？ ###
证明是演示和验证是否已在特定硬件平台上正确实例化某个软件的过程。 它还可确保其证据是可验证的，以确保它在安全平台中运行且未被篡改。 [详细](attestation.md) 了解如何对 enclave 应用进行证明。

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>能否使用 Azure 机密计算 AKS 群集启用加速网络？ ###
不是。 在 AKS 上构成机密计算节点的 DCSv2 虚拟机上不支持加速网络。 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>我是否可以将现有的容器化应用程序与 Azure 机密计算一起用于 AKS？ ###
是的，请查看 [机密容器页](confidential-containers.md) ，详细了解平台启用程序。

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>适用于机密节点的 AKS 映像上的 Intel SGX 驱动程序版本是什么版本？ ### 
目前，Azure 机密计算 DCSv2 Vm 与 Intel SGX DCAP 1.33 一起安装。 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>我是否可以将安装后脚本/自定义驱动程序注入到由 AKS 设置的节点？ ###
不是。 [基于 AKS 的机密计算节点](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 支持允许自定义安装和完全控制 Kubernetes 控制平面的机密计算节点。
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>我应该使用 Docker 基本映像开始使用 enclave 应用程序吗？ ###
不同的启用码 (Isv 和 OSS 项目) 提供启用机密容器的方式。 查看 [机密容器页](confidential-containers.md) ，了解更多详细信息以及对实现的各个引用。

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>能否使用其他标准 AKS Sku 运行 ACC 节点 () 生成一个异类节点池群集？ ###

是的，可以在同一 AKS 群集中运行不同的节点池，包括 ACC 节点。 若要将 enclave 应用程序面向特定节点池，请考虑添加节点选择器或应用 EPC 限制。 请参阅 [此处](confidential-nodes-aks-get-started.md)有关机密节点的快速入门的更多详细信息。

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>能否通过 ACC 运行 Windows 节点和 windows 容器？ ###
目前没有。 *acconaks@microsoft.com* 如果你有 Windows 节点或容器需求，请联系产品团队。 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>如果容器的大小大于可用的 EPC 内存，该怎么办？ ###
EPC 内存适用于在 enclave 中进行了编程以执行的应用程序部分。 容器的总大小并不是将其与最大可用 EPC 内存进行比较的正确方法。 事实上，使用 SGX 的 DCSv2 计算机允许最大的 VM 内存 32 GB，其中应用程序的不受信任部分将会使用该内存。 但是，如果容器消耗的内存超出可用的数目，则在 enclave 中运行的程序的部分性能可能会受到影响。

为了更好地管理辅助节点中的 EPC 内存，请考虑通过 Kubernetes 的基于 EPC 内存的限制管理。 请按照下面的示例进行参考。

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>如果我的 enclave 消耗的内存超过最大可用值，会发生什么？ ###

在同一 Vm 或辅助角色节点中的 enclave 应用程序之间共享可用的 EPC 内存总量。 如果你的应用程序使用的 EPC 内存大于可用的，则应用程序性能可能会受到影响。 出于此原因，我们建议你在部署 yaml 文件中设置每个应用程序的 toleration，以更好地管理每个工作节点的可用 EPC 内存，如以上示例中所示。 或者，你始终可以选择在辅助角色节点池 VM 大小上向上移动，或者添加更多节点。 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>为什么无法在 enclave 应用程序中执行分叉 () 和 exec 来运行多个进程？ ###

目前，Azure 机密计算 DCsv2 SKU Vm 支持在 enclave 中执行的程序的单个地址空间。 单进程是围绕高安全性设计的当前限制。 但是，机密容器启用码可能具有替代方法来克服此限制。
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>是否自动安装任何其他 daemonset 以公开 SGX 驱动程序？ ###

是的。 Daemonset 的名称为 sgx 设备插件。 在 [此处](confidential-nodes-aks-overview.md)了解有关各自用途的详细信息。  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>应为机密计算节点选择的 VM SKU 是什么？ ###

DCSv2 Sku。 [支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)中提供了[DCSv2 sku](../virtual-machines/dcv2-series.md) 。

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>我是否仍然可以在机密计算节点上计划并运行非 enclave 容器？ ###

是的。 Vm 还具有可运行标准容器工作负荷的常规内存。 在决定部署模型之前，请考虑应用程序的安全和威胁模型。
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>是否可以通过 Azure 门户通过 DCSv2 节点池来预配 AKS？ ###

是的。 还可以使用 Azure CLI，如 [此处](confidential-nodes-aks-get-started.md)所述。

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>支持哪些 Ubuntu 版本和 VM 生成？ ###
第2代上的18.04。 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>我们能更改 AKS 上的当前 Intel SGX DCAP 驱动程序版本吗？ ###

不是。 若要执行任何自定义安装，建议选择 [AKS-Engine 机密计算辅助角色节点](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) 部署。 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>你支持和建议的 Kubernetes 版本是什么？ ###

我们支持并建议 Kubernetes 版本1.16 及更高版本。 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>产品的已知当前限制是什么？ ###

- 仅支持 Ubuntu 18.04 Gen 2 VM 节点 
- 不支持 Windows 节点或 Windows 容器
- 不支持基于 EPC 内存的水平 Pod 自动缩放。 支持 CPU 和基于内存的常规缩放。
- 当前不支持用于机密应用的 AKS 上的 Dev 空间

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>只会在 enclave 中为机密计算加载签名和受信任的映像？ ###
在 enclave 初始化期间不是本机的，而是通过证明可以验证 "是"。 参考 [此处](../attestation/basic-concepts.md#benefits-of-policy-signing)。 

### <a name="next-steps"></a>后续步骤
查看 [机密容器页](confidential-containers.md) ，了解有关机密容器的更多详细信息。
