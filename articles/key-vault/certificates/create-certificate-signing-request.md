---
title: 在 Azure Key Vault 中创建和合并 CSR
description: 在 Azure Key Vault 中创建和合并 CSR
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 6d66648680aa14baa53372732df52a6c247a0117
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483757"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>在 Key Vault 中创建和合并 CSR

Azure Key Vault 支持将你选择的任何证书颁发机构颁发的数字证书存储在密钥保管库中。 它支持使用私钥/公钥对创建证书签名请求，可由所选的任何证书颁发机构签名。 选择的证书颁发机构可以是内部企业 CA，也可以是外部公共 CA。 证书签名请求（也称为 CSR 或证书请求）是用户向证书颁发机构 (CA) 发送的一条消息，用于请求颁发数字证书。

若要详细了解证书的常规信息，请参阅 [Azure Key Vault 证书](./about-certificates.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>在 Key Vault 中添加合作 CA 颁发的证书
Key Vault 与以下两个证书颁发机构合作，以简化证书的创建。 

|提供程序|证书类型|配置设置  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault 提供 DigiCert 的 OV 或 EV SSL 证书| [集成指南](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault 提供 GlobalSign 的 OV 或 EV SSL 证书| [集成指南](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>在 Key Vault 中添加非合作 CA 颁发的证书

以下步骤将帮助你从没有与 Key Vault 合作的证书颁发机构（例如，GoDaddy 不是受信任的密钥保管库 CA）创建证书 


### <a name="azure-powershell"></a>Azure PowerShell



1. 首先，创建证书策略。 由于此方案中所选的 CA 不受支持，Key Vault 不会代表用户注册或续订证书颁发者颁发的证书，因此 IssuerName 设置为“未知”。

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > 如果使用的在值中具有逗号 (,) 的相对可分辨名称 (RDN)，请使用单引号并将包含特殊字符的值括在双引号中。 示例：`$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`。 在此示例中，`OU` 值作为“Docs, Contoso”读取。 此格式适用于包含逗号的所有值。

2. 创建证书签名请求

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. 获取由 CA 签名的 CSR 请求。`$csr.CertificateSigningRequest` 是针对该证书的 base4 编码的证书签名请求。 可以获取此 blob，并将其转储到证书颁发者的证书请求网站中。 此步骤因 CA 而异，最好的方法是查看 CA 提供的关于如何执行此步骤的指南。 此外，还可以使用 certreq 或 openssl 之类的工具来对证书请求进行签名，并完成证书生成过程。


4. 在 Key Vault 中合并已签名的请求。证书颁发者对证书请求进行签名后，可以带回已签名的证书，并将其与在 Azure Key Vault 中创建的初始私钥/公钥对合并

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    现已成功合并证书请求。

### <a name="azure-portal"></a>Azure 门户

1.  若要为所选的 CA 生成 CSR，请导航到要添加证书的密钥保管库。
2.  在密钥保管库属性页中，选择“证书”。
3.  选择“生成/导入”选项卡。
4.  在“创建证书”屏幕上，选择以下值：
    - 证书创建方法：生成。
    - 证书名称：ContosoManualCSRCertificate。
    - 证书颁发机构 (CA) 类型：非集成 CA 颁发的证书
    - 主题：`"CN=www.contosoHRApp.com"`
    - 根据需要选择其他值。 单击“创建”。

    ![证书属性](../media/certificates/create-csr-merge-csr/create-certificate.png)  


6.  此时，将看到证书已添加到“证书”列表中。 选择刚创建的新证书。 证书的当前状态为“已禁用”，因为它尚未由 CA 颁发。
7. 单击“证书操作”选项卡，然后选择“下载 CSR” 。

   ![突出显示“下载 CSR”按钮的屏幕截图。](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.  将 .csr 文件带到 CA，以便对请求进行签名。
9.  CA 对请求进行签名后，请带回证书文件以在同一“证书操作”屏幕中合并已签名的请求。

现已成功合并证书请求。

> [!NOTE]
> 如果 RDN 值有逗号，也可以通过将值括在双引号中，在“主题”字段中添加它们，如步骤 4 中所示。
> “主题”的示例条目：`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com` 在本示例中，RDN `OU` 包含在名称中有逗号的值。 `OU` 生成的输出是“Docs, Contoso”。


## <a name="adding-more-information-to-csr"></a>向 CSR 添加更多信息

如果想要在创建 CSR 时添加更多信息，例如 - 
    - 国家/地区：
    - 城市/区域：
    - 省/自治区/直辖市：
    - 组织：
    - 组织单位：可以在创建 CSR 时通过在 subjectName 中定义这些信息来添加所有这些信息。

示例
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> 如果你正在 CSR 中请求具有所有这些详细信息的 DV 证书，则 CA 可能会拒绝该请求，因为 CA 可能无法验证请求中的所有信息。 如果你正在请求 OV 证书，那么在 CSR 中添加所有这些信息更合适。


## <a name="troubleshoot"></a>疑难解答

- 错误类型“指定的 X.509 证书内容中终端实体证书的公钥与指定私钥的公共部分不一致。请检查证书是否有效”。如果你不将 CSR 与启动的同一 CSR 请求合并，则会发生此错误。 每次创建 CSR 时，它都会创建一个在合并签名请求时必须匹配的私钥。
    
- 当 CSR 合并时，它会合并整个链吗？
    是的，它会合并整个链，前提是用户返回 p7b 文件进行合并。

- 如果颁发的证书在 Azure 门户中处于“已禁用”状态，请继续查看“证书操作”以查看该证书的错误消息。

有关详细信息，请参阅 [Key Vault REST API 中的证书操作参考](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

- 错误类型“提供的主题名称不是有效的 X500 名称”如果在 SubjectName 的值中包含任何“特殊字符”，则可能发生此错误。 请参阅 Azure 门户和 PowerShell 说明中的注释。 

## <a name="next-steps"></a>后续步骤

- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
