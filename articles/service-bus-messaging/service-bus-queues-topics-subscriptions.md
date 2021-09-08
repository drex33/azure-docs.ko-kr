---
title: Azure Service Bus 메시징 - 큐, 토픽 및 구독
description: 이 문서에서는 Azure Service Bus 메시징 엔터티(큐, 토픽 및 구독)에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 08/27/2021
ms.openlocfilehash: 8756da7f4000f5ef3b478567934ba99a90b0ab4c
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102270"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus 큐, 토픽 및 구독
Azure Service Bus는 신뢰할 수 있는 메시지 큐 및 지속형 게시/구독 메시징을 포함하여 클라우드 기반, 메시지 지향 미들웨어 기술 집합을 지원합니다. 이러한 조정된 메시징 기능은 Service Bus 메시징 워크로드를 사용하여 분리된 메시징 기능 게시-구독, 임시 분리 및 부하 분산 시나리오로 여겨질 수 있습니다. 분리된 통신에는 많은 이점이 있습니다. 예를 들어 필요하면 클라이언트와 서버가 연결되고 비동기 방식으로 작업을 수행할 수 있습니다.

Service Bus에서 메시지 기능의 핵심이 되는 메시지 엔터티는 **큐**, **토픽 및 구독**, 규칙/동작입니다.

## <a name="queues"></a>큐
큐는 하나 이상의 경쟁 소비자에게 **FIFO(선입선출)** 메시지 배달을 제공합니다. 즉, 일반적으로 수신기는 큐에 추가된 순서대로 메시지를 수신하여 처리합니다. 그리고 하나의 메시지 소비자만 각 메시지를 받고 처리합니다. 큐를 사용하는 주요 이점은 애플리케이션 구성 요소를 **임시로 분리** 할 수 있다는 점입니다. 즉, 생산자(발신자)와 소비자(수신자)가 동시에 메시지를 보내고 받을 필요가 없습니다. 메시지가 큐에 지속적으로 저장되기 때문입니다. 또한 생산자는 계속해서 메시지를 처리하고 보내기 위해 소비자의 회신을 기다릴 필요가 없습니다.

관련된 이점은 **부하 평준화** 로 생산자와 소비자가 서로 다른 속도로 메시지를 주고받을 수 있습니다. 많은 애플리케이션에서 시스템 부하는 시간에 따라 달라집니다. 하지만 각 작업 단위에 필요한 처리 시간은 일반적으로 일정합니다. 큐를 사용한 메시지 생산자와 소비자 조정은 소비 애플리케이션이 최대 부하가 아닌 평균 부하만 처리할 수 있으면 된다는 것을 의미합니다. 수신 부하가 변경됨에 따라 큐의 깊이가 증가하고 축소됩니다. 따라서 애플리케이션 부하를 처리하는 데 필요한 인프라의 크기와 관련하여 비용을 직접 절약할 수 있습니다. 부하가 증가하면 큐에서 읽을 작업자 프로세스가 더 추가될 수 있습니다. 각 메시지는 하나의 작업자 프로세스를 통해서만 처리됩니다. 또한 이 가져오기 기반 부하 분산에서는 작업자 컴퓨터가 최대 처리 용량으로 메시지를 가져오더라도 작업자 컴퓨터 사용률을 최적화할 수 있습니다. 이 패턴을 종종 **경쟁 소비자** 패턴이라고 합니다.

메시지 생산자와 소비자 간을 중개하는 큐를 사용하면 구성 요소 간에 내재하는 느슨한 연결을 제공합니다. 생산자와 소비자가 서로를 인식하지 않기 때문에 생산자에 영향을 주지 않고 소비자를 업그레이드할 수 있습니다.

### <a name="create-queues"></a>큐 만들기
[Azure Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md) 또는 [Resource Manager 템플릿](service-bus-resource-manager-namespace-queue.md)을 사용하여 큐를 만들 수 있습니다. 그런 다음 [C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [Python](service-bus-python-how-to-use-queues.md) 및 [JavaScript](service-bus-nodejs-how-to-use-queues.md)로 작성된 클라이언트를 사용하여 메시지를 보내고 받습니다. 

### <a name="receive-modes"></a>수신 모드
Service Bus에서 메시지를 받는 두 가지 모드를 지정할 수 있습니다.

- **받기 및 삭제**. 이 모드에서는 Service Bus가 소비자로부터 요청을 받으면 메시지를 사용되는 것으로 표시하고 소비자 애플리케이션에 반환합니다. 이 모드는 가장 간단한 모델입니다. 오류가 발생할 경우 애플리케이션에서 메시지 처리를 허용할 수 없는 시나리오에 가장 적합합니다. 이 시나리오를 이해하려면 소비자가 수신 요청을 실행한 다음, 처리하기 전에 충돌하는 시나리오를 고려합니다. Service Bus가 메시지를 사용 중인 것으로 표시하기 때문에 애플리케이션은 다시 시작할 때 메시지를 사용하기 시작합니다. 크래시 전에 사용된 메시지는 누락됩니다.
- **보기 및 잠금**. 이 모드에서는 수신 작업이 2단계이며, 메시지가 누락되면 안 되는 애플리케이션을 지원할 수 있습니다. 
    1. 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 **잠근** 후, 메시지를 애플리케이션에 반환합니다. 
    1. 애플리케이션은 메시지 처리를 완료한 후 Service Bus 서비스에 수신 프로세스의 두 번째 단계를 완료하도록 요청합니다. 그러면 서비스가 **메시지를 사용되는 것으로 표시** 합니다. 

        애플리케이션이 어떤 이유로든 메시지를 처리할 수 없는 경우 Service Bus 서비스에 메시지를 **중단** 하도록 요청할 수 있습니다. Service Bus가 메시지의 **잠금을 해제** 하여 다시 동일한 소비자나 다른 경쟁 소비자가 메시지를 받을 수 있도록 합니다. 두 번째로 잠금과 연결된 **시간 제한** 이 있습니다. 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우 Service Bus가 메시지를 잠금 해제하여 다시 받을 수 있게 합니다.

        애플리케이션이 메시지를 처리했지만 Service Bus 서비스에 메시지 완료를 요청하기 전에 크래시되는 경우 Service Bus는 애플리케이션이 다시 시작하면 메시지를 다시 배달합니다. 이 프로세스는 일반적으로 **최소 1회 처리** 라고 합니다. 즉, 각 메시지가 한 번 이상 처리됩니다. 그러나 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 시나리오에서 중복 처리를 허용할 수 없는 경우 애플리케이션에 다른 논리를 추가하여 중복을 검색합니다. 자세한 내용은 [중복 검색](duplicate-detection.md)을 참조하세요. 이 기능은 **정확히 한번** 처리라고 합니다.

        > [!NOTE]
        > 이러한 두 모드에 대한 자세한 내용은 [수신 작업 확인](message-transfers-locks-settlement.md#settling-receive-operations)을 참조하세요.

## <a name="topics-and-subscriptions"></a>토픽 및 구독
큐를 사용하면 각 메시지를 단일 소비자가 처리할 수 있습니다. 큐와 달리, 토픽 및 구독은 **게시-구독** 패턴으로 일 대 다 형태의 통신을 제공합니다. 이는 다수의 수신자로 확장하는 데 유용합니다. 게시된 각 메시지는 토픽에 등록된 각 구독에서 사용할 수 있습니다. 게시자가 메시지를 토픽에 보내고, 하나 이상의 구독자가 이러한 구독에 설정된 필터 규칙에 따라 메시지의 복사본을 받습니다. 구독은 추가 필터를 사용하여 수신하려는 메시지를 제한할 수 있습니다. 게시자는 메시지를 큐에 보내는 것과 동일한 방식으로 토픽에 메시지를 보냅니다. 그러나 소비자는 토픽에서 직접 메시지를 받지 않습니다. 대신, 소비자는 토픽의 구독에서 메시지를 받습니다. 토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 소비자는 큐에서 메시지를 받는 것과 동일한 방식으로 구독에서 메시지를 받습니다.

큐의 메시지 보내기 기능은 토픽에 해당하고 메시지 받기 기능은 구독에 해당합니다. 다른 기능 중에서 이 기능은 구독에서 큐와 관련하여 이 섹션의 앞부분에서 설명한 동일한 패턴(경쟁 소비자, 임시 분리, 부하 평준화 및 부하 분산)을 지원한다는 것을 의미합니다.

### <a name="create-topics-and-subscriptions"></a>토픽 및 구독 만들기
토픽을 만드는 것은 이전 섹션에서 설명한 대로 큐를 만드는 것과 비슷합니다. [Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-tutorial-topics-subscriptions-cli.md) 또는 [Resource Manager 템플릿](service-bus-resource-manager-namespace-topic.md)을 사용하여 토픽과 구독을 만들 수 있습니다. 그런 다음 [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [Python](service-bus-python-how-to-use-topics-subscriptions.md) 및 [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)로 작성된 클라이언트를 사용하여 토픽에 메시지를 보내고 구독에서 메시지를 받습니다. 

### <a name="rules-and-actions"></a>규칙 및 동작
대부분의 시나리오에서 특정 특성을 가진 메시지를 다른 방법으로 처리해야 합니다. 이 처리를 사용하려면 desired 속성이 있는 메시지를 찾은 다음, 해당 속성에 대한 특정 수정 작업을 수행하도록 구독을 구성하면 됩니다. Service Bus 구독이 토픽으로 전송된 모든 메시지를 확인하는 동안 가상 구독 큐로 이러한 메시지의 하위 집합을 복사할 수 있습니다. 이 필터링은 구독 필터를 사용하여 수행됩니다. 이와 같은 수정을 **필터 동작** 이라고 합니다. 구독을 만들 때 메시지의 속성에 대해 작동하는 필터 식을 제공할 수 있습니다. 속성에는 시스템 속성(예: **Label**) 및 사용자 지정 애플리케이션 속성(예: **StoreName**)이 모두 있을 수 있습니다. 이 경우 SQL 필터 식은 선택 사항입니다. SQL 필터 식이 없으면 구독에 정의된 필터 작업은 해당 구독의 모든 메시지에서 수행됩니다.

전체 작동하는 예는 GitHub에서 [TopicFilters 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples/TopicFilters)을 참조하세요.

필터에 대한 자세한 내용은 [토픽 필터 및 작업](topic-filters.md)을 참조하세요.

## <a name="java-message-service-jms-20-entities"></a>JMS(Java Message Service) 2.0 엔터티
다음 엔터티는 JMS(Java Message Service) 2.0 API를 통해 액세스할 수 있습니다.

  * 임시 큐
  * 임시 항목
  * 공유되는 지속형 구독
  * 공유되지 않는 지속형 구독
  * 공유되는 비지속형 구독
  * 공유되지 않는 비지속형 구독

[JMS 2.0 엔터티](java-message-service-20-entities.md) 및 [사용](how-to-use-java-message-service-20.md) 방법에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

선택한 언어로 샘플을 사용하여 Azure Service Bus 기능을 살펴봅니다. 

- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(최신)](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Python용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [JavaScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript용 Azure Service Bus 클라이언트 라이브러리 샘플](/samples/azure/azure-sdk-for-js/service-bus-typescript/)

이전 .NET 및 Java 클라이언트 라이브러리에 대한 샘플은 다음을 참조하세요.
- [.NET용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [Java용 Azure Service Bus 클라이언트 라이브러리 샘플(레거시)](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)