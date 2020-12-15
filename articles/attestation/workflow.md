---
title: Azure 证明工作流
description: Azure 证明的工作流。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 09d793f3d8ed544a386a362677f24be6d18673d7
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748717"
---
# <a name="workflow"></a>工作流

Microsoft Azure 证明接收来自 enclave 的证据，并根据 Azure 安全基准和可配置策略评估证据。 成功验证后，Azure 证明会生成证明令牌来确认 enclave 的可信度。

Azure 证明工作流涉及以下参与者：

- **信赖方**：该组件依赖 Azure 证明来验证 enclave 有效性。 
- **客户端**：该组件从 enclave 收集信息并将请求发送到 Azure 证明。 
- **Azure 证明**：该组件接受来自客户端的 enclave 证据，对其进行验证，然后将证明令牌返回给客户端


## <a name="intel-software-guard-extensions-sgx-enclave-validation-work-flow"></a>Intel® Software Guard Extensions (SGX) enclave 验证工作流

下面是典型的 SGX enclave 证明工作流的一般步骤（使用 Azure 证明）：

1. 客户端从 enclave 收集证据。 证据是有关 enclave 环境和在 enclave 内运行的客户端库的信息。
1. 客户端具有引用 Azure 证明实例的 URI。 客户端将证据发送到 Azure 证明。 提交给提供程序的确切信息取决于 enclave 类型。
1. Azure 证明对提交的信息进行验证，并根据配置的策略对其进行评估。 如果验证成功，Azure 证明会颁发证明令牌并将其返回给客户端。 如果此步骤失败，Azure 证明会向客户端报告一个错误。 
1. 客户端会将证明令牌发送给信赖方。 信赖方将调用 Azure 证明的公钥元数据终结点来检索签名证书。 然后验证证明令牌的签名，并确保 enclave 可信度。 

![SGX enclave 验证流](./media/sgx-validation-flow.png)

> [!Note]
> 在 [2018-09-01-preview](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/attestation/data-plane/Microsoft.Attestation/stable/2018-09-01-preview) API 版本中发送证明请求时，客户端需要将证据连同 Azure AD 访问令牌一起发送到 Azure 证明。

## <a name="next-steps"></a>后续步骤
- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)
