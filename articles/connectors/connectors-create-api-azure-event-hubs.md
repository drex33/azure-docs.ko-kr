---
title: Azure Event Hubs에 대한 연결
description: 이벤트 허브에 연결하고 Azure Logic Apps의 워크플로에 트리거 또는 작업을 추가합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/16/2021
tags: connectors
ms.openlocfilehash: 079d131cac55c6d7a54547a3720546ab6422f7d5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114473079"
---
# <a name="connect-to-an-event-hub-from-workflows-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로에서 이벤트 허브에 연결

Azure Event Hubs 커넥터를 사용하면 논리 앱 워크플로를 Azure의 이벤트 허브에 연결할 수 있습니다. 그런 다음, 이벤트 허브로 전송되는 이벤트를 워크플로에서 모니터링하고 관리하도록 할 수 있습니다. 예를 들어 워크플로는 이벤트 허브에서 이벤트를 확인하고 보내고 받을 수 있습니다. 이 문서에서는 이벤트 허브에 연결하고 Event Hubs 트리거 또는 작업을 워크플로에 추가하는 방법을 보여주면서 Azure Event Hubs 커넥터 사용을 위한 시작 가이드를 제공합니다.

Azure Event Hubs 또는 Azure Logic Apps에 대한 자세한 내용은 [Azure Event Hubs 정의](../event-hubs/event-hubs-about.md) 또는 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 참조하세요.

## <a name="available-operations"></a>사용 가능한 작업

모든 작업 및 기타 기술 정보(예: 속성, 한도 등)는 [Event Hubs 커넥터의 참조 페이지](/connectors/eventhubs/)를 검토하세요.

> [!NOTE]
> [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 호스트되는 논리 앱에서는 커넥터의 ISE 버전이 [ISE 메시지 한도](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* [Event Hubs 네임스페이스 및 이벤트 허브](../event-hubs/event-hubs-create.md)

* 이벤트 허브에 액세스하려는 논리 앱 워크플로

  Event Hubs 트리거를 사용하여 워크플로를 시작하려면 빈 워크플로가 필요합니다. [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 처음 사용하는 경우 이 [빠른 시작을 참조하여 예제 논리 앱 워크플로를 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>사용 권한 확인 및 연결 문자열 가져오기

워크플로에서 이벤트 허브에 액세스할 수 있는지 확인하려면 권한을 확인한 다음, 이벤트 허브의 네임스페이스에 대한 연결 문자열을 가져옵니다.

1. [Azure Portal](https://portal.azure.com)에서 특정 이벤트 허브가 아닌 Event Hubs *네임스페이스* 로 이동합니다.

1. 네임스페이스 메뉴의 **설정** 에서 **공유 액세스 정책** 을 선택합니다. **클레임** 열에서 해당 네임스페이스에 대해 최소한 **관리** 권한이 있는지 확인합니다.

   ![Azure Portal, Event Hubs 네임스페이스 및 "클레임" 열에 "관리" 권한이 보이는 스크린샷](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. 연결 정보를 나중에 수동으로 입력하려는 경우 이벤트 허브 네임스페이스에 대한 연결 문자열을 가져옵니다.

   1. **정책** 열에서 **RootManageSharedAccessKey** 를 선택합니다.

   1. 기본 키 연결 문자열을 찾습니다. 나중에 사용할 수 있도록 연결 문자열을 복사하고 저장합니다.

      ![복사 단추가 선택된 기본 키의 연결 문자열을 보여주는 스크린샷](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > 연결 문자열이 Event Hubs 네임스페이스 또는 특정 Event Hub와 연결되어 있는지 확인하려면 연결 문자열에 `EntityPath` 매개 변수가 있는지 확인합니다. 이 매개 변수를 찾으면 연결 문자열은 특정 Event Hubs "엔터티"에 대한 것이며 워크플로에 사용할 올바른 문자열이 아닙니다.

<a name="create-connection"></a>

## <a name="create-an-event-hub-connection"></a>이벤트 허브 연결 만들기

Event Hubs 트리거 또는 작업을 처음 추가하면 이벤트 허브에 대한 연결을 만들라는 메시지가 표시됩니다.

1. 메시지가 표시되면 다음 옵션 중 하나를 선택합니다.

   * 다음 연결 정보를 제공합니다.

     | 속성 | 필수 | 값 | 설명 |
     |----------|----------|-------|-------------|
     | **연결 이름** | 예 | <*connection-name*> | 연결에 만들 이름 |
     | **Event Hubs 네임스페이스** | 예 | <*event-hubs-namespace*> | 사용하려는 Event Hubs 네임스페이스를 선택합니다. |
     |||||

   * 이전에 저장한 연결 문자열을 수동으로 입력하려면 **수동으로 연결 정보 입력** 을 선택합니다. [연결 문자열을 찾는 방법](#permissions-connection-string)을 알아보세요.

1. 선택하지 않은 경우 사용할 Event Hubs 정책을 선택한 다음, **만들기** 를 선택합니다.

   ![제공된 연결 정보와 "만들기"가 선택된 상태를 보여주는 스크린샷](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

1. 연결을 만든 후에 [Event Hubs 트리거 추가](#add-trigger) 또는 [Event Hubs 작업 추가](#add-action)를 계속 진행합니다.

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Event Hubs 트리거 추가

Azure Logic Apps에서 모든 워크플로는 특정 조건이 충족될 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)로 시작해야 합니다. 트리거가 실행될 때마다 Logic Apps 서비스는 워크플로 인스턴스를 만들고 워크플로의 단계를 실행하기 시작합니다.

다음 단계에서는 트리거를 추가하는 일반적인 방법을 설명합니다. 예를 들어 **이벤트 허브에서 이벤트를 사용할 수 있는 경우** 입니다. 이 예에서는 이벤트 허브에서 새 이벤트가 있는지 확인하고 새 이벤트가 있으면 워크플로 실행을 시작하는 트리거를 추가하는 방법을 보여줍니다.

1. Logic Apps 디자이너에서 빈 논리 앱 워크플로를 엽니다(아직 열려 있지 않은 경우).

1. 작업 검색 상자에서 `event hubs`를 입력합니다. 트리거 목록에서 **이벤트가 Event Hub에서 사용 가능한 경우** 라는 이름의 트리거를 선택합니다.

   ![트리거 선택](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. 이벤트 허브에 대한 연결을 생성하라는 메시지가 표시되면 [요청된 연결 정보를 제공](#create-connection)합니다.

1. 트리거에서 모니터링할 이벤트 허브에 대한 정보를 제공합니다. 예를 들면:

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이벤트 허브 이름** | 예 | 모니터링할 이벤트 허브의 이름 |
   | **콘텐츠 형식** | 아니요 | 이벤트의 콘텐츠 형식입니다. 기본값은 `application/octet-stream`입니다. |
   | **소비자 그룹 이름** | 예 | 이벤트를 읽는 데 사용할 [Event Hubs 소비자 그룹의 이름](../event-hubs/event-hubs-features.md#consumer-groups)입니다. 지정하지 않으면 기본값 소비자 그룹이 사용됩니다. |
   | **최대 이벤트 수** | 아니요 | 최대 이벤트 수입니다. 트리거는 1과 이 속성으로 지정된 이벤트의 수 범위의 숫자를 반환합니다. |
   | **간격** | 예 | 빈도에 따라 워크플로가 실행되는 빈도를 설명하는 양의 정수 |
   | **빈도** | 예 | 되풀이 시간 단위 |
   ||||

   추가 속성을 보려면 **새 매개 변수 추가** 목록을 엽니다. 매개 변수를 선택하면 해당 속성이 트리거에 추가됩니다. 예를 들면 다음과 같습니다.

   ![트리거 속성](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   **추가 속성**

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **콘텐츠 스키마** | 예 | 이벤트 허브에서 읽을 이벤트의 JSON 콘텐츠 스키마입니다. 예를 들어 콘텐츠 스키마를 지정하는 경우 스키마와 일치하는 이벤트에 대해서만 워크플로를 트리거할 수 있습니다. |
   | **최소 파티션 키** | 아니요 | 읽을 최소 [파티션](../event-hubs/event-hubs-features.md#partitions) ID를 입력합니다. 기본적으로 모든 파티션이 읽혀집니다. |
   | **최대 파티션 키** | 아니요 | 읽을 최대 [파티션](../event-hubs/event-hubs-features.md#partitions) ID를 입력합니다. 기본적으로 모든 파티션이 읽혀집니다. |
   | **표준 시간대** | 아니요 | 이 트리거는 UTC 오프셋을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. <p>자세한 내용은 [Azure Logic Apps를 사용하여 되풀이 작업과 워크플로 만들기 및 실행](../connectors/connectors-native-recurrence.md)을 참조하세요. |
   | **시작 시간** | 아니요 | 시작 시간을 다음 형식으로 입력합니다. <p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss<p>또는<p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ<p>자세한 내용은 [Azure Logic Apps를 사용하여 되풀이 작업과 워크플로 만들기 및 실행](../connectors/connectors-native-recurrence.md)을 참조하세요. |
   ||||

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

1. 이제 트리거 출력을 사용하여 다른 작업을 수행할 수 있도록 하나 이상의 작업을 계속 추가합니다.

   예를 들어 특정 값(예: 범주)에 따라 이벤트를 필터링하려면 **이벤트 보내기** 작업에서 조건에 맞는 이벤트만 보내도록 조건을 추가하면 됩니다. 

## <a name="trigger-polling-behavior"></a>트리거 폴링 동작

모든 Event Hubs 트리거는 긴 폴링 트리거입니다. 이 동작은 트리거가 발생할 때 모든 이벤트를 처리한 다음, 더 많은 이벤트가 이벤트 허브에 표시되도록 30초 동안 대기함을 의미합니다. 설계상, 30초 동안 표시되는 이벤트가 없으면 트리거를 건너뜁니다. 그렇지 않으면 트리거는 이벤트 허브가 빈 상태가 될 때까지 이벤트를 계속 읽습니다. 다음 트리거 폴링은 트리거 속성에 설정한 되풀이 간격을 기준으로 발생합니다.

예를 들어 트리거가 4개의 파티션으로 설정된 경우 이 지연 시간은 트리거가 모든 파티션 폴링을 완료하기까지 최대 2분이 걸릴 수 있습니다. 이 지연 시간 내에 이벤트가 수신되지 않으면 트리거 실행을 건너뜁니다. 그렇지 않으면 트리거는 이벤트 허브가 빈 상태가 될 때까지 이벤트를 계속 읽습니다. 다음 트리거 폴링은 트리거의 속성에 지정한 되풀이 간격을 기준으로 발생합니다.

메시지가 표시되는 특정 파티션을 알고 있는 경우 이 파티션의 이벤트만 읽거나, 트리거의 최대 및 최소 파티션 키를 설정하여 해당 파티션의 이벤트를 읽도록 트리거를 업데이트할 수 있습니다. 자세한 내용은 [Event Hubs 트리거 추가](#add-trigger) 섹션을 참조하세요.
     
## <a name="trigger-checkpoint-behavior"></a>검사점 동작 트리거

Event Hubs 트리거가 이벤트 허브의 각 파티션에서 이벤트를 읽는 경우, 트리거는 자체 상태를 사용하여 스트림 오프셋(파티션의 이벤트 위치) 및 트리거가 이벤트를 읽는 파티션에 대한 정보를 유지합니다.

워크플로가 실행될 때마다 트리거는 트리거 상태에 의해 유지되는 스트림 오프셋부터 시작하여 파티션에서 이벤트를 읽습니다. 라운드 로빈 방식으로 트리거는 이벤트 허브의 각 파티션을 반복하고 후속 트리거 실행에서 이벤트를 읽습니다. 단일 실행은 한 번에 하나의 파티션에서 이벤트를 가져옵니다.

트리거는 스토리지에서 이 검사점 기능을 사용하지 않으므로 추가 비용이 발생하지 않습니다. 그러나 핵심은 Event Hubs 트리거를 업데이트하면 트리거의 상태가 재설정되어 트리거가 스트림 시작 시 이벤트를 읽도록 할 수 있습니다.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Event Hubs 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 따르고 워크플로에서 작업을 수행합니다. 다음은 작업을 추가하는 일반적인 방법(예: **이벤트 보내기**)을 설명하는 단계입니다. 이 예제의 경우 워크플로는 이벤트 허브에서 새 이벤트를 확인하는 Event Hubs 트리거로 시작됩니다.

1. Logic Apps 디자이너에서 논리 앱 워크플로를 엽니다(아직 열려 있지 않은 경우).

1. 트리거 또는 다른 작업 아래에서 새 단계를 추가합니다.

   기존 단계 사이에 단계를 추가하려면 마우스를 화살표 위로 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 작업 검색 상자에서 `event hubs`를 입력합니다. 작업 목록에서 **이벤트 보내기** 라는 작업을 선택합니다.

   !["이벤트 보내기" 작업을 선택합니다.](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. 이벤트 허브에 대한 연결을 생성하라는 메시지가 표시되면 [요청된 연결 정보를 제공](#create-connection)합니다.

1. 작업에서 보내려는 이벤트에 대한 정보를 제공합니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **이벤트 허브 이름** | 예 | 이벤트를 보낼 이벤트 허브 |
   | **콘텐츠** | 아니요 | 전송하려는 이벤트의 콘텐츠 |
   | **속성** | 예 | 보낼 앱 속성 및 값 |
   | **파티션 키** | 아니요 | 이벤트를 보낼 위치에 대한 [파티션](../event-hubs/event-hubs-features.md#partitions) ID |
   ||||

   추가 속성을 보려면 **새 매개 변수 추가** 목록을 엽니다. 매개변수를 선택하면 해당 속성이 작업에 추가됩니다. 예를 들면 다음과 같습니다.

   ![이벤트 허브 이름을 선택하고 이벤트 콘텐츠를 제공](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   예를 들어 Event Hubs 트리거의 출력을 다른 이벤트 허브로 보낼 수 있습니다.

   ![이벤트 보내기 예제](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

## <a name="connector-reference"></a>커넥터 참조

모든 작업 및 기타 기술 정보(예: 속성, 한도 등)는 [Event Hubs 커넥터의 참조 페이지](/connectors/eventhubs/)를 검토하세요.

> [!NOTE]
> [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 호스트되는 논리 앱에서는 커넥터의 ISE 버전이 [ISE 메시지 한도](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)를 사용합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
