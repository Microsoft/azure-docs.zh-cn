---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3f71eb1045f08a2eb6121c8c1c2ce86685606acf
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986881"
---
|名称 |说明 |策略 |版本 |
|---|---|---|---|
|[审核带有不安全密码安全设置的计算机](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |此计划可部署策略要求，并审核带有不安全的密码安全设置的计算机。 有关 Guest Configuration 策略的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[部署先决条件以在虚拟机上启用来宾配置策略](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |此计划添加系统分配的托管标识，并向符合来宾配置策略监视条件的虚拟机部署平台相应的来宾配置扩展。 这是所有来宾配置策略的先决条件，在使用任何来宾配置策略之前必须被分配到策略分配范围。 有关来宾配置的详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |4 |1.0.0-preview |
|[Windows 计算机应符合 Azure 安全基线的要求](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |此计划审核具有不符合 Azure 安全基线的设置的 Windows 计算机。 有关详细信息，请访问 [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
