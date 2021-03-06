---
title: 安装适用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具 |Microsoft Docs
description: 提供有关安装可与 Azure NetApp 文件一起使用的 Azure 应用程序一致快照工具的指南。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 00aaa5bdc0d48adb735679fc4a71b3431970ef09
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737161"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a> (预览版安装 Azure 应用程序一致性快照工具) 

本文提供了有关安装可与 Azure NetApp 文件一起使用的 Azure 应用程序一致快照工具的指南。

## <a name="introduction"></a>简介

可下载的自助安装程序旨在使快照工具易于设置，并以非根用户权限运行 (例如 azacsnap) 。 安装程序将设置用户，并将快照工具放入 "用户" `$HOME/bin` 子目录 (默认值为 " `/home/azacsnap/bin`) "。
自助安装程序将尝试根据执行安装 (的用户的配置为所有文件确定正确的设置和路径，例如 root) 。 如果 (启用与存储的通信并且 SAP HANA) 以 root 身份运行，则安装会将私钥复制 `hdbuserstore` 到备份用户的位置。 但是，在安装后，熟悉的管理员可以通过与存储后端和 SAP HANA 进行通信的步骤手动完成。

## <a name="prerequisites-for-installation"></a>安装的先决条件

按照准则设置和执行快照和灾难恢复命令。 建议在安装和使用快照工具之前，以 root 身份完成以下步骤。

1. **操作系统已修补**：请参阅 [如何在 Azure 上) 安装和配置 SAP HANA (大型实例](../virtual-machines/workloads/sap/hana-installation.md#operating-system)。
1. **已设置时间同步**。 客户需要提供 NTP 兼容时间服务器，并相应地配置操作系统。
1. **已安装 hana** ：请参阅 hana [数据库上的 SAP NetWeaver 安装](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database)中的 hana 安装说明。
1. 有关更多) 详细信息，请 **[与存储](#enable-communication-with-storage)** (有关的详细信息，请参阅单独的部分：客户必须使用私钥/公钥对设置 SSH，并为在存储后端安装的快照工具的每个节点提供公共密钥。
   1. **对于 Azure NetApp 文件 (请参阅单独的部分了解详细信息)**：客户必须生成服务主体身份验证文件。
   1. **对于 Azure 大型实例 (请参阅单独的部分以了解详细信息)**：客户必须使用私钥/公钥对设置 SSH，并为每个节点提供公钥，以便在存储后端将快照工具的计划执行到 Microsoft 操作。

      通过使用 SSH 连接到某个节点 (例如) 来对此进行测试 `ssh -l <Storage UserName> <Storage IP Address>` 。
      键入 `exit` 以注销存储提示。

      Microsoft 操作会在预配时提供存储用户和存储 IP。
  
1. **[启用与 SAP HANA 的通信](#enable-communication-with-sap-hana)** (有关更多详细信息，请参阅单独的部分) ：客户必须设置适当的 SAP HANA 用户，该用户具有执行快照所需的权限。
   1. 此设置可以从命令行进行测试，如下所示： `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - 以上示例适用于与 SAP HANA 的非 SSL 通信。

## <a name="enable-communication-with-storage"></a>启用与存储的通信

本部分介绍如何启用与存储的通信。

### <a name="azure-netapp-files"></a>Azure NetApp 文件

创建 RBAC 服务主体

1. 在 Azure Cloud Shell 会话中，请确保你已登录到要在默认情况下与服务主体关联的订阅：

    ```azurecli-interactive
    az account show
    ```

1. 如果订阅不正确，请使用

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. 按照以下示例使用 Azure CLI 创建服务主体

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. 这会生成类似于以下示例的输出：

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > 此命令会在订阅级别自动将 RBAC 参与者角色分配给服务主体，你可以将范围缩小到你的测试将在其中创建资源的特定资源组。

1. 将输出内容剪切并粘贴到名为的文件中，该文件与 `azureauth.json` 命令存储在同一系统上 `azacsnap` ，并使用适当的系统权限保护文件。

### <a name="azure-large-instance"></a>Azure 大型实例

与存储后端的通信通过加密的 SSH 通道执行。 以下示例步骤说明了如何为此通信设置 SSH 的相关指导。

1. 修改 `/etc/ssh/ssh_config` 文件

    请参阅以下输出，其中已 `MACs hmac-sha1` 添加行：

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. 创建私钥/公钥对

    使用以下示例命令生成密钥对，生成密钥时不要输入密码。

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. 将公钥发送到 Microsoft 操作

    将 `cat /root/.ssh/id_rsa.pub` 以下 (示例) 的命令的输出发送到 Microsoft 操作，以使快照工具能够与存储子系统通信。

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>启用与 SAP HANA 的通信

快照工具与 SAP HANA 进行通信，并需要具有相应权限的用户启动并释放数据库保存点。 下面的示例演示如何安装 SAP HANA v2 用户和与 `hdbuserstore` SAP HANA 数据库的通信。

下面的示例命令在 SAP HANA 2 上的 SYSTEMDB 中设置用户 (AZACSNAP) 。
数据库中，根据需要更改 IP 地址、用户名和密码：

1. 连接到 SYSTEMDB 以创建用户

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. 创建用户

    此示例在 SYSTEMDB 中创建 AZACSNAP 用户。

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. 授予用户权限

    此示例设置 AZACSNAP 用户的权限，以允许执行数据库一致的存储快照。

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *可选* -防止用户的密码过期

    > [!NOTE]
    > 在进行此更改之前，请检查公司策略。

   此示例将禁用 AZACSNAP 用户的密码过期，如果不进行此更改，用户的密码将过期，从而导致无法正确执行快照。  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. 设置 SAP HANA 安全用户存储 (更改密码) 此示例使用 `hdbuserstore` Linux shell 中的命令设置 SAP HANA Secure 用户存储区。

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. 检查 SAP HANA 安全的用户存储以检查安全用户存储是否设置正确，使用 `hdbuserstore` 命令列出类似于以下示例的输出。 SAP 网站上提供了有关使用的更多详细信息 `hdbuserstore` 。

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="additional-instructions-for-using-the-log-trimmer-sap-hana-20-and-later"></a>使用 log 修剪器 (SAP HANA 2.0 及更高版本的其他说明) 

如果使用 log 修剪器，则以下示例命令将在 SAP HANA 2.0 数据库系统上的租户数据库 () 中设置一个用户 (AZACSNAP) 。 请记得根据需要更改 IP 地址、用户名和密码：

1. 连接到租户数据库以创建用户，特定于租户的详细信息是 `<IP_address_of_host>` 和 `<SYSTEM_USER_PASSWORD>` 。  另外，请注意 `30015` 与租户数据库通信所需的端口 () 。

    ```bash
    hdbsql -n <IP_address_of_host>:30015 - i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output  
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql TENANTDB=>
    ```

1. 创建用户

    此示例在 SYSTEMDB 中创建 AZACSNAP 用户。

    ```sql
    hdbsql TENANTDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. 授予用户权限

    此示例设置 AZACSNAP 用户的权限，以允许执行数据库一致的存储快照。

    ```sql
    hdbsql TENANTDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *可选* -防止用户的密码过期

    > [!NOTE]
    > 在进行此更改之前，请检查公司策略。

   此示例将禁用 AZACSNAP 用户的密码过期，如果不进行此更改，用户的密码将过期，从而导致无法正确执行快照。  

   ```sql
   hdbsql TENANTDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

> [!NOTE]  
> 为所有租户数据库重复这些步骤。 可以对 SYSTEMDB 使用以下 SQL 查询来获取所有租户的连接详细信息。

```sql
SELECT HOST, SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%'
```

请参阅下面的示例查询和输出。

```bash
hdbsql -jaxC -n 10.90.0.31:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> " SELECT HOST,SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%' "
```

```output
sapprdhdb80,30013,SYSTEMDB
sapprdhdb80,30015,H81
sapprdhdb80,30041,H82
```

### <a name="using-ssl-for-communication-with-sap-hana"></a>使用 SSL 与 SAP HANA 通信

该 `azacsnap` 工具利用 SAP HANA 的 `hdbsql` 命令与 SAP HANA 通信。 这包括在加密与 SAP HANA 通信时使用 SSL 选项。 `azacsnap` 使用 `hdbsql` 命令的 SSL 选项，如下所示。

使用选项时，始终使用以下 `azacsnap --ssl` 内容：

- `-e` -启用 TLS encryptionTLS/SSL 加密。 服务器选择可用的最高级别。
- `-ssltrustcert` -指定是否验证服务器的证书。
- `-sslhostnameincert "*"` -指定用于验证服务器标识的主机名。 如果将指定 `"*"` 为主机名，则不会验证服务器的主机名。

SSL 通信还要求密钥存储和信任存储区文件。  虽然可以将这些文件存储在 Linux 安装上的默认位置，为了确保将正确的密钥材料用于各种 SAP HANA 系统 (即，在不同的密钥存储和信任存储区文件用于每个 SAP HANA 系统的情况下，) `azacsnap` 会要求在配置文件中指定的位置存储密钥存储和信任存储区文件 `securityPath` `azacsnap` 。

#### <a name="key-store-files"></a>密钥存储文件

- 如果使用多个具有相同密钥材料的 Sid，则可以更轻松地在配置文件中定义的 securityPath 位置创建链接 `azacsnap` 。  请确保使用 SSL 的每个 SID 都存在这些值。
  - 对于 openssl：
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - 对于 commoncrypto：
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- 如果使用多个具有每个 SID 的不同密钥材料的 Sid，请复制 (或移动) 文件并将其重命名为 Sid 配置文件中定义的 securityPath 位置 `azacsnap` 。
  - 对于 openssl：
    - `mv key.pem <securityPath>/<SID>_keystore`
  - 对于 commoncrypto：
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

`azacsnap`调用时 `hdbsql` ，它将添加 `-sslkeystore=<securityPath>/<SID>_keystore` 到命令行。

#### <a name="trust-store-files"></a>信任存储区文件

- 如果使用多个具有相同密钥材料的 Sid，则会创建硬链接到配置文件中定义的 securityPath 位置 `azacsnap` 。  请确保使用 SSL 的每个 SID 都存在这些值。
  - 对于 openssl：
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - 对于 commoncrypto：
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- 如果使用多个具有每个 SID 的不同密钥材料的 Sid，请复制 (或移动) 文件并将其重命名为 Sid 配置文件中定义的 securityPath 位置 `azacsnap` 。
  - 对于 openssl：
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - 对于 commoncrypto：
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> `<SID>`文件名的组成部分必须是 SAP HANA 系统标识符全部大写 (例如，、等 `H80` `PR1` ) 

`azacsnap`调用时 `hdbsql` ，它将添加 `-ssltruststore=<securityPath>/<SID>_truststore` 到命令行。

因此，在 `azacsnap -c test --test hana --ssl openssl` `SID` `H80` 配置文件中运行时，它将执行 `hdbsql` 以下连接：

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\`字符是命令行换行，用于提高在命令行上传递的多个参数的清晰度。

## <a name="installing-the-snapshot-tools"></a>安装快照工具

可下载的自助安装程序旨在使快照工具易于设置，并以非根用户权限运行 (例如 azacsnap) 。 安装程序将设置用户，并将快照工具放入 "用户" `$HOME/bin` 子目录 (默认值为 " `/home/azacsnap/bin`) "。

自助安装程序将尝试根据执行安装 (的用户的配置为所有文件确定正确的设置和路径，例如 root) 。 如果先前的安装步骤 (启用与存储的通信，并且 SAP HANA) 以 root 身份运行，则安装会将私钥和复制 `hdbuserstore` 到备份用户的位置。 但是，在安装后，熟悉的管理员可以通过与存储后端和 SAP HANA 进行通信的步骤手动完成。

> [!NOTE]
> 对于 Azure 大型实例安装的早期 SAP HANA，预安装快照工具的目录为 `/hana/shared/<SID>/exe/linuxx86_64/hdb` 。

完成 [必备步骤](#prerequisites-for-installation) 后，现在可以使用自助安装程序安装快照工具，如下所示：

1. 将下载的自助安装程序复制到目标系统。
1. 以用户身份执行自助安装程序 `root` ，请参阅以下示例。 如有必要，请使用命令使文件可执行 `chmod +x *.run` 。

如果运行不带任何参数的自助安装程序命令，将显示有关使用安装程序安装快照工具的帮助，如下所示：

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> 自助安装程序提供了一个选项，用于从捆绑包中提取 ( X) 快照工具，而无需执行任何用户创建和设置。 这允许有经验的管理员手动完成安装步骤，或复制命令以升级现有安装。

### <a name="easy-installation-of-snapshot-tools-default"></a>简单地安装快照工具 (默认) 

安装程序已设计为在 Azure 上快速安装 SAP HANA 的快照工具。 默认情况下，如果仅使用-I 选项运行安装程序，它将执行以下步骤：

1. 创建快照用户 ' azacsnap '，主目录，并设置组成员身份。
1. 配置 azacsnap 用户的登录名 `~/.profile` 。
1. 搜索 filesystem 对于要添加到 azacsnap 的目录 `$PATH` ，这些通常是指向 SAP HANA 工具的路径，例如 `hdbsql` 和 `hdbuserstore` 。
1. 在 filesystem 中搜索要添加到 azacsnap 的目录 `$LD_LIBRARY_PATH` 。 许多命令需要设置库路径才能正确执行，这会为已安装的用户配置该路径。
1. 从 "root" 用户 (运行安装) 的用户复制用于 azacsnap 的后端存储的 SSH 密钥。 这假定 "root" 用户已配置与存储的连接
    - 请参阅 "[启用与存储的通信](#enable-communication-with-storage)" 部分。
1. 复制目标用户的 SAP HANA 连接安全用户存储 azacsnap。 这假定 "root" 用户已配置安全用户存储区–请参阅 "启用与 SAP HANA 通信" 部分。
1. 快照工具将被提取到中 `/home/azacsnap/bin/` 。
1. 中的命令 `/home/azacsnap/bin/` 将其权限设置 (所有权和可执行文件位等 ) 。

下面的示例演示了在运行时安装了默认安装选项的正确输出。

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>卸载快照工具

如果使用默认设置安装了快照工具，则仅卸载需要删除用户已安装的命令 (默认 = azacsnap) 。

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>手动安装快照工具

在某些情况下，需要手动安装这些工具，但建议使用安装程序的默认选项来简化此过程。

每行以字符开头 `#` 演示了由根用户运行的字符后面的示例命令。 `\`行末尾的是 shell 命令的标准行继续符。

作为根超级用户，可按如下所示实现手动安装：

1. 获取 "sapsys" 组 ID，在这种情况下，组 ID = 1010

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. 使用步骤1中的组 ID 创建快照用户 ' azacsnap '、主目录和设置组成员身份。

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. 请确保用户 azacsnap 的登录名 `.profile` 存在。

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. 搜索要添加到 azacsnap 中的目录的文件系统 $PATH，它们通常是指向 SAP HANA 工具的路径，例如 `hdbsql` 和 `hdbuserstore` 。

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 将更新的路径添加到用户的配置文件

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. 在文件系统中搜索要添加到 azacsnap $LD _LIBRARY_PATH 的目录。

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 将更新的库路径添加到用户的配置文件

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. 在 Azure 大型实例上
    1. 从 "root" 用户 (运行安装) 的用户复制用于 azacsnap 的后端存储的 SSH 密钥。 这假定 "root" 用户已配置与存储的连接
       > 请参阅 "[启用与存储的通信](#enable-communication-with-storage)" 部分。

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. 为 SSH 文件正确设置用户权限

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. 在 Azure NetApp 文件上
    1. `DOTNET_BUNDLE_EXTRACT_BASE_DIR`根据 .Net Core 单文件提取指南配置用户的路径。
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. 复制目标用户的 SAP HANA 连接安全用户存储 azacsnap。 这假定 "root" 用户已配置安全用户存储区。
    > 请参阅 "[启用与 SAP HANA 的通信](#enable-communication-with-sap-hana)" 部分。

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. 为文件正确设置用户权限 `hdbuserstore`

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. 将快照工具提取到/home/azacsnap/bin/。

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. 使命令可执行

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. 确保在用户的主目录上设置了正确的所有权权限

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>完成快照工具的设置

安装程序提供了在安装快照工具之后完成的步骤。
请按照以下步骤配置和测试快照工具。  成功测试后，请执行第一个数据库一致的存储快照。

以下输出显示了运行具有默认安装选项的安装程序后需要完成的步骤：

1. 更改为快照用户帐户
    1. `su - azacsnap`
1. 设置 HANA Secure 用户存储
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. 更改为命令的位置
   1. `cd /home/azacsnap/bin/`
1. 配置客户详细信息文件
   1. `azacsnap -c configure –-configuration new`
1. 测试与存储的连接 ...。
   1. `azacsnap -c test –-test storage`
1. 测试与 HANA 的连接 ...。
    1. 无 SSL
       1. `azacsnap -c test –-test hana`
    1. 对于 SSL，你将需要选择正确的 SSL 选项
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. 运行第一个快照备份
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

如果在安装前未完成 "[与 SAP HANA 进行通信](#enable-communication-with-sap-hana)"，则需要执行步骤2。

> [!NOTE]
> 应正确执行测试命令。 否则，命令可能会失败。

## <a name="configuring-the-database"></a>配置数据库

本部分介绍如何配置数据库。

### <a name="sap-hana-configuration"></a>SAP HANA 配置

需要对 SAP HANA 应用某些建议的更改，以确保保护日志备份和目录。 默认情况下， `basepath_logbackup` 和 `basepath_catalogbackup` 会将其文件输出到 `$(DIR_INSTANCE)/backup/log` 目录，并且此路径不太可能是在 `azacsnap` 配置为快照这些文件的卷上，不会使用存储快照来保护这些文件。

以下 `hdbsql` 命令示例旨在演示如何将日志和目录路径设置为可以通过快照的存储卷上的位置 `azacsnap` 。 请确保检查命令行上的值是否与本地 SAP HANA 配置匹配。

### <a name="configure-log-backup-location"></a>配置日志备份位置

在此示例中，对参数进行了更改 `basepath_logbackup` 。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>配置目录备份位置

在此示例中，对参数进行了更改 `basepath_catalogbackup` 。 首先，检查以确保 `basepath_catalogbackup` 文件系统上存在该路径，如果不创建与该目录具有相同所有权的路径，则为。

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

如果需要创建路径，以下示例将创建路径并设置正确的所有权和权限。 需要以 root 身份运行这些命令。

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

下面的示例将更改 SAP HANA 设置。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>检查日志和目录备份位置

进行上述更改后，请在以下命令中确认设置是否正确。
在此示例中，在以上指南中设置的设置将显示为 "系统设置"。

> 此查询还返回用于比较的默认设置。

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>配置日志备份超时

SAP HANA 执行日志备份的默认设置为900秒 (15 分钟) 。 建议将此值减小到300秒， (即5分钟) 。  然后，可以运行定期备份 (例如，每隔10分钟) 将 log_backups 卷添加到配置文件的 "其他卷" 部分。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>检查日志备份超时

更改日志备份超时后，请检查以确保已按如下所示进行了设置。
在此示例中，设置的设置将显示为系统设置，但此查询还会返回用于比较的默认设置。

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>后续步骤

- [配置 Azure 应用程序一致性快照工具](azacsnap-cmd-ref-configure.md)
