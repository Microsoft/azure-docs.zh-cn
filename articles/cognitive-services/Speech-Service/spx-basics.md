---
title: 语音 CLI 基础知识
titleSuffix: Azure Cognitive Services
description: 了解如何使用语音 CLI 命令工具在不编写代码以及进行最少设置的情况下使用语音服务。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: acc19d9a04909dcf0e79c93e0c8a3fb8225ee1b4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546892"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>了解语音 CLI 的基础知识

本文介绍了语音 CLI 的基本用法模式，这是一种无需编写代码即可使用语音服务的命令行工具。 无需创建开发环境或编写任何代码，你可以快速测试语音服务的主要功能，以了解它能否充分满足你的用例的要求。 语音 CLI 随时可投入生产，可用通过 `.bat` 或 shell 脚本，使用它自动化语音服务中的简单工作流。

本文假定你具有命令提示符、终端或 PowerShell 的相关工作知识。

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>基本用法

本部分介绍了一些基本的 SPX 命令，这些命令对于首次测试和试验通常非常有用。 首先，运行以下命令，查看内置于该工具的帮助。

```shell
spx
```

注意，请参阅命令参数右侧列出的帮助主题。 可输入这些命令来获取有关子命令的详细帮助。

可按关键字搜索帮助主题。 例如，输入以下命令，查看语音 CLI 用法示例列表：

```shell
spx help find --topics "examples"
```

输入以下命令，查看用于识别命令的选项：

```shell
spx help recognize
```

现在利用语音 CLI，使用系统的默认麦克风来进行语音识别。 

>[!WARNING]
> 如果使用的是 Docker 容器，则此命令不起作用。

运行以下命令：

```shell
spx recognize --microphone
```

使用语音 CLI，还可以识别音频文件中的语音。

```shell
spx recognize --file /path/to/file.wav
```
> [!TIP]
> 如果要识别 Docker 容器中音频文件的语音，请确保音频文件位于上一步中安装的目录中。

SPX 将在输入命令后开始侦听当前活动输入设备上的音频，并在你按下 `ENTER` 后停止。 然后，识别所录制的语音，并将其转换为控制台输出中的文本。 使用语音 CLI，还可以轻松地进行文本转语音合成。 

运行以下命令会将已输入的文本作为输入，并将合成的语音输出到当前活动的输出设备。

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

除了语音识别和合成外，还可以通过语音 CLI 进行语音翻译。 与上面的语音识别命令类似，运行以下命令以从默认麦克风中捕获音频，并在目标语言中将其转换为文本。

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

在此命令中，你既指定了源语言（要翻译的语言），又指定了目标语言（翻译成的语言） 。 使用 `--microphone` 参数将侦听当前活动输入设备上的音频，并在你按 `ENTER` 后停止。 输出即将文本翻译为目标语言，写入到文本文件。

> [!NOTE]
> 有关所有受支持的语言及其相应的区域设置代码列表，请参阅[语言和区域设置文章](language-support.md)。

### <a name="configuration-files-in-the-datastore"></a>数据存储中的配置文件

语音 CLI 的行为可依赖于配置文件中的设置，可以使用 @ 符号在语音 CLI 调用中引用这些设置。
语音 CLI 在当前工作目录下它创建的新 `./spx/data` 子目录中保存新设置。
查找配置值时，语音 CLI 将在当前工作目录中查找，再在 `./spx/data` 的数据存储中查找，然后在其他数据存储（包括 `spx` 二进制文件中的最终只读数据存储）中查找。
以前，你使用了数据存储来保存 `@key` 和 `@region` 值，因此无需通过每个命令行调用来指定它们。
你还可以使用配置文件来存储你自己的配置设置，甚至使用它们来传递 URL 或在运行时生成的其他动态内容。

本部分介绍了使用本地数据存储中的配置文件借助 `spx config` 来存储和提取命令设置，并使用 `--output` 选项存储语音 CLI 的输出。

下面的示例将清除 `@my.defaults` 配置文件，为文件中的“键”和“区域”添加键值对，并在调用 `spx recognize` 时使用此配置 。

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

你还可以向配置文件写入动态内容。 例如，以下命令将创建一个自定义语音模型，并在配置文件中存储新模型的 URL。 下一条命令要等到该 URL 的模型可以使用时才返回。

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

以下示例将两条 URL 写入 `@my.datasets.txt` 配置文件。
在此方案中，`--output` 可以包括一个可选“添加”关键字，以创建配置文件或追加到现有配置文件。


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

有关数据存储文件的详细信息，包括使用默认配置文件（用于命令特定默认设置的 `@spx.default`、`@default.config` 和 `@*.default.config`），请输入以下命令：

```shell
spx help advanced setup
```

## <a name="batch-operations"></a>批处理操作

上一部分中的命令非常适合用于快速查看语音服务的工作方式。 在评估是否可以满足用例时，你可能需要对已有的输入范围执行批处理操作，以查看服务如何处理各种情况。 本节介绍如何完成下列操作：

* 在音频文件目录上运行批处理语音识别
* 循环访问 `.tsv` 文件并运行批处理文本转语音合成

## <a name="batch-speech-recognition"></a>批处理语音识别

如果有音频文件的目录，则通过语音 CLI 可以轻松地快速运行批处理语音识别。 只需运行以下命令，即可使用 `--files` 命令指向目录。 在此示例中，将 `\*.wav` 追加到目录，以识别目录中存在的所有 `.wav` 文件。 此外，指定 `--threads` 参数以在 10 个并行线程上运行识别。

> [!NOTE]
> `--threads` 参数也可以在下一部分中用于 `spx synthesize` 命令，可用线程将取决于 CPU 及其当前负载百分比。

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

使用 `--output file` 参数将识别的语音输出写入到 `speech_output.tsv`。 下面是输出文件结构的示例。

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="synthesize-speech-to-a-file"></a>将语音合成到文件中

运行以下命令，将扬声器的输出更改为 `.wav` 文件。

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

语音 CLI 将采用英文向 `greetings.wav` 音频文件生成自然语言。
在 Windows 中，输入 `start greetings.wav` 可以播放音频文件。


## <a name="batch-text-to-speech-synthesis"></a>批处理文本转语音合成

运行批处理文本转语音的最简单方法是创建一个新的 `.tsv`（制表符分隔值）文件，并利用语音 CLI 中的 `--foreach` 命令。 请考虑以下文件 `text_synthesis.tsv`：

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 接下来，运行命令以指向 `text_synthesis.tsv`，对每个 `text` 字段执行合成，然后将结果作为 `.wav` 文件写入相应的 `audio.output` 路径中。 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

此命令等效于对 `.tsv` 文件中的每个记录运行 `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav`。 请注意以下几点：

* 列标题 `audio.output` 和 `text` 分别对应于命令行参数 `--audio output` 和 `--text`。 多部分命令行参数（如 `--audio output`）应在文件中格式化，无空格，无前导短划线，使用句点分隔字符串，例如 `audio.output`。 使用此模式，可以将任何其他现有命令行参数作为其他列添加到文件中。
* 以这种方式格式化文件时，不需要将其他参数传递给 `--foreach`。
* 请确保通过选项卡将 `.tsv` 中的每个值分隔开。

但是，如果你具有如下面示例的 `.tsv` 文件（其列标头不匹配命令行参数）：

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

可以在 `--foreach` 调用中使用以下语法将这些字段名称替代为正确的参数。 此调用与上面相同。

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>后续步骤

* 使用语音 CLI 完成[语音识别](get-started-speech-to-text.md?pivots=programmer-tool-spx)或[语音合成](get-started-text-to-speech.md?pivots=programmer-tool-spx)快速入门。
