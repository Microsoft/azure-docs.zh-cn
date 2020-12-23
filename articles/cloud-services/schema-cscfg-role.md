---
title: Azure 云服务角色架构 | Microsoft Docs
description: 服务配置文件的 Role 元素指定要为每个角色、配置值和证书指纹部署多少个角色实例。
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79528415"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure 云服务配置角色架构

配置文件的 `Role` 元素指定要为服务中的每个角色部署的角色实例数，任何配置设置的值，以及与角色关联的任何证书的缩略图。

有关 Azure 服务配置架构的详细信息，请参阅[云服务（经典）配置架构](schema-cscfg-file.md)。 有关 Azure 服务定义架构的详细信息，请参阅[云服务（经典）定义架构](schema-csdef-file.md)。

##  <a name="role-element"></a><a name="Role"></a> Role 元素
下面的示例显示了 `Role` 元素及其子元素。

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

下表介绍了 `Role` 元素的属性。

| 属性 | 说明 |
| --------- | ----------- |
| name   | 必需。 指定角色的名称。 该名称必须与在服务定义文件中为角色提供的名称匹配。|
| vmName | 可选。 指定虚拟机的 DNS 名称。 该名称不能超过 10 个字符。|

下表介绍了 `Role` 元素的子元素。

| 元素 | 说明 |
| ------- | ----------- |
| Instances | 必需。 指定要为角色部署的实例数。 实例数是由用于 `count` 属性的整数定义的。|
| 设置   | 可选。 在角色的设置集合中指定设置名称和值。 设置名称是由用于 `name` 属性的字符串定义的，设置值是由用于 `value` 属性的字符串定义的。|
| Certificate | 可选。 指定要与角色关联的服务证书的名称、指纹和算法。 证书名称是由用于 `name` 属性的字符串定义的。 证书指纹是由用于 `thumbprint` 属性的一串不含空格的十六进制数字定义的。 必须使用数字和大写字母字符来表示十六进制数字。 证书算法是由用于 `thumbprintAlgorithm` 属性的字符串定义的。|

## <a name="see-also"></a>另请参阅
[云服务（经典）配置架构](schema-cscfg-file.md)