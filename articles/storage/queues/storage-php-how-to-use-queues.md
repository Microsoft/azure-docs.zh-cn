---
title: 如何通过 PHP 使用队列存储-Azure 存储
description: 了解如何使用 Azure 队列存储服务创建和删除队列，以及插入、获取和删除消息。 示例用 PHP 编写。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 69369d81892a10c390aa31a2c46f79fdfa41206d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592018"
---
# <a name="how-to-use-queue-storage-from-php"></a>如何通过 PHP 使用队列存储

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

本指南演示如何使用 Azure 队列存储服务执行常见方案。 这些示例是通过 [用于 PHP 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-php)中的类编写的。 介绍的方案包括插入、扫视、获取和删除队列消息以及创建和删除队列。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>创建 PHP 应用程序

创建访问 Azure 队列存储的 PHP 应用程序的唯一要求是从代码中引用 [用于 php 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-php) 中的类。 可以使用任何开发工具（包括“记事本”）创建应用程序。

在本指南中，将使用队列存储服务功能，可在 PHP 应用程序中本地调用这些功能，或在 Azure 中的 web 应用程序中运行的代码中调用这些功能。

## <a name="get-the-azure-client-libraries"></a>获取 Azure 客户端库

### <a name="install-via-composer"></a>通过编辑器安装

1. 在项目的根目录中创建一个名为的文件 `composer.json` ，并向其中添加以下代码：

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. 下载 [`composer.phar`](https://getcomposer.org/composer.phar) 到项目根目录。

3. 打开命令提示符，并在项目根目录中运行以下命令：

    ```console
    php composer.phar install
    ```

或者，请在 GitHub 上的 [Azure 存储 PHP 客户端库](https://github.com/Azure/azure-storage-php) 中，克隆源代码。

## <a name="configure-your-application-to-access-queue-storage"></a>配置应用程序以访问队列存储

若要使用 Azure 队列存储的 Api，需要：

1. 使用语句引用自动加载器文件 [`require_once`](https://www.php.net/manual/en/function.require-once.php) 。
2. 引用可使用的所有类。

下面的示例演示了如何包括 autoloader 文件并引用 `QueueRestProxy` 类。

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

在下面的示例中， `require_once` 语句始终显示，但只引用运行此示例所需的类。

## <a name="set-up-an-azure-storage-connection"></a>设置 Azure 存储连接

若要实例化 Azure 队列存储客户端，必须首先具有有效的连接字符串。 队列存储连接字符串的格式如下所示。

若要访问实时服务：

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

若要访问模拟器存储：

```php
UseDevelopmentStorage=true
```

若要创建 Azure 队列存储客户端，需要使用 `QueueRestProxy` 类。 可使用以下方法之一：

- 将连接字符串直接传递给它。
- 使用 web 应用中的环境变量来存储连接字符串。 要配置连接字符串，请参阅 [Azure Web 应用配置设置](../../app-service/configure-common.md)文档。

在此处列出的示例中，将直接传递连接字符串。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>创建队列

`QueueRestProxy`对象允许您通过使用方法来创建队列 `CreateQueue` 。 创建队列时，可以在该队列上设置选项，但此操作不是必需的。 此示例演示如何在队列上设置元数据。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> 不应依赖元数据密钥区分大小写的性质。 以小写形式从服务中读取所有密钥。

## <a name="add-a-message-to-a-queue"></a>向队列添加消息

若要将消息添加到队列，请使用 `QueueRestProxy->createMessage` 。 此方法接受队列名称、消息文本和消息选项（这些都是可选的）。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>扫视下一条消息

通过调用，你可以扫视队列前面的一条或多条消息，而不必从队列中将其删除 `QueueRestProxy->peekMessages` 。 默认情况下，该 `peekMessage` 方法将返回单个消息，但你可以使用方法更改该值 `PeekMessagesOptions->setNumberOfMessages` 。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>取消对下一条消息的排队

代码分两步从队列中删除消息。 首先，调用 `QueueRestProxy->listMessages` ，这会使消息对从队列中读取的任何其他代码不可见。 默认情况下，此消息持续 30 秒不可见。  (如果此时间段内未删除该消息，则该消息将在队列中再次可见。 ) 若要完成从队列中删除消息，必须调用 `QueueRestProxy->deleteMessage` 。 此删除消息的两步过程可确保当代码因硬件或软件故障而无法处理消息时，其他代码实例可以获取同一消息并重试。 代码在处理消息后会立即调用 `deleteMessage`。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>更改已排队消息的内容

通过调用，可以在队列中就地更改消息的内容 `QueueRestProxy->updateMessage` 。 如果消息表示工作任务，可使用此功能来更新该工作任务的状态。 以下代码使用新内容更新队列消息，并将可见性超时设置为再延长 60 秒。 这会保存与消息关联的工作的状态，并额外为客户端提供一分钟的时间来继续处理消息。 您可以使用此方法跟踪队列消息上的多步骤工作流，而无需在处理步骤因硬件或软件故障而失败时从头开始重新开始。 通常同时保留重试计数，当消息重试次数超过 *n* 时再删除该消息。 这可避免每次处理某条消息时都触发应用程序错误。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>用于取消对消息进行排队的其他选项

可通过两种方式自定义队列中的消息检索。 首先，可获取一批消息（最多 32 条）。 其次，可设置更长或更短的可见超时时间，允许代码使用更长或更短的时间来彻底处理每条消息。 以下代码示例使用 `getMessages` 方法在一次调用中获取 16 条消息。 然后，它使用循环处理每条消息 `for` 。 它还将每条消息的不可见超时时间设置为 5 分钟。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>获取队列长度

可以获取队列中消息的估计数。 `QueueRestProxy->getQueueMetadata`方法检索有关队列的元数据。 `getApproximateMessageCount`对返回的对象调用方法将提供队列中消息的计数。 此计数仅为近似值，因为在队列存储响应请求后，可以添加或删除消息。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>删除队列

若要删除队列及其中的所有消息，请调用 `QueueRestProxy->deleteQueue` 方法。

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>后续步骤

现在，你已了解有关 Azure 队列存储的基础知识，请单击下面的链接了解更复杂的存储任务：

- 请访问 [Azure 存储 PHP 客户端库的 API 参考](https://azure.github.io/azure-storage-php/)
- 请参阅 [高级队列示例](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)。

有关详细信息，请参阅 [PHP 开发人员中心](https://azure.microsoft.com/develop/php/)。
