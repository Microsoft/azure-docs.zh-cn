---
title: 在 Azure 上安装 IBM zD&T 开发/测试环境 | Microsoft Docs
description: 在 Azure 虚拟机 (VM) 基础结构即服务 (IaaS) 上部署 IBM Z 开发和测试环境 (zD&T)。
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 32b63256f89d6d051305890c3387140f243e1a70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954320"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>在 Azure 上安装 IBM zD&T 开发/测试环境

要为 IBM Z 系统上的大型机工作负载创建开发/测试环境，可以在 Azure 虚拟机 (VM) 基础结构即服务 (IaaS) 上部署 IBM Z 开发和测试环境 (zD&T)。

借助 zD&T，可以将 x86 平台节省的成本用于不太关键的开发和测试环境，然后将更新推送回 Z 系统生产环境。 有关详细信息，请参阅 [IBM ZD&T 安装说明](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)。

Azure 和 Azure Stack 支持以下版本：

- zD&T Personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

所有版本的 zD&T 都只能在 x86 Linux 系统上运行，而不能在 Windows Server 上运行。 Enterprise Edition 在 Red Hat Enterprise Linux (RHEL) 或 Ubuntu/Debian 上均受支持。 RHEL 和 Debian VM 映像均适用于 Azure。

本文介绍如何在 Azure 上设置 zD&T Enterprise Edition，以便可以使用 zD&T Enterprise Edition Web 服务器来创建和管理环境。 安装 zD&T 不会安装任何环境。 必须将它们分别创建为安装包。 例如，应用程序开发人员控制的分发 (ADCD)是测试环境的卷映像。 它们包含在 IBM 提供的媒体分发上的 zip 映像中。 请参阅如何[在 Azure 上设置 ADCD 环境](demo.md)。

有关详细信息，请参阅 IBM 知识中心中的 [zD&T 概述](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html)。

本文介绍如何在 Azure 上设置 Z 开发和测试环境 (zD&T) Enterprise Edition。 然后，可以使用 zD&T Enterprise Edition Web 服务器在 Azure 上创建和管理基于 Z 的环境。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> IBM 只允许在开发/测试环境中安装 zD&T Enterprise Edition，而不能在生产环境进行安装。

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 你需要访问仅适用于 IBM 客户和合作伙伴的媒体。 有关详细信息，请联系 IBM 代表或查看 [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) 网站上的联系信息。

- [授权服务器](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)。 这对于访问环境是必需的。 创建该服务器的方式取决于从 IBM 为软件授予许可的方式：

     - 基于硬件的授权服务器需要一个 USB 硬件设备，其中包含访问软件的所有部分所必需的 Rational 令牌。 必须从 IBM 获得它。

     - 基于软件的授权服务器要求设置集中式服务器来管理授权密钥。 此方法是首选方法，要求在管理服务器中设置从 IBM 接收到的密钥。

## <a name="create-the-base-image-and-connect"></a>创建基础映像并连接

1. 在 Azure 门户中，[创建一个具有所需操作系统配置的 VM](../../../linux/quick-create-portal.md)。 本文假定一个运行 Ubuntu 16.04 的 B4ms VM（具有 4 个 vCPU 和 16 GB 内存）。

2. 创建该 VM 后，为 SSH 打开入站端口 22，为 FTP 打开 21，为 Web 服务器打开 9443。

3. 通过“连接”按钮获取 VM 的“概述”边栏选项卡上显示的 SSH 凭据 。 选择“SSH”选项卡，并将 SSH 登录命令复制到剪贴板。

4. 从本地电脑登录到 [Bash shell](../../../../cloud-shell/quickstart.md) 并粘贴命令。 它将采用 ssh\<user id\>\@\<IP Address\> 格式。 系统提示输入凭据时，请输入凭据以建立与主目录的连接。

## <a name="copy-the-installation-file-to-the-server"></a>将安装文件复制到服务器

Web 服务器的安装文件为 ZDT\_Install\_EE\_V12.0.0.1.tgz。 它包含在 IBM 提供的媒体中。 必须将此文件上传到 Ubuntu VM。

1. 在命令行中输入以下命令，确保新创建的映像中的所有内容都是最新的：

    ```
    sudo apt-get update
    ```

2. 创建安装的目标目录：

    ```
    mkdir ZDT
    ```

3. 将文件从本地计算机复制到 VM：

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 此命令将安装文件复制到主目录中的 ZDT 目录，具体取决于客户端运行的是 Windows 还是 Linux。

## <a name="install-the-enterprise-edition"></a>安装 Enterprise Edition

1. 使用以下命令，转到 ZDT 目录并解压缩 ZDT\_Install\_EE\_V12.0.0.1.tgz 文件：

    ```
    cd ZDT
    tar zxvf ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 运行安装程序：

    ```
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.x86_64
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 选择“1”以安装 Enterprise Server。

4. 按“Enter”并仔细阅读许可协议。 在许可证末尾，输入“Yes”以继续。

5. 当系统提示更改新创建的用户 ibmsys1 的密码时，请使用命令 sudo passwd ibmsys1 并输入新密码 。

6. 若要验证安装是否成功，请输入

    ```
    dpkg -l | grep zdtapp
    ```

7. 验证输出是否包含字符串 zdtapp 12.0.0.0，该字符串指示已成功安装包

### <a name="starting-enterprise-edition"></a>启动 Enterprise Edition

请记住，当 Web 服务器启动时，它将在安装过程中创建的 zD&T 用户 ID 下运行。

1. 若要启动 Web 服务器，请使用根用户 ID 运行以下命令：

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 复制脚本输出的 URL，如下所示：

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. 将该 URL 粘贴到 Web 浏览器中，以打开 zD&T 安装的管理组件。

## <a name="next-steps"></a>后续步骤

[在 IBM zD&T v1 中设置应用程序开发人员控制的分发 (ADCD)](./demo.md)
