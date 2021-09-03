---
title: Azure Service Bus로 메시지 교환
description: Azure Logic Apps에서 Azure Service Bus를 사용하여 메시지를 보내고 받는 자동화된 태스크 및 워크플로 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 08/18/2021
tags: connectors
ms.openlocfilehash: 43ea4bd0eea40e5e74b92f67241adbe7bee1dcc3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122568398"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Azure Logic Apps 및 Azure Service Bus를 사용하여 클라우드에서 메시지 교환

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 커넥터를 사용하면 영업 및 구매 주문, 저널 및 조직용 애플리케이션 간 재고 이동과 같은 데이터를 전송하는 자동화된 태스크 및 워크플로를 만들 수 있습니다. 커넥터는 메시지의 모니터링, 전송 및 관리뿐만 아니라 다음과 같이 큐, 세션, 토픽, 구독 등을 사용한 작업을 수행합니다.

* 큐, 토픽 및 토픽 구독에 메시지가 도착하거나(자동 완성) 수신되면(보기 잠금) 모니터링합니다.
* 메시지를 보냅니다.
* 토픽 구독을 만들고 삭제합니다.
* 큐 및 토픽 구독에서 메시지를 관리합니다. 예를 들어 가져오기, 지연된 가져오기, 완료, 지연, 중단 및 배달하지 못한 편지가 있습니다.
* 큐 및 토픽 구독에서 메시지 및 세션에 대한 잠금을 갱신합니다.
* 큐 및 토픽에서 세션을 닫습니다.

트리거를 사용하여 Service Bus에서 응답을 가져오고 논리 앱 워크플로의 다른 작업에서 출력을 사용하도록 할 수 있습니다. 또한 다른 작업에서 Service Bus 작업의 출력을 사용하도록 할 수 있습니다. Service Bus 및 Azure Logic Apps가 처음인 경우 [Azure Service Bus란?](../service-bus-messaging/service-bus-messaging-overview.md) 및 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 살펴보세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* Service Bus 네임스페이스 및 메시징 엔터티(예: 큐). 이러한 항목에 없는 경우 [Service Bus 네임스페이스 및 큐를 만드는](../service-bus-messaging/service-bus-create-namespace-portal.md) 방법을 알아봅니다.

* [논리 앱 워크플로를 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식.

* Service Bus 네임스페이스 및 메시징 엔터티를 사용하는 논리 앱 워크플로입니다. Service Bus 트리거로 워크플로를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). 워크플로에 Service Bus 동작을 사용하려면 [되풀이 트리거](../connectors/connectors-native-recurrence.md) 같은 다른 트리거를 통해 논리 앱 워크플로를 시작합니다.

## <a name="considerations-for-azure-service-bus-operations"></a>Azure Service Bus 작업에 대한 고려 사항

### <a name="infinite-loops"></a>무한 루프

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

### <a name="large-messages"></a>대용량 메시지

대용량 메시지 지원은 [단일 테넌트 Azure Logic Apps(표준)](../logic-apps/single-tenant-overview-compare.md) 워크플로에서 기본 제공 Service Bus 작업을 사용하는 경우에만 사용할 수 있습니다. 기본 제공 버전의 트리거 또는 작업을 사용하여 대용량 메시지를 보내고 받을 수 있습니다.

  메시지 수신의 경우 [Azure Functions 확장에서 다음 설정을 변경](../azure-functions/functions-bindings-service-bus.md#hostjson-settings)하여 시간 제한을 늘릴 수 있습니다.

  ```json
  {
     "version": "2.0",
     "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle.Workflows",
        "version": "[1.*, 2.0.0)"
     },
     "extensions": {
        "serviceBus": {
           "batchOptions": {
              "operationTimeout": "00:15:00"
           }
        }  
     }
  }
  ```

  메시지 보내기의 경우 [`ServiceProviders.ServiceBus.MessageSenderOperationTimeout` 앱 설정을 추가](../logic-apps/edit-app-settings-host-settings.md)하여 시간 제한을 늘릴 수 있습니다.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>사용 권한 확인

논리 앱 리소스에 Service Bus 네임스페이스에 액세스하기 위한 권한이 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정을 사용하여 로그인합니다.

1. Service Bus *네임스페이스* 로 이동합니다. 네임스페이스 페이지의 **설정** 에서 **공유 액세스 정책** 을 선택합니다. **클레임** 아래에서 해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

   ![Service Bus 네임스페이스에 대한 관리 권한](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Service Bus 네임스페이스에 대한 연결 문자열을 가져옵니다. 논리 앱에서 연결 정보를 입력하는 경우 이 문자열이 필요합니다.

   1. **공유 액세스 정책** 창에서 **RootManageSharedAccessKey** 를 선택합니다.

   1. 기본 연결 문자열 옆에 있는 복사 단추를 선택합니다. 나중에 사용할 연결 문자열을 저장합니다.

      ![Service Bus 네임스페이스 연결 문자열 복사](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 연결 문자열이 Service Bus 네임스페이스 또는 메시징 엔터티와 연결되어 있는지 확인하려면 `EntityPath` 매개 변수에 대한 연결 문자열을 검색합니다. 이 매개 변수를 찾은 경우 연결 문자열은 특정 엔터티에 대한 것이고 논리 앱 워크플로에 사용할 올바른 문자열이 아닙니다.

## <a name="add-service-bus-trigger"></a>Service Bus 트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 워크플로 디자이너에서 빈 논리 앱을 엽니다.

1. 포털 검색 상자에 `azure service bus`를 입력합니다. 표시되는 트리거 목록에서 원하는 트리거를 선택합니다.

   예를 들어 새 항목이 Service Bus 큐로 보내질 때 논리 앱 워크플로를 트리거하려면 **큐에 메시지가 수신될 때(자동 완성)** 트리거를 선택합니다.

   ![Service Bus 트리거 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   Service Bus 트리거를 사용하는 경우에 대한 몇 가지 고려 사항은 다음과 같습니다.

   * 모든 Service Bus 트리거는 *긴 폴링* 트리거입니다. 즉, 트리거가 발생될 때 이 트리거는 모든 메시지를 처리한 다음, 더 많은 메시지가 큐 또는 토픽 구독에 나타날 때까지 30초 동안 기다립니다. 30초 동안 표시되는 메시지가 없으면 트리거 실행을 건너뜁니다. 그렇지 않으면 큐 또는 토픽 구독이 빈 상태가 될 때까지 트리거는 메시지를 계속 읽습니다. 다음 트리거 폴링은 트리거의 속성에 지정된 되풀이 간격을 기준으로 합니다.

   * **큐에 하나 이상의 메시지가 도착할 때(자동 완성)** 트리거와 같은 일부 트리거는 하나 이상의 메시지를 반환할 수 있습니다. 이러한 트리거가 발생되면 1과 트리거의 **최대 메시지 수** 속성으로 지정된 메시지 수 사이의 값을 반환합니다.

     > [!NOTE]
     > 자동 완성 트리거는 메시지를 자동으로 완성하지만 다음에 Service Bus를 호출해야만 완료됩니다. 이 동작은 논리 앱의 디자인에 영향을 줄 수 있습니다. 예를 들어 논리 앱 워크플로가 제한된 상태로 전환되는 경우, 이 변경으로 인해 중복 메시지가 나타날 수 있으므로 자동 완성 트리거의 동시성을 변경하지 마세요. 동시성 제어를 변경하면 이러한 조건이 생성됩니다. 즉, `WorkflowRunInProgress` 코드를 사용하면 시스템이 제한된 트리거를 건너뛰고 폴링 간격 후 다음 트리거 실행이 수행됩니다. Service Bus 잠금 기간을 폴링 간격보다 긴 값으로 설정해야 합니다. 그러나 이 설정에도 불구하고 논리 앱 워크플로가 다음 폴링 간격에 제한된 상태로 유지되는 경우 메시지가 완료되지 않을 수 있습니다.

   * Service Bus 트리거에 [동시성 설정을 켜면](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) `maximumWaitingRuns` 속성의 기본값은 10입니다. Service Bus 엔터티의 잠금 기간 설정 및 논리 앱 워크플로의 실행 기간에 따라 이 기본값이 너무 커서 "잠금 손실" 예외가 발생할 수 있습니다. 시나리오에 가장 적합한 값을 찾으려면 `maximumWaitingRuns` 속성에 값 1 또는 2를 사용하여 테스트를 시작합니다. 최대 대기 실행 값을 변경하려면 [대기 실행 제한 변경](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)을 참조하세요.

1. 트리거가 Service Bus 네임스페이스에 처음으로 연결하는 경우 워크플로 디자이너에서 연결 정보를 묻는 메시지를 표시하면 다음 단계를 따릅니다.

   1. 연결에 사용할 이름을 입력하고 Service Bus 네임스페이스를 선택합니다.

      ![연결 이름을 지정하고 Service Bus 네임스페이스를 선택하는 것을 보여 주는 스크린샷.](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      대신 연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력** 을 선택합니다. 연결 문자열이 없는 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아봅니다.

   1. Service Bus 정책을 선택하고 **만들기** 를 선택합니다.

      ![Service Bus 정책 선택을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. 큐 또는 토픽과 같이 원하는 메시징 엔터티를 선택합니다. 이 예제에서는 Service Bus 큐를 선택합니다.

      ![Service Bus 큐 선택을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 선택한 트리거에 필요한 정보를 입력합니다. 다른 사용 가능한 속성을 동작에 추가하려면 **새 매개 변수 추가** 목록을 열고 원하는 속성을 선택합니다.

   이 예제 트리거의 경우 폴링 간격 및 큐 점검을 위한 주기를 선택합니다.

   ![Service Bus 트리거의 폴링 간격 설정을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   사용 가능한 트리거에 대한 자세한 내용은 커넥터의 [참조 페이지](/connectors/servicebus/)를 확인하세요.

1. 원하는 동작을 추가하여 논리 앱 워크플로를 계속 빌드합니다.

   예를 들어 새 메시지가 도착하면 이메일을 보내는 작업을 추가할 수 있습니다. 트리거가 큐를 확인하고 새 메시지를 발견하는 경우 논리 앱 워크플로는 찾은 메시지에 대해 사용자가 선택한 작업을 실행합니다.

## <a name="add-service-bus-action"></a>Service Bus 동작 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)의 워크플로 디자이너에서 논리 앱을 엽니다.

1. 동작을 추가하려는 단계에서 **새 단계** 를 선택합니다.

   또는 단계 사이에 동작을 추가하려면 해당 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택하고 **동작 추가** 를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 `azure service bus`을 입력합니다. 표시되는 동작 목록에서 원하는 동작을 선택합니다. 

   이 예제에서는 **메시지 보내기** 동작을 선택합니다.

   ![Service Bus 선택 동작을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 동작이 Service Bus 네임스페이스에 처음으로 연결하는 경우 워크플로 디자이너에서 연결 정보를 묻는 메시지를 표시하면 다음 단계를 따릅니다.

   1. 연결에 사용할 이름을 입력하고 Service Bus 네임스페이스를 선택합니다.

      ![연결 이름을 지정하고 Service Bus 네임스페이스를 선택하는 것을 보여 주는 스크린샷.](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      대신 연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력** 을 선택합니다. 연결 문자열이 없는 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아봅니다.

   1. Service Bus 정책을 선택하고 **만들기** 를 선택합니다.

      ![Service Bus 정책을 선택하고 만들기 단추를 선택하는 스크린샷](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. 큐 또는 토픽과 같이 원하는 메시징 엔터티를 선택합니다. 이 예제에서는 Service Bus 큐를 선택합니다.

      ![Service Bus 큐 선택을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 선택한 작업에 필요한 정보를 입력합니다. 다른 사용 가능한 속성을 동작에 추가하려면 **새 매개 변수 추가** 목록을 열고 원하는 속성을 선택합니다.

   예를 들어 동작에 추가할 **콘텐츠** 및 **콘텐츠 형식** 속성을 선택합니다. 그런 다음, 보내려는 메시지의 콘텐츠를 지정합니다.

   ![메시지 콘텐츠 형식 및 세부 정보를 입력하는 것을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   사용 가능한 동작 및 속성에 대한 자세한 내용은 커넥터의 [참조 페이지](/connectors/servicebus/)를 확인하세요.

1. 원하는 다른 동작을 추가하여 논리 앱 워크플로를 계속 빌드합니다.

   예를 들어 메시지가 전송되었음을 확인하는 이메일을 보내는 동작을 추가할 수 있습니다.

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>상관 관계가 지정된 메시지를 순서대로 보내기

관련 메시지를 특정 순서로 보내야 하는 경우에는 [Azure Service Bus 커넥터](../connectors/connectors-create-api-servicebus.md)를 사용하여 [*순차 호위(convoy)* 패턴](/azure/architecture/patterns/sequential-convoy)을 사용할 수 있습니다. 상관 관계가 지정된 메시지에는 Service Bus [세션](../service-bus-messaging/message-sessions.md)의 ID와 같이 해당 메시지 간의 관계를 정의하는 속성이 있습니다.

논리 앱을 만들 때 순차 호위(convoy) 패턴을 구현하는 **Service Bus 세션을 사용하여 상관 관계가 지정된 순서대로 배달** 템플릿을 선택할 수 있습니다. 자세한 내용은 [관련 메시지를 순서대로 보내기](../logic-apps/send-related-messages-sequential-convoy.md)를 참조하세요.

## <a name="delays-in-updates-to-your-logic-app-taking-effect"></a>논리 앱에 대한 업데이트 적용 지연

Service Bus 트리거의 폴링 간격이 짧은 경우(예: 10초) 논리 앱 워크플로에 대한 업데이트가 최대 10분 동안 적용되지 않을 수 있습니다. 이 문제를 해결하려면 논리 앱을 사용하지 않도록 설정하고 변경한 다음, 논리 앱 워크플로를 다시 사용하도록 설정하면 됩니다.

<a name="connector-reference"></a>

## <a name="connector-reference"></a>커넥터 참조

Service Bus에서 Service Bus 커넥터는 [구독 또는 항목과 같은 Service Bus 메시징 엔터티](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)별로 한 번에 최대 1,500개의 고유 세션을 커넥터 캐시에 저장할 수 있습니다. 세션 수가 이 한도를 초과하면 이전 세션이 캐시에서 제거됩니다. 자세한 내용은 [메시지 세션](../service-bus-messaging/message-sessions.md)을 참조하세요.

커넥터의 Swagger 설명에 있는 트리거, 작업, 한도에 대한 다른 기술 정보는 [커넥터의 참조 페이지](/connectors/servicebus/)를 확인하세요. Azure Service Bus Messaging에 대한 자세한 내용은 [Azure Service Bus란?](../service-bus-messaging/service-bus-messaging-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Azure Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
