---
title: .NET 和 AMQP 1.0 中的 Azure 服务总线 | Microsoft Docs
description: 本文介绍如何使用 AMQP（高级消息队列协议）通过 .NET 应用程序使用 Azure 服务总线。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 20800363327aefda073cd484dc737b28e60466a7
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632844"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>使用 AMQP 1.0 通过 .NET 使用服务总线

AMQP 1.0 支持在服务总线包 2.1 版或更高版本中提供。 为确保使用最新版本，可以从 [NuGet][NuGet]下载服务总线安装包。

> [!NOTE]
> 可以使用适用于服务总线的 .NET 库高级消息队列协议 (AMQP) 或 Service Bus 消息传送协议 (SBMP) 。 AMQP 是 .NET 库使用的默认协议。 建议你使用 AMQP 协议 (这是默认) ，而不是将其重写。 

## <a name="configure-net-applications-to-use-amqp-10"></a>将 .NET 应用程序配置为使用 AMQP 1.0

默认情况下，服务总线 .NET 客户端库使用 AMQP 协议与服务总线服务进行通信。 还可以显式指定 AMQP 作为传输类型，如以下部分所示。 

在当前版本中，有一些在使用 AMQP 时不受支持的 API 功能。 这些不受支持的功能在[行为差异](#behavioral-differences)部分列出。 在使用 AMQP 时，一些高级配置设置还具有不同的含义。

### <a name="configuration-using-appconfig"></a>使用 App.config 进行配置

应用程序使用 App.config 配置文件存储设置是一个很好的做法。 对于服务总线应用程序，可以使用 App.config 存储服务总线连接字符串。 示例 App.config 文件如下所示：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

`Microsoft.ServiceBus.ConnectionString` 设置的值是用于配置服务总线连接的服务总线连接字符串。 其格式如下所示：

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

其中 `namespace` 和 `SAS key` 是在创建服务总线命名空间时从 [Azure 门户][Azure portal]获取的。 有关详细信息，请参阅[使用 Azure 门户创建服务总线命名空间][Create a Service Bus namespace using the Azure portal]。

使用 AMQP 时，在连接字符串后面追加 `;TransportType=Amqp`。 此表示法指示客户端库使用 AMQP 1.0 连接到服务总线。

### <a name="amqp-over-websockets"></a>基于 WebSockets 的 AMQP
若要通过 Websocket 使用 AMQP，请 `TransportType` 在连接字符串中设置为 `AmqpWebSockets` 。 例如：`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`。 

如果使用的是 .NET ServiceBusConnection 库，请将 [TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) 设置为 [TransportType 枚举](/dotnet/api/microsoft.azure.servicebus.transporttype)的 AmqpWebSockets。

如果使用的是 .NET ServiceBusClient 库，请将 [TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclient.transporttype) 设置为 [ServiceBusTransportType 枚举](/dotnet/api/azure.messaging.servicebus.servicebustransporttype)的 AmqpWebSockets。


## <a name="message-serialization"></a>消息序列化

使用默认协议时，.NET 客户端库的默认序列化行为是使用 [DataContractSerializer][DataContractSerializer] 类型序列化 [BrokeredMessage][BrokeredMessage] 实例，以便在客户端库和服务总线服务之间传输。 使用 AMQP 传输模式时，客户端库使用 AMQP 类型系统将[中转消息][BrokeredMessage]序列化为 AMQP 消息。 此序列化使得消息能够由可能在不同平台上运行的接收应用程序接收和解释，例如，使用 JMS API 来访问服务总线的 Java 应用程序。

构造 [BrokeredMessage][BrokeredMessage] 实例时，可以提供 .NET 对象作为构造函数的参数以充当消息的正文。 对于可映射到 AMQP 基元类型的对象，正文序列化为 AMQP 数据类型。 如果该对象不能直接映射到 AMQP 基元类型（即，应用程序定义的自定义类型），则使用 [DataContractSerializer][DataContractSerializer]序列化对象，并且已序列化的字节将在 AMQP 数据消息中发送。

为了便于使用非 .NET 客户端进行互操作，仅在消息的正文中使用可直接序列化为 AMQP 类型的 .NET 类型。 下表详细介绍了这些类型以及到 AMQP 类型系统的相应映射。

| .NET 正文对象类型 | 映射的 AMQP 类型 | AMQP 正文部分类型 |
| --- | --- | --- |
| bool |boolean |AMQP 值 |
| 字节 |ubyte |AMQP 值 |
| ushort |ushort |AMQP 值 |
| uint |uint |AMQP 值 |
| ulong |ulong |AMQP 值 |
| sbyte |字节 |AMQP 值 |
| short |short |AMQP 值 |
| int |int |AMQP 值 |
| long |long |AMQP 值 |
| float |float |AMQP 值 |
| Double |Double |AMQP 值 |
| decimal |decimal128 |AMQP 值 |
| char |char |AMQP 值 |
| DateTime |timestamp |AMQP 值 |
| Guid |uuid |AMQP 值 |
| byte[] |binary |AMQP 值 |
| string |string |AMQP 值 |
| System.Collections.IList |list |AMQP 值：集合中包含的项只能是此表中定义的类型。 |
| System.Array |数组 |AMQP 值：集合中包含的项只能是此表中定义的类型。 |
| System.Collections.IDictionary |map |AMQP 值：集合中包含的项只能是此表中所定义的类型。注意：仅支持字符串键。 |
| Uri |描述型 string（请参阅下表） |AMQP 值 |
| DateTimeOffset |描述型 long（请参阅下表） |AMQP 值 |
| TimeSpan |描述型 long（请参阅下文） |AMQP 值 |
| Stream |binary |AMQP 数据（可能有多个）。 数据部分包含从流对象读取的原始字节。 |
| 其他对象 |binary |AMQP 数据（可能有多个）。 包含使用 DataContractSerializer 或应用程序提供的序列化程序的对象的已序列化二进制值。 |

| .NET 类型 | 映射的 AMQP 描述类型 | 注释 |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>行为差异

与默认协议相比，使用 AMQP 时在服务总线 .NET API 的行为方面也有一些细微的差异：

* 将忽略 [OperationTimeout][OperationTimeout] 属性。
* `MessageReceiver.Receive(TimeSpan.Zero)` 是以 `MessageReceiver.Receive(TimeSpan.FromSeconds(10))` 的形式实现的。
* 通过锁定令牌完成消息只能由最初收到消息的消息接收方完成。

## <a name="control-amqp-protocol-settings"></a>控制 AMQP 协议设置

[.NET API](/dotnet/api/) 公开了几项设置以控制 AMQP 协议的行为：

* [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)：控制应用于链接的初始额度。 默认值为 0。
* [MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)：控制在打开连接时协商期间提供的最大 AMQP 帧大小。 默认值为 65,536 字节。
* [MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)：如果可批量传输，则此值确定发送处置的最大延迟。 默认情况下由发送方/接收方继承。 单个发送方/接收方可以覆盖默认值（即 20 毫秒）。
* [MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)：控制是否通过 TLS 连接建立 AMQP 连接。 默认值为 **true**。

## <a name="next-steps"></a>后续步骤

准备好了解详细信息？ 请访问以下链接：

* [服务总线 AMQP 概述]
* [AMQP 1.0 协议指南]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[服务总线 AMQP 概述]: service-bus-amqp-overview.md
[AMQP 1.0 协议指南]: service-bus-amqp-protocol-guide.md