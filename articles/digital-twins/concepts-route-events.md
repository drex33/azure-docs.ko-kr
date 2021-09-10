---
title: 이벤트 경로
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 내에서, 다른 Azure 서비스로 이벤트를 라우팅하는 방법에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2718dd070cc12cb58f8033d5a2757d8aedf05e05
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771205"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Azure Digital Twins 내부 및 외부에서 이벤트 라우팅

Azure Digital Twins에서 **이벤트 경로** 를 사용하여 서비스 외부의 소비자에게 데이터를 전송합니다. 

Azure Digital Twins 데이터를 전송하는 두 가지 주요 사례는 다음과 같습니다.
* Azure Digital Twins 그래프의 한 트윈에서 다른 트윈으로 데이터 전송 예를 들어 하나의 디지털 트윈에서 속성이 변경되면 업데이트된 데이터를 기반으로 다른 디지털 트윈에 알리고 업데이트할 수 있습니다.
* 추가 스토리지 또는 처리를 위해 다운스트림 데이터 서비스로 데이터 전송(데이터 송신이라고도 함) 예를 들어
  - 병원은 대량 분석을 위해 손 씻기 관련 이벤트의 시계열 데이터를 기록하기 위해 Azure Digital Twins 이벤트 데이터를 [TSI(Time Series Insights)](../time-series-insights/overview-what-is-tsi.md)로 보낼 수 있습니다.
  - 이미 [Azure Maps](../azure-maps/about-azure-maps.md)를 사용하고 있는 비즈니스에서는 Azure Digital Twins를 사용하여 솔루션을 향상시킬 수 있습니다. Azure Digital Twins를 설정하고, 트윈 그래프에서 [디지털 트윈](concepts-twins-graph.md)으로 Azure Map 엔터티를 Azure Digital Twins로 가져온 후 Azure Map을 신속하게 사용하도록 설정하거나 Azure Maps 및 Azure Digital Twins 데이터를 함께 활용하는 강력한 쿼리를 실행할 수 있습니다.

이벤트 경로는 이러한 두 시나리오에 모두 사용됩니다.

## <a name="about-event-routes"></a>이벤트 경로 정보

이벤트 경로를 사용하면 Azure Digital Twins의 디지털 트윈에서 구독의 사용자 지정된 엔드포인트로 이벤트 데이터를 보낼 수 있습니다. 3개의 Azure 서비스는 현재 엔드포인트, 즉 [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md) 및 [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)에 지원됩니다. 이러한 각 Azure 서비스는 다른 서비스에 연결될 수 있으며, 매개자 역할을 하여 필요한 처리를 위해 TSI 또는 Azure Maps와 같은 최종 대상으로 데이터를 전송합니다.

다음 다이어그램은 Azure Digital Twins 측면을 사용하여 더 큰 IoT 솔루션을 통한 이벤트 데이터의 흐름을 보여 줍니다.

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="엔드포인트를 통해 여러 다운스트림 서비스로 데이터를 라우팅하는 Azure Digital Twins의 다이어그램." border="false":::

이벤트 경로에 대한 일반적인 다운스트림 대상은 TSI, Azure Maps, 스토리지 및 분석 솔루션과 같은 리소스입니다.

### <a name="event-routes-for-internal-digital-twin-events"></a>내부 디지털 트윈 이벤트에 대한 이벤트 경로

또한 이벤트 경로는 트윈 그래프 내의 이벤트를 처리하고 디지털 트윈 간 데이터를 전송하는 데 사용됩니다. 이 이벤트 처리 정렬 작업은 Event Grid를 통해 [Azure Functions](../azure-functions/functions-overview.md)와 같은 컴퓨팅 리소스에 이벤트 경로를 연결하여 수행됩니다. 그런 다음, 이러한 함수는 트윈이 이벤트를 수신하고 응답하는 방법을 정의합니다. 

컴퓨팅 리소스가 이벤트 경로를 통해 받은 이벤트에 따라 트윈 그래프를 수정하려는 경우 미리 수정할 트윈을 파악하는 것이 좋습니다. 

이벤트 메시지에는 메시지를 보낸 원본 트윈의 ID도 포함되므로 컴퓨팅 리소스는 쿼리 또는 트래버스 관계를 사용하여 원하는 작업에 대한 대상 트윈을 찾을 수 있습니다. 

또한 컴퓨팅 리소스는 보안 및 액세스 권한을 독립적으로 설정해야 합니다.

디지털 트윈 이벤트를 처리하도록 Azure 함수를 설정하는 프로세스를 살펴보려면 [트윈 간 이벤트 처리 설정](how-to-send-twin-to-twin-events.md)을 참조하세요.

## <a name="create-an-endpoint"></a>엔드포인트 만들기

이벤트 경로를 정의하려면 개발자는 먼저 엔드포인트를 정의해야 합니다. **엔드포인트** 는 라우팅 연결을 지원하는 Azure Digital Twins 외부의 대상입니다. 지원되는 대상은 다음과 같습니다.
* Event Grid 사용자 지정 토픽
* 이벤트 허브
* Service Bus

[엔드포인트를 만들](how-to-manage-routes.md#create-an-endpoint-for-azure-digital-twins)려면 Azure Digital Twins REST API, CLI 명령 또는 Azure Portal을 사용할 수 있습니다.

엔드포인트를 정의하는 경우 다음을 제공해야 합니다.
* 엔드포인트의 이름
* 엔드포인트 유형(Event Grid, Event Hub 또는 Service Bus)
* 인증할 기본 연결 문자열 및 보조 연결 문자열 
* 엔드포인트의 토픽 경로(예: *your-topic.westus2.eventgrid.azure.net*)

컨트롤 플레인에서 사용할 수 있는 엔드포인트 API는 다음과 같습니다.
* 엔드포인트 만들기
* 엔드포인트 목록 가져오기
* 이름으로 엔드포인트 가져오기
* 이름으로 엔드포인트 삭제

## <a name="create-an-event-route"></a>이벤트 경로 만들기
 
[이벤트 경로를 만들](how-to-manage-routes.md#create-an-event-route)려면 Azure Digital Twins REST API, CLI 명령 또는 Azure Portal을 사용할 수 있습니다.

다음은 `CreateOrReplaceEventRouteAsync` [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 호출을 사용하여 클라이언트 애플리케이션 내에서 이벤트 경로를 만드는 예제입니다. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. 먼저 `DigitalTwinsEventRoute` 개체가 만들어지고 생성자는 엔드포인트의 이름을 사용합니다. 이 `endpointName` 필드는 Event Hub, Event Grid 또는 Service Bus 등의 엔드포인트를 식별합니다. 이러한 엔드포인트는 구독에서 생성되고 이 등록을 호출하기 전에 컨트롤 플레인 API를 사용하여 Azure Digital Twins에 연결되어야 합니다.

2. 이벤트 경로 개체에는 이 경로를 따르는 이벤트 유형을 제한하는 데 사용할 수 있는 [필터](how-to-manage-routes.md#filter-events) 필드도 있습니다. `true` 필터는 추가 필터링 없이 경로를 사용하도록 설정합니다. `false` 필터는 경로를 사용하지 않도록 설정합니다. 

3. 그런 다음, 이 이벤트 경로 개체는 경로에 대한 이름과 함께 `CreateOrReplaceEventRouteAsync`에 전달됩니다.

> [!TIP]
> 모든 SDK 함수는 동기 및 비동기 버전으로 제공됩니다.

## <a name="dead-letter-events"></a>배달 못한 편지 이벤트

엔드포인트가 일정 시간 안에 또는 일정 횟수의 이벤트 배달 시도 후 이벤트를 배달할 수 없는 경우, 미배달 이벤트를 스토리지 계정에 보낼 수 있습니다. 이 프로세스를 **배달 못한 편지** 라고 합니다. **다음 조건 중 하나** 가 충족되면 Azure Digital Twins는 이벤트를 배달하지 않습니다. 

* 이벤트가 TTL(Time to Live) 기간 내에 배달되지 않은 경우
* 이벤트 전달 시도 횟수가 한도를 초과한 경우

조건 중 하나가 충족되면 이벤트가 삭제되거나 배달 못한 편지로 처리됩니다. 기본적으로 각 엔드포인트는 배달 못한 편지를 설정 **하지 않습니다**. 이 기능을 사용하려면 엔드포인트를 만들 때 전송되지 않은 이벤트를 보유할 스토리지 계정을 지정해야 합니다. 그런 다음, 이 스토리지 계정에서 이벤트를 끌어와 전송을 해결합니다.

배달 못한 편지 위치를 설정하기 전에 컨테이너를 포함하는 스토리지 계정이 있어야 합니다. 엔드포인트를 만들 때 이 컨테이너에 대한 URL을 제공합니다. 배달 못한 편지는 SAS 토큰과 함께 컨테이너 URL로 제공됩니다. 이 토큰은 스토리지 계정 내의 대상 컨테이너에 대한 `write` 권한만이 필요합니다. 완전히 구성된 URL의 형식은 다음과 같습니다. `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>`

SAS 토큰에 대해 자세히 알아보려면 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../storage/common/storage-sas-overview.md)를 참조하세요.

배달 못한 편지를 사용하여 엔드포인트를 설정하는 방법을 알아보려면 [Azure Digital Twins에서 엔드포인트 및 경로 관리](how-to-manage-routes.md#create-an-endpoint-with-dead-lettering)를 참조하세요.

### <a name="types-of-event-messages"></a>이벤트 메시지 형식

IoT Hub의 다양한 이벤트 유형 및 Azure Digital Twins는 아래에 설명된 대로 서로 다른 유형의 알림 메시지를 생성합니다.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>다음 단계

이벤트 경로를 설정하고 관리하는 방법을 참조하세요.
* [엔드포인트 및 경로 관리](how-to-manage-routes.md)

또는 Azure Functions를 사용하여 Azure Digital Twins 내에서 이벤트를 라우팅하는 방법을 참조하세요.
* [트윈 간 이벤트 처리를 설정](how-to-send-twin-to-twin-events.md)합니다.