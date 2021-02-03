---
title: 通过自定义语音改进合成 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音是一组在线工具，使用它可为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 请遵循以下链接开始创建自定义语音转文本体验。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 4e487a3eab70e7d561468a8fac2103e177d2abc5
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524969"
---
# <a name="get-started-with-custom-voice"></a>自定义语音入门

[自定义语音](https://aka.ms/customvoice)是一组在线工具，使用它可为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 请遵循以下链接开始创建自定义文本转语音体验。

## <a name="whats-in-custom-voice"></a>什么是自定义语音？

在开始使用自定义语音之前，需要一个 Azure 帐户和一个语音服务订阅。 创建帐户后，可以准备数据、训练和测试模型、评估语音质量，并最终部署自定义语音模型。

下图突出显示了使用[自定义语音门户](https://aka.ms/customvoice)创建自定义语音模型的步骤。 请使用链接了解详细信息。

![自定义语音体系结构图](media/custom-voice/custom-voice-diagram.png)

1. [订阅和创建项目](#set-up-your-azure-account) - 创建 Azure 帐户和语音服务订阅。 使用此统一订阅可以访问语音转文本、文本转语音、语音翻译和自定义语音门户。 然后，可以使用语音服务订阅创建第一个自定义语音项目。

2. [上传数据](how-to-custom-voice-create-voice.md#upload-your-datasets) - 使用自定义语音门户或自定义语音 API 上传数据（音频和文本）。 在门户中，可以调查和评估发音评分以及信噪比。 有关详细信息，请参阅[如何为自定义语音准备数据](how-to-custom-voice-prepare-data.md)。

3. [训练模型](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – 使用数据创建自定义的“文本转语音”语音模型。 可在不同的语言中训练模型。 训练并测试模型后，如果你对结果感到满意，则可以部署该模型。

4. [部署模型](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 为文本语音模型创建自定义终结点，并使用该终结点在产品、工具和应用程序中进行语音合成。

## <a name="custom-neural-voices"></a>自定义神经语音

自定义语音当前同时支持标准层和神经层。 自定义的神经语音使用户能够生成更高质量的语音模型，同时需要较少的数据，并提供可帮助你部署 AI 责任的度量值。 我们建议你应使用自定义的神经语音为更自然的对话接口开发更逼真的声音，并使你的客户和最终用户能够以一种有责任的方式从最新的文本到语音技术中获益。 [详细了解自定义的神经声音](https://aka.ms/CNV-Transparency-Note)。 

> [!NOTE]
> 作为 Microsoft 对设计责任 AI 的承诺的一部分，我们限制了自定义的神经语音的使用。 只有在查看了应用程序并将其与我们的责任 AI 原则结合使用之后，才能获得技术的访问权限。 在[此处](https://aka.ms/customneural)了解有关[策略的](https://aka.ms/gating-overview)详细信息。 自定义语音的标准版本和神经版本支持的 [语言](language-support.md#customization) 和 [区域](regions.md#custom-voices) 是不同的。 在开始之前，请查看详细信息。  

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

需要拥有语音服务订阅，才能使用“自定义语音识别”门户创建自定义模型。 请遵照这些说明在 Azure 中创建语音服务订阅。 如果你没有 Azure 帐户，可以注册一个新帐户。  

创建 Azure 帐户和语音服务订阅后，需要登录到自定义语音门户并连接订阅。

1. 从 Azure 门户获取语音服务订阅密钥。
2. 登录到[自定义语音门户](https://aka.ms/custom-voice)。
3. 选择你的订阅并创建语音项目。
4. 若要切换到另一个语音订阅，请使用顶部导航栏中的齿轮图标。

> [!NOTE]
> 必须先在 Azure 中创建 F0 或 S0 语音服务密钥，然后才能使用该服务。 自定义的神经语音仅支持 S0 层。 

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容在自定义语音门户中组织成 **项目**。 每个项目特定于某个国家/语言，以及要创建的语音的性别。 例如，对于呼叫中心的使用美式英语 (en-US) 的聊天机器人，可以创建女性语音项目。

若要创建第一个项目，请选择“文本转语音/自定义语音”选项卡，然后单击“新建项目”。  遵照向导中的说明创建项目。 创建项目后，将看到四个选项卡：“数据”、“训练”、“测试”和“部署”。    使用[后续步骤](#next-steps)中提供的链接了解如何使用每个选项卡。

> [!IMPORTANT]
> [“自定义语音”门户](https://aka.ms/custom-voice)最近已更新！ 如果以前已在 CRIS.ai 门户或使用 API 创建了数据、模型、测试并已发布了终结点，则需要在新门户中创建一个新项目以连接到这些旧实体。

## <a name="next-steps"></a>后续步骤

- [准备自定义语音数据](how-to-custom-voice-prepare-data.md)
- [创建自定义语音](how-to-custom-voice-create-voice.md)
- [指南：录制语音样本](record-custom-voice-samples.md)