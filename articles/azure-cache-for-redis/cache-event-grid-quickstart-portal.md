---
title: '빠른 시작: Azure Portal을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅'
description: Azure Event Grid를 사용하여 Azure Cache for Redis 이벤트를 구독하고, 이벤트를 웹후크에 보내고, 웹 애플리케이션에서 이벤트를 처리합니다.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.custom:
- mode-portal
ms.openlocfilehash: 72596521850ba3b3b10ef572aa768672869ec7ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620923"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Cache for Redis 이벤트를 웹 엔드포인트로 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cache for Redis 인스턴스를 만들고, 해당 인스턴스에 대한 이벤트를 구독하고, 이벤트를 트리거하고, 결과를 확인합니다. 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 그러나 이 빠른 시작을 간소화하기 위해 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

완료되면 이벤트 데이터가 웹앱에 보내졌음을 확인할 수 있습니다.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="JSON 형식의 Azure Event Grid 뷰어 크기 조정":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Azure Cache for Redis 캐시 인스턴스 만들기

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

캐시 인스턴스에 대한 이벤트를 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

1. GitHub README에서 **Azure에 배포** 를 선택하여 솔루션을 구독에 배포합니다.

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Azure에 배포 단추":::

1. **사용자 지정 배포** 페이지에서 다음 단계를 수행합니다.
    1. **리소스 그룹** 에서 캐시 인스턴스를 만들 때 만든 리소스 그룹을 선택합니다. 자습서를 완료한 후 리소스 그룹을 삭제하면 더 쉽게 정리할 수 있습니다.  
    2. **사이트 이름** 에 웹앱의 이름을 입력합니다.
    3. **호스팅 계획 이름** 에 웹앱을 호스팅하는 데 사용할 App Service 계획의 이름을 입력합니다.
    4. **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다.
    5. **구매** 를 선택합니다.

    | 설정      | 제안 값  | 설명 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **구독** | 드롭다운하여 구독을 선택합니다. | 이 웹앱을 만들 구독입니다. |
    | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. |
    | **사이트 이름** | 웹앱의 이름을 입력합니다. | 이 값은 비워 둘 수 없습니다. |
    | **호스팅 계획 이름** | 웹앱을 호스팅하는 데 사용할 App Service 요금제의 이름을 입력합니다. | 이 값은 비워 둘 수 없습니다. |

1. 포털에서 경고(벨 아이콘)를 선택하고 **리소스 그룹으로 이동** 을 선택합니다.

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Azure Portal 배포 알림":::

1. **리소스 그룹** 페이지의 리소스 목록에서 자신이 만든 웹앱을 선택합니다. 이 목록에는 App Service 요금제 및 캐시 인스턴스도 표시됩니다.

1. 웹앱의 **App Service** 페이지에서 URL을 선택하여 웹 사이트로 이동합니다. URL은 `https://<your-site-name>.azurewebsites.net` 형식이어야 합니다.

1. 사이트가 보이지만 사이트에 이벤트가 아직 게시되지 않았음을 확인합니다.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="비어 있는 Event Grid 뷰어 사이트":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 구독

이 단계에서는 추적하려는 이벤트와 해당 이벤트를 보낼 위치를 Event Grid에 알리는 항목을 구독합니다.

1. 포털에서 이전에 만든 캐시 인스턴스로 이동합니다.
1. **Azure Cache for Redis** 페이지의 왼쪽 메뉴에서 **이벤트** 를 선택합니다.
1. **웹후크** 를 선택합니다. 엔드포인트에 대한 웹 후크를 사용하여 뷰어 앱에 이벤트를 보냅니다.

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure Portal 이벤트 페이지":::

1. **이벤트 구독 만들기** 페이지에서 다음을 입력합니다.

    | 설정      | 제안 값  | 설명 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **이름** | 이벤트 구독의 이름을 입력합니다. | 값은 3~64자여야 합니다. 문자, 숫자 및 대시만 포함할 수 있습니다. |
    | **이벤트 유형** | 드롭다운을 클릭하고 대상에 푸시할 이벤트 유형을 선택합니다. 이 빠른 시작에서는 캐시 인스턴스의 크기를 조정합니다. | 사용 가능한 옵션은 패치, 크기 조정, 가져오기 및 내보내기입니다. |
    | **엔드포인트 유형** | **웹후크** 를 선택합니다. | 이벤트를 받는 이벤트 처리기입니다. |
    | **엔드포인트** | **엔드포인트 선택** 을 선택하고, 웹앱의 URL을 입력하고, `api/updates`를 홈페이지 URL(예: `https://cache.azurewebsites.net/api/updates`)에 추가한 다음, **선택 확인** 을 선택합니다. | 이전에 만든 웹앱의 URL입니다. |

1. 이제 **이벤트 구독 만들기** 페이지에서 **만들기** 를 선택하여 이벤트 구독을 만듭니다.

1. 웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 웹앱은 구독의 유효성을 검사하는 코드를 포함합니다.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid 뷰어":::

## <a name="send-an-event-to-your-endpoint"></a>엔드포인트에 이벤트 보내기

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴 보겠습니다. Azure Cache for Redis 인스턴스의 크기를 조정합니다.

1. Azure Portal에서 Azure Cache for Redis 인스턴스로 이동하고, 왼쪽 메뉴에서 **크기 조정** 을 선택합니다.

1. **크기 조정** 페이지에서 원하는 가격 책정 계층을 선택하고, **선택** 을 선택합니다.

    다른 가격 책정 계층으로 크기를 조정할 수 있지만 다음과 같은 제한 사항이 있습니다.

    * 높은 가격 책정 계층에서 낮은 가격 책정 계층으로 크기를 조정할 수 없습니다.
      * **프리미엄** 캐시에서 **표준** 또는 **기본** 캐시로 축소할 수 없습니다.
      * **표준** 캐시에서 **기본** 캐시로 축소할 수 없습니다.
    * **기본** 캐시에서 **표준** 캐시로 크기를 조정할 수 있지만 동시에 크기를 변경할 수는 없습니다. 다른 크기가 필요한 경우 후속 크기 조정 작업을 통해 원하는 크기로 조정할 수 있습니다.
    * **기본** 캐시에서 바로 **프리미엄** 캐시로 확장할 수 없습니다. 먼저 크기 조정 작업을 통해 **기본** 에서 **표준** 으로 확장한 다음, 후속 크기 조정 작업을 통해 **표준** 에서 **프리미엄** 으로 확장합니다.
    * 더 큰 크기에서 **C0(250MB)** 크기로 축소할 수 없습니다.

    캐시가 새 가격 책정 계층으로 조정하는 동안에는 왼쪽의 **Azure Cache for Redis** 를 사용하여 **크기 조정 중** 상태가 표시됩니다. 크기 조정이 완료되면 상태가 **Scaling(크기 조정 중)** 에서 **실행 중** 으로 변경됩니다.

1. 이벤트를 트리거했고 Event Grid가 구독할 때 구성한 엔드포인트로 메시지를 보냈습니다. 메시지는 JSON 형식이며 하나 이상의 이벤트가 있는 배열을 포함합니다. 다음 예제에서 JSON 메시지는 이벤트가 1개 있는 배열을 포함합니다. 웹앱을 살펴보고, **ScalingCompleted** 이벤트를 받았는지 확인합니다.

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="JSON 형식의 Azure Event Grid 뷰어 크기 조정":::

## <a name="clean-up-resources"></a>리소스 정리

이 이벤트를 계속 사용하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 그렇지 않은 경우 이 빠른 시작에서 만든 리소스를 삭제합니다.

리소스 그룹을 선택하고 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이제 사용자 지정 항목 및 이벤트 구독을 만드는 방법에 대해 알아보았습니다. 다음으로 어떤 Event Grid가 도움이 되는지 자세히 알아보세요.

* [Azure Cache for Redis 이벤트에 대한 대응](cache-event-grid.md)
* [Event Grid 정보](../event-grid/overview.md)
