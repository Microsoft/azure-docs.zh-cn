---
title: 本地 Azure AD 密码保护常见问题解答
description: 查看 Azure AD 本地 Active Directory 域服务环境中的密码保护的常见问题
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d5517afe7407da7428d4a83f3d2de67836280c7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741892"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Azure AD 本地密码保护常见问题

本部分提供有关 Azure AD 密码保护的许多常见问题的解答。

## <a name="general-questions"></a>一般问题

**问：用户如何选择安全密码应提供哪些指导？**

在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/research/publication/password-guidance)

**问：在非公有云中是否支持本地 Azure AD 密码保护？**

公有云和 Arlington 云中支持本地 Azure AD 密码保护。 尚未为其他云中的可用性发布日期。

Azure AD 门户允许修改本地特定的 "Windows Server Active Directory 的密码保护" 配置，即使在不支持的云中也是如此。此类更改将持久保存，但不会生效。 不支持在不受支持的云中注册本地代理或林，任何此类注册尝试都将始终失败。

**问：我如何向本地用户的子集应用 Azure AD 密码保护权益？**

不支持。 部署并启用后，Azure AD 密码保护不会区分对待 - 所有用户都会获得均等的安全权益。

**问：密码更改和密码设置 (或重置) 之间的区别是什么？**

密码更改是指用户在证明其了解旧密码后选择新密码。 例如，密码更改会在用户登录到 Windows 时执行，然后系统会提示选择一个新密码。

如果管理员使用新密码将帐户替换为密码（例如，使用 Active Directory 用户和计算机管理工具），则密码设置 (有时称为密码重置) 。 此操作需要 (通常是域管理员) 的高级权限，并且执行该操作的人员通常不知道旧密码。 技术支持方案通常会执行密码集，例如，在协助用户忘记密码时。 首次使用密码创建新用户帐户时，还会看到密码设置事件。

不管密码是否已更改或已设置，密码验证策略的行为都是相同的。 Azure AD 密码保护 DC 代理服务会记录不同的事件，以通知你是否已完成密码更改或设置操作。  请参阅 [Azure AD 密码保护监视和日志记录](./howto-password-ban-bad-on-premises-monitor.md)。

**问：在安装后 Azure AD 密码保护是否验证现有密码？**

无 Azure AD 密码保护只能在密码更改或设置操作期间对明文密码强制执行密码策略。 Active Directory 接受密码后，只保留该密码的身份验证协议特定的哈希。 明文密码永远不会被保留，因此 Azure AD 密码保护无法验证现有密码。

初始部署 Azure AD 密码保护之后，所有用户和帐户最终将开始使用 Azure AD 密码保护验证密码，因为其现有密码会在一段时间后过期。 如果需要，此过程可以通过一次性手动过期的用户帐户密码来加速。

除非手动过期，否则配置为 "密码永不过期" 的帐户永远不会被迫更改其密码。

**问：为什么在尝试使用 "Active Directory 用户和计算机管理" 管理单元尝试设置弱密码时记录重复的密码拒绝事件？**

"Active Directory 用户和计算机管理" 管理单元将首先尝试使用 Kerberos 协议设置新密码。 失败时，管理单元将再次尝试使用旧版 (SAM RPC) 协议设置密码 (所使用的特定协议并不重要) 。 如果 Azure AD 密码保护将新密码视为弱密码，则此管理单元行为将导致记录两组密码重置拒绝事件。

**问：为什么使用空用户名记录 Azure AD 密码保护密码验证事件？**

Active Directory 支持测试密码以查看是否通过了域的当前密码复杂性要求（例如，使用 [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) api）的功能。 以这种方式验证密码时，测试还包括通过密码筛选器基于 dll 的产品（如 Azure AD 密码保护）进行的验证-但传递到给定密码筛选器 dll 的用户名将为空。 在此方案中，Azure AD 密码保护仍将使用当前生效的密码策略来验证密码，并将发出事件日志消息来捕获结果，但是事件日志消息将包含空的用户名字段。

**问：是否支持与其他基于密码筛选器的产品并行安装 Azure AD 密码保护？**

是。 支持多个已注册的密码筛选器 dll 是一项 Windows 核心功能，并不特定于 Azure AD 密码保护。 在接受密码之前，所有已注册的密码筛选器 dll 必须同意。

**问：如何在 Active Directory 环境中部署和配置 Azure AD 密码保护，而无需使用 Azure？**

不支持。 Azure AD 密码保护是旨在扩展到本地 Active Directory 环境中的一项 Azure 功能。

**问：如何在 Active Directory 级别修改策略的内容？**

不支持。 仅可使用 Azure AD 门户管理策略。 另请参阅前面的问题。

**问：为何需要使用 DFSR 进行 sysvol 复制？**

FRS（DFSR 以前的技术）存在很多已知问题，在较新版本的 Windows Server Active Directory 中完全不受支持。 Azure AD 密码保护的零测试将在配置了 FRS 的域上完成。

有关详细信息，请参阅以下文章：

[将 sysvol 复制迁移到 DFSR 的用例](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[FRS 即将终结](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

如果你的域尚未使用 DFSR，则必须先将其迁移到使用 DFSR，然后才能安装 Azure AD 密码保护。 有关详细信息，请参阅以下链接：

[SYSVOL 复制迁移指南： FRS 到 DFS 复制](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Azure AD 密码保护 DC 代理软件当前将安装在仍在使用 FRS 进行 sysvol 复制的域中的域控制器上，但该软件在此环境中将无法正常运行。 其他负面影响包括未能复制的单个文件，sysvol 还原过程会成功，但不会以静默方式复制所有文件。 应该尽快迁移你的域以使用 DFSR，这两种方法都适用于 DFSR 固有的优势，还会取消阻止 Azure AD 密码保护的部署。 在仍使用 FRS 的域中运行时，软件的未来版本将自动禁用。

**问：在域 sysvol 共享上，该功能需要多少磁盘空间？**

准确的空间用量根据多种因素而异，例如，Microsoft 全局受禁密码列表和租户自定义密码列表中的受禁令牌数目和长度，以及加密开销。 这些列表的内容将来可能会不断扩充。 考虑到这一点，合理的预期是该功能至少需要占用域 sysvol 共享中的 5 MB 空间。

**问：安装或升级 DC 代理软件后为何需要重新启动？**

此项要求是核心 Windows 行为造成的。

**问：是否有任何方法可将 DC 代理配置为使用特定的代理服务器？**

不是。 由于代理服务器是无状态的，具体使用哪种代理服务器并不重要。

**问：是否可以与其他服务（如 Azure AD Connect）并行部署 Azure AD 密码保护代理服务？**

是。 Azure AD 密码保护代理服务和 Azure AD Connect 永远不会直接相互冲突。

遗憾的是，在 Azure AD 密码保护代理软件安装的 Microsoft Azure AD 连接代理更新程序服务的版本与 [Azure Active Directory 应用程序代理](../manage-apps/application-proxy.md) 软件安装的服务版本之间发现不兼容性。 这种不兼容性可能会导致代理更新服务无法联系 Azure 获取软件更新。 不建议在同一台计算机上安装 Azure AD 密码保护代理和 Azure Active Directory 应用程序代理。

**问： DC 代理和代理的安装和注册顺序是什么？**

支持对代理安装、DC 代理安装、林注册和代理注册进行排序。

**问：我是否应该关心域控制器上的性能点击是否超过了部署此功能的情况？**

在现有的正常 Active Directory 部署中，Azure AD 密码保护 DC 代理服务应该不会显著影响域控制器的性能。

对于大部分 Active Directory 部署而言，密码更改操作在任意给定的域控制器上只占总体工作负荷的一小部分。 例如，假设某个 Active Directory 域包含 10000 个用户帐户，某个 MaxPasswordAge 策略设置为 30 天。 一般情况下，此域每天会发生 10000/30 = 大约 333 次密码更改操作，即使是在单个域控制器中，此操作次数也是微不足道的。 考虑一种更糟糕的潜在情况：假设在单个 DC 上执行的大约 333 次密码更改是在一小时内完成的。 例如，当许多员工在星期一上午上班时，就可能会发生这种此情况。 即使出现这种情况，每 60 分钟的密码更改次数也只有大约 333 次，即每分钟 6 次，同样，这并不是一个很高的负载。

但是，如果当前域控制器已在性能限制级别运行（例如，达到了 CPU、磁盘空间、磁盘 I/O 等的上限），则我们建议添加更多的域控制器或扩展可用磁盘空间，然后再部署此功能。 另请参阅前面有关 sysvol 磁盘空间用量的问题。

**问：我只想在域中的几个 Dc 上测试 Azure AD 密码保护。是否可以强制更改用户密码以使用这些特定 Dc？**

不是。 当用户更改其密码时，Windows 客户端 OS 会控制要使用的域控制器。 基于诸如 Active Directory 站点和子网分配、特定于环境的网络配置等因素来选择域控制器。Azure AD 密码保护不会控制这些因素，并且不会影响选择哪个域控制器来更改用户的密码。

在一定程度上实现此目标的方法之一是在给定 Active Directory 站点中的所有域控制器上部署 Azure AD 密码保护。 这种方法能够合理覆盖分配到该站点的 Windows 客户端，因此，也会合理覆盖登录到这些客户端并更改其密码的用户。

**问：如果我只在主域控制器上安装 Azure AD 密码保护 DC 代理服务 (PDC) ，则该域中的所有其他域控制器是否也受到保护？**

不是。 如果在给定的非 PDC 域控制器上更改用户密码时，则明文密码永远不会发送到 PDC（这种想法是常见的错误认知）。 一旦在给定的 DC 上接受新密码，该 DC 将使用该密码来为该密码创建各种特定于身份验证协议的哈希，然后在目录中保存这些哈希。 不会保存明文密码。 然后，更新的哈希将复制到 PDC。 在某些情况下，用户密码可以直接在 PDC 上更改，同样，这取决于网络拓扑和 Active Directory 站点设计等多种因素。 （请参阅前面的问题。）

总而言之，在 PDC 上部署 Azure AD 密码保护 DC 代理服务需要对整个跨中的功能实现 100% 的安全覆盖。 仅在 PDC 上部署该功能不能为域中的其他任何 DC 提供 Azure AD 密码保护安全优势。

**问：为什么在本地 Active Directory 环境中安装代理后，自定义智能锁定仍不起作用？**

仅 Azure AD 支持自定义智能锁定。 即使安装了代理，对 Azure AD 门户中的自定义智能锁定设置的更改也不会影响本地 Active Directory 环境。

**问： System Center Operations Manager 管理包是否可用于 Azure AD 密码保护？**

不是。

**问：为什么在将策略配置为处于审核模式时，为什么 Azure AD 仍拒绝弱密码？**

仅本地 Active Directory 环境支持审核模式。 当计算密码时，Azure AD 隐式始终处于 "强制" 模式。

**问：我的用户在 Azure AD 密码保护拒绝密码时，会看到传统的 Windows 错误消息。是否可以自定义此错误消息，以便用户知道实际发生的情况？**

不是。 当域控制器拒绝密码时，用户所见到的错误消息由客户端计算机控制，而不是由域控制器控制。 不管密码是否被默认 Active Directory 密码策略或基于密码筛选器的解决方案（如 Azure AD 密码保护）拒绝，都会发生此行为。

## <a name="additional-content"></a>更多内容

以下链接不是核心 Azure AD 密码保护文档的一部分，但可能有关该功能的其他信息的有用来源。

[Azure AD 密码保护现已正式发布！](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[电子邮件仿冒 Protection 指南–第15部分：在本地实现 Microsoft Azure AD 密码保护服务 (！ ) ](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)（Azure AD 密码保护和智能锁定现已推出公共预览版！）

## <a name="microsoft-premierunified-support-training-available"></a>可用的 Microsoft Premier\Unified 支持培训

如果你想要详细了解 Azure AD 密码保护并将其部署在自己的环境中，可以利用面向已签署 Premier 或 Unified 合同的客户的 Microsoft 前瞻服务。 此服务称为 Azure Active Directory：密码保护。 请联系技术客户经理获取详细信息。

## <a name="next-steps"></a>后续步骤

如果本文未解答你遇到的本地 Azure AD 密码保护问题，请在下方提交反馈 - 谢谢！

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)