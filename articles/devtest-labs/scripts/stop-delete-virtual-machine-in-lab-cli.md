---
title: Azure CLI - 在实验室中停止并删除虚拟机
description: 本文提供了一个 Azure CLI 脚本，该脚本在 Azure 开发测试实验室中停止并删除实验室中的虚拟机。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198194"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>使用 Azure CLI 在 Azure 开发测试实验室的实验室中停止并删除虚拟机

此 Azure CLI 脚本在实验室中停止并删除虚拟机 (VM)。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | 在实验室中停止虚拟机 (VM)。 此操作可能需要一段时间才能完成。 |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | 在实验室中删除虚拟机 (VM)。 此操作可能需要一段时间才能完成。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可以在 [Azure 实验室服务 CLI 示例](../samples-cli.md)中找到其他 Azure 实验室服务 CLI 脚本示例。
