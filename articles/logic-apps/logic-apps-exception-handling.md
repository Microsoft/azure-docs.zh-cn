---
title: 在工作流中处理错误和异常
description: 了解如何处理在使用 Azure 逻辑应用创建的自动任务和工作流中发生的错误和异常
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: estfan, logicappspm, azla
ms.date: 02/18/2021
ms.topic: article
ms.openlocfilehash: fbe797937021763bb97ca09e1da792d9a7010f9a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702498"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理错误和异常

恰当地处理依赖系统造成的停机或问题对任何集成体系结构而言可能都是一个难题。 为了帮助创建可适当处理问题和故障的可靠、可复原集成，逻辑应用提供了用于处理错误和异常的一流体验。

<a name="retry-policies"></a>

## <a name="retry-policies"></a>重试策略

对于最基本的异常和错误处理，可以在支持的任何操作或触发器（例如，请参阅 [HTTP 操作](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)）中使用 *重试策略*。 重试策略指定当原始请求（导致 408、429 或 5xx 响应的任何请求）超时或失败时，操作或触发器是否且如何重试请求。 如果未使用任何其他重试策略，则使用默认策略。

重试策略类型如下所示：

| 类型 | 说明 |
|------|-------------|
| **Default** | 此策略可 *按指数级增长* 间隔发送最多 4 次重试，增幅为 7.5 秒，但范围限定在 5 到 45 秒之间。 |
| **指数间隔**  | 此策略会等待从指数增长的范围中随机选定的时间间隔，然后再发送下一个请求。 |
| **固定间隔**  | 此策略会等待指定的时间间隔，然后再发送下一个请求。 |
| 无  | 不重新发送请求。 |
|||

要了解重试策略限制，请参阅[逻辑应用限制和配置](../logic-apps/logic-apps-limits-and-config.md#http-limits)。

### <a name="change-retry-policy"></a>更改重试策略

要选择其他重试策略，请执行以下步骤：

1. 在逻辑应用设计器中打开逻辑应用。

1. 打开操作或触发器的“设置”。

1. 如果操作或触发器支持重试策略，请在“重试策略”下选择所需类型。

或者，可以在支持重试策略的操作或触发器的 `inputs` 部分指定重试策略。 如果不指定重试策略，该操作将使用默认策略。

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*必需*

| Value | 类型 | 说明 |
|-------|------|-------------|
| <*重试-策略类型*> | 字符串 | 要使用的重试策略类型：`default`、`none`、`fixed` 或 `exponential` |
| <*重试间隔*> | 字符串 | 其中值必须使用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)的重试时间间隔。 默认的最小时间间隔是 `PT5S`，而最大时间间隔是 `PT1D`。 如果使用指数式时间间隔策略，可更改最小值和最大值。 |
| <*重试次数*> | Integer | 重试尝试次数，它必须介于 1 和 90 之间 |
||||

*可选*

| Value | 类型 | 说明 |
|-------|------|-------------|
| <*最小间隔*> | 字符串 | 对于指数式时间间隔策略，是指随机选定的时间间隔的最小时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） |
| <*最大间隔*> | 字符串 | 对于指数式时间间隔策略，是指随机选定的时间间隔的最大时间间隔（采用 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)） |
||||

下面详细介绍不同的策略类型。

<a name="default-retry"></a>

### <a name="default"></a>默认

如果不指定重试策略，则操作将使用默认策略，它实际上是一个[指数式时间间隔策略](#exponential-interval)，按指数级增长的时间间隔（以 7.5 秒为增幅）最多发送 4 次重试。 时间间隔的范围为 5 到 45 秒。

尽管未在操作或触发器中显式定义，但默认策略在示例 HTTP 操作中的行为方式如下所示：

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>None

要指定操作或触发器不重试失败的请求，请将 <retry-policy-type> 设置为 `none`。

### <a name="fixed-interval"></a>固定间隔

要指定操作或触发器在等待指定的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `fixed`。

*示例*

该重试策略在首次请求失败后再尝试获取最新资讯两次，每次尝试之间延迟 30 秒：

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>指数间隔

要指定操作或触发器在等待随机的时间间隔后再发送下一个请求，请将 <retry-policy-type> 设置为 `exponential`。 随机时间间隔选自呈指数增长的范围。 此外，可通过自行指定最小时间间隔和最大时间间隔替代默认的最小和最大时间间隔。

**随机变量范围**

下表展示了逻辑应用如何为每次重试生成指定范围内的一个统一随机变量（不超过重试次数）：

| 重试次数 | 最小间隔 | 最大间隔 |
|--------------|------------------|------------------|
| 1 | 最大 (0，<*最小间隔*>)  | 最小 (间隔，<*最大间隔*>)  |
| 2 | 最大 (时间间隔，<*最小间隔*>)  | 最小 (2 * 间隔，<*最大间隔*>)  |
| 3 | 最大 (2 * 时间间隔，<*最小间隔*>)  | 最小 (4 * 时间间隔，<*最大间隔*>)  |
| 4 | 最大 (4 * 时间间隔，<*最小间隔*>)  | 最小 (8 * 时间间隔，<*最大间隔*>)  |
| .... | .... | .... |
||||

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>通过更改 "运行后的" 行为来捕获和处理故障

在逻辑应用设计器中添加操作时，会隐式声明要用于运行这些操作的顺序。 操作完成运行后，该操作会标记为、、或等状态 `Succeeded` `Failed` `Skipped` `TimedOut` 。 在每个操作定义中， `runAfter` 属性指定必须首先完成的前置任务操作以及该前置任务操作可以运行前该前置操作允许的状态。 默认情况下，你在设计器中添加的操作仅在前置任务完成且状态为后运行 `Succeeded` 。

当某个操作引发未处理的错误或异常时，将标记该操作 `Failed` 并标记任何后续操作 `Skipped` 。 如果对具有并行分支的操作发生此行为，则逻辑应用引擎将遵循其他分支来确定其完成状态。 例如，如果某个分支以 `Skipped` 操作结尾，则该分支的完成状态将基于该跳过的操作的前置状态。 逻辑应用运行完成后，通过评估所有分支状态，引擎确定整个运行的状态。 如果任何分支失败，将标记整个逻辑应用运行 `Failed` 。

![显示如何计算运行状态的示例](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

若要确保某个操作仍可以运行，而不管其前置状态如何，请 [自定义操作的 "后运行" 行为](#customize-run-after) ，以处理前置任务的不成功状态。

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>自定义 "运行后运行" 行为

您可以自定义操作的 "后运行" 行为，以便在前置任务状态为 `Succeeded` 、、 `Failed` `Skipped` 、 `TimedOut` 或任意状态时运行操作。 例如，若要在 Excel Online `Add_a_row_into_a_table` 前置任务操作被标记后发送电子邮件 `Failed` ，而不是 `Succeeded` 按以下任一步骤更改 "运行之后" 行为：

* 在 "设计" 视图中，选择 "省略号 (**...** ") 按钮，然后选择 " **配置在其后运行**"。

  ![为操作配置 "运行后运行" 行为](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  操作形状显示前置操作所需的默认状态，在此示例中，将 **行添加到表** 中：

  ![操作的默认 "运行之后" 行为](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  将 "之后运行" 行为更改为所需的状态，在此示例中 **已失败** ：

  ![将 "在此后运行" 行为更改为 "已失败"](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  若要指定操作是在前置操作被标记为或时 `Failed` 运行 `Skipped` `TimedOut` ，请选择 "其他" 状态：

  ![将 "运行后运行" 行为更改为具有任何其他状态](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* 在 "代码" 视图中，在操作的 JSON 定义中，编辑 `runAfter` 属性，该属性遵循以下语法：

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  对于本示例，请将 `runAfter` 属性从更改 `Succeeded` 为 `Failed` ：

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  若要指定操作是在前置操作被标记为的情况 `Failed` 下运行， `Skipped` 还是要 `TimedOut` 添加其他状态：

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>评估具有作用域的操作及其结果

与在使用属性执行单独操作后运行步骤类似 `runAfter` ，你可以将操作组合到一个 [作用域](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)内。 如果希望以逻辑方式将各个操作组合在一起，可以使用作用域，评估作用域的聚合状态，并基于该状态执行操作。 当某个作用域中的所有操作都完成运行后，该作用域本身也确定了其自己的状态。

若要检查作用域的状态，可以使用与检查逻辑应用的运行状态（例如、等）相同的条件 `Succeeded` `Failed` 。

默认情况下，当作用域的所有操作都成功时，作用域的状态将被标记为 `Succeeded` 。 如果作用域中的最后一个操作结果为 `Failed` 或 `Aborted` ，则将对该作用域的状态进行标记 `Failed` 。

若要在范围中捕获异常 `Failed` 并运行处理这些错误的操作，可以使用 `runAfter` 该范围的属性 `Failed` 。 这样，如果作用域中 *有任何* 操作失败，并且你使用 `runAfter` 该作用域的属性，则可以创建单个操作来捕获失败。

有关作用域的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>获取失败的上下文和结果

尽管从作用域中捕获失败非常有用，但可能还需要借助上下文来确切了解失败的操作以及返回的任何错误或状态代码。 [ `result()` 函数](../logic-apps/workflow-definition-language-functions-reference.md#result)通过接受单个参数（作用域的名称）并返回包含来自这些第一级操作的结果的数组，来返回作用域操作中的顶级操作的结果。 这些操作对象包含与函数返回的属性相同的属性 `actions()` ，例如操作的开始时间、结束时间、状态、输入、相关 id 和输出。 

> [!NOTE]
> `result()`函数 *仅* 返回第一级操作的结果，而不从更深的嵌套操作（如 switch 或 condition 操作）返回结果。

若要获取作用域中失败操作的上下文，可以使用 `@result()` 具有作用域的名称和属性的表达式 `runAfter` 。 若要将返回的数组筛选为具有状态的操作 `Failed` ，可以添加 [**筛选器数组** 操作](logic-apps-perform-data-operations.md#filter-array-action)。 若要为返回的失败操作运行操作，请获取返回的筛选数组，并 [**对每个** 循环](../logic-apps/logic-apps-control-flow-loops.md)使用。

下面是一个示例，接下来是一个详细说明，它使用 "My_Scope" 作用域操作中失败的任何操作的响应正文发送 HTTP POST 请求：

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

下面是描述此示例中发生的情况的详细演练：

1. 要获取“My_Scope”中所有操作的结果，**筛选数组** 操作将使用筛选表达式：`@result('My_Scope')`

1. **筛选数组** 的条件是 `@result()` 状态等于的任何项 `Failed` 。 此条件将具有“My_Scope”中所有操作结果的数组筛选为仅包含失败操作结果的数组。

1. 对 `For_each` *筛选的数组* 输出执行循环操作。 此步骤针对前面筛选的每个失败操作结果执行操作。

   如果作用域中的单个操作失败，则循环中的操作 `For_each` 只运行一次。 如果存在多个失败的操作，则将对每个失败执行一次操作。

1. 在 `For_each` 项响应正文中发送 HTTP POST，这是 `@item()['outputs']['body']` 表达式。

   `@result()` 项的形状与 `@actions()` 形状相同，可按相同的方式进行分析。

1. 包括两个自定义标头，其中包含失败操作的名称 (`@item()['name']`) 和失败的运行客户端跟踪 ID (`@item()['clientTrackingId']`)。

下面提供了单个 `@result()` 项的示例供参考，其中显示 `name`、`body` 和 `clientTrackingId` 属性已在前面的示例进行分析。 在 `For_each` 操作之外， `@result()` 返回这些对象的数组。

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

若要执行不同的异常处理模式，可以使用本文中前面所述的表达式。 您可以选择在接受整个筛选的故障数组的范围之外执行单个异常处理操作，并删除该 `For_each` 操作。 你还可以根据前面所述，将其他有用的属性包括在 `\@result()` 响应中。

## <a name="set-up-azure-monitor-logs"></a>设置 Azure Monitor 日志

上述模式非常适合于处理运行中的错误和异常，不过也可以独立于运行本身来标识和响应错误。 [Azure Monitor](../azure-monitor/overview.md) 提供了一种将所有工作流事件（包括所有运行和操作状态）发送到 [Log Analytics 工作区](../azure-monitor/logs/data-platform-logs.md)、 [Azure 存储帐户](../storage/blobs/storage-blobs-overview.md)或 [azure 事件中心](../event-hubs/event-hubs-about.md)的简单方法。

要评估运行状态，可以监视日志和指标，或将它们发布到你习惯使用的任何监视工具中。 一种潜在选项是通过事件中心将所有事件流式传输到 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)。 在流分析中，可以根据诊断日志中的任何异常、平均值或失败编写实时查询。 可以使用流分析将信息发送到其他数据源，例如队列、主题、SQL、Azure Cosmos DB 或 Power BI。

## <a name="next-steps"></a>后续步骤

* [了解如何使用 Azure 逻辑应用构建错误处理逻辑](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [查找更多逻辑应用示例和方案](../logic-apps/logic-apps-examples-and-scenarios.md)
