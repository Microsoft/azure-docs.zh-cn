---
title: Azure 通知中心模板
description: 了解如何使用 Azure 通知中心模板。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635636"
---
# <a name="notification-hubs-templates"></a>通知中心模板

模板可让客户端应用程序指定它想要接收的确切通知格式。 通过使用模板，应用可获得多个不同优势，其中包括：

- 平台不可知的后端
- 个性化通知
- 客户端版本独立性
- 易于本地化

本部分提供两个深入的示例，介绍如何使用模板跨平台向所有设备发送与平台无关的通知，以及将广播通知个性化到每个设备。

## <a name="using-templates-cross-platform"></a>使用跨平台模板

发送推送通知的标准方法是向平台通知服务（WNS、APNS）发送要传出的每个通知的特定负载。 例如，要向 APNS 发送警报，有效负载将是以下格式的 JSON 对象：

```json
{"aps": {"alert" : "Hello!" }}
```

要在 Windows 应用商店应用程序中发送类似的 toast 消息，XML 负载将如下所示：

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

可以为 MPNS (Windows Phone) 和 FCM (Android) 平台创建类似的有效负载。

此要求将强制应用后端针对每个平台生成不同的负载，并有效地使后端负责应用的表示层部分。 某些考虑因素包括本地化和图形布局（尤其针对 Windows 应用商店应用，其中包含不同磁贴类型的通知）。

使用通知中心模板功能，客户端应用可以创建称为模板注册的特殊注册，其中除了包含标记集外，还包含一个模板。 使用通知中心模板功能，客户端应用可以将设备与模板关联，无论你使用的是 (首选) 还是注册进行安装。 由于前面的负载示例，仅与平台无关的信息是实际的警报消息 (**Hello！**) 。 模板是通知中心的一组说明，说明如何为该特定客户端应用的注册设置与平台无关的消息的格式。 在前面的示例中，与平台无关的消息是单个属性：`message = Hello!`。

下图说明了该过程：

![显示跨平台模板使用过程的关系图](./media/notification-hubs-templates/notification-hubs-hello.png)

iOS 客户端应用注册的模板如下所示：

```json
{"aps": {"alert": "$(message)"}}
```

Windows 应用商店客户端应用的相应模板为：

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

请注意，实际消息将替换为表达式 `$(message)` 。 此表达式将指示通知中心每次向此特定注册发送一条消息，以生成后面的消息并插入公共值。

如果使用的是安装模型，则安装 "templates" 键会保存多个模板的 JSON。 如果使用的是注册模型，则客户端应用程序可以创建多个注册以使用多个模板;例如，用于警报消息的模板和用于磁贴更新的模板。 客户端应用程序还可以混合使用本机注册（不带模板的注册）和模板注册。

通知中心为每个模板发送一个通知，而不考虑它们是否属于同一客户端应用程序。 可以使用此行为将与平台无关的通知转换成其他通知。 例如，与通知中心相同的独立于平台的消息可在 toast 警报和磁贴更新中无缝翻译，无需后端才能识别。 某些平台 (例如，如果在短时间内发送多个通知，则 iOS) 可能会将多个通知折叠到同一个设备。

## <a name="using-templates-for-personalization"></a>使用模板进行个性化设置

使用模板的另一个优点就是能够使用通知中心对通知执行基于注册的个性化设置。 例如，假设有一个天气应用，其中显示了特定位置的天气情况。 用户可以选择摄氏度或华氏度，以及一天或五天的天气预报。 使用模板，每个客户端应用安装可以注册所需的格式（1 天摄氏度、1 天华氏度、5 天摄氏度、5 天华氏度），并让后端发送一条消息，其中包含填充这些模板所需的全部信息（例如，使用摄氏度和华氏度的五天天气预报）。

使用摄氏温度的一天天气预报模板如下所示：

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

发送到通知中心的消息包含以下所有属性：

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

通过使用此模式，后端只需发送一条消息，而不必为应用用户存储特定的个性化选项。 下图演示了此方案：

![显示后端如何仅向每个平台发送一条消息的关系图。](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>如何注册模板

若要使用安装模型注册模板 (首选) 或注册模型，请参阅 [注册管理](notification-hubs-push-notification-registration-management.md)。

## <a name="template-expression-language"></a>模板表达式语言

模板限定为 XML 或 JSON 文档格式。 此外，只能在特定位置放置表达式;例如，for XML 的节点属性或值，JSON 的字符串属性值。

下表显示了模板中允许使用的语言：

| 表达式       | 说明 |
| ---------------- | --- |
| $(prop)          | 对具有给定名称的事件属性的引用。 属性名称不区分大小写。 此表达式将解析为属性的文本值，如果该属性不存在，则解析为空字符串。 |
|$(prop, n)       | 同上，但会在 n 个字符处对文本进行显式剪切，例如，$(title, 20) 会在 20 个字符处对 title 属性的内容进行剪切。 |
| .(prop, n)      | 同上，但会在剪切后的文本后面添加三个点作为后缀。 剪裁的字符串和后缀的总大小不超过 n 个字符。 (标题，20) ，其中输入属性为 "这是标题行"，结果为 "标题行" **。** |
| %(prop)          | 类似于 $(name)，不过其输出已经过 URI 编码。 |
| #(prop)          | 在 JSON 模板中使用（例如，用于 iOS 和 Android 模板）。<br><br>此函数的工作方式与前面指定的 "$ (") "完全相同，但在 JSON (模板中使用时除外，如 Apple templates) 。 在这种情况下，如果此函数不在 "{"、"}" 的周围 (例如 "myJsonProperty"： "# (name) " ) ，并且它的计算结果为 JavaScript 格式的数值，例如，regexp： (0&#124; (&#91;1-9&#93;&#91;0-9&#93; * ) )  (\.&#91;0-9&#93;+) ？ ( (e&#124;e)  (+&#124;-) ？ &#91;0-9&#93;+) ？，则输出 JSON 为数值。<br><br>例如，"徽章：" # (名称) "变为" 徽章 "： 40 (，而不是" 40 ") 。 |
| "text" 或 "text" | 一个文本。 文本包含以单引号或双引号括住的任意文本。 |
| expr1 + expr2    | 用于将两个表达式联接成单个字符串的串联运算符。 |

表达式可以采用上述任一格式。

使用连接时，必须使用 `{}` 括住整个表达式。 例如，`{$(prop) + ‘ - ’ + $(prop2)}`。

例如，以下模板不是有效的 XML 模板：

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

如前所述，使用串联时，表达式必须用大括号括住。 例如：

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>后续步骤

[了解 Azure 通知中心](notification-hubs-push-notification-overview.md)
