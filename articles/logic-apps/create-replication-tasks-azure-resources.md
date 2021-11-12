---
title: Azure 리소스에 대 한 복제 작업 만들기
description: Azure Logic Apps의 워크플로에 따라 복제 작업 템플릿을 사용 하 여 Azure 리소스를 복제 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 42364c1c1fc0f9d2d220b4b9156a4a7ab4a410fa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337411"
---
# <a name="create-replication-tasks-for-azure-resources-using-azure-logic-apps-preview"></a>Azure Logic Apps (미리 보기)를 사용 하 여 Azure 리소스에 대 한 복제 작업 만들기

> [!IMPORTANT]
> 이 기능은 미리 보기로 제공되고 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

최대 가용성 및 안정성은 Azure 서비스에 대 한 최상위 운영 우선 순위 이지만 네트워킹 또는 이름 확인 문제, 오류 또는 일시적인 응답 중지로 인 한 통신을 위한 여러 가지 방법이 있습니다. 이러한 조건은 재해 복구 상황에서 수행 하는 것과 같이 지역 배포를 완전히 중단 하는 것이 아니라 "재해" 되지 않습니다. 그러나 일부 앱의 비즈니스 시나리오는 몇 분 또는 몇 초 이내에 지속 되는 가용성 이벤트의 영향을 받을 수 있습니다.

Azure 지역에서 Azure 리소스에 예측할 수 없는 이벤트의 영향을 줄이려면 비즈니스 연속성을 유지할 수 있도록 이러한 리소스의 콘텐츠를 한 지역에서 다른 지역으로 복제할 수 있습니다. Azure에서 한 지역의 원본에서 다른 지역의 대상으로 데이터, 이벤트 또는 메시지를 이동 하는 [*복제 작업*](#replication-task) 을 만들 수 있습니다. 이렇게 하면 소스가 오프 라인 상태가 되 고 대상에서 수행 해야 하는 경우 대상을 쉽게 사용할 수 있습니다.

> [!NOTE]
> 복제 작업을 사용 하 여 동일한 지역의 엔터티 간에 콘텐츠를 이동할 수도 있지만 전체 지역을 사용할 수 없게 되거나 중단 되는 경우 원본 및 대상 모두에 영향을 줍니다.

이 문서에서는 Azure Logic Apps으로 구동 되는 복제 작업에 대 한 개요를 제공 하 고 Azure Service Bus 큐에 대 한 예제 복제 작업을 만드는 방법을 보여 줍니다. 논리 앱 및 워크플로를 처음 접하는 경우에 [는](logic-apps-overview.md) [Azure Logic Apps에 대 한 Azure Logic Apps 및 단일 테 넌 트 및 다중 테 넌 트 및 통합 서비스 환경을](single-tenant-overview-compare.md)검토 하세요.

<a name="replication-task"></a>

## <a name="what-is-a-replication-task"></a>복제 작업이란 무엇인가요?

일반적으로 복제 작업은 원본에서 데이터, 이벤트 또는 메시지를 수신 하 고 해당 콘텐츠를 대상으로 이동한 다음 소스가 Event Hubs 엔터티인 경우를 제외 하 고 원본에서 해당 콘텐츠를 삭제 합니다. 복제 태스크는 일반적으로 콘텐츠를 변경 되지 않은 상태로 이동 하지만 Azure Logic Apps으로 구동 되는 복제 작업은 또한 [복제 속성](#replication-properties)을 추가 합니다. 원본 및 대상 프로토콜이 서로 다른 경우에도 이러한 작업은 메타 데이터 구조 간의 매핑을 수행 합니다. 복제 태스크는 상태 비저장 이며 작업의 병렬 또는 순차적 실행에 대 한 상태 또는 기타 부작용을 공유 하지 않습니다.

사용 가능한 복제 작업 템플릿을 사용 하는 경우 생성 하는 각 복제 작업에는 복제 작업에 대 한 여러 워크플로를 포함할 수 있는 **논리 앱 (표준)** 리소스의 기본 [상태 비저장 워크플로가](single-tenant-overview-compare.md#stateful-stateless) 있습니다. 이 리소스는 복제 및 페더레이션 태스크를 포함 하 여 서버를 사용 하지 않는 응용 프로그램을 구성 하 고 실행 하기 위한 확장 가능 하 고 안정적인 실행 환경인 단일 테 넌 트 Azure Logic Apps에서 호스팅됩니다. 또한 단일 테 넌 트 Azure Logic Apps 런타임은 [Azure Functions 확장성 모델](../azure-functions/functions-bindings-register.md) 을 사용 하 고 Azure Functions 런타임에서 확장으로 호스팅됩니다. 이 설계는 논리 앱 워크플로에 대 한 이식성, 유연성 및 추가 성능을 제공 하 고 Azure Functions 플랫폼과 Azure App Service 에코 시스템에서 상속 된 다른 기능 및 혜택을 제공 합니다.

복제 및 페더레이션에 대 한 자세한 내용은 다음 설명서를 검토 하세요.

- [다중 사이트 및 다중 지역 페더레이션 Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [이벤트 복제 작업 패턴](../event-hubs/event-hubs-federation-patterns.md)
- [Service Bus 메시지 복제 및 지역 간 페더레이션](../service-bus-messaging/service-bus-federation-overview.md)
- [메시지 복제 작업 패턴](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-task-templates"></a>

## <a name="replication-task-templates"></a>복제 태스크 템플릿

현재 [azure Event Hubs](../event-hubs/event-hubs-about.md) 및 [azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)에서 복제 작업 템플릿을 사용할 수 있습니다. 다음 표에는이 미리 보기에서 현재 사용할 수 있는 복제 태스크 템플릿이 나열 되어 있습니다.

| 리소스 종류 | 복제 원본 및 대상 |
|---------------|-------------------------------|
| Azure Event Hubs 네임스페이스 | -인스턴스를 Event Hubs 인스턴스로 Event Hubs <br>-Service Bus 큐에 Event Hubs 인스턴스 <br>-Event Hubs 인스턴스 Service Bus 항목 |
| Azure Service Bus 네임스페이스 | -큐를 Service Bus 큐 Service Bus <br>-Service Bus 항목에 큐 Service Bus <br>-Service Bus 항목 Service Bus 항목 <br>-Event Hubs 인스턴스로 큐 Service Bus <br>-Service Bus 큐에 Service Bus 토픽 <br>-Event Hubs 인스턴스에 Service Bus 항목 |
|||

### <a name="replication-topology-and-workflow"></a>복제 토폴로지 및 워크플로

Azure Logic Apps (표준)에서 제공 하는 복제 작업의 작동 원리를 시각화 하는 데 도움을 주기 위해 다음 다이어그램에서는 Event Hubs 인스턴스 및 Service Bus 큐에 대 한 복제 태스크 구조와 워크플로를 보여 줍니다.

#### <a name="replication-topology-for-event-hubs"></a>Event Hubs에 대 한 복제 토폴로지

다음 다이어그램에서는 Event Hubs 인스턴스 간의 토폴로지 및 복제 작업 워크플로를 보여 줍니다.

![Event Hubs 인스턴스 사이에서 "논리 앱 (표준)" 워크플로를 통해 제공 되는 복제 작업에 대 한 토폴로지를 보여 주는 개념적 다이어그램](media/create-replication-tasks-azure-resources/replication-topology-event-hubs.png)

Azure Event Hubs의 복제 및 페더레이션에 대 한 자세한 내용은 다음 설명서를 검토 하세요.

- [다중 사이트 및 다중 지역 페더레이션 Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [이벤트 복제 작업 패턴](../event-hubs/event-hubs-federation-patterns.md)

#### <a name="replication-topology-for-service-bus"></a>Service Bus에 대 한 복제 토폴로지

다음 다이어그램에서는 Service Bus 큐 간의 토폴로지 및 복제 작업 워크플로를 보여 줍니다.

![Service Bus 큐 사이에서 "논리 앱 (표준)" 워크플로를 통해 제공 되는 복제 작업에 대 한 토폴로지를 보여 주는 개념적 다이어그램](media/create-replication-tasks-azure-resources/replication-topology-service-bus-queues.png)

Azure Service Bus의 복제 및 페더레이션에 대 한 자세한 내용은 다음 설명서를 검토 하세요.

- [Service Bus 메시지 복제 및 지역 간 페더레이션](../service-bus-messaging/service-bus-federation-overview.md)
- [메시지 복제 작업 패턴](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-properties"></a>

## <a name="metadata-and-property-mappings"></a>메타 데이터 및 속성 매핑

Event Hubs 원본 Event Hubs 네임 스페이스에서 가져온 다음 항목은 대상 Event Hubs 네임 스페이스의 새 서비스 할당 값으로 대체 됩니다. 이벤트의 서비스 할당 된 메타 데이터, 원래 큐에 대기 시간, 시퀀스 번호 및 오프셋입니다. 그러나 Azure에서 제공 하는 샘플의 [도우미 함수](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 및 복제 작업에 대 한 원래 값은 사용자 속성 `repl-enqueue-time` (ISO8601 string), 및에 보존 됩니다. `repl-sequence` `repl-offset` 이러한 속성에는 `string` 형식이 있으며 해당 원래 속성의 문자열 형식 값이 포함 됩니다. 이벤트가 여러 번 전달되는 경우에는 직접적인 원본의 서비스 할당된 메타데이터가 이미 존재하는 속성에 추가되고 값은 세미콜론으로 구분됩니다. 자세한 내용은 [서비스 할당 메타 데이터-이벤트 복제 태스크 패턴](../event-hubs/event-hubs-federation-patterns.md#service-assigned-metadata)을 참조 하세요.

Service Bus에 대해 원본 Service Bus 큐 또는 토픽에서 가져온 다음 항목은 메시지의 서비스 할당 된 메타 데이터, 원래 큐에 넣기 시간 및 시퀀스 번호의 새 서비스 할당 Service Bus 값으로 대체 됩니다. 그러나 Azure에서 제공 하는 샘플의 기본 복제 태스크의 경우 원래 값은 사용자 속성 `repl-enqueue-time` (ISO8601 string) 및에 유지 `repl-sequence` 됩니다. 이러한 속성에는 `string` 형식이 있으며 해당 원래 속성의 문자열 형식 값이 포함 됩니다. 메시지가 여러 번 전달되는 경우에는 직접적인 원본의 서비스 할당된 메타데이터가 이미 존재하는 속성에 추가되고 값은 세미콜론으로 구분됩니다. 자세한 내용은 [서비스 할당 메타 데이터-메시지 복제 작업 패턴](../service-bus-messaging/service-bus-federation-patterns.md#service-assigned-metadata)을 참조 하세요.

태스크가 Service Bus에서 Event Hubs로 복제할 때 작업은 속성만 속성 `User Properties` 에 매핑합니다 `Properties` . 그러나 태스크가 Event Hubs에서 Service Bus로 복제 되는 경우 작업은 다음 속성을 매핑합니다.

| Event Hubs에서 | Service Bus |
|-----------------|----------------|
| ContentType | ContentType |
| CorrelationId | CorrelationId |
| MessageId | MessageId |
| PartitionKey | PartitionKey SessionId |
| 속성 | 사용자 속성 |
| ReplyTo | ReplyTo |
| ReplyToGroupName | ReplyToSessionId |
| 제목 | 레이블 |
| 대상 | 대상 |
|||

<a name="order-preservation"></a>

## <a name="order-preservation"></a>순서 유지

Event Hubs에서 동일한 수의 [파티션](../event-hubs/event-hubs-features.md#partitions) 간 복제는 이벤트를 변경 하지 않고 1:1 클론을 만들지만 중복 항목을 포함할 수도 있습니다. 그러나 파티션 키에 따라 이벤트의 상대적인 순서만 유지 되지만 중복 항목을 포함할 수도 있습니다. 자세한 내용은 [스트림 및 주문 보존](../event-hubs/event-hubs-federation-patterns.md#streams-and-order-preservation)을 검토 하세요.

Service Bus에 대해 원본에서 검색 된 동일한 세션 id를 가진 메시지 시퀀스가 대상 큐 또는 토픽에 동일한 세션 id를 가진 원래 시퀀스의 일괄 처리로 전송 되도록 세션을 사용 하도록 설정 해야 합니다. 자세한 내용은 [시퀀스 및 순서 유지](../service-bus-messaging/service-bus-federation-patterns.md#sequences-and-order-preservation)검토를 참조 하세요.

복제 작업을 만들 수 있는 Azure 서비스의 다중 사이트 및 다중 지역 페더레이션을 자세히 알아보려면 다음 문서를 검토 하세요.

- [다중 사이트 및 다중 지역 페더레이션 Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [이벤트 복제 작업 패턴](../event-hubs/event-hubs-federation-patterns.md)
- [Service Bus 메시지 복제 및 지역 간 페더레이션](../service-bus-messaging/service-bus-federation-overview.md)
- [메시지 복제 작업 패턴](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="pricing"></a>

## <a name="pricing"></a>가격 책정

아래에서 복제 작업은 단일 테 넌 트 Azure Logic Apps에서 호스트 되는 **논리 앱 (표준)** 리소스의 상태 비저장 워크플로에 의해 구동 됩니다. 이 복제 작업을 만들 때 요금은 즉시 부과 되기 시작 합니다. 사용량, 계량, 요금 청구 및 가격 책정 모델은 [표준 호스팅 계획](logic-apps-pricing.md#standard-pricing) 및 [표준 계획 가격 책정 계층](logic-apps-pricing.md#standard-pricing-tiers)을 따릅니다.

<a name="scale-up"></a>

Event Hubs에서 수신 하는 이벤트 수 또는 Service Bus 처리 하는 메시지를 기준으로 호스팅 계획을 확장 하거나 축소 하 여 활성 복제 중에 최소 vcpu 사용량과 낮은 대기 시간을 유지할 수 있습니다. 이 동작을 수행 하려면 복제 작업에 사용할 논리 앱 리소스를 만들 때 [적절 한 표준 계획 가격 책정 계층을 선택](#scale-out) 하 여 CPU 사용량을 제한 하거나 시작 하지 않고 빠른 복제 속도를 보장할 수 있도록 Azure Logic Apps 합니다.

> [!NOTE]
> 앱이 WS1 계획의 인스턴스 하나에서 시작 하 여 두 개의 인스턴스로 확장 된 경우 계획을 매일 실행 한다고 가정 하 고 비용은 WS1 비용의 두 배입니다. 앱을 WS2 계획으로 확장 하 고 인스턴스 하나를 사용 하는 경우 비용은 사실상 두 WS1 계획 인스턴스와 동일 합니다. 마찬가지로, 앱을 WS3 계획으로 확장 하 고 인스턴스 하나를 사용 하는 경우 비용은 실제로 두 WS2 계획 인스턴스 또는 4 개의 WS1 plan 인스턴스와 동일 합니다.

<a name="scale-out"></a>

다음 예에서는 시나리오의 Event Hubs 또는 Service Bus 및 다양 한 구성 값에 따라 특정 복제 작업 시나리오에 대 한 최상의 처리량과 비용을 제공 하는 호스팅 계획 가격 책정 계층 및 구성 옵션을 보여 줍니다.

> [!NOTE]
> 다음 섹션의 예제에서는 800을 프리페치 수에 대 한 기본값으로 사용 하 고, Event Hubs에 대 한 최대 이벤트 일괄 처리 크기 및 Service Bus에 대 한 최대 메시지 수를 사용 하 여 이벤트 또는 메시지 크기가 1kb 인 것으로 가정 합니다. 이벤트 크기에 따라 프리페치 수, 최대 이벤트 일괄 처리 크기 또는 최대 메시지 수를 조정 하는 것이 좋습니다. 예를 들어 이벤트 크기나 메시지 크기가 1kb를 초과 하는 경우 프리페치 수의 값과 최대 이벤트 일괄 처리 크기 또는 800의 메시지 수를 줄일 수 있습니다.

### <a name="event-hubs-scale-out"></a>규모 확장 Event Hubs

다음 예에서는 [파티션](../event-hubs/event-hubs-features.md#partitions)수, 초당 이벤트 수, 기타 구성 값을 기준으로 *동일한 지역에 있는* 두 Event Hubs 네임 스페이스 간의 복제 작업에 대 한 호스팅 계획 가격 책정 계층 및 구성 옵션을 보여 줍니다.

이 섹션의 예제에서는 이벤트 크기가 1kb 인 것으로 가정 하 여 프리페치 수 및 최대 이벤트 일괄 처리 크기의 기본값으로 800를 사용 합니다. 이벤트 크기에 따라 프리페치 수와 최대 이벤트 일괄 처리 크기를 조정 하는 것이 좋습니다. 예를 들어 이벤트 크기가 1kb를 초과 하는 경우 프리페치 수 및 최대 이벤트 일괄 처리 크기 값을 800에서 줄일 수 있습니다.

| 가격 책정 계층 | 파티션 수 | 초당 이벤트 수 | 최대 버스트 * | 항상 준비 인스턴스 * | 프리페치 수 * | 최대 이벤트 일괄 처리 크기 * |
|--------------|-----------------|-------------------|----------------|-------------------------|-----------------|-----------------|
| **WS1** | 1 | 1000 | 1 | 1 | 800 | 800 |
| **WS1** | 2 | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 4 | 4000 | 2 | 1 | 800 | 800 |
| **WS2** | 8 | 8000 | 2 | 1 | 800 | 800 |
| **WS3** | 16 | 16000 | 2 | 1 | 800 | 800 |
| **WS3** | 32 | 32000 | 3 | 1 | 800 | 800 |
||||||||

\* 각 가격 책정 계층에 대해 변경할 수 있는 값에 대 한 자세한 내용은 다음 표를 참조 하세요.

| 값 | 설명 |
|-------|-------------|
| **최대 버스트** | 부하 상태에서 확장할 *최대* 탄력적 작업자 수입니다. 기본 앱에서 다음 테이블 행의 *항상 준비 인스턴스* 를 초과 하는 인스턴스를 필요로 하는 경우 인스턴스 수가 최대 버스트 제한에 도달할 때까지 앱이 계속 확장 될 수 있습니다. 이 값을 변경 하려면이 문서의 뒷부분에 있는 [호스팅 계획 scale out 설정 편집](#edit-plan-scale-out-settings) 을 검토 하세요. <p>**참고**: 계획 크기를 초과 하는 인스턴스는 실행 중이 고 초 단위로 사용자에 게 할당 된 경우에 *만* 청구 됩니다. 플랫폼은 정의 된 최대 제한까지 앱을 확장 하는 가장 좋은 노력을 합니다. <p>**팁**: 권장 사항으로, 필요한 경우 사용 하지 않는 인스턴스가 청구 되지 않으므로 필요한 경우 플랫폼을 확장 하 여 더 큰 부하를 처리할 수 있도록 하는 데 필요할 수 있는 최대 값을 선택 합니다. <p>자세한 내용은 워크플로 표준 계획이 Azure Functions Premium 계획과 몇 가지 측면을 공유 하므로 다음 문서를 검토 하세요. <p>- [요금제 및 SKU 설정-Azure Functions Premium 계획](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [클라우드 버스트](https://azure.microsoft.com/overview/what-is-cloud-bursting/)란? |
| **상시 준비 인스턴스** | 앱을 호스팅하기 위해 항상 준비 되 고 준비 되는 최소 인스턴스 수입니다. 최소 수는 항상 1입니다. 이 값을 변경 하려면이 문서의 뒷부분에 있는 [호스팅 계획 scale out 설정 편집](#edit-plan-scale-out-settings) 을 검토 하세요. <p>**참고**: 계획 크기를 초과 하는 인스턴스는 자신에 게 할당 될 때 실행 되 고 *있는지 여부* 에 따라 비용이 청구 됩니다. <p>자세한 내용은 워크플로 표준 계획이 Azure Functions Premium 계획: [항상 준비 인스턴스-Azure Functions Premium 계획과](../azure-functions/functions-premium-plan.md#always-ready-instances)같은 일부 측면을 공유 하는 다음 문서를 검토 하세요. |
| **프리페치 수** | 기본 클래스에서 사용 하는 `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` 프리페치 수를 결정 하는 논리 앱 리소스의 앱 설정에 대 한 기본값입니다 `EventProcessorHost` . 이 앱 설정에 다른 값을 추가 하거나 지정 하려면 다음 예제와 같이 [앱 설정 관리](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)를 검토 합니다. <p>- **이름**: `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **값**: `800` (최대 제한 없음) <p>속성에 대 한 자세한 내용은 `prefetchCount` 다음 설명서를 검토 하세요. <p>- [host. json 설정-Azure Functions에 대 한 Azure Event Hubs 트리거 및 바인딩](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [EventProcessorOptions 속성](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount) <br>- [응용 프로그램의 여러 인스턴스에 걸쳐 파티션 부하를 분산](../event-hubs/event-processor-balance-partition-load.md)합니다. <br>- [이벤트 프로세서 호스트](../event-hubs/event-hubs-event-processor-host.md) <br>- [EventProcessorHost 클래스](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) |
| **최대 이벤트 일괄 처리 크기** | `AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize`각 수신 루프가 수신 하는 최대 이벤트 수를 결정 하는 논리 앱 리소스의 앱 설정에 대 한 기본값입니다. 이 앱 설정에 대해 다른 값을 추가하거나 지정하려면 [앱 설정 관리 - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)을 검토합니다. 예를 들면 다음과 같습니다. <p>- **이름:**`AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__maxBatchSize` <br>- **값:** `800` (최대 제한 없음) <p>속성에 대한 자세한 내용은 `maxBatchSize` 다음 설명서를 검토하세요. <p>- [host.json 설정 - Azure Functions 대한 트리거 및 바인딩 Azure Event Hubs](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) <br>- [EventProcessorOptions.MaxBatchSize 속성](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize) <br>- [이벤트 프로세서 호스트](../event-hubs/event-hubs-event-processor-host.md) |
|||

### <a name="service-bus-scale-out"></a>Service Bus 규모 확장

다음 예제에서는 초당 메시지 수와 기타 구성 값에 따라 동일한 지역의 두 Service Bus 네임스페이스 간의 복제 *태스크에* 대한 호스팅 계획 가격 책정 계층 및 구성 옵션을 보여 줍니다.

이 섹션의 예제에서는 메시지 크기가 1KB라고 가정하여 프리페치 수 및 최대 메시지 수의 기본값으로 800을 사용합니다. 메시지 크기에 따라 프리페치 수와 최대 메시지 수를 조정할 수 있습니다. 예를 들어 메시지 크기가 1KB를 초과하면 프리페치 수 및 최대 메시지 수의 값을 800으로 줄일 수 있습니다.

| 가격 책정 계층 | 초당 메시지 수 | 최대 버스트* | 항상 준비된 인스턴스* | 프리페치 수* | 최대 메시지 수* |
|--------------|---------------------|-----------------|-------------------------|-----------------|------------------------|
| **WS1** | 2000 | 1 | 1 | 800 | 800 |
| **WS2** | 2500 | 1 | 1 | 800 | 800 |
| **WS3** | 3500 | 1 | 1 | 800 | 800 |
|||||||

\* 각 가격 책정 계층에 대해 변경할 수 있는 값에 대한 자세한 내용은 다음 표를 검토하세요.

| 값 | 설명 |
|-------|-------------|
| **최대 버스트 수** | 부하에서 규모 확장할 *탄력적* 작업자의 최대 수입니다. 기본 앱에 다음 테이블 행의 *항상 준비된 인스턴스를* 초과하는 인스턴스가 필요한 경우 인스턴스 수가 최대 버스트 제한에 도달할 때까지 앱이 계속 확장될 수 있습니다. 이 값을 변경하려면 이 문서의 후반부에서 [호스팅 계획 규모 확장 설정 편집을](#edit-plan-scale-out-settings) 검토하세요. <p>**참고:** 계획 크기를 초과하는 모든 인스턴스는 실행 중일 *때만* 요금이 청구되고 초당 할당됩니다. 플랫폼은 정의된 최대 제한까지 앱을 확장하기 위해 최상의 노력을 기울입니다. <p>**팁:** 필요한 경우 사용되지 않는 인스턴스에 대한 요금이 청구되지 않으므로 플랫폼이 더 큰 부하를 처리하도록 확장할 수 있도록 필요한 것보다 높은 최대값을 선택합니다. <p>자세한 내용은 워크플로 표준 계획이 Azure Functions Premium 계획과 몇 가지 측면을 공유하기 때문에 다음 설명서를 검토하세요. <p>- [계획 및 SKU 설정 - Azure Functions Premium 계획](../azure-functions/functions-premium-plan.md#plan-and-sku-settings) <br>- [클라우드 버스팅이란?](https://azure.microsoft.com/overview/what-is-cloud-bursting/) |
| **상시 준비 인스턴스** | 앱을 호스트할 준비가 되어 있고 웜인 인스턴스의 최소 수입니다. 최소 수는 항상 1입니다. 이 값을 변경하려면 이 문서의 후반부에서 [호스팅 계획 규모 확장 설정 편집을](#edit-plan-scale-out-settings) 검토하세요. <p>**참고:** 계획 크기를 초과하는 모든 인스턴스는 할당될 때 실행 *중인지 여부에 관계없이* 요금이 청구됩니다. <p>자세한 내용은 워크플로 표준 계획이 Azure Functions Premium 계획과 일부 측면을 공유하기 때문에 다음 설명서를 [검토하세요. 항상 준비된 인스턴스 - 계획 Azure Functions Premium.](../azure-functions/functions-premium-plan.md#always-ready-instances) |
| **프리페치 수** | `AzureFunctionsJobHost__extensions__serviceBus__prefetchCount`기본 클래스에서 사용하는 프리 페치 횟수를 결정하는 논리 앱 리소스의 앱 설정에 대한 기본값입니다. `ServiceBusProcessor` 이 앱 설정에 대해 다른 값을 추가하거나 지정하려면 [앱 설정 관리 - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)을 검토합니다. 예를 들면 다음과 같습니다. <p>- **이름:**`AzureFunctionsJobHost__extensions__eventHubs__eventProcessorOptions__prefetchCount` <br>- **값:** `800` (최대 제한 없음) <p>속성에 대한 자세한 내용은 `prefetchCount` 다음 설명서를 검토하세요. <p>- [host.json 설정 - Azure Functions 대한 Azure Service Bus 바인딩](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) <br>- [ServiceBusProcessor.PrefetchCount 속성](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount) <br>- [ServiceBusProcessor 클래스](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) |
| **최대 메시지 수** | `AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount`트리거될 때 보낼 최대 메시지 수를 결정하는 논리 앱 리소스의 앱 설정에 대한 기본값입니다. 이 앱 설정에 대해 다른 값을 추가하거나 지정하려면 [앱 설정 관리 - local.settings.json](edit-app-settings-host-settings.md?tabs=azure-portal#manage-app-settings)을 검토합니다. 예를 들면 다음과 같습니다. <p>- **이름:**`AzureFunctionsJobHost__extensions__serviceBus__batchOptions__maxMessageCount` <br>- **값:** `800` (최대 제한 없음) <p>속성에 대한 자세한 내용은 `maxMessageCount` [host.json 설정 - Azure Functions 바인딩 Azure Event Hubs](../azure-functions/functions-bindings-service-bus.md#hostjson-settings)설명서를 검토하세요.|
|||

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

- 지역 재해 복구 장애 조치 시나리오를 테스트할 수 있도록 서로 다른 Azure 지역에 있어야 하는 원본 및 대상 리소스 또는 엔터티입니다. 이러한 엔터티는 사용하려는 작업 템플릿에 따라 달라질 수 있습니다. 이 문서의 예제에서는 서로 다른 네임스페이스와 Azure 지역에 있는 두 개의 Service Bus 큐를 사용합니다.

- 복제 작업을 만들 때 다시 사용할 수 있는 **논리 앱(표준)** 리소스입니다. 이렇게 하면 복제 시나리오의 요구 사항(예: 용량, 처리량 및 크기 조정)에 따라 [호스팅 계획 및 가격 책정 계층을 선택하여](#pricing) 복제 작업에 맞게 이 리소스를 사용자 지정할 수 있습니다. 복제 작업을 만들 때 이 리소스를 만들 수 있지만 지역, 호스팅 계획 및 가격 책정 계층은 변경할 수 없습니다. 다음 목록에서는 이전에 만든 논리 앱 리소스에 대한 다른 이유와 모범 사례를 제공합니다.

  - 복제 작업의 원본 및 대상 엔터티와 다른 지역에서 이 논리 앱 리소스를 만들 수 있습니다.

    현재 이 지침은 Azure 리소스 내에서 복제 작업의 네이티브 통합으로 인해 제공됩니다. 엔터티 간에 복제 작업을 만들고 기존 논리 앱 리소스를 사용하는 대신 새 논리 앱 리소스를 만들도록 선택하면 *새 논리 앱이 원본 엔터티 와 동일한 지역에 만들어집니다.* 원본 지역을 사용할 수 없게 되면 복제 작업도 작동할 수 없습니다. 장애 조치(failover) 시나리오에서 작업은 [활성-수동 복제 패턴이](../service-bus-messaging/service-bus-federation-overview.md#active-passive-replication) 달성하려고 하는 대상 엔터티인 새 원본에서 데이터를 읽기 시작할 수도 없습니다.

  - 기본 특성을 사용하는 대신 호스팅 계획 및 가격 책정 계층을 선택하여 이 논리 앱 리소스를 미리 사용자 지정할 수 있습니다. 이렇게 하면 복제 태스크에서 더 빠른 복제를 위해 초당 더 많은 이벤트 또는 메시지를 처리할 수 있습니다. 복제 작업을 만들 때 이 리소스를 만드는 경우 이러한 기본 특성이 수정됩니다.

  - 특히 활성-수동 복제 패턴을 따르려는 경우 이 논리 앱 리소스에 *복제 작업 워크플로만* 포함되도록 할 수 있습니다. 기존 논리 앱을 사용하여 복제 작업을 만드는 경우 이 옵션은 해당 논리 앱 리소스에 작업(상태 비활성 워크플로)을 추가합니다.

  자세한 내용은 [Azure Portal 단일 테넌트 Azure Logic Apps(표준)를](create-single-tenant-workflows-azure-portal.md)사용하여 통합 워크플로 만들기를 참조하세요.

- 선택 사항: 대상 네임스페이스에 대한 연결 문자열입니다. 이 옵션을 사용하면 대상이 다른 구독에 있으므로 구독 간 복제를 설정할 수 있습니다.

   대상 엔터티에 대한 연결 문자열을 찾으려면 다음 단계를 수행합니다.

   1. [Azure Portal](https://portal.azure.com)대상 네임스페이스로 이동합니다.

   1. 네임스페이스 탐색 메뉴의 **설정** 아래에서 **공유 액세스 정책을** 선택합니다.

   1. 열리는 **공유 액세스 정책** 창의 **정책** 아래에서 **RootManageSharedAccessKey 를** 선택합니다.

   1. 열리는 **SAS 정책: RootManageSharedAccessKey** 창에서 **기본 연결 문자열** 값을 복사합니다.

   1. 나중에 문자열을 사용하여 대상 네임스페이스에 연결할 수 있도록 연결 문자열을 어딘가에 저장합니다.

<a name="naming"></a>

## <a name="naming-conventions"></a>명명 규칙

아직 만들지 않은 경우 복제 작업 또는 엔터티에 사용하는 명명 전략을 신중하게 고려해야 합니다. 이름을 쉽게 식별할 수 있고 구별할 수 있는지 확인합니다. 예를 들어 Event Hubs 네임스페이스로 작업하는 경우 복제 작업은 원본 네임스페이스의 모든 Event Hubs 인스턴스에서 복제됩니다. Service Bus 큐로 작업하는 경우 다음 표에서는 엔터티 및 복제 작업의 이름을 지정하는 예제를 제공합니다.

| 원본 이름 | 예제 | 복제 앱 | 예제 | 대상 이름 | 예제 |
|-------------|---------|-----------------|---------|-------------|---------|
| 네임스페이스: `<name>-sb-<region>` | `fabrikam-sb-weu` | 논리 앱: `<name-source-region-target-region>` | `fabrikam-rep-weu-wus` | 네임스페이스: `<name>-sb-<region>` | `fabrikam-sb-wus` |
| 큐: `<name>` | `jobs-transfer` | 워크플로: `<name>` | `jobs-transfer-workflow` | 큐: `<name>` | `jobs` |
|||||||

<a name="create-replication-task"></a>

## <a name="create-a-replication-task"></a>복제 작업 만들기

이 예제에서는 Service Bus 큐에 대한 복제 작업을 만드는 방법을 보여줍니다.

1. [Azure Portal](https://portal.azure.com)소스로 사용할 Service Bus 네임스페이스를 찾습니다.

1. 네임스페이스 탐색 메뉴의 **Automation** 섹션에서 **작업(미리 보기)** 을 선택합니다.

   !["작업(미리 보기)"이 선택된 Azure Portal 및 Azure Service Bus 네임스페이스 메뉴를 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/service-bus-automation-menu.png)

1. **작업** 창에서 작업 **템플릿을** 선택할 수 있도록 작업 추가를 선택합니다.

   !["작업 추가"가 선택된 "작업(미리 보기)" 창을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/add-replication-task.png)

1. 작업 **추가 창의** 템플릿 **선택** 아래에서 만들려는 복제 태스크에 대한 템플릿에서 선택을 **선택합니다.** 다음 페이지가 표시되지 않으면 **다음: 인증을** 선택합니다.

   이 예제에서는 Service Bus **큐에서 큐로 복제** 작업 템플릿을 선택하여 Service Bus 큐 간에 콘텐츠를 복제합니다.

   !["Service Bus 큐에서 큐로 복제" 템플릿이 선택된 "작업 추가" 창을 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/select-replicate-service-bus-template.png)

1. **인증** 탭의 **연결** 섹션에서 모든 연결에 대한 인증 자격 증명을 제공할 수 있도록 태스크에 표시되는 모든 연결에 대해 **만들기를** 선택합니다. 각 작업의 연결 유형은 작업에 따라 다릅니다.

   이 예제에서는 대상 큐가 있는 대상 Service Bus 네임스페이스에 대한 연결을 만들라는 메시지를 표시합니다. 원본 Service Bus 네임스페이스에 대한 연결이 이미 있습니다.

   ![대상 Service Bus 네임스페이스에 연결하기 위해 선택한 "만들기" 옵션을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/create-authenticate-connections.png)

1. 대상에 필요한 정보를 제공한 다음, **만들기를** 선택합니다.

   이 예제에서는 연결의 표시 이름을 제공한 다음 대상 큐가 있는 Service Bus 네임스페이스를 선택합니다.

   ![지정된 연결 표시 이름과 Service Bus 네임스페이스가 선택된 "커넥트" 창을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/connect-target-service-bus-namespace.png)

   > [!TIP]
   > 연결 문자열을 사용하여 연결을 대신 만들 수도 있습니다. 이 옵션을 사용하면 대상을 다른 구독에 사용할 수 있으므로 구독 간 복제를 설정할 수 있습니다. 대상 또는 복제 태스크 만들기를 시작한 위치에 기반한 원본이 동적으로 구성되므로 대상만 연결하면 됩니다. 연결 문자열을 사용하려면 다음 단계를 사용합니다.
   >
   > 1. **커넥트** 창에서 **연결 문자열을 통해 커넥트** 선택합니다.
   >
   > 2. 연결 **문자열** 상자에 대상 네임스페이스에 대한 연결 문자열을 입력합니다.

   다음 예제에서는 성공적으로 생성된 연결을 보여줍니다.

   ![Service Bus 네임스페이스에 대한 연결이 완료된 "작업 추가" 창을 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/connected-service-bus-namespaces.png)

1. 모든 연결을 완료한 후 **다음: 구성을** 선택합니다.

1. **구성** 탭에서 태스크 이름 및 작업에 필요한 기타 정보를 제공합니다.

   > [!NOTE]
   > 작업 이름은 만든 후에 변경할 수 없으므로 [기본 워크플로를 편집](#edit-task-workflow)하는 경우에도 계속 적용되는 이름을 고려해야 합니다. 기본 워크플로에 대한 변경 내용은 작업 템플릿이 아니라 사용자가 만든 작업에만 적용됩니다.
   >
   > 예를 들어 작업의 이름을 `fabrikam-rep-weu-wus` 로 지정하지만 나중에 다른 용도로 기본 워크플로를 편집하는 경우 작업 이름을 일치하도록 변경할 수 없습니다.

   1. 기존 **논리 앱(표준)** 리소스에 작업 워크플로를 추가하려면 **논리 앱** 목록에서 기존 논리 앱을 선택합니다. 대신 새 **논리 앱(표준)** 리소스를 만들려면 **논리 앱** 목록에서 **새 만들기를** 선택하고 새 논리 앱에 사용할 이름을 제공합니다.

      > [!NOTE]
      > 복제 작업을 만드는 동안 새 논리 앱 리소스를 만드는 경우 논리 앱은 *원본 엔터티와 동일한 지역에* 만들어지며, 원본 지역을 사용할 수 없게 되고 장애 조치(failover) 시나리오에서 작동하지 않는 경우 문제가 됩니다. 모범 사례는 원본이 아닌 다른 지역에 **논리 앱(표준)** 리소스를 만드는 것입니다. 복제 작업을 만들 때 기존 논리 앱을 대신 선택하고 기본 상태 비저장 워크플로를 기존 논리 앱에 추가합니다. 자세한 내용은 필수 [구성 조건 을 검토하세요.](#prerequisites)

   1. 완료되면 **검토 + 만들기** 를 선택합니다.

   ![작업 이름, 원본 및 대상 큐 이름, 논리 앱 리소스에 사용할 이름과 같은 복제 작업 정보가 있는 "작업 추가" 창을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/configure-replication-task.png)

1. **검토 + 만들기** 탭에서 복제 작업이 작업에 필요한 Azure 리소스를 확인합니다.

   - 복제 태스크에 대한 새 논리 앱 리소스를 만들도록 선택한 경우 복제 태스크가 작동하도록 만드는 데 필요한 Azure 리소스가 창에 표시됩니다. 예를 들어 이러한 리소스에는 논리 앱 리소스, 워크플로 및 기타 런타임 작업에 대한 구성 정보가 포함된 Azure Storage 계정이 포함됩니다. 예를 들어 Event Hubs 이 스토리지 계정에는 원본 지역이 중단되거나 사용할 수 없게 되면 원본 엔터티가 중지되는 스트림의 위치 또는 *오프셋과* 검사점 정보가 포함됩니다.

     다음 예제에서는 새 논리 앱을 **만들도록** 선택한 경우 검토 + 만들기 탭을 보여줍니다.

     ![새 논리 앱을 만들 때 리소스 정보가 있는 "검토 + 만들기" 창을 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/validate-replication-task-new-logic-app.png)

   - 복제 태스크에 기존 논리 앱 리소스를 다시 사용하려는 경우 복제가 작동하는 데 다시 사용할 Azure 리소스가 창에 표시됩니다.

     다음 예제에서는 기존 논리 앱을 다시 사용하도록 선택한 경우 **검토 + 만들기** 탭을 보여줍니다.

     ![기존 논리 앱을 다시 사용 시 리소스 정보가 있는 "검토 + 만들기" 창을 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/validate-replication-task-existing-logic-app.png)

   > [!NOTE]
   > 원본, 대상 또는 둘 다 가상 네트워크 뒤에 있는 경우 작업을 만든 후 권한 및 액세스를 설정해야 합니다. 이 시나리오에서는 논리 앱 워크플로가 복제 작업을 수행할 수 있도록 권한 및 액세스 권한이 필요합니다.

1. 준비가 되면 **만들기** 를 선택합니다.

   자동으로 라이브로 실행 중인 만든 작업이 이제 **작업** 목록에 표시됩니다.

   > [!TIP]
   > 작업이 즉시 표시되지 않으면 작업 목록을 새로 고치거나 잠시 기다렸다가 새로 고칩니다. 도구 모음에서 **새로 고침** 을 선택합니다.

   ![만든 복제 작업이 있는 "작업" 창을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/created-replication-task.png)

1. 리소스가 가상 네트워크 뒤에 있는 경우 논리 앱 리소스 및 워크플로에서 해당 리소스에 액세스할 수 있는 권한을 설정해야 합니다.

## <a name="set-up-retry-policy"></a>재시도 정책 설정

복제 관계의 어느 쪽에서든 가용성 이벤트 중 데이터 손실을 방지하려면 견고성을 위해 재시도 정책을 구성해야 합니다. 복제 태스크에 대한 재시도 정책을 구성하려면 [Azure Logic Apps 재시도 정책에 대한 설명서](logic-apps-exception-handling.md#retry-policies) 및 [기본 워크플로를 편집하는](#edit-task-workflow)단계를 검토합니다.

<a name="review-task-history"></a>

## <a name="review-task-history"></a>작업 기록 검토

이 예제에서는 워크플로 실행의 태스크 기록을 상태, 입력, 출력 및 기타 정보와 함께 보고 Service Bus 큐 복제 태스크에 대한 예제를 계속 사용하는 방법을 보여 드립니다.

1. [Azure Portal](https://portal.azure.com)검토하려는 작업 기록이 있는 Azure 리소스 또는 엔터티를 찾습니다.

   이 예제에서 이 리소스는 Service Bus 네임스페이스입니다.

1. 리소스 탐색 메뉴의 **설정** **아래에서 Automation** 섹션에서 **작업(미리 보기)** 을 선택합니다.

1. **작업** 창에서 검토하려는 작업을 찾습니다. 해당 작업의 **실행** 열에서 **보기** 를 선택합니다.

   !["작업" 창, 복제 작업 및 선택한 "보기" 옵션을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-runs-for-task.png)

   이 단계에서는 표준 논리 앱 리소스에 포함된 기본 상태 비저장 워크플로에 대한 **개요** 창을 엽니다.

1. 상태 비활성 워크플로에 대한 실행 기록을 보려면 **개요** 창 도구 모음에서 **디버그 모드 사용을** 선택합니다.

   **실행 기록** 탭에는 해당 식별자, 상태, 시작 시간 및 실행 기간과 함께 태스크에 대한 모든 이전 진행 중 및 대기 중인 실행이 표시됩니다.

   ![작업의 실행, 해당 상태 및 기타 정보를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/run-history-list.png)

   다음 표에서는 실행의 가능한 상태를 설명합니다.

   | 상태 레이블 | Description |
   |--------------|-------------|
   | **취소** | 실행 중 작업이 취소되었습니다. |
   | **실패** | 작업에 실패한 하나 이상의 동작이 있지만 실패를 처리하기 위한 후속 동작이 없었습니다. |
   | **실행 중** | 작업이 현재 실행되고 있습니다. |
   | **성공함** | 모든 작업에 성공했습니다. 동작이 실패한 경우에도 작업이 성공적으로 완료될 수 있지만 실패를 처리하기 위한 후속 동작이 있었습니다. |
   | **대기 중** | 작업의 이전 인스턴스가 아직 실행 중이므로 실행이 아직 시작되지 않았으며 일시 중지되었습니다. |
   |||

1. 실행의 각 단계에 대한 상태 및 기타 정보를 보려면 해당 실행을 선택합니다.

   실행 세부 정보 창이 열리고 실행된 기본 워크플로가 표시됩니다.

   - 워크플로는 항상 [*트리거*](../connectors/apis-list.md#triggers)로 시작합니다. 이 작업의 경우 워크플로는 메시지가 원본 Service Bus 큐에 도착할 때까지 기다리는 Service Bus 트리거로 시작합니다.

   - 각 단계에는 상태 및 실행 기간이 표시됩니다. 기간이 0초인 단계를 실행하는 데 1초 미만이 걸렸습니다.

   ![워크플로의 실행, 상태 및 실행 기간의 각 단계를 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/run-history-details.png)

1. 각 단계에 대한 입력 및 출력을 검토하려면 단계를 선택합니다. 이 단계를 선택하면 해당 단계에 대한 입력, 출력 및 속성 세부 정보를 표시하는 창이 열립니다.

   이 예제에서는 Service Bus 트리거에 대한 입력을 보여줍니다.

   ![트리거 입력, 출력 및 속성을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-trigger-inputs-outputs-properties.png)

Azure 리소스에 대한 복제 작업의 컨텍스트와 별도로 앱, 데이터, 서비스 및 시스템을 통합할 수 있도록 자체 자동화된 워크플로를 빌드하는 방법을 알아보려면 [Azure Portal 단일 테넌트 Azure Logic Apps(표준)를](create-single-tenant-workflows-azure-portal.md)사용하여 통합 워크플로 만들기를 참조하세요.

<a name="monitor"></a>

## <a name="monitor-replication-tasks"></a>복제 작업 모니터링

복제 태스크 또는 기본 논리 앱 워크플로의 성능 및 상태를 확인하려면 Azure Monitor 기능인 [애플리케이션 Insights](../azure-monitor/app/app-insights-overview.md)를 사용할 수 있습니다. [애플리케이션 Insights 애플리케이션 맵은](../azure-monitor/app/app-map.md) 복제 작업을 모니터링하는 데 사용할 수 있는 유용한 시각적 도구입니다. 이 맵은 캡처된 모니터링 정보에서 자동으로 생성되므로 복제 작업 원본 및 대상 전송의 성능 및 안정성을 탐색할 수 있습니다. 즉각적인 진단 인사이트 및 로그 세부 정보의 짧은 대기 시간 시각화를 위해 [라이브 메트릭](../azure-monitor/app/live-stream.md) 포털 도구와 Azure Monitor 기능을 사용할 수 있습니다.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>작업 편집

작업을 변경하려면 다음 옵션을 사용할 수 있습니다.

- 연결 정보 또는 구성 정보와 같은 태스크의 속성을 변경할 수 있도록 태스크 ["인라인"을 편집합니다.](#edit-task-inline)

- [디자이너에서 작업의 기본 워크플로를 편집합니다.](#edit-task-workflow)

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>"인라인"으로 작업 편집

1. [Azure Portal](https://portal.azure.com)에서 업데이트하려는 작업이 있는 리소스를 찾습니다.

1. 리소스 탐색 메뉴의 **Automation** 섹션에서 **작업(미리 보기)** 을 선택합니다.

1. 작업 목록에서 업데이트하려는 작업을 찾습니다. 작업의 줄임표( **...** ) 메뉴를 열고, **인라인 편집** 을 선택합니다.

   ![열린 줄임표 메뉴와 선택한 옵션인 "인라인 편집"을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-task-in-line.png)

   기본적으로 **인증** 탭이 나타나고 기존 연결이 표시됩니다.

1. 새 인증 자격 증명을 추가하거나 연결에 대해 다른 기존 인증 자격 증명을 선택하려면 연결의 줄임표( **...** ) 메뉴를 열고, **새 연결 추가** 를 선택하거나 사용 가능한 경우 다른 인증 자격 증명을 선택합니다.

   > [!NOTE]
   > 원본 연결이 아닌 대상 연결만 편집할 수 있습니다.

   !["인증" 탭, 기존 연결 및 선택한 타원 메뉴를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-connections.png)

1. 다른 작업 속성을 업데이트하려면 **다음: 구성을** 선택합니다.

   이 예제의 작업에 대해 다른 원본 및 대상 큐를 지정할 수 있습니다. 그러나 작업 이름과 기본 논리 앱 및 워크플로는 동일하게 유지됩니다.

   ![편집할 수 있는 "구성" 탭 및 속성을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-task-configuration.png)

1. 완료되면 **저장** 을 선택합니다.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>작업의 기본 워크플로 편집

복제 작업 뒤에 있는 기본 워크플로를 편집할 수 있습니다. 이렇게 하면 만든 태스크에 대한 원래 구성이 변경되지만 작업 템플릿 자체는 변경할 수 없습니다. 변경 내용을 저장한 후에는 편집된 작업이 더 이상 원래 작업과 동일한 기능을 수행하지 않습니다. 원래 기능을 수행하는 작업을 원하는 경우 동일한 템플릿을 사용하여 새 작업을 만들어야 할 수 있습니다. 원래 작업을 다시 만들지 않으려면 디자이너를 사용하여 작업 뒤에 있는 워크플로를 변경하지 마세요. 대신 통합 요구 사항에 맞게 **논리 앱(표준)** 상태 비지정 워크플로를 만듭니다. 자세한 내용은 [Azure Portal 단일 테넌트 Azure Logic Apps(표준)를](create-single-tenant-workflows-azure-portal.md)사용하여 통합 워크플로 만들기를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 업데이트하려는 작업이 있는 리소스를 찾습니다.

1. 리소스 탐색 메뉴의 **Automation** 섹션에서 **작업을** 선택합니다.

1. 작업 목록에서 업데이트하려는 작업을 찾습니다. 작업의 줄임표( **...** ) 메뉴를 열고, **Logic Apps에서 열기** 를 선택합니다.

   ![열린 타원 메뉴와 선택한 옵션인 "Logic Apps 열기"를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/open-task-in-designer.png)

   Azure Portal 이제 워크플로를 편집할 수 있는 디자이너로 컨텍스트를 변경합니다.

   ![디자이너 및 기본 워크플로를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-task-workflow-designer.png)

   이제 워크플로의 트리거 및 작업뿐만 아니라 트리거 및 작업에 대한 속성을 편집할 수 있습니다.

1. 트리거 또는 작업에 대한 속성을 보려면 해당 트리거 또는 작업을 선택합니다.

   ![Service Bus 트리거 속성 창을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-service-bus-trigger.png)

   이 예제에서는 트리거의 **IsSessionsEnabled** 속성이 **예** 로 변경됩니다.

1. 변경 내용을 저장하려면 디자이너 도구 모음에서 **저장** 을 선택합니다.

   ![디자이너 도구 모음 및 선택한 "저장" 명령을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/save-updated-workflow.png)

1. 업데이트된 워크플로를 테스트하고 실행하려면 업데이트된 워크플로가 포함된 논리 앱 리소스를 엽니다. 워크플로 탐색 메뉴에서 **개요**  >  **실행 트리거**  >  **실행을** 선택합니다.

   실행이 완료되면 디자이너에서 워크플로의 실행 세부 정보를 표시합니다. 각 단계에 대한 입력 및 출력을 검토하려면 단계를 선택합니다. 이 단계를 선택하면 해당 단계에 대한 입력, 출력 및 속성 세부 정보를 표시하는 창이 열립니다.

   이 예제에서는 업데이트된 트리거 속성 값과 함께 선택한 Service Bus 트리거의 입력, 출력 및 속성을 보여줍니다.

   ![트리거의 입력, 출력 및 속성이 있는 워크플로의 실행 세부 정보를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-updated-run-details-trigger-inputs.png)

1. 작업이 계속 실행되지 않도록 워크플로를 사용하지 않도록 설정하려면 **개요** 도구 모음에서 **사용 안 함을** 선택합니다. 자세한 내용은 [단일 테넌트 워크플로 사용 안 함 또는 사용을 참조하세요.](create-single-tenant-workflows-azure-portal.md#disable-or-enable-workflows)

<a name="failover"></a>

## <a name="set-up-failover-for-azure-event-hubs"></a>Azure Event Hubs 대한 장애 조치(failover) 설정

동일한 엔터티 형식 간의 Azure Event Hubs 복제의 경우 지역 재해 복구를 수행하려면 원본 엔터티에서 대상 엔터티로 장애 조치(failover)를 수행한 다음 영향을 받는 이벤트 소비자 및 생산자에게 새 원본이 되는 대상 엔터티에 엔드포인트를 사용하도록 요청해야 합니다. 따라서 재해가 발생하고 원본 엔터티가 장애 인계되면 복제 작업을 포함한 소비자와 생산자가 새 원본으로 리디렉션됩니다. 복제 태스크에서 만든 스토리지 계정에는 원본 지역이 중단되거나 사용할 수 없게 되면 원본 엔터티가 중지되는 스트림의 위치 또는 오프셋과 검사점 정보가 포함됩니다.

스토리지 계정에 원래 원본의 레거시 정보가 포함되지 않고 복제 작업이 새 원본 스트림의 시작부터 이벤트를 읽고 복제하기 시작하도록 하려면 원본 원본에서 레거시 정보를 수동으로 정리하고 복제 작업을 다시 구성해야 합니다.

1. [Azure Portal](https://portal.azure.com)복제 작업 뒤에 있는 논리 앱 리소스 또는 기본 워크플로를 엽니다.

   > [!NOTE]
   > 논리 앱 리소스는 복제 작업 워크플로만 포함해야 합니다.

1. 리소스 또는 워크플로의 탐색 메뉴에서 **개요를** 선택합니다. **개요** 도구 모음에서 워크플로에 대해 **사용 안 함을** 선택하거나 논리 앱 리소스에 대해 **중지를** 선택합니다.

1. 복제 작업의 기본 논리 앱 리소스에서 원본 엔터티의 검사점 및 스트림 오프셋 정보를 저장하는 데 사용되는 스토리지 계정을 찾으려면 다음 단계를 수행합니다.

   1. 논리 앱 리소스 메뉴의 **설정** 아래에서 **구성을** 선택합니다.

   1. **구성** 창의 **애플리케이션 설정** 탭에서 **AzureWebJobsStorage** 앱 설정을 선택합니다.

      이 설정은 논리 앱 리소스에서 사용하는 연결 문자열 및 스토리지 계정을 지정합니다.

      > [!NOTE]
      > 앱 설정이 목록에 표시되지 않으면 값 **표시를** 선택합니다.

   1. 스토리지 계정 이름을 볼 수 있도록 **AzureWebJobsStorage** 앱 설정을 선택합니다.

   이 예제에서는 이 스토리지 계정의 이름을 찾는 방법을 보여 `storagefabrikamreplb0c` 주며, 여기서는 다음과 같습니다.

   !["AzureWebJobsStorage" 앱 설정 및 스토리지 계정 이름이 있는 연결 문자열이 있는 기본 논리 앱 리소스의 "구성" 창을 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/find-storage-account-name.png)

   1. 스토리지 계정 리소스가 있는지 확인하려면 Azure Portal 검색 상자에 이름을 입력한 다음 스토리지 계정을 선택합니다. 예를 들면 다음과 같습니다.

   ![스토리지 계정 이름이 입력된 Azure Portal 검색 상자를 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/find-storage-account.png)

1. 이제 다음 단계를 사용하여 원본 엔터티의 검사점 및 오프셋 정보가 포함된 폴더를 삭제합니다.

   1. 최신 버전이 없는 경우 최신 [Azure Storage Explorer 데스크톱 클라이언트](https://azure.microsoft.com/features/storage-explorer/)를 다운로드, 설치 및 엽니다.

      > [!NOTE]
      > 삭제 정리 작업의 경우 현재 Azure Portal 스토리지 탐색기, 브라우저, 편집기 또는 관리 환경이 *아닌* Azure Storage Explorer 클라이언트를 사용해야 합니다.
      >
      > PowerShell [ `Remove-AzStorageDirectory` 명령을](/powershell/module/az.storage/remove-azstoragedirectory)통해 컨테이너 폴더를 삭제할 수 있지만 이 명령은 *빈* 폴더에서만 작동합니다.

   1. 아직 로그인하지 않은 경우 Azure 계정으로 로그인하고 스토리지 계정 리소스에 대한 Azure 구독이 선택되어 있는지 확인합니다. 자세한 내용은 [Storage Explorer 시작을 검토하세요.](../vs-azure-tools-storage-manage-with-storage-explorer.md)

   1. 탐색기 창의 Azure 구독 이름 아래에서 **Storage 계정**{  >  ***your-storage-account-name*}**  >  **Blob 컨테이너**  >  **azure-webjobs-eventhub로** 이동합니다.

      > [!NOTE]
      > **azure-webjobs-eventhub** 폴더가 없으면 복제 작업이 아직 실행되지 않은 것입니다. 폴더는 복제 태스크가 한 번 이상 실행된 후에만 나타납니다.

      ![선택한 "azure-webjobs-eventhub" 폴더를 표시하기 위해 스토리지 계정 및 Blob 컨테이너가 열려 있는 Azure Storage Explorer 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/azure-webjobs-eventhub-storage-explorer.png)

   1. 열리는 **azure-webjobs-eventhub** 창에서 형식의 이름이 있는 Event Hubs 네임스페이스 폴더를 `<source-Event-Hubs-namespace-name>.servicebus.windows.net` 선택합니다.

   1. 네임스페이스 폴더가 열리면 **azure-webjobs-eventhub** 창에서 *<former-source-entity-name*> 폴더를 선택합니다. 도구 모음 또는 폴더의 바로 가기 메뉴에서 **삭제를** 선택합니다. 예를 들면 다음과 같습니다.

      !["삭제" 단추도 선택된 이전 원본 Event Hubs 엔터티 폴더를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/delete-former-source-entity-folder-storage-explorer.png)

   1. 폴더를 삭제할 것인지 확인합니다.

1. 복제 작업 뒤에 있는 논리 앱 리소스 또는 워크플로로 돌아갑니다. 논리 앱을 다시 시작하거나 워크플로를 다시 사용하도록 설정합니다.

생산자와 소비자가 새 원본 엔드포인트를 사용하도록 하려면 새 원본 엔터티에 대한 정보를 사용하여 쉽게 도달하고 업데이트할 수 있는 위치에서 찾을 수 있도록 해야 합니다. 생산자 또는 소비자가 자주 또는 지속적인 오류가 발생하는 경우 해당 위치를 확인하고 구성을 조정해야 합니다. 해당 구성을 공유하는 방법에는 여러 가지가 있지만 DNS 및 파일 공유가 그 예입니다.

지역 재해 복구에 대한 자세한 내용은 다음 설명서를 검토하세요.

- [Azure Event Hubs - 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md)
- [Azure Service Bus - 지역 재해 복구](../service-bus-messaging/service-bus-geo-dr.md)

<a name="edit-plan-scale-out-settings"></a>

## <a name="edit-hosting-plan-scale-out-settings"></a>호스팅 계획 스케일 아웃 설정 편집

### <a name="portal"></a>[포털](#tab/portal)

1. [Azure Portal](https://portal.azure.com)복제 작업에 대한 기본 논리 앱 리소스를 엽니다.

1. 논리 앱 리소스 메뉴의 **설정** 아래에서 **규모 확장(계획 App Service)을** 선택합니다.

   ![최대 버스트, 최소 인스턴스, 항상 준비된 인스턴스 및 규모 확장 제한 적용에 대한 호스팅 계획 설정을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-app-service-plan-settings.png)

1. 시나리오의 요구 사항에 따라 **계획 규모 확장** 및 **앱 규모 확장에서** 최대 버스트 및 항상 준비된 인스턴스의 값을 각각 변경합니다.

1. 완료되면 **규모 확장(App Service 계획)** 창 도구 모음에서 **저장을** 선택합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLI를 사용하여 앱에 대해 상시 준비 인스턴스를 구성할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <logic-app-app-name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```

---

자세한 내용은 워크플로 표준 계획이 Azure Functions Premium 계획과 몇 가지 측면을 공유하기 때문에 다음 설명서를 검토하세요.

- [계획 및 SKU 설정 - Azure Functions Premium 계획](../azure-functions/functions-premium-plan.md#plan-and-sku-settings)
- [클라우드 버스팅이란?](https://azure.microsoft.com/overview/what-is-cloud-bursting/)
- [항상 준비된 인스턴스 - 계획 Azure Functions Premium](../azure-functions/functions-premium-plan.md#always-ready-instances)

<a name="problems-failures"></a>

## <a name="replication-problems-and-failures"></a>복제 문제 및 실패

이 섹션에서는 복제가 실패하거나 작동을 중지할 수 있는 가능한 방법에 대해 설명합니다.

- 메시지 크기 제한

  복제 작업에서 복제 [속성](#replication-properties)를 추가하므로 1MB보다 작은 메시지를 보내야 합니다. 그렇지 않은 경우 메시지 크기가 태스크에서 [복제 속성을](#replication-properties)추가한 후 Event Hubs 엔터티로 보낼 수 있는 이벤트 크기보다 크면 복제 프로세스가 실패합니다.

  예를 들어 메시지 크기가 1MB라고 가정합니다. 작업에서 복제 속성을 추가하면 메시지 크기가 1MB보다 큽니다. 메시지를 보내려고 하는 아웃바운드 호출이 실패합니다.

- 파티션 키

  이벤트에 파티션 키가 있는 경우 해당 인스턴스의 파티션 수가 같으면 Event Hubs 인스턴스 간 복제가 실패합니다.

## <a name="next-steps"></a>다음 단계

- [단일 테넌트 Azure Logic Apps의 워크플로 디자이너 정보](designer-overview.md)
- [단일 테넌트 Azure Logic Apps에서 논리 앱에 대한 호스트 및 앱 설정 편집](edit-app-settings-host-settings.md)
- [단일 테넌트 Azure Logic Apps의 환경에 있는 워크플로에서 사용할 매개 변수 만들기](parameterize-workflow-app.md)
