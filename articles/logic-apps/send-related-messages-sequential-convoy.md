---
title: 순차 호위(convoy)를 사용하여 순서대로 상호 관련된 메시지 보내기
description: Azure Service Bus가 포함된 Azure Logic Apps에서 순차 호위(convoy) 패턴을 사용하여 순서대로 관련 메시지를 보냅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: apseth, divswa, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: e0df7b263ef5d62b064a5afe537cffc926230b0a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238703"
---
# <a name="send-related-messages-in-order-by-using-a-sequential-convoy-in-azure-logic-apps-with-azure-service-bus"></a>Azure Service Bus가 포함된 Azure Logic Apps에서 순차 호위(convoy)를 사용하여 순서대로 관련 메시지 보내기

[Azure Service Bus 커넥터](../connectors/connectors-create-api-servicebus.md)를 통해 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용할 때 상호 관련된 메시지를 특정 순서대로 보내야 하는 경우 [*순차 호위(convoy)* 패턴](/azure/architecture/patterns/sequential-convoy)을 따를 수 있습니다. 상호 관련된 메시지에는 Service Bus의 [세션](../service-bus-messaging/message-sessions.md) ID와 같이 해당 메시지 간 관계를 정의하는 속성이 있습니다.

예를 들어 “Session 1” 세션에 10개의 메시지가 있고 “session 2” 세션에 5개의 메시지가 있으며 모두 동일한 [Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)로 전송된다고 가정해 보겠습니다. 큐의 메시지를 처리하는 논리 앱을 만들어 “Session 1”의 모든 메시지가 단일 트리거 실행을 통해 처리되고 “Session 2”의 모든 메시지가 다음 트리거 실행을 통해 처리되도록 할 수 있습니다.

![일반적인 순차 호위(convoy) 패턴](./media/send-related-messages-sequential-convoy/sequential-convoy-pattern-general.png)

이 문서에서는 **Service Bus 세션을 사용하는 상호 관련된 순차적 전송** 템플릿을 사용하여 이 패턴을 구현하는 논리 앱을 만드는 방법을 보여 줍니다. 이 템플릿은 Service Bus 커넥터의 **메시지가 큐에 수신되는 경우(보기 및 잠금)** 트리거로 시작하고 [Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)에서 메시지를 수신하는 논리 앱 워크플로를 정의합니다. 이 논리 앱이 수행하는 대략적인 단계는 다음과 같습니다.

* 트리거가 Service Bus 큐에서 읽는 메시지에 따라 세션을 초기화합니다.

* 현재 워크플로를 실행하는 동안 큐에 있는 동일한 세션의 모든 메시지를 읽고 처리합니다.

이 템플릿의 JSON 파일을 검토하려면 [GitHub: service-bus-sessions.json](https://github.com/Azure/logicapps/blob/master/templates/service-bus-sessions.json)을 참조하세요.

자세한 내용은 [순차 호위(convoy) 패턴 - Azure 아키텍처 클라우드 디자인 패턴](/azure/architecture/patterns/sequential-convoy)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱에서 사용할 메시징 엔터티인 Service Bus 네임스페이스 및 [Service Bus 큐](../service-bus-messaging/service-bus-queues-topics-subscriptions.md). 해당 항목과 논리 앱은 동일한 Azure 구독을 사용해야 합니다. 큐를 만들 때 **세션 사용** 을 선택했는지 확인합니다. 해당 항목이 없는 경우 [Service Bus 네임스페이스 및 큐를 만드는 방법](../service-bus-messaging/service-bus-create-namespace-portal.md)을 알아봅니다.

  [!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

* 논리 앱 만드는 방법에 관한 기본 지식. Azure Logic Apps를 처음 사용하는 경우 빠른 시작의 [첫 번째 자동화된 워크플로 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 시도해 보세요.

<a name="permissions-connection-string"></a>

## <a name="check-access-to-service-bus-namespace"></a>Service Bus 네임스페이스에 대한 액세스 확인

논리 앱에 Service Bus 네임스페이스에 액세스할 수 있는 권한이 있는지 확실하지 않은 경우 권한이 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Service Bus ‘네임스페이스’를 찾아 선택합니다.

1. 네임스페이스 메뉴의 **설정** 에서 **공유 액세스 정책** 을 선택합니다. **클레임** 아래에서 해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

   ![Service Bus 네임스페이스에 대한 관리 권한](./media/send-related-messages-sequential-convoy/check-service-bus-permissions.png)

1. Service Bus 네임스페이스에 대한 연결 문자열을 가져옵니다. 나중에 논리 앱에서 네임스페이스에 대한 연결을 만들 때 이 문자열을 사용할 수 있습니다.

   1. **공유 액세스 정책** 창의 **정책** 에서 **RootManageSharedAccessKey** 를 선택합니다.
   
   1. 기본 연결 문자열 옆에 있는 복사 단추를 선택합니다. 나중에 사용할 연결 문자열을 저장합니다.

      ![Service Bus 네임스페이스 연결 문자열 복사](./media/send-related-messages-sequential-convoy/copy-service-bus-connection-string.png)

   > [!TIP]
   > 연결 문자열이 Service Bus 네임스페이스 또는 메시징 엔터티와 연결되어 있는지 확인하려면 `EntityPath` 매개 변수에 대한 연결 문자열을 검색합니다. 이 매개 변수를 찾은 경우 연결 문자열은 특정 엔터티에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

## <a name="create-logic-app"></a>논리 앱 만들기

이 섹션에서는 이 워크플로 패턴을 구현하는 트리거와 작업이 포함된 **Service Bus 세션을 사용하는 상호 관련된 순차적 전송** 템플릿을 사용하여 논리 앱을 만듭니다. 또한, Service Bus 네임스페이스에 대한 연결을 만들고 사용할 Service Bus 큐의 이름을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 빈 논리 앱을 만듭니다. Azure 홈페이지에서 **리소스 만들기** > **통합** > **논리 앱** 을 선택합니다.

1. 템플릿 갤러리가 표시되면 스크롤하여 비디오 및 일반 트리거 섹션을 지나갑니다. **템플릿** 섹션에서 **Service Bus 세션을 사용하는 상호 관련된 순차적 전송** 템플릿을 선택합니다.

   ![“Service Bus 세션을 사용하는 상호 관련된 순차적 전송” 템플릿 선택](./media/send-related-messages-sequential-convoy/select-correlated-in-order-delivery-template.png)

1. 확인 상자가 표시되면 **이 템플릿 사용** 을 선택합니다.

1. 논리 앱 디자이너의 **Service Bus** 셰이프에서 **계속** 을 선택하고 셰이프에 표시되는 더하기 기호( **+** )를 선택합니다.

   ![“계속”을 선택하여 Azure Service Bus에 연결](./media/send-related-messages-sequential-convoy/connect-to-service-bus.png)

1. 이제 다음 옵션 중 하나를 선택하여 Service Bus 연결을 만듭니다.

   * 이전에 Service Bus 네임스페이스에서 복사한 연결 문자열을 사용하려면 다음 단계를 수행합니다.

     1. **수동으로 연결 정보 입력** 을 선택합니다.

     1. **연결 이름** 에 이 연결의 이름을 지정합니다. **연결 문자열** 에 네임스페이스 연결 문자열을 붙여넣고 **만들기** 를 선택합니다. 예를 들면 다음과 같습니다.

        ![연결 이름 및 Service Bus 연결 문자열 입력](./media/send-related-messages-sequential-convoy/provide-service-bus-connection-string.png)

        > [!TIP]
        > 이 연결 문자열이 없는 경우 [Service Bus 네임스페이스 연결 문자열을 찾고 복사](#permissions-connection-string)하는 방법을 알아봅니다.

   * 현재 Azure 구독에서 Service Bus 네임스페이스를 선택하려면 다음 단계를 수행합니다.

     1. **연결 이름** 에 이 연결의 이름을 지정합니다. **Service Bus 네임스페이스** 에서는 사용하는 Service Bus 네임스페이스를 선택합니다. 예를 들면 다음과 같습니다.

        ![연결 이름을 입력하고 Service Bus 네임스페이스 선택](./media/send-related-messages-sequential-convoy/create-service-bus-connection.png)

     1. 다음 창이 표시되면 Service Bus 정책을 선택하고 **만들기** 를 선택합니다.

        ![Service Bus 정책 및 “만들기” 차례로 선택](./media/send-related-messages-sequential-convoy/create-service-bus-connection-2.png)

1. 완료되면 **계속** 을 선택합니다.

   이제 논리 앱 디자이너에는 **Service Bus 세션을 사용하는 상호 관련된 순차적 전송** 템플릿이 표시됩니다. 이 템플릿에는 `Try-Catch` 패턴을 따르는 오류 처리를 구현할 두 개의 범위를 비롯하여 트리거와 작업이 포함된 미리 채워진 워크플로가 있습니다.

이제 템플릿의 트리거 및 작업에 대한 자세한 내용을 알아볼 수도 있고 계속해서 [논리 앱 템플릿에 대한 값을 입력](#complete-template)하도록 이동할 수도 있습니다.

<a name="template-summary"></a>

## <a name="template-summary"></a>템플릿 요약

다음은 세부 정보가 축소된 **Service Bus 세션을 사용하는 상호 관련된 순차적 전송** 템플릿의 최상위 워크플로입니다.

![템플릿의 최상위 워크플로](./media/send-related-messages-sequential-convoy/template-top-level-flow.png)

| 이름 | Description |
|------|-------------|
| **`When a message is received in a queue (peek-lock)`** | 지정된 되풀이에 따라 이 Service Bus 트리거는 지정된 Service Bus 큐에서 메시지를 확인합니다. 메시지가 큐에 있으면 트리거가 실행되어 워크플로 인스턴스를 만들고 실행합니다. <p><p>‘보기 및 잠금’이라는 용어는 큐에서 메시지를 검색하도록 트리거가 요청을 보내는 것을 의미합니다. 메시지가 있는 경우 잠금 기간이 만료될 때까지 해당 메시지에서 다른 처리가 발생하지 않도록 트리거가 메시지를 검색하여 잠급니다. 자세한 내용은 [세션 초기화](#initialize-session)를 참조하세요. |
| **`Init isDone`** | 이 [**변수 초기화** 작업](../logic-apps/logic-apps-create-variables-store-values.md#initialize-variable)에서는 부울 변수를 만듭니다. 이 부울 변수는 `false`로 설정되어 있으며 다음 조건이 true인 경우 표시됩니다. <p><p>- 세션에서 읽을 수 있는 메시지가 더 이상 없습니다. <br>- 더 이상 세션 잠금을 갱신할 필요가 없으므로 현재 워크플로 인스턴스를 마칠 수 있습니다. <p><p>자세한 내용은 [세션 초기화](#initialize-session)를 참조하세요. |
| **`Try`** | 이 [**범위** 작업](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)에는 메시지 처리를 위해 실행하는 작업이 포함되어 있습니다. `Try` 범위에서 문제가 발생하는 경우 후속 `Catch` **범위** 작업에서 해당 문제를 처리합니다. 자세한 내용은 [“Try” 범위](#try-scope)를 참조하세요. |
| **`Catch`**| 이 [**범위** 작업](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)에는 이전 `Try` 범위에서 문제가 발생하는 경우 실행되는 작업이 포함되어 있습니다. 자세한 내용은 [“Catch” 범위](#catch-scope)를 참조하세요. |
|||

<a name="try-scope"></a>

### <a name="try-scope"></a>“Try” 범위

다음은 세부 정보가 축소된 `Try` [범위 작업](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)의 최상위 흐름입니다.

![“Try” 범위 작업 워크플로](./media/send-related-messages-sequential-convoy/try-scope-action.png)

| 이름 | Description |
|------|-------------|
| **`Send initial message to topic`** | 이 작업은 큐에 있는 세션의 첫 번째 메시지를 처리하는 작업으로 바꿀 수 있습니다. 세션 ID는 세션을 지정합니다. <p><p>이 템플릿의 경우 Service Bus 작업은 첫 번째 메시지를 Service Bus 토픽으로 보냅니다. 자세한 내용은 [초기 메시지 처리](#handle-initial-message)를 참조하세요. |
| (병렬 분기) | 이 [병렬 분기 작업](../logic-apps/logic-apps-control-flow-branches.md)에서는 두 개의 경로를 만듭니다. <p><p>- 분기 #1: 메시지를 계속 처리합니다. 자세한 내용은 [분기 #1: 큐의 초기 메시지 완료](#complete-initial-message)를 참조하세요. <p><p>- 분기 #2: 오류가 발생하면 메시지를 중단하고 다른 트리거 실행에서 선택하도록 해제합니다. 자세한 내용은 [분기 #2: 큐의 초기 메시지 중단](#abandon-initial-message)을 참조하세요. <p><p>두 경로는 다음 행에 설명된 대로 나중에 **큐의 세션을 닫고 성공** 작업에서 조인됩니다. |
| **`Close a session in a queue and succeed`** | 이 Service Bus 작업은 앞에서 설명한 분기를 조인하고 다음 이벤트 중 하나가 발생하면 큐의 세션을 닫습니다. <p><p>- 워크플로에서 큐의 사용 가능한 메시지 처리를 완료합니다. <br>- 오류가 발생하여 워크플로에서 초기 메시지를 중단합니다. <p><p>자세한 내용은 [큐의 세션을 닫고 성공](#close-session-succeed)을 참조하세요. |
|||

<a name="complete-initial-message"></a>

#### <a name="branch-1-complete-initial-message-in-queue"></a>분기 #1: 큐의 초기 메시지 완료

| 이름 | Description |
|------|-------------|
| `Complete initial message in queue` | 이 Service Bus 작업은 성공적으로 검색된 메시지를 완료로 표시하고 해당 메시지를 다시 처리하지 않도록 큐에서 제거합니다. 자세한 내용은 [초기 메시지 처리](#handle-initial-message)를 참조하세요. |
| `While there are more messages for the session in the queue` | 이 [**Until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop)는 메시지가 존재하거나 1시간이 경과할 때까지 메시지를 계속 가져옵니다. 이 루프의 작업에 대한 자세한 내용은 [큐에 세션에 대한 메시지가 더 있는 경우](#while-more-messages-for-session)를 참조하세요. |
| **`Set isDone = true`** | 메시지가 더 이상 없으면 이 [**변수 설정** 작업](../logic-apps/logic-apps-create-variables-store-values.md#set-variable)에서 `isDone`을 `true`로 설정합니다. |
| **`Renew session lock until cancelled`** | 이 [**Until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop)는 메시지가 존재하거나 1시간이 경과할 때까지 이 논리 앱에서 세션 잠금을 유지하도록 합니다. 이 루프의 작업에 대한 자세한 내용은 [취소될 때까지 세션 잠금 갱신](#renew-session-while-messages-exist)을 참조하세요. |
|||

<a name="abandon-initial-message"></a>

#### <a name="branch-2-abandon-initial-message-from-the-queue"></a>분기 #2: 큐의 초기 메시지 중단

첫 번째 메시지를 처리하는 작업이 실패하는 경우 Service Bus 작업 **큐의 초기 메시지 중단** 은 다른 워크플로 인스턴스 실행에서 해당 메시지를 선택하여 처리할 수 있도록 해제합니다. 자세한 내용은 [초기 메시지 처리](#handle-initial-message)를 참조하세요.

<a name="catch-scope"></a>

### <a name="catch-scope"></a>“Catch” 범위

`Try` 범위의 작업이 실패하는 경우에도 논리 앱은 여전히 해당 세션을 닫아야 합니다. `Try` 범위 작업 결과 `Failed`, `Skipped`, `TimedOut` 상태가 되는 경우 `Catch` [범위 작업](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)이 실행됩니다. 이 범위에서는 문제가 발생한 세션 ID가 포함된 오류 메시지를 반환하고 논리 앱을 종료합니다.

다음은 세부 정보가 축소된 `Catch` 범위 작업의 최상위 흐름입니다.

![“Catch” 범위 작업 워크플로](./media/send-related-messages-sequential-convoy/catch-scope-action.png)

| 이름 | Description |
|------|-------------|
| **`Close a session in a queue and fail`** | 이 Service Bus 작업은 큐의 세션을 닫아 세션 잠금이 열린 상태로 유지되지 않도록 합니다. 자세한 내용은 [큐의 세션을 닫고 실패](#close-session-fail)를 참조하세요. |
| **`Find failure msg from 'Try' block`** | 이 [**배열 필터링** 작업](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)에서는 지정된 조건에 따라 `Try` 범위 내 모든 작업의 입력 및 출력 배열을 만듭니다. 이 경우 이 작업에서는 결과가 `Failed` 상태인 작업의 출력을 반환합니다. 자세한 내용은 [‘Try’ 블록에서 실패 메시지 찾기](#find-failure-message)를 참조하세요. |
| **`Select error details`** | 이 [**선택** 작업](../logic-apps/logic-apps-perform-data-operations.md#select-action)에서는 지정된 조건에 따라 JSON 개체가 포함된 배열을 만듭니다. 해당 JSON 개체는 이전 작업인 `Find failure msg from 'Try' block`에서 만든 배열의 값으로 빌드됩니다. 이 경우 이 작업에서는 이전 작업에서 반환된 오류 세부 정보로 만든 JSON 개체가 포함된 배열을 반환합니다. 자세한 내용은 [선택 오류 세부 정보](#select-error-details)를 참조하세요. |
| **`Terminate`** | 이 [**종료** 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)에서는 워크플로 실행을 중지하고, 진행 중인 모든 작업을 취소하고, 나머지 작업을 건너뛰고, 지정된 상태, 세션 ID, `Select error details` 작업의 오류 결과를 반환합니다. 자세한 내용은 [종료 논리 앱](#terminate-logic-app)을 참조하세요. |
|||

<a name="complete-template"></a>

## <a name="complete-the-template"></a>템플릿 완료

**Service Bus 세션을 사용하는 상호 관련된 순차적 전송** 템플릿에서 트리거 및 작업에 대한 값을 제공하려면 다음 단계를 수행합니다. 논리 앱을 저장하려면 먼저 별표( **\*** )로 표시된 모든 필수 값을 제공해야 합니다.

<a name="initialize-session"></a>

### <a name="initialize-the-session"></a>세션 초기화

* **메시지가 큐에 수신되는 경우(보기 및 잠금)** 트리거에 대해 다음과 같이 정보를 제공하여 템플릿이 **세션 ID** 속성으로 세션을 초기화할 수 있도록 합니다.

  ![“메시지가 큐에 수신되는 경우(보기 및 잠금)”에 대한 Service Bus 트리거 세부 정보](./media/send-related-messages-sequential-convoy/service-bus-check-message-peek-lock-trigger.png)

  > [!NOTE]
  > 처음에는 논리 앱이 예상보다 더 자주 실행되지 않고 예상치 못한 청구 요금이 발생하지 않도록 폴링 간격을 3분으로 설정합니다. 메시지가 도착하면 즉시 논리 앱이 트리거되도록 간격 및 빈도를 30초로 설정하는 것이 가장 좋습니다.

  | 속성 | 이 시나리오에 필수 | 값 | Description |
  |----------|----------------------------|-------|-------------|
  | **큐 이름** | 예 | <*queue-name*> | 이전에 만든 Service Bus 큐의 이름입니다. 이 예에서는 “Fabrikam-Service-Bus-Queue”를 사용합니다. |
  | **큐 유형** | 예 | **기본** | 기본 Service Bus 큐입니다. |
  | **Session id** | 예 | **다음으로 사용 가능** | 이 옵션은 Service Bus 큐에 있는 메시지의 세션 ID에 따라 각 트리거 실행 세션을 가져옵니다. 또한, 다른 논리 앱이나 다른 클라이언트에서 이 세션과 관련된 메시지를 처리할 수 없도록 세션이 잠깁니다. 이 워크플로의 후속 작업에서는 본 문서의 뒷부분에 설명된 대로 해당 세션과 연결된 모든 메시지를 처리합니다. <p><p>다른 **Session id** 옵션에 대한 자세한 내용은 다음과 같습니다. <p>- **없음**: 기본 옵션으로, 세션을 생성하지 않으며 순차 호위(convoy) 패턴을 구현하는 데는 사용할 수 없습니다. <p>- **사용자 지정 값 입력**: 사용할 세션 ID를 알고 있으며 항상 해당 세션 ID에 대해 트리거를 실행하려는 경우 이 옵션을 사용합니다. <p>**참고**: Service Bus 커넥터가 Azure Service Bus에서 커넥터 캐시에 한 번에 저장할 수 있는 고유한 세션 수는 제한되어 있습니다. 세션 수가 이 한도를 초과하면 오래된 세션이 캐시에서 제거됩니다. 자세한 내용은 [Azure Logic Apps 및 Azure Service Bus를 사용하여 클라우드에서 메시지 교환](../connectors/connectors-create-api-servicebus.md#connector-reference)을 참조하세요. |
  | **간격** | 예 | <*number-of-intervals*> | 메시지를 확인하기 전 되풀이 간 시간 단위 수입니다. |
  | **빈도** | 예 | **초**, **분**, **시간**, **일**, **주** 또는 **월** | 메시지를 확인할 때 사용하는 되풀이 시간 단위입니다. <p>**팁**: **표준 시간대** 또는 **시작 시간** 을 추가하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택합니다. |
  |||||

  자세한 트리거 정보는 [Service Bus - 메시지가 큐에 수신되는 경우(보기 및 잠금)](/connectors/servicebus/#when-a-message-is-received-in-a-queue-(peek-lock))를 참조하세요. 트리거는 [ServiceBusMessage](/connectors/servicebus/#servicebusmessage)를 출력합니다.

세션을 초기화한 후 워크플로는 **변수 초기화** 작업을 사용하여 부울 변수를 만듭니다. 이 부울 변수는 처음에 `false`로 설정되어 있으며 다음 조건이 true인 경우 표시됩니다. 

* 세션에서 읽을 수 있는 메시지가 더 이상 없습니다.

* 더 이상 세션 잠금을 갱신할 필요가 없으므로 현재 워크플로 인스턴스를 마칠 수 있습니다.

![“Init isDone”에 대한 “변수 초기화” 작업 세부 정보](./media/send-related-messages-sequential-convoy/init-is-done-variable.png)

다음으로, **Try** 블록에서 워크플로가 읽은 첫 번째 메시지에 대한 작업을 수행합니다.

<a name="handle-initial-message"></a>

### <a name="handle-the-initial-message"></a>초기 메시지 처리

첫 번째 작업은 자리 표시자 Service Bus 작업인 **토픽에 초기 메시지 보내기** 입니다. 이 작업은 큐에 있는 세션의 첫 번째 메시지를 처리할 다른 작업으로 바꿀 수 있습니다. 세션 ID는 메시지가 시작된 세션을 지정합니다.

자리 표시자 Service Bus 작업은 **세션 ID** 속성으로 지정된 Service Bus 토픽에 첫 번째 메시지를 보냅니다. 이렇게 하면 특정 세션과 연결된 모든 메시지가 동일한 토픽으로 이동합니다. 이 템플릿의 후속 작업에 대한 모든 **세션 ID** 속성은 동일한 세션 ID 값을 사용합니다.

![“토픽에 초기 메시지 보내기”에 대한 Service Bus 작업 세부 정보](./media/send-related-messages-sequential-convoy/send-initial-message-to-topic-action.png)

1. Service Bus 작업, **큐의 초기 메시지 완료** 에서는 Service Bus 큐의 이름을 입력하고 작업의 다른 모든 기본 속성 값은 그대로 유지합니다.

   ![“큐의 초기 메시지 완료”에 대한 Service Bus 작업 세부 정보](./media/send-related-messages-sequential-convoy/complete-initial-message-queue.png)

1. Service Bus 작업, **큐의 초기 메시지 중단** 에서는 Service Bus 큐의 이름을 입력하고 작업의 다른 모든 기본 속성 값은 그대로 유지합니다.

   !["큐의 초기 메시지 중단"에 대한 Service Bus 작업 세부 정보](./media/send-related-messages-sequential-convoy/abandon-initial-message-from-queue.png)

다음으로, **큐의 초기 메시지 완료** 작업 뒤에 오는 작업에 필요한 정보를 입력합니다. **큐에 세션에 대한 메시지가 더 있는 경우** 루프의 작업을 시작합니다.

<a name="while-more-messages-for-session"></a>

### <a name="while-there-are-more-messages-for-the-session-in-the-queue"></a>큐에 세션에 대한 메시지가 더 있는 경우

이 [**Until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop)는 큐에 메시지가 존재하거나 1시간이 경과할 때까지 해당 작업을 실행합니다. 루프의 시간 제한을 변경하려면 루프의 **시간 제한** 속성 값을 편집합니다.

* 메시지가 있는 동안에는 큐에서 추가 메시지를 가져옵니다.

* 남아 있는 메시지 수를 확인합니다. 여전히 메시지가 있는 경우 계속해서 메시지를 처리합니다. 메시지가 더 이상 없으면 워크플로는 `isDone` 변수를 `true`로 설정하고 루프를 종료합니다.

![Until 루프 - 큐에 있는 동안 메시지 처리](./media/send-related-messages-sequential-convoy/while-more-messages-for-session-in-queue.png)

1. Service Bus 작업, **세션에서 추가 메시지 가져오기** 에서는 Service Bus 큐의 이름을 입력합니다. 그 외에는 작업의 다른 모든 기본 속성 값을 유지합니다.

   > [!NOTE]
   > 기본적으로 최대 메시지 수는 `175`로 설정되지만 이 한도는 Service Bus의 메시지 크기 및 최대 메시지 크기 속성의 영향을 받습니다. 자세한 내용은 [큐의 메시지 크기](../service-bus-messaging/service-bus-quotas.md)를 참조하세요.

   ![Service Bus 작업 - “세션에서 추가 메시지 가져오기”](./media/send-related-messages-sequential-convoy/get-additional-messages-from-session.png)

   다음으로, 워크플로가 다음과 같이 병렬 분기로 분할됩니다.

   * 추가 메시지를 확인하는 동안 오류 또는 실패가 발생하는 경우 `isDone` 변수를 `true`로 설정합니다.

   * **메시지가 있는 경우 메시지 처리** 조건은 남은 메시지 수가 0인지 여부를 확인합니다. false이고 메시지가 더 있는 경우 처리를 계속합니다. true이고 더 이상 메시지가 없는 경우 워크플로는 `isDone` 변수를 `true`로 설정합니다.

   ![조건 - 메시지가 있는 경우 메시지 처리](./media/send-related-messages-sequential-convoy/process-messages-if-any.png)

   **false인 경우** 섹션에서는 **For each** 루프가 FIFO(선입 선출) 순으로 각 메시지를 처리합니다. 루프의 **설정** 에 **동시성 제어** 설정이 `1`로 지정되어 있으므로 한 번에 하나의 메시지만 처리됩니다.

   ![“For each” 루프 - 한 번에 하나의 메시지 처리](./media/send-related-messages-sequential-convoy/for-each-additional-message.png)

1. Service Bus 작업, **큐의 메시지 완료** 및 **큐의 메시지 중단** 에서는 Service Bus 큐의 이름을 입력합니다.

   ![Service Bus 작업 - “큐의 메시지 완료” 및 “큐의 메시지 중단”](./media/send-related-messages-sequential-convoy/abandon-or-complete-message-in-queue.png)

   **큐에 세션에 대한 메시지가 더 있는 경우** 가 완료되면 워크플로가 `isDone` 변수를 `true`로 설정합니다.

다음으로, **취소될 때까지 세션 잠금 갱신** 루프에서 작업에 필요한 정보를 입력합니다.

<a name="renew-session-while-messages-exist"></a>

### <a name="renew-session-lock-until-cancelled"></a>취소될 때까지 세션 잠금 갱신

이 [**Until** 루프](../logic-apps/logic-apps-control-flow-loops.md#until-loop)는 큐에 메시지가 존재하거나 해당 작업을 실행하는데 1시간이 경과할 때까지 이 논리 앱에서 세션 잠금을 유지하도록 할 수 있습니다. 루프의 시간 제한을 변경하려면 루프의 **시간 제한** 속성 값을 편집합니다.

* 25초 또는 처리 중인 큐의 잠금 시간 제한 기간보다 짧은 시간 동안 연기하세요. 최소 잠금 기간이 30초이므로 기본값만 사용해도 충분합니다. 하지만 적절하게 조정하여 루프가 실행되는 횟수를 최적화할 수 있습니다.

* `isDone` 변수가 `true`로 설정되었는지 확인합니다.

  * 가 로 설정된 경우 `isDone` `true` 워크플로는 여전히 메시지를 처리하므로 워크플로는 큐의 세션에 대한 잠금을 갱신하고 루프 조건을 다시 확인합니다.

    Service Bus 작업, [**큐의 세션에서 잠금 갱신**](#renew-lock-on-session)에서는 Service Bus 큐의 이름을 입력해야 합니다.

  * `isDone`이 `true`로 설정되면 워크플로가 큐의 세션에서 잠금을 갱신하지 않으며 루프를 종료합니다.

  ![Until 루프 - “취소될 때까지 세션 잠금 갱신”](./media/send-related-messages-sequential-convoy/renew-lock-until-session-cancelled.png)

<a name="renew-lock-on-session"></a>

#### <a name="renew-lock-on-the-session-in-a-queue"></a>큐의 세션에서 잠금 갱신

이 Service Bus 작업은 워크플로가 메시지를 계속 처리하는 동안 큐의 세션에서 잠금을 갱신합니다.

* Service Bus 작업, **큐의 세션에서 잠금 갱신** 에서는 Service Bus 큐의 이름을 입력합니다.

  ![Service Bus 작업 - “큐의 세션에서 잠금 갱신”](./media/send-related-messages-sequential-convoy/renew-lock-on-session-in-queue.png)

다음으로, Service Bus 작업, **큐의 세션을 닫고 성공** 에 필요한 정보를 입력합니다.

<a name="close-session-succeed"></a>

### <a name="close-a-session-in-a-queue-and-succeed"></a>큐의 세션을 닫고 성공

이 Service Bus 작업은 워크플로가 큐의 모든 사용 가능한 메시지 처리를 완료하거나 워크플로가 초기 메시지를 중단하면 큐의 세션을 닫습니다.

* Service Bus 작업, **큐의 세션을 닫고 성공** 에서는 Service Bus 큐의 이름을 입력합니다.

  ![Service Bus 작업 - “큐의 세션을 닫고 성공”](./media/send-related-messages-sequential-convoy/close-session-in-queue-succeed.png)

다음 섹션에서는 워크플로에 발생하는 오류 및 예외를 처리하는 `Catch` 섹션의 작업에 대해 설명합니다.

<a name="close-session-fail"></a>

### <a name="close-a-session-in-a-queue-and-fail"></a>큐의 세션을 닫고 실패

이 Service Bus 작업은 항상 `Catch` 범위의 첫 번째 작업으로 실행되며 큐의 세션을 닫습니다.

* Service Bus 작업, **큐의 세션을 닫고 실패** 에서는 Service Bus 큐의 이름을 입력합니다.

  ![Service Bus 작업 - “큐의 세션을 닫고 실패”](./media/send-related-messages-sequential-convoy/close-session-in-queue-fail.png)

다음으로, 발생한 오류 또는 실패에 대한 정보에 논리 앱이 액세스할 수 있도록 워크플로는 `Try` 범위 내 모든 작업의 입력 및 출력이 포함된 배열을 만듭니다.

<a name="find-failure-message"></a>

### <a name="find-failure-msg-from-try-block"></a>‘Try’ 블록에서 실패 메시지 찾기

이 [**배열 필터링** 작업](../logic-apps/logic-apps-perform-data-operations.md#filter-array-action)에서는 [`result()` 함수](../logic-apps/workflow-definition-language-functions-reference.md#result)를 사용하여 지정된 조건에 따라 `Try` 범위 내 모든 작업의 입력 및 출력이 포함된 배열을 만듭니다. 이 경우 이 작업에서는 [`equals()` 함수](../logic-apps/workflow-definition-language-functions-reference.md#equals) 및 [`item()` 함수](../logic-apps/workflow-definition-language-functions-reference.md#item)를 사용하여 `Failed` 상태가 된 작업의 출력을 반환합니다.

![배열 필터링 작업 - “‘Try’ 블록에서 실패 메시지 찾기”](./media/send-related-messages-sequential-convoy/find-failure-message.png)

이 작업에 대한 JSON 정의는 다음과 같습니다.

```json
"Find_failure_msg_from_'Try'_block": {
   "inputs": {
      "from": "@Result('Try')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "Close_the_session_in_the_queue_and_fail": [
         "Succeeded"
      ]
   },
   "type": "Query"
},
```

다음으로, 워크플로는 `Find failure msg from 'Try' block` 작업에서 반환된 배열의 오류 정보가 포함된 JSON 개체로 배열을 만듭니다.

<a name="select-error-details"></a>

### <a name="select-error-details"></a>선택 오류 세부 정보

이 [**선택** 작업](../logic-apps/logic-apps-perform-data-operations.md#select-action)에서는 이전 작업인 `Find failure msg from 'Try' block`에서 출력된 입력 배열을 기반으로 JSON 개체가 포함된 배열을 만듭니다. 특히 이 작업은 배열의 각 개체에 대해 지정된 속성만 포함된 배열을 반환합니다. 이 경우 배열에는 작업 이름 및 오류 결과 속성이 포함됩니다.

![선택 작업 - “선택 오류 세부 정보”](./media/send-related-messages-sequential-convoy/select-error-details.png)

이 작업에 대한 JSON 정의는 다음과 같습니다.

```json
"Select_error_details": {
   "inputs": {
      "from": "@body('Find_failure_msg_from_''Try''_block')[0]['outputs']",
      "select": {
         "action": "@item()['name']",
         "errorResult": "@item()"
      }
   },
   "runAfter": {
      "Find_failure_msg_from_'Try'_block": [
         "Succeeded"
      ]
   },
   "type": "Select"
},
```

다음으로, 워크플로는 논리 앱 실행을 중지하고 해당 실행 상태와 함께, 발생한 오류 또는 실패에 대한 추가 정보를 반환합니다.

<a name="terminate-logic-app"></a>

### <a name="terminate-logic-app-run"></a>논리 앱 실행 종료

이 [**종료** 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action)에서는 논리 앱 실행을 중지하고, 세션 ID 및 `Select error details` 작업의 오류 결과와 함께 `Failed`를 논리 앱의 실행 상태로 반환합니다.

![논리 앱 실행을 중지하는 종료 작업](./media/send-related-messages-sequential-convoy/terminate-logic-app-run.png)

이 작업에 대한 JSON 정의는 다음과 같습니다.

```json
"Terminate": {
   "description": "This Failure Termination only runs if the Close Session upon Failure action runs - otherwise the LA will be terminated as Success",
   "inputs": {
      "runError": {
         "code": "",
         "message": "There was an error processing messages for Session ID @{triggerBody()?['SessionId']}. The following error(s) occurred: @{body('Select_error_details')['errorResult']}"
         },
         "runStatus": "Failed"
      },
      "runAfter": {
         "Select_error_details": [
            "Succeeded"
         ]
      },
      "type": "Terminate"
   }
},
```

## <a name="save-and-run-logic-app"></a>논리 앱 저장 및 실행

템플릿을 완료한 후에는 논리 앱을 저장할 수 있습니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

논리 앱을 테스트하려면 Service Bus 큐로 메시지를 보냅니다. 

## <a name="next-steps"></a>다음 단계

* [Service Bus 커넥터 트리거 및 작업](/connectors/servicebus/)에 대한 자세한 내용을 알아봅니다.
