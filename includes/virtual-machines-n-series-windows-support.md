---
title: include 文件
description: include 文件
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f99aedc21c3b51975649f8944ab53536d365a7d1
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096259"
---
## <a name="supported-operating-systems-and-drivers"></a>支持的操作系统和驱动程序

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) 驱动程序

NVIDIA Tesla (CUDA) 的 NC、NCv2、NCv3、NCasT4_v3、ND 和 NDv2 系列 Vm 的驱动程序， (仅对下表中列出的操作系统支持 NV 系列) 的可选操作。 在本文发布时，驱动程序下载链接是最新的。 有关最新驱动程序，请访问 [NVIDIA](https://www.nvidia.com/) 网站。

> [!TIP]
> 作为一种在 Windows Server VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Windows Server 2016 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。


| 操作系统 | 驱动程序 |
| -------- |------------- |
| Windows Server 2019 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) ( .exe)  |
| Windows Server 2016 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) ( .exe)  |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驱动程序

Microsoft 为用作虚拟工作站或虚拟应用程序的 NV 和 NVv3 系列 Vm 重新分发 NVIDIA 网格驱动程序安装程序。 仅在 Azure NV 系列 Vm 上安装这些网格驱动程序，且仅在下表中列出的操作系统上安装。 这些驱动程序包括 Azure 中 GRID Virtual GPU Software 的许可。 无需设置 NVIDIA vGPU 软件许可证服务器。

Azure 重新发布的网格驱动程序不适用于非 NV 系列 Vm，如 NCv2、NCv3、ND 和 NDv2 系列 Vm。 一种例外情况是 NCas_T4_V3 VM 系列，其中网格驱动程序将启用类似于 NV 系列的图形功能。

Nvidia K80 Gpu 的 NC-Series 不支持网格/图形应用程序。  

请注意，Nvidia 扩展将始终安装最新的驱动程序。 我们在此处提供了与旧版本相关的客户的以前版本的链接。

对于 Windows Server 2019、Windows Server 2016 和 Windows 10 (生成 2004) ：
- [网格 11.2 (452.57) ](https://go.microsoft.com/fwlink/?linkid=874181) () 
- [网格 11.1 (452.39) ](https://download.microsoft.com/download/9/9/1/99186e1b-d27d-47d5-9957-175c88f4efbe/452.39_grid_win10_64bit_whql.exe) ()  

对于 Windows Server 2012 R2： 
- [网格 11.0 (451.48) ](https://download.microsoft.com/download/f/7/2/f729e28b-57b8-4141-b577-38d2390973ef/451.48_grid_server2012R2_64bit_international.exe) ()  
- [网格 10.1 (442.66) ](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) ()   

有关所有以前的 Nvidia GRID 驱动程序链接的完整列表，请访问 [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)
