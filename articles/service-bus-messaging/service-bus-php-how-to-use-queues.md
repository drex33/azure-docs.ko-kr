---
title: PHP에서 Azure Service Bus 큐를 사용하는 방법
description: 이 문서에서는 Service Bus 큐와 메시지를 주고 받는 PHP 애플리케이션을 만드는 방법에 대해 알아봅니다.
services: service-bus-messaging
ms.devlang: PHP
ms.topic: how-to
ms.date: 07/23/2021
ms.openlocfilehash: d0909c4e2ea5ef53a1438ce05f5ddad485c80248
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725134"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>PHP에서 Service Bus 큐를 사용하는 방법
[!INCLUDE [service-bus-selector-queues](./includes/service-bus-selector-queues.md)]

이 문서에서는 Service Bus 큐와 메시지를 주고 받는 PHP 애플리케이션을 만드는 방법에 대해 알아봅니다. 

> [!IMPORTANT]
> 2021년 2월부터 PHP용 Azure SDK는 사용 중지 단계로 전환되었으며 더 이상 Microsoft에서 공식적으로 지원되지 않습니다. 자세한 내용은 GitHub의 [공지](https://github.com/Azure/azure-sdk-for-php#important-annoucement)를 참조하세요. 이 문서는 곧 사용 중지됩니다. 

## <a name="prerequisites"></a>필수 조건
1. Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
2. 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다.
    1. Service Bus **큐** 의 빠른 **개요** 를 읽습니다. 
    2. Service Bus **네임스페이스** 를 만듭니다. 
    3. **연결 문자열** 을 가져옵니다. 

        > [!NOTE]
        > 이 문서에서는 PHP를 사용하여 Service Bus 네임스페이스에 **큐** 를 만들 것입니다. 
3. [PHP용 Azure SDK](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>PHP 애플리케이션 만들기
Azure Blob service에 액세스하는 PHP 애플리케이션을 만드는 데 유일한 요구 사항은 코드 내에서 [PHP용 Azure SDK](https://github.com/Azure/azure-sdk-for-php)의 클래스를 참조하는 것입니다. 어떠한 개발 도구를 사용해도 애플리케이션 또는 메모장을 만들 수 있습니다.

> [!NOTE]
> PHP를 설치하려면 [OpenSSL 확장](https://php.net/openssl)도 설치되어 있고 사용하도록 설정되어 있어야 합니다.

이 가이드에서는 PHP 애플리케이션 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 서비스 기능을 사용합니다.

## <a name="get-the-azure-client-libraries"></a>Azure 클라이언트 라이브러리 가져오기
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 애플리케이션 구성
Service Bus 큐 API를 사용하려면 다음을 수행합니다.

1. [require_once][require_once] 문을 사용하여 자동 로더 파일을 참조합니다.
2. 사용할 수 있는 모든 클래스 참조

다음 예제에서는 자동 로더 파일을 포함하고 `ServicesBuilder` 클래스를 참조하는 방법을 보여 줍니다.

> [!NOTE]
> 이 예제 및 이 문서의 다른 예제에서는 작성기를 통해 Azure용 PHP 클라이언트 라이브러리를 설치했다고 가정합니다. 라이브러리를 수동으로 또는 PEAR 패키지로 설치한 경우 **WindowsAzure.php** 자동 로더 파일을 참조해야 합니다.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

아래 예제에서 `require_once` 문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

## <a name="set-up-a-service-bus-connection"></a>Service Bus 연결 설정
Service Bus 클라이언트를 인스턴스화하려면 먼저 다음 형식의 유효한 연결 문자열이 있어야 합니다.

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

여기서 `Endpoint`는 일반적으로 `[yourNamespace].servicebus.windows.net` 형식입니다.

Azure 서비스 클라이언트를 만들려면 `ServicesBuilder` 클래스를 사용해야 합니다. 다음을 할 수 있습니다.

* 연결 문자열을 직접 전달합니다.
* **CCM(CloudConfigurationManager)** 을 사용하여 여러 외부 소스에서 연결 문자열을 확인할 수 있습니다.
  * 기본적으로 하나의 외부 소스, 환경 변수에 대한 지원이 제공됩니다.
  * `ConnectionStringSource` 클래스를 확장하여 새 소스를 추가할 수 있습니다.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>큐 만들기
`ServiceBusRestProxy` 클래스를 사용하여 Service Bus 큐에 대한 관리 작업을 수행할 수 있습니다. `ServiceBusRestProxy` 개체는 관리에 필요한 토큰 사용 권한을 캡슐화하는 적절한 연결 문자열을 사용한 `ServicesBuilder::createServiceBusService` 팩터리 메서드를 통해 구성됩니다.

다음 예제에서는 `ServiceBusRestProxy`를 인스턴스화하고 `ServiceBusRestProxy->createQueue`을 호출하여 `MySBNamespace` 네임스페이스 내에 이름이 `myqueue`인 큐를 만드는 방법을 보여줍니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> `ServiceBusRestProxy` 개체의 `listQueues` 메서드를 사용하여 네임스페이스 내에 지정된 이름의 큐가 이미 있는지 확인할 수 있습니다.
> 
> 

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
Service Bus 큐에 메시지를 보내기 위해 애플리케이션은 `ServiceBusRestProxy->sendQueueMessage` 메서드를 호출합니다. 다음 코드는 위에서 `MySBNamespace` 서비스 네임스페이스 내에서 이전에 만든 `myqueue` 큐에 메시지를 보내는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Service Bus 큐와 주고 받은 메시지는 [BrokeredMessage][BrokeredMessage] 클래스 인스턴스입니다. [BrokeredMessage][BrokeredMessage] 개체에는 애플리케이션별 사용자 지정 속성을 저장하는 데 사용되는 표준 메서드 및 속성 세트와 임의 애플리케이션 데이터 본문이 있습니다.

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 큐 크기의 상한은 5GB입니다.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기

큐에서 메시지를 받는 가장 쉬운 방법은 `ServiceBusRestProxy->receiveQueueMessage` 메서드를 사용하는 것입니다. 메시지는 [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) 및 [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock)의 두 가지 모드로 받을 수 있습니다. **PeekLock** 이 기본값입니다.

[ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, Service Bus가 큐의 메시지에 대한 읽기 요청을 받으면 메시지를 이용되는 것으로 표시하고 애플리케이션에 반환합니다. [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) 모드는 가장 단순한 모델이며, 애플리케이션이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus는 메시지를 이용되는 것으로 표시하기 때문에 애플리케이션이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

기본값인 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) 모드에서는 메시지 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 애플리케이션을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후에 받은 메시지를 `ServiceBusRestProxy->deleteMessage`에 전달하여 수신 프로세스의 두 번째 단계를 완료합니다. Service Bus는 `deleteMessage` 호출을 확인한 후 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

다음 예제에서는 [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) 모드(기본 모드)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신자 애플리케이션이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 `deleteMessage` 메서드 대신 `unlockMessage` 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 큐 메시지의 잠금을 해제하므로 동일한 소비 애플리케이션이나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 애플리케이션에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `deleteMessage` 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이를 *최소 한 번 이상 처리* 라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 중복 메시지 배달을 처리하는 논리를 애플리케이션에 추가하는 것이 좋습니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 `getMessageId` 메서드를 사용합니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계
지금까지 Service Bus 큐의 기본 사항에 대해 알아보았습니다. 자세한 내용은 [큐, 토픽 및 구독][Queues, topics, and subscriptions]을 참조하세요.

자세한 내용은 [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)를 참조하세요.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


