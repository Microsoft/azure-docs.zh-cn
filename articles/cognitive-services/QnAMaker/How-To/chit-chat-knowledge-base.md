---
title: 向 QnA Maker 知识库添加聊天内容
titleSuffix: Azure Cognitive Services
description: 在创建 KB 时向机器人中添加个性化聊天内容可使其更健谈而有趣。 使用 QnA Maker，可以轻松将预填充的一组最常用的聊天内容添加到知识库中。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 0463ccf12a254ebda1ee3d6f9cc9bfe7f43b4e80
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108178711"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>向知识库添加聊天内容

向机器人中添加聊天内容可使其更健谈而有趣。 使用 QnA Maker 中的聊天功能，可以轻松地将预填充的一组最常用的聊天内容添加到知识库 (KB) 中。 这可以用作你的机器人的个性化内容的起点，并节省从头开始编写它们的时间和成本。

此数据集有大约 100 个场景的聊天内容，使用多种角色（如专业、友好、有趣）的口音。 选择与机器人的语音最接近的角色。 对于给定的用户查询，QnA Maker 会尝试将其与最接近的已知聊天内容 QnA 匹配。

不同个性的一些示例如下。 你可以查看所有个性化[数据集](https://github.com/microsoft/botframework-cli/blob/main/packages/qnamaker/docs/chit-chat-dataset.md)以及个性化详细信息。

对于 `When is your birthday?` 的用户查询，每个个性都有一个风格的响应：

<!-- added quotes so acrolinx doesn't score these sentences -->
|个性|示例|
|--|--|
|Professional|年龄这一概念不适用于我。|
|友好|我没有确切的年龄。|
|有趣|我没有年龄哦。|
|体贴|我没有年龄。|
|热情|我是机器人，所以我没有年龄。|
||


## <a name="language-support"></a>语言支持

聊天数据集支持以下语言：

|语言|
|--|
|中文|
|英语|
|法语|
|德国|
|意大利语|
|日语|
|朝鲜语|
|葡萄牙语|
|西班牙语|


## <a name="add-chit-chat-during-kb-creation"></a>在 KB 创建过程中添加聊天内容
在知识库创建过程中，在添加源 URL 和文件以后，就会有一个添加聊天内容的选项。 选择需要用作聊天内容库的个性。 如果不希望添加聊天内容，或者数据源中已经有聊天内容支持，请选择“无”。

## <a name="add-chit-chat-to-an-existing-kb"></a>向现有 KB 添加聊天内容
选择 KB，导航到“设置”页。 有一个链接，指向所有采用相应 **.tsv** 格式的聊天内容数据集。 下载所需个性，然后将其作为文件源上传。 请确保在下载和上传文件时不编辑格式或元数据。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

![向现有 KB 添加聊天内容](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

![向现有 KB 预览版添加聊天](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset-v2.png)

---

## <a name="edit-your-chit-chat-questions-and-answers"></a>编辑聊天内容的问题和解答
编辑 KB 时，会看到一个适用于聊天内容的新源，具体取决于所选个性。 现在可以添加更改的问题或编辑响应，就像使用任何其他的源一样。

![编辑聊天内容 QnA](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

若要查看元数据，请在工具栏中选择“视图选项”，然后选择“显示元数据” 。

## <a name="add-additional-chit-chat-questions-and-answers"></a>添加其他的聊天内容问题和解答
可以添加新的不在预定义数据集中的聊天 QnA 对。 确保不复制聊天内容集中已涵盖的 QnA 对。 添加任何新的聊天内容 QnA 时，它会添加到“编辑”源。 若要确保排名程序理解这是聊天内容，请添加元数据键/值对“编辑: 聊天内容”，如下图所示：

:::image type="content" source="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png" alt-text="添加聊天内容 QnA" lightbox="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png":::

## <a name="delete-chit-chat-from-an-existing-kb"></a>从现有 KB 中删除聊天内容
选择 KB，导航到“设置”页。 特定的聊天内容源作为文件列出，使用所选的个性名称。 可以将其作为源文件删除。

![从 KB 中删除聊天内容](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入知识库](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>另请参阅

[QnA Maker 概述](../Overview/overview.md)
