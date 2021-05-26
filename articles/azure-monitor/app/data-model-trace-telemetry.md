---
title: Azure Application Insights 数据模型 - 跟踪遥测
description: 适用于跟踪遥测的 Application Insights 数据模型
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: cb298812b9970882c4bb3c7d2562bb5cbbf69b2d
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110098895"
---
# <a name="trace-telemetry-application-insights-data-model"></a>跟踪遥测：Application Insights 数据模型

[Application Insights](./app-insights-overview.md) 中的跟踪遥测表示搜索文本的 `printf` 样式跟踪语句。 `Log4Net`、`NLog` 和其他基于文本的日志文件条目将转换成此类型的实例。 跟踪没有作为可扩展性的度量。

## <a name="message"></a>Message

跟踪消息。

最大长度：32768 个字符

## <a name="severity-level"></a>严重性级别

跟踪严重性级别。 值可以是 `Verbose`、`Information`、`Warning`、`Error`、`Critical`。

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>后续步骤

- [在 Application Insights 中浏览 .NET 跟踪日志](./asp-net-trace-logs.md)。
- [在 Application Insights 中浏览 Java 跟踪日志](java-2x-trace-logs.md)。
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](data-model.md)。
- [编写自定义跟踪遥测](./api-custom-events-metrics.md#tracktrace)
- 查看 Application Insights 支持的[平台](./platforms.md)。

